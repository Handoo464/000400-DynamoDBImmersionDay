---
title : "Cấu hình dịch vụ"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2.2. </b> "
---

Trong phần này, bạn sẽ tải dữ liệu vào bảng DynamoDB và cấu hình tài nguyên OpenSearch Service.

Trước khi bắt đầu phần này, hãy đảm bảo rằng [quá trình thiết lập](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/) đã được hoàn tất cho bất kỳ cách nào bạn đang chạy phòng thực hành này. Thiết lập sẽ triển khai một số tài nguyên.

Các thành phần phụ thuộc từ Mẫu Cloud9 CloudFormation:

- Vùng lưu trữ S3: Được sử dụng để lưu trữ dữ liệu DynamoDB xuất ban đầu cho Zero-ETL Pipeline.
- Vai trò IAM: Được sử dụng để cấp quyền cho các truy vấn và tích hợp quy trình.
- Cloud9 IDE: Bảng điều khiển để thực thi lệnh, xây dựng tích hợp và chạy truy vấn mẫu.

Tài nguyên mẫu zETL CloudFormation:

- Bảng DynamoDB: Bảng DynamoDB để lưu trữ mô tả sản phẩm. Đã bật Khôi phục theo thời điểm (PITR) và Luồng DynamoDB.
- Miền Amazon OpenSearch Service: Cụm OpenSearch Service một nút để nhận dữ liệu từ DynamoDB và hoạt động như một cơ sở dữ liệu vector.

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl.png)