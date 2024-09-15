---
title : "Bước 2: Đảm bảo idempotency của hàm ReduceLambda"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 6.4.2. </b> "
---

Mục tiêu của bước này là chỉnh sửa hàm `ReduceLambda` để đảm bảo tính idempotency, có nghĩa là các giá trị của bảng hạ nguồn `AggregateTable` sẽ không thay đổi khi các bản ghi cũ được xử lý lại trong hàm `ReduceLambda`. Giao dịch DynamoDB cung cấp tính idempotency thông qua tham số `ClientRequestToken` có thể được cung cấp với thao tác API `TransactWriteItems`. `ClientRequestToken` đảm bảo rằng các lần gọi giao dịch tiếp theo với token đã được sử dụng trong 10 phút trước đó sẽ không dẫn đến các cập nhật cho bảng DynamoDB.

Chúng ta tính toán giá trị hash trên tất cả các thông điệp trong batch mà hàm Lambda được gọi để sử dụng làm `ClientRequestToken`. Lambda đảm bảo rằng hàm được thử lại với cùng một batch thông điệp khi xảy ra lỗi. Do đó, bằng cách đảm bảo rằng tất cả các đường dẫn mã trong hàm Lambda là quyết định, chúng ta có thể đảm bảo tính idempotency của các giao dịch và đạt được quá trình xử lý chỉ một lần ở giai đoạn cuối cùng này của pipeline. Phương pháp này có một điểm yếu vì chúng ta chỉ bảo vệ chống lại các thông điệp được xử lý lại trong một khoảng thời gian 10 phút sau cuộc gọi `TransactWriteItems` đầu tiên hoàn tất, vì `ClientRequestToken` chỉ có giá trị trong không quá 10 phút, như được nêu trong [tài liệu chính thức](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TransactWriteItems.html#DDB-TransactWriteItems-request-ClientRequestToken).

![Kiến trúc-1](/images/6/6.4/4.png)

1. Điều hướng đến dịch vụ AWS Lambda trong AWS Management Console.
2. Nhấp vào hàm `ReduceLambda` để chỉnh sửa cấu hình của nó.
3. Nhấp vào tab `Code` để truy cập mã nguồn của hàm Lambda.

Tìm đoạn mã sau trong mã nguồn của hàm Lambda:

```python
# Batch of Items
batch = [
    { 'Update':
        {
            'TableName' : constants.AGGREGATE_TABLE_NAME,
            'Key' : {constants.AGGREGATE_TABLE_KEY : {'S' : entry}},
            'UpdateExpression' : "ADD #val :val ",
            'ExpressionAttributeValues' : {
                ':val': {'N' : str(totals[entry])}
            },
            'ExpressionAttributeNames': {
                "#val" : "Value"
            }
        }
    } for entry in totals.keys()]

response = ddb_client.transact_write_items(
        TransactItems = batch
)
```

Đoạn mã này thực hiện các thao tác sau:

- Tạo danh sách các từ điển Python chứa các mục tương ứng với các thao tác mục sẽ được xử lý bởi API `TransactWriteItems`. Để xem tất cả các tùy chọn cho trường bao gồm `Update`, xem [tài liệu API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TransactWriteItems.html).
- Cụ thể, mỗi mục `Update` trong cuộc gọi API thực hiện một thay đổi đối với một mục DynamoDB được khóa bằng `entry` bằng cách tăng nguyên tử thuộc tính `Value` lên tổng số đã tính toán.

## Chỉnh sửa câu lệnh ddb_client.transact_write_items để bao gồm ClientRequestToken

Đoạn mã dưới đây có hai chỉnh sửa:

- Tính toán thuộc tính `ClientRequestToken` dưới dạng giá trị hash của tất cả các thông điệp trong batch Lambda.
- Cung cấp `ClientRequestToken` như một phần của cuộc gọi API `TransactWriteItems` của DynamoDB.

```python
# Batch of Items
batch = [
    { 'Update':
        {
            'TableName' : constants.AGGREGATE_TABLE_NAME,
            'Key' : {constants.AGGREGATE_TABLE_KEY : {'S' : entry}},
            'UpdateExpression' : "ADD #val :val ",
            'ExpressionAttributeValues' : {
                ':val': {'N' : str(totals[entry])}
            },
            'ExpressionAttributeNames': {
                "#val" : "Value"
            }
        }
    } for entry in totals.keys()]

# Calculate hash to ensure this batch hasn't been processed already:
record_list_hash = hashlib.md5(str(records).encode()).hexdigest()

response = ddb_client.transact_write_items(
    TransactItems = batch,
    ClientRequestToken = record_list_hash
)
```

Áp dụng những thay đổi này vào mã nguồn của hàm Lambda, bằng cách chỉnh sửa thủ công hoặc sao chép đoạn mã từ trên:

- Tính toán hash trên tất cả các bản ghi trong batch (xem dòng 18 trong đoạn mã trên).
- Cung cấp hash này cho hàm `ddb_client.transact_write_items` như một `ClientRequestToken` (dòng 8 trong đoạn mã trên).
- Cuối cùng, nhấp vào `Deploy` để áp dụng các thay đổi.

## Làm sao để biết nó hoạt động?

Kiểm tra bảng xếp hạng của bạn. Nếu tất cả các bước trước đó được hoàn thành thành công, bạn sẽ bắt đầu tích lũy điểm trên 300 điểm. Nếu không, hãy kiểm tra CloudWatch Logs của hàm `ReduceLambda` để kiểm tra xem có lỗi nào không. Nếu bạn thấy bất kỳ lỗi nào, chúng có thể cung cấp gợi ý về cách khắc phục. Nếu bạn cần trợ giúp, đi đến `Summary & Conclusions` ở bên trái, sau đó `Solutions`, và bạn có thể xem mã mong muốn của `ReduceLambda`.

Ngay cả khi bạn đã làm mọi thứ đúng, tỷ lệ lỗi sẽ không giảm xuống 0! Các lỗi gây ra thủ công vẫn sẽ có, nhưng bây giờ pipeline có thể chịu đựng chúng và vẫn đảm bảo sự tổng hợp nhất quán.
