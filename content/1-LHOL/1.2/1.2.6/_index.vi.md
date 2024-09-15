---
title : "Transactions"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 1.2.6. </b> "
---

DynamoDB cung cấp [API TransactWriteItems](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TransactWriteItems.html), một thao tác ghi đồng bộ nhóm lên đến 100 yêu cầu hành động (tổng cộng không vượt quá giới hạn kích thước 4MB cho giao dịch). Nó được gọi bằng [lệnh CLI transact-write-items](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/transact-write-items.html).

Các hành động này có thể nhắm đến các mục trong các bảng khác nhau, nhưng không thể nhắm đến các tài khoản hoặc Vùng AWS khác nhau, và không có hai hành động nào có thể nhắm đến cùng một mục. Các hành động được hoàn thành một cách nguyên tử sao cho tất cả đều thành công, hoặc tất cả đều thất bại. Để thảo luận sâu hơn về Cấp độ Cô lập cho Giao dịch, hãy xem [Hướng dẫn Dành cho Nhà Phát triển](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html#transaction-isolation).

Bạn sẽ nhớ từ các mô-đun trước rằng dữ liệu mẫu chứa nhiều bảng liên quan: **Forum**, **Thread**, và **Reply**. Khi một mục **Reply** mới được thêm vào, chúng ta cũng cần tăng số lượng Messages trong mục **Forum** liên quan. Điều này nên được thực hiện trong một giao dịch để cả hai thay đổi đều thành công hoặc cả hai thay đổi đều thất bại cùng một lúc, và ai đó đọc dữ liệu này sẽ thấy cả hai thay đổi hoặc không thấy thay đổi nào cùng một lúc.

Giao dịch trong DynamoDB tôn trọng khái niệm idempotency. Idempotency cho phép bạn gửi cùng một giao dịch nhiều lần, nhưng DynamoDB sẽ chỉ thực thi giao dịch đó một lần. Điều này đặc biệt hữu ích khi sử dụng các API không tự chúng là idempotent, như sử dụng UpdateItem để tăng hoặc giảm một trường số. Khi thực thi một giao dịch, bạn sẽ chỉ định một chuỗi để đại diện cho **ClientRequestToken** (còn gọi là Idempotency Token). Để thảo luận thêm về idempotency, vui lòng xem [Hướng dẫn Dành cho Nhà Phát triển](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html).

Lệnh này trong CLI sẽ là:

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

Hãy xem mục **Forum** và bạn sẽ thấy rằng số đếm Messages đã được tăng lên 1, từ 4 lên 5.

```bash
aws dynamodb get-item \
    --table-name Forum \
    --key '{"Name" : {"S": "Amazon DynamoDB"}}'
```

```text
...
"Messages": {
    "N": "5"
}
...
```

Nếu bạn chạy cùng lệnh giao dịch này một lần nữa với cùng giá trị `client-request-token`, bạn có thể xác minh rằng các lần thực thi khác của giao dịch về cơ bản bị bỏ qua và thuộc tính **Messages** vẫn giữ nguyên ở **5**.

Bây giờ chúng ta cần thực hiện một giao dịch khác để đảo ngược thao tác trên và dọn dẹp bảng:

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

