---
title : "Xóa Data"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 1.2.5. </b> "
---


[DeleteItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html) của DynamoDB được sử dụng để xóa một mục. Nó được gọi bằng lệnh  [delete-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/delete-item.html) .

Việc xóa trong DynamoDB luôn là các thao tác đơn lẻ. Không có lệnh đơn nào bạn có thể thực hiện để xóa tất cả các hàng trong bảng trong một lần thực hiện.

Nhớ rằng mục mà chúng ta đã thêm vào bảng `Reply` trong đường dẫn trước:

```bash
aws dynamodb get-item \
    --table-name Reply \
    --key '{
        "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
        "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"}
    }'
```

Bây giờ, chúng ta sẽ xóa mục này. Khi sử dụng lệnh `delete-item`, chúng ta cần tham chiếu đầy đủ Khóa chính như chúng ta đã làm với `get-item`:

```bash
aws dynamodb delete-item \
    --table-name Reply \
    --key '{
        "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
        "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"}
    }'
```

Việc xóa một mục nhiều lần là an toàn. Bạn có thể chạy lệnh trên bao nhiêu lần tùy thích mà không báo cáo lỗi; nếu khóa không tồn tại, thì API `DeleteItem` vẫn trả về thành công.

Sau khi đã xóa mục đó khỏi bảng `Reply`, chúng ta cũng cần giảm số lượng tin nhắn liên quan **Forum** _Messages_ count.

```bash
aws dynamodb update-item \
    --table-name Forum \
    --key '{
        "Name" : {"S": "Amazon DynamoDB"}
    }' \
    --update-expression "SET Messages = :newMessages" \
    --condition-expression "Messages = :oldMessages" \
    --expression-attribute-values '{
        ":oldMessages" : {"N": "5"},
        ":newMessages" : {"N": "4"}
    }' \
    --return-consumed-capacity TOTAL
```

