---
title : "Tạo Dead Letter Queue"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 4.3.2. </b> "
---

Nếu hàm Lambda không thể xử lý thành công một bản ghi mà nó nhận được từ DynamoDB stream, dịch vụ Lambda nên ghi thông tin siêu dữ liệu (metadata) của bản ghi lỗi vào hàng đợi chết (Dead Letter Queue - DLQ) để lý do của lỗi có thể được điều tra và giải quyết.

Vì vậy, hãy tạo một [Amazon SQS Dead Letter Queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html) tên là **orders-ddbs-dlq** cho trigger hàm Lambda của bạn bằng lệnh AWS CLI dưới đây.

```bash
aws sqs create-queue --queue-name orders-ddbs-dlq
```

Ví dụ kết quả:

```
{
    "QueueUrl": "https://sqs.{aws-region}.amazonaws.com/{aws-account-id}/orders-ddbs-dlq"
}
```

Sau đó, bạn sẽ cần ARN của hàng đợi. Sử dụng lệnh dưới đây, sửa đổi URL hàng đợi sau _--queue-url_ để khớp với kết quả của lệnh trước đó, và sau đó lưu ARN để sử dụng sau này.

```bash
aws sqs get-queue-attributes --attribute-names "QueueArn" --query 'Attributes.QueueArn' --output text \
--queue-url "https://sqs.{aws-region}.amazonaws.com/{aws-account-id}/orders-ddbs-dlq"
```