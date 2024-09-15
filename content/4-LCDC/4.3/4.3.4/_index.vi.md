---
title : "Cập nhật IAM Role"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4.3.4 </b> "
---

Cấu hình hàm Lambda của bạn để sao chép các bản ghi đã thay đổi từ DynamoDB streams của bảng Orders sang bảng OrdersHistory bằng cách thực hiện các bước sau.

1. Truy cập bảng điều khiển IAM trên AWS Management Console và kiểm tra chính sách IAM, tức là **AWSLambdaMicroserviceExecutionRole...**, được tạo khi bạn tạo hàm Lambda create-order-history-ddbs.

![Bảng điều khiển hàm AWS Lambda](/images/4/4.3/5.png)

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:DeleteItem",
                "dynamodb:GetItem",
                "dynamodb:PutItem",
                "dynamodb:Scan",
                "dynamodb:UpdateItem"
            ],
            "Resource": "arn:aws:dynamodb:{aws-region}:{aws-account-id}:table/*"
        }
    ]
}
```

2. Cập nhật câu lệnh chính sách bằng cách chỉnh sửa và thay thế chính sách hiện tại bằng câu lệnh chính sách IAM sau.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:DescribeStream",
                "dynamodb:GetRecords",
                "dynamodb:GetShardIterator",
                "dynamodb:ListStreams"
            ],
            "Resource": "arn:aws:dynamodb:{aws-region}:{aws-account-id}:table/Orders/stream/*"
        },
        {
            "Effect": "Allow",
            "Action": "dynamodb:PutItem",
            "Resource": "arn:aws:dynamodb:{aws-region}:{aws-account-id}:table/OrdersHistory"
        },
        {
            "Effect": "Allow",
            "Action": "sqs:SendMessage",
            "Resource": "arn:aws:sqs:{aws-region}:{aws-account-id}:orders-ddbs-dlq"
        }
    ]
}
```

Chính sách IAM đã cập nhật cung cấp cho hàm Lambda create-order-history-ddbs quyền cần thiết để đọc các sự kiện từ stream của bảng Orders DynamoDB, ghi các mục mới vào bảng OrdersHistory DynamoDB và gửi tin nhắn đến hàng đợi SQS orders-ddbs-dlq.

{{%notice tip%}}
Thay thế **{aws-region}** và **{aws-account-id}** trong câu lệnh chính sách trên bằng giá trị đúng cho khu vực AWS của bạn và ID tài khoản AWS của bạn.
{{%/notice%}}

3. Đi đến trình chỉnh sửa bảng điều khiển hàm Lambda. Chọn **Layers** sau đó chọn **Add a Layer**.

![Bảng điều khiển hàm AWS Lambda](/images/4/4.3/6.png)

![Bảng điều khiển hàm AWS Lambda](/images/4/4.3/7.png)

4. Chọn **Specify an ARN**, nhập ARN của Lambda Layer dưới đây.

```bash
arn:aws:lambda:{aws-region}:017000801446:layer:AWSLambdaPowertoolsPythonV2:58
```

5. Nhấp vào **Verify** sau đó chọn **Add**.

![Bảng điều khiển hàm AWS Lambda](/images/4/4.3/8.png)

Thay thế {aws-region} bằng ID của khu vực AWS mà bạn đang làm việc.

6. Đi đến phần cấu hình của trình chỉnh sửa bảng điều khiển Lambda. Chọn **Environment variables** sau đó chọn **Edit**.

![Bảng điều khiển hàm AWS Lambda](/images/4/4.3/9.png)

7. Thêm một biến môi trường mới có tên **ORDERS_HISTORY_DB** và đặt giá trị của nó là **OrdersHistory**.

![Bảng điều khiển hàm AWS Lambda](/images/4/4.3/10.png)

8. Chọn **Triggers** sau đó chọn **Add trigger**.

![Bảng điều khiển hàm AWS Lambda](/images/4/4.3/11.png)

9. Chọn **DynamoDB** làm nguồn kích hoạt.
10. Chọn bảng DynamoDB **Orders**.
11. Đặt **Batch size** thành **10** và giữ nguyên các giá trị khác.

![Bảng điều khiển hàm AWS Lambda](/images/4/4.3/12.png)

12. Nhấp vào **Additional settings** để mở rộng phần này.
13. Cung cấp ARN của hàng đợi SQS **orders-ddbs-dlq** mà bạn đã tạo trước đó.

```bash
arn:aws:sqs:{aws-region}:{aws-account-id}:orders-ddbs-dlq
```

14. Đặt số lần thử lại (Retry attempts) thành 3.

![Bảng điều khiển hàm AWS Lambda](/images/4/4.3/13.png)

15. Chọn **Add**.