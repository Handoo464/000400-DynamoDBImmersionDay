---
title : "Xóa Data"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 1.2.5. </b> "
---

DynamoDB cung cấp [API DeleteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html) để xóa một mục. Nó được gọi bằng [lệnh CLI delete-item](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/delete-item.html).

Việc xóa trong DynamoDB luôn là các thao tác đơn lẻ. Không có lệnh nào mà bạn có thể chạy để xóa tất cả các hàng trong bảng, chẳng hạn.

Hãy nhớ mục mà chúng ta đã thêm vào bảng **Reply** trong phần trước:

```bash
aws dynamodb get-item \
    --table-name Reply \
    --key '{
        "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
        "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"}
    }'
```

Hãy xóa mục này. Khi sử dụng lệnh `delete-item`, chúng ta cần tham chiếu đến đầy đủ Khóa Chính giống như khi dùng `get-item`:

```bash
aws dynamodb delete-item \
    --table-name Reply \
    --key '{
        "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
        "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"}
    }'
```

Bạn có thể an toàn xóa cùng một mục nhiều lần. Bạn có thể chạy cùng một lệnh trên nhiều lần tùy ý mà không gặp lỗi; nếu khóa không tồn tại thì API DeleteItem vẫn trả về thành công.

Sau khi chúng ta đã xóa mục đó khỏi bảng **Reply**, chúng ta cũng cần giảm số đếm **Messages** liên quan trong bảng **Forum**.

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

