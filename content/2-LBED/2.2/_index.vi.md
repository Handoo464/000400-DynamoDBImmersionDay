---
title : "Cấu hình dịch vụ"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2.2. </b> "
---

Trong phần này, bạn sẽ tải dữ liệu vào bảng DynamoDB của mình và cấu hình các tài nguyên OpenSearch Service.

Trước khi bắt đầu phần này, hãy đảm bảo rằng [quá trình thiết lập](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/) đã hoàn tất tùy theo cách bạn đang chạy lab này. Quá trình thiết lập sẽ triển khai một số tài nguyên.

Các phụ thuộc từ Mẫu CloudFormation của Cloud9:

- **S3 Bucket**: Được sử dụng để lưu trữ xuất dữ liệu ban đầu của DynamoDB cho Zero-ETL Pipeline.
- **IAM Role**: Được sử dụng để cấp quyền cho tích hợp pipeline và truy vấn.
- **Cloud9 IDE**: Bảng điều khiển để thực thi lệnh, xây dựng tích hợp và chạy các truy vấn mẫu.

Các tài nguyên từ Mẫu CloudFormation zETL:

- **Bảng DynamoDB**: Bảng DynamoDB để lưu trữ mô tả sản phẩm. Có tính năng khôi phục theo thời gian (Point-in-time Recovery - PITR) và DynamoDB Streams được bật.
- **Amazon OpenSearch Service Domain**: Cụm OpenSearch Service với một nút để nhận dữ liệu từ DynamoDB và hoạt động như một cơ sở dữ liệu vector.

![Final Deployment Architecture](/images/2/1.png)