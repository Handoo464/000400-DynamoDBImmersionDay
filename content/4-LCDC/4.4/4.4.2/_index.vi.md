---
title : "Tạo Dead Letter Queue"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 4.4.2. </b> "
---

Nếu hàm Lambda không thể xử lý thành công bất kỳ bản ghi nào mà nó nhận được từ Amazon Kinesis, dịch vụ Lambda nên ghi siêu dữ liệu của bản ghi lỗi vào hàng đợi chết (Dead Letter Queue - DLQ) để lý do thất bại có thể được điều tra và khắc phục.

Vì vậy, hãy tạo một [Amazon SQS Dead Letter Queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html) có tên **orders-kds-dlq** cho trigger của hàm Lambda của bạn bằng cách sử dụng lệnh AWS CLI dưới đây.

```bash
aws sqs create-queue --queue-name orders-kds-dlq
```

Ví dụ về kết quả đầu ra:

```
{
    "QueueUrl": "https://sqs.{aws-region}.amazonaws.com/{aws-account-id}/orders-kds-dlq"
}
```

Như trước đây, bạn sẽ cần ARN của hàng đợi. Sử dụng lệnh dưới đây, thay đổi URL hàng đợi sau _--queue-url_ để phù hợp với kết quả của lệnh trước đó.

```bash
aws sqs get-queue-attributes --attribute-names "QueueArn" --query 'Attributes.QueueArn' --output text \
--queue-url "https://sqs.{aws-region}.amazonaws.com/{aws-account-id}/orders-kds-dlq"
```