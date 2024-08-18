---
title : "Làm việc với Table Scans"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.2.3. </b> "
---

Như đã đề cập trước đó,  [Scan API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html) có thể được gọi thông qua  [scan CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html) Sẽ thực hiện quét toàn bộ bảng và trả về các mục trong từng phần 1MB.

Scan API tương tự như Query API, ngoại trừ việc vì chúng ta muốn quét toàn bộ bảng chứ không chỉ một Item Collection, nên không có Biểu thức Điều kiện Khóa (Key Condition Expression) cho một Scan. Tuy nhiên, bạn có thể chỉ định một Biểu thức Lọc (Filter Expression) để giảm kích thước tập hợp kết quả (mặc dù điều này sẽ không giảm lượng năng lượng tiêu thụ).

Ví dụ, chúng ta có thể tìm tất cả các phản hồi trong bảng Reply mà được đăng bởi Người Dùng A:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --return-consumed-capacity TOTAL
```

Lưu ý rằng trong phản hồi, chúng ta thấy các dòng sau:

```
"Count": 3,
"ScannedCount": 4,
```

Điều này cho biết rằng Scan đã quét tất cả 4 mục (ScannedCount) trong bảng và đó là những gì chúng ta bị tính phí để đọc, nhưng Filter Expression đã giảm kích thước tập hợp kết quả xuống còn 3 mục (Count).

Đôi khi quét dữ liệu, sẽ có nhiều dữ liệu hơn mức có thể chứa trong phản hồi nếu quét chạm giới hạn 1MB ở phía máy chủ, hoặc có thể còn nhiều mục hơn so với tham số `--max-items` mà chúng ta đã chỉ định. Trong trường hợp đó, phản hồi của quét sẽ bao gồm một `NextToken`, mà sau đó chúng ta có thể sử dụng để thực hiện một cuộc quét tiếp theo bắt đầu từ nơi chúng ta đã dừng lại. Ví dụ, trong quét trước đó, chúng ta biết rằng có 3 mục trong tập kết quả. Hãy chạy lại nhưng giới hạn số mục tối đa là 2:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --max-items 2 \
    --return-consumed-capacity TOTAL
```

Chúng ta có thể thấy trong phản hồi rằng có một:

```
"NextToken": "eyJFeGNsdXNpdmVTdGFydEtleSI6IG51bGwsICJib3RvX3RydW5jYXRlX2Ftb3VudCI6IDJ9"
```

Vì vậy, chúng ta có thể thực hiện yêu cầu quét một lần nữa, lần này truyền giá trị `NextToken` vào tham số `--starting-token`:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --max-items 2 \
    --starting-token eyJFeGNsdXNpdmVTdGFydEtleSI6IG51bGwsICJib3RvX3RydW5jYXRlX2Ftb3VudCI6IDJ9 \
    --return-consumed-capacity TOTAL
```

### **Bài Tập**
Khám phá dữ liệu trong bảng Forum và viết một lệnh quét để trả về chỉ các Forums có hơn 1 thread và hơn 50 lượt xem.

**Gợi ý**: Đọc về các Từ Được Đặt Hàng (Reserved Words) trong DynamoDB và cách xử lý các tình huống khi một trong các tên thuộc tính của bạn là một từ được đặt hàng.

Giải pháp có thể mở rộng bên dưới nhưng hãy cố gắng tìm ra nó trước khi tiếp tục.

Đầu tiên ta cần hiểu về cấu trúc dữ liệu của bảng Forum Table vì thế thực hiện scan để thấy những thuộc tính tồn tại: 
```bash
aws dynamodb scan \
    --table-name Forum
```

```json
{
    "Count": 2,
    "Items": [
        {
            "Category": {
                "S": "Amazon Web Services"
            },
            "Name": {
                "S": "Amazon S3"
            }
        },
        {
            "Category": {
                "S": "Amazon Web Services"
            },
            "Threads": {
                "N": "2"
            },
            "Messages": {
                "N": "4"
            },
            "Name": {
                "S": "Amazon DynamoDB"
            },
            "Views": {
                "N": "1000"
            }
        }
    ],
    "ScannedCount": 2,
    "ConsumedCapacity": null
}
```

Chúng ta có thể thấy rằng một số mục có thuộc tính số `Threads` và thuộc tính số `Views`. Để giải quyết vấn đề này, chúng ta muốn sử dụng các thuộc tính đó trong `FilterExpression`. Hãy chắc chắn chỉ định rằng những giá trị này có kiểu Number bằng cách sử dụng "N" trong tham số `--expression-attribute-values`.

```bash
aws dynamodb scan \
    --table-name Forum \
    --filter-expression 'Threads >= :threads AND Views >= :views' \
    --expression-attribute-values '{
        ":threads" : {"N": "1"},
        ":views" : {"N": "50"}
    }' \
    --return-consumed-capacity TOTAL
```

Khi bạn chạy lệnh này, bạn sẽ nhận được lỗi sau:

```text
An error occurred (ValidationException) when calling the Scan operation: Invalid FilterExpression: Attribute name is a reserved keyword; reserved keyword: Views
```
Điều này là do thuộc tính `Views` thực sự là một  [DynamoDB Reserved Word](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ReservedWords.html). DynamoDB cho phép chúng ta đặt một dấu chấm hỏi trong `FilterExpression` và cung cấp tên thuộc tính thực tế trong tham số `--expression-attribute-names` của CLI. Để biết thêm thông tin, hãy xem [Expression Attribute Names in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ExpressionAttributeNames.html) trong Developer Guide.

Để giải quyết vấn đề này, bạn có thể sử dụng lệnh sau:

```bash
aws dynamodb scan \
    --table-name Forum \
    --filter-expression 'Threads >= :threads AND #Views >= :views' \
    --expression-attribute-values '{
        ":threads" : {"N": "1"},
        ":views" : {"N": "50"}
    }' \
    --expression-attribute-names '{"#Views" : "Views"}' \
    --return-consumed-capacity TOTAL
```
