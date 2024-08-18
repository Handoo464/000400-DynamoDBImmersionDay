---
title : "Transactions"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 1.2.6. </b> "
---

**TransactWriteItems** API của DynamoDB là một hoạt động ghi đồng bộ, cho phép nhóm tối đa 100 yêu cầu hành động (với giới hạn kích thước 4MB tổng cho giao dịch). Nó được gọi thông qua lệnh CLI **transact-write-items**.

Các hành động này có thể nhắm đến các item trong nhiều bảng khác nhau, nhưng không thể ở các tài khoản hoặc vùng AWS khác nhau, và không hai hành động nào được nhắm đến cùng một item. Các hành động được hoàn thành một cách nguyên tử, có nghĩa là hoặc tất cả chúng thành công, hoặc tất cả chúng thất bại. Để tìm hiểu thêm về các mức độ cô lập cho giao dịch, bạn có thể tham khảo [Developer Guide](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html#transaction-isolation).

Bạn sẽ nhớ từ các mô-đun trước rằng dữ liệu mẫu chứa nhiều bảng có liên quan: **Forum**, **Thread**, và **Reply**. Khi thêm một item mới **Reply**, chúng ta cũng cần tăng số **Messages** trong item **Forum** liên quan. Điều này cần được thực hiện trong một giao dịch để cả hai thay đổi đều thành công hoặc cả hai thay đổi đều thất bại cùng một lúc, và ai đó đang đọc dữ liệu này sẽ thấy cả hai thay đổi hoặc không nhìn thấy thay đổi nào.

Giao dịch trong DynamoDB tôn trọng khái niệm **idempotency (tính đồng nhất)** . Idempotency giúp bạn có khả năng gửi cùng một giao dịch nhiều lần, nhưng DynamoDB chỉ thực hiện giao dịch đó một lần. Điều này đặc biệt hữu ích khi sử dụng các API không phải là idempotent, chẳng hạn như sử dụng **UpdateItem** để tăng hoặc giảm một trường số. Khi thực hiện một giao dịch, bạn sẽ chỉ định một chuỗi để đại diện cho **ClientRequestToken** (còn gọi là Idempotency Token). Để tìm hiểu thêm về idempotency, xin vui lòng xem  [Developer Guide](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html).

Lệnh trong CLI sẽ là:

```bash
aws dynamodb transact-write-items --client-request-token TRANSACTION1 --transact-items '[
    {
        "Put": {
            "TableName" : "Reply",
            "Item" : {
                "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
                "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"},
                "Message" : {"S": "DynamoDB Thread 2 Reply 3 text"},
                "PostedBy" : {"S": "User C"}
            }
        }
    },
    {
        "Update": {
            "TableName" : "Forum",
            "Key" : {"Name" : {"S": "Amazon DynamoDB"}},
            "UpdateExpression": "ADD Messages :inc",
            "ExpressionAttributeValues" : { ":inc": {"N" : "1"} }
        }
    }
]'
```

Khi bạn nhìn vào item **Forum**, bạn sẽ thấy rằng số lượng **Messages** đã được tăng thêm 1, từ 4 lên 5.

```bash
aws dynamodb get-item \
    --table-name Forum \
    --key '{"Name" : {"S": "Amazon DynamoDB"}}'
```

Nếu bạn chạy cùng một lệnh giao dịch một lần nữa, với cùng một giá trị **client-request-token**, bạn có thể xác minh rằng các lần thực hiện giao dịch khác về cơ bản bị bỏ qua và số **Messages** vẫn giữ nguyên ở mức 5.

Bây giờ chúng ta cần thực hiện một giao dịch khác để đảo ngược hoạt động trên và dọn dẹp bảng:

```bash
aws dynamodb transact-write-items --client-request-token TRANSACTION2 --transact-items '[
    {
        "Delete": {
            "TableName" : "Reply",
            "Key" : {
                "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
                "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"}
            }
        }
    },
    {
        "Update": {
            "TableName" : "Forum",
            "Key" : {"Name" : {"S": "Amazon DynamoDB"}},
            "UpdateExpression": "ADD Messages :inc",
            "ExpressionAttributeValues" : { ":inc": {"N" : "-1"} }
        }
    }
]'
```

