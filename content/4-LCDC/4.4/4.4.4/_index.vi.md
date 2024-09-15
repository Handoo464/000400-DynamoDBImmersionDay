---
title : "Cấu hình hàm Lambda"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4.4.4. </b> "
---

Cấu hình hàm Lambda của bạn để sao chép các bản ghi đã thay đổi từ Orders Kinesis Data Stream sang bảng OrdersHistory.

1. Truy cập trang tổng quan IAM trên AWS Management Console và kiểm tra chính sách IAM, ví dụ: **AWSLambdaMicroserviceExecutionRole...**, được tạo khi bạn tạo hàm Lambda **create-order-history-kds**.

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

2. Cập nhật câu lệnh chính sách bằng cách chỉnh sửa và thay thế chính sách hiện có bằng câu lệnh chính sách IAM sau.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kinesis:DescribeStream",
                "kinesis:GetRecords",
                "kinesis:GetShardIterator",
                "kinesis:ListStreams"
            ],
            "Resource": "arn:aws:kinesis:{aws-region}:{aws-account-id}:stream/Orders"
        },
        {
            "Effect": "Allow",
            "Action": "dynamodb:PutItem",
            "Resource": "arn:aws:dynamodb:{aws-region}:{aws-account-id}:table/OrdersHistory"
        },
        {
            "Effect": "Allow",
            "Action": "sqs:SendMessage",
            "Resource": "arn:aws:sqs:{aws-region}:{aws-account-id}:orders-kds-dlq"
        }
    ]
}
```

{{%notice tip%}}
Thay thế **{aws-region}** và **{aws-account-id}** trong câu lệnh chính sách trên bằng AWS region và ID tài khoản AWS của bạn tương ứng.
{{%/notice%}}

3. Chọn **Layers**, sau đó chọn **Add a Layer**.

![AWS Lambda function console](/images/4/4.4/3.png)

![AWS Lambda function console](/images/4/4.4/9.png)

4. Chọn **Specify an ARN**, nhập ARN của Lambda Layer bên dưới.

```bash
arn:aws:lambda:{aws-region}:017000801446:layer:AWSLambdaPowertoolsPythonV2:58
```

5. Nhấp vào **Verify**, sau đó chọn **Add**.

![AWS Lambda function console](/images/4/4.4/4.png)

Thay thế {aws-region} bằng ID cho AWS region mà bạn đang làm việc.

6. Đi đến phần cấu hình của trình soạn thảo console Lambda. Chọn **Environment variables** rồi chọn **Edit**.

![AWS Lambda function console](/images/4/4.4/5.png)

7. Thêm một biến môi trường mới có tên **ORDERS_HISTORY_DB** và đặt giá trị của nó là **OrdersHistory**.

![AWS Lambda function console](/images/4/4.4/6.png)

8. Đi đến phần cấu hình của trình soạn thảo console Lambda. Chọn **Triggers**, sau đó chọn **Add trigger**.
9. Chọn **Kinesis** làm nguồn kích hoạt.
10. Chọn bảng DynamoDB **Orders**.
11. Đặt **Batch size** là **10** và để tất cả các giá trị khác không thay đổi.

![AWS Lambda function console](/images/4/4.4/7.png)

12. Nhấp vào **Additional settings** để mở rộng phần này.
13. Cung cấp ARN của hàng đợi SQS orders-kds-dlq mà bạn đã tạo trước đó.
14. Đặt Retry attempts thành 3.

![AWS Lambda function console](/images/4/4.4/8.png)

15. Chọn **Add**.