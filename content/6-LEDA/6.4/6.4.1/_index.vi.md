---
title : "Bước 1: Ngăn chặn trùng lặp tại hàm StateLambda"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 6.4.1. </b> "
---

Mục tiêu của bước này là chỉnh sửa hàm `StateLambda` để đảm bảo nó không ghi thành công các thông điệp trùng lặp vào các tài nguyên hạ nguồn.

![Kiến trúc-1](/images/6/6.4/3.png)

## Nghiên cứu mã nguồn của StateLambda

1. Sử dụng AWS Management Console và điều hướng đến dịch vụ AWS Lambda trong console.
2. Nhấp vào hàm `StateLambda` để chỉnh sửa cấu hình của nó.
3. Nhấp vào tab `Code` để truy cập mã nguồn của hàm Lambda.

Trong trình duyệt mã của Lambda, tìm đoạn mã sau:

```python
table.update_item(
    Key = {
        constants.STATE_TABLE_KEY: record_id
        },
    UpdateExpression = 'SET  #VALUE     = :new_value,' + \
                            '#VERSION   = :new_version,' + \
                            '#HIERARCHY = :new_hierarchy,' + \
                            '#TIMESTAMP = :new_time',
    ExpressionAttributeNames={
        '#VALUE':       constants.VALUE_COLUMN_NAME,
        '#VERSION':     constants.VERSION_COLUMN_NAME,
        '#HIERARCHY':   constants.HIERARCHY_COLUMN_NAME,
        '#TIMESTAMP':   constants.TIMESTAMP_COLUMN_NAME
        },
    ExpressionAttributeValues={
        ':new_version':     record_version,
        ':new_value':       Decimal(str(record_value)),
        ':new_hierarchy':   json.dumps(record_hierarchy, sort_keys = True),
        ':new_time':        Decimal(str(record_time))
        }
    )
```

Lời gọi phương thức này tạo ra một thao tác [UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html) với các thuộc tính sau:

- Một thao tác ghi sẽ được thực hiện trên các thuộc tính được liệt kê trong `UpdateExpression`:
  - Giá trị, phiên bản, hệ thống phân cấp, và dấu thời gian sẽ được thiết lập với các giá trị cung cấp trong `ExpressionAttributeValues`.
  - Tên thuộc tính được định nghĩa trong `ExpressionAttributeNames` được lấy từ các hằng số chúng ta đã định nghĩa bên ngoài tệp hàm này.
- Giá trị của biến `STATE_TABLE_KEY` chứa tên khóa phân vùng, và giá trị của thuộc tính sẽ là `record_id`.
- Đáng chú ý là thao tác ghi này không có kiểm tra điều kiện để ngăn chặn ghi trùng lặp.

## Chỉnh sửa câu lệnh table.update_item để bao gồm biểu thức điều kiện

Bây giờ, hãy so sánh với đoạn mã sau:

```python
table.update_item(
    Key = {
        constants.STATE_TABLE_KEY: record_id
        },
    ConditionExpression = 'attribute_not_exists(' + constants.STATE_TABLE_KEY + ') OR ' + constants.VERSION_COLUMN_NAME + '< :new_version',
    UpdateExpression = 'SET  #VALUE     = :new_value,' + \
                            '#VERSION   = :new_version,' + \
                            '#HIERARCHY = :new_hierarchy,' + \
                            '#TIMESTAMP = :new_time',
    ExpressionAttributeNames={
        '#VALUE':       constants.VALUE_COLUMN_NAME,
        '#VERSION':     constants.VERSION_COLUMN_NAME,
        '#HIERARCHY':   constants.HIERARCHY_COLUMN_NAME,
        '#TIMESTAMP':   constants.TIMESTAMP_COLUMN_NAME
        },
    ExpressionAttributeValues={
        ':new_version':     record_version,
        ':new_value':       Decimal(str(record_value)),
        ':new_hierarchy':   json.dumps(record_hierarchy, sort_keys = True),
        ':new_time':        Decimal(str(record_time))
        }
    )
```

Đoạn mã ở dòng 5 thêm một điều kiện phức hợp đảm bảo rằng một mục chỉ được chèn nếu nó chưa tồn tại, hoặc nếu có, thì nó chỉ được thay thế nếu mục _mới_ có số phiên bản lớn hơn (ví dụ, là một phiên bản mới hơn của mục). Đây là phiên bản đơn giản hóa của biểu thức điều kiện đó:

> attribute_not_exists('PRIMARY KEY') OR 'CURRENT VERSION' < 'NEW VERSION'

Giải thích, điều kiện đầu tiên chỉ ra rằng tại thời điểm chèn dữ liệu, bảng _không nên_ chứa một mục với khóa phân vùng `pk` bằng với `record_id` nếu không thao tác ghi sẽ thất bại (xem [hàm attribute_not_exists](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html#Expressions.OperatorsAndFunctions.Functions)), điều này ngụ ý rằng đây là lần đầu tiên một mục/thông điệp như vậy được chèn vào. Sau đó, với sự kết hợp của từ khóa _OR_, điều kiện nói rằng nếu một hàng đã tồn tại trong bảng và số phiên bản của hàng được chèn lớn hơn hàng hiện tại thì thao tác ghi nên thành công.

{{%notice tip%}}
Bạn chưa cần thay đổi gì trong mã Lambda của mình, điều này sẽ đến trong ít phút nếu bạn đọc tiếp.
{{%/notice%}}

### Tại sao nó hoạt động?

Biểu thức điều kiện này cho phép chúng ta phát hiện và xử lý các trường hợp khi một thông điệp bị trùng lặp bởi các thành phần đầu nguồn, hoặc nếu một số thông điệp đến không đúng thứ tự. Những tình huống này có thể xảy ra nếu Lambda ở đầu nguồn đưa cùng một thông điệp vào dòng Kinesis nhiều hơn một lần, chẳng hạn. Trong các trường hợp như vậy, lỗi `ConditionalCheckFailedException` sẽ được đưa ra và không có dữ liệu nào được chèn vào cơ sở dữ liệu. Tiếp theo, chúng ta cần chỉnh sửa mã của mình để xử lý đúng các lỗi này vì chúng hiện được coi là bình thường và mong đợi.

## Bắt ngoại lệ ClientError và triển khai thay đổi

Chúng ta muốn tránh việc hàm Lambda bị lỗi và sau đó phải khởi động lại, vì vậy chúng ta sẽ thêm một trình xử lý lỗi phù hợp trong trường hợp gặp lỗi khi ghi điều kiện. Đây là một phương pháp tốt nhất để kiểm tra tên của ngoại lệ, ví dụ: `e.response['Error']['Code']`, để xác định liệu ngoại lệ có bình thường hay chỉ ra một vấn đề sâu hơn.

Đoạn mã sau đây sẽ bỏ qua lỗi `ConditionalCheckFailedException` trong khi vẫn đưa ra lỗi cho tất cả các ngoại lệ khác:

```python
try:
    table.update_item(
        Key = {
            constants.STATE_TABLE_KEY: record_id
            },
        ConditionExpression = 'attribute_not_exists(' + constants.STATE_TABLE_KEY + ') OR ' + constants.VERSION_COLUMN_NAME + '< :new_version',
        UpdateExpression = 'SET  #VALUE     = :new_value,' + \
                                '#VERSION   = :new_version,' + \
                                '#HIERARCHY = :new_hierarchy,' + \
                                '#TIMESTAMP = :new_time',
        ExpressionAttributeNames={
            '#VALUE':       constants.VALUE_COLUMN_NAME,
            '#VERSION':     constants.VERSION_COLUMN_NAME,
            '#HIERARCHY':   constants.HIERARCHY_COLUMN_NAME,
            '#TIMESTAMP':   constants.TIMESTAMP_COLUMN_NAME
            },
        ExpressionAttributeValues={
            ':new_version':     record_version,
            ':new_value':       Decimal(str(record_value)),
            ':new_hierarchy':   json.dumps(record_hierarchy, sort_keys = True),
            ':new_time':        Decimal(str(record_time))
            }
        )
except ClientError as e:
    if e.response['Error']['Code']=='ConditionalCheckFailedException':
        print('Conditional put failed.' + \
            ' This is either a duplicate or a more recent version already arrived.')
        print('Id: ',           record_id)
        print('Hierarchy: ',    record_hierarchy)
        print('Value: ',        record_value)
        print('Version: ',      record_version)
        print('Timestamp: ',    record_time)
    else:
        raise e
```

Sao chép đoạn mã trên và thay thế nó vào vị trí của câu lệnh `table.update_item(...)` hiện có trong mã hàm `StateLambda` của bạn. Sau đó nhấp vào `Deploy` để áp dụng các thay đổi.

{{%notice tip%}}
Thay đổi trên cũng sẽ giúp tránh các ghi trùng lặp khi dịch vụ Lambda thử lại hàm `StateLambda` sau khi trước đó đã thất bại với một lô thông điệp đến. Với thay đổi này, chúng ta tránh ghi trùng lặp vào `StateTable`, điều này đảm bảo chúng ta không tạo ra các thông điệp bổ sung trong dòng DynamoDB stream của `StateTable`.
{{%/notice%}}

## Làm sao để biết bạn đã sửa lỗi?

Điều hướng đến `StateLambda` và mở `Logs` dưới tab `Monitor`. Kiểm tra các thông báo nhật ký bằng cách nhấp vào ô LogStream liên kết và xác minh rằng bạn thấy chuỗi sau trong các dòng nhật ký: `Conditional put failed. This is either a duplicate...`. Thông báo này được tạo ra bởi mã xử lý ngoại lệ ở trên. Điều này cho chúng ta biết rằng biểu thức điều kiện đang hoạt động như mong đợi.