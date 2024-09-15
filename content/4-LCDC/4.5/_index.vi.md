---
title : "5. Tóm tắt và dọn dẹp"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 4.5. </b> "
---

Chúc mừng! Bạn đã hoàn thành xong buổi workshop.

Trong buổi workshop này, bạn đã khám phá cách thu thập các thay đổi cấp mục trên bảng DynamoDB bằng cách sử dụng DynamoDB Streams và Kinesis Data Streams. Trong trường hợp này, bạn đã ghi phiên bản trước của các mục đã cập nhật vào một bảng DynamoDB khác. Bằng cách áp dụng các kỹ thuật tương tự, bạn có thể xây dựng các giải pháp phức tạp dựa trên sự kiện được kích hoạt bởi các thay đổi đối với các mục mà bạn đã lưu trữ trên DynamoDB.

Nếu bạn đã sử dụng tài khoản do Workshop Studio cung cấp, bạn không cần phải thực hiện bất kỳ việc dọn dẹp nào. Tài khoản sẽ bị hủy khi sự kiện kết thúc.

Nếu bạn đã sử dụng tài khoản của riêng mình, vui lòng loại bỏ các tài nguyên sau:

- Các Mapping Nguồn Sự kiện cho Hàm Lambda:

```bash
UUID_1=`aws lambda list-event-source-mappings --function-name create-order-history-kds --query 'EventSourceMappings[].UUID' --output text`
UUID_2=`aws lambda list-event-source-mappings --function-name create-order-history-ddbs --query 'EventSourceMappings[].UUID' --output text`
aws lambda delete-event-source-mapping --uuid ${UUID_1}
aws lambda delete-event-source-mapping --uuid ${UUID_2}
```

- Các hàm AWS Lambda được tạo trong phòng thí nghiệm:

```bash
aws lambda delete-function --function-name create-order-history-ddbs
aws lambda delete-function --function-name create-order-history-kds
```

- Dữ liệu Kinesis stream được tạo trong phòng thí nghiệm:

```bash
aws kinesis delete-stream --stream-name Orders
```

- Các bảng Amazon DynamoDB được tạo trong phần Bắt đầu của phòng thí nghiệm:

```bash
aws dynamodb delete-table --table-name Orders
aws dynamodb delete-table --table-name OrdersHistory
```

- Các hàng đợi Amazon SQS được tạo trong phòng thí nghiệm:

```bash
aws sqs delete-queue --queue-url https://sqs.${REGION}.amazonaws.com/${ACCOUNT_ID}/orders-ddbs-dlq
aws sqs delete-queue --queue-url https://sqs.${REGION}.amazonaws.com/${ACCOUNT_ID}/orders-kds-dlq
```

- Các chính sách IAM gắn vào vai trò thực thi IAM mà bạn đã tạo:

![Xóa Chính sách IAM](/images/4/4.5/1.png)

![Xóa Chính sách IAM](/images/4/4.5/2.png)

- Các vai trò thực thi AWS IAM được tạo cho các hàm lambda:

![Xóa Vai trò IAM](/images/4/4.5/3.png)

Đây là các bước để hoàn tất quá trình dọn dẹp.