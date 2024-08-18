---
title : "Tổng quan bài tập"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.5.1. </b> "
---


Trong mô-đun này, bạn sẽ tạo một môi trường để lưu trữ cơ sở dữ liệu MySQL trên Amazon EC2. Phiên bản này sẽ được sử dụng để lưu trữ cơ sở dữ liệu nguồn và mô phỏng phía tại chỗ của kiến trúc di chuyển. Tất cả các tài nguyên để cấu hình cơ sở hạ tầng nguồn được triển khai thông qua [Amazon CloudFormation](https://aws.amazon.com/cloudformation/)  Mẫu. Có hai mẫu CloudFormation được sử dụng trong bài tập này sẽ triển khai các tài nguyên sau.

Tài nguyên mẫu CloudFormation MySQL:

- VPC OnPrem: VPC nguồn sẽ đại diện cho môi trường nguồn tại chỗ ở khu vực Bắc Virginia. VPC này sẽ lưu trữ cơ sở dữ liệu MySQL nguồn trên Amazon EC2
- Cơ sở dữ liệu Amazon EC2 MySQL: Amazon EC2 Amazon Linux 2 AMI đã cài đặt và chạy MySQL
- Tải tập dữ liệu IMDb: Mẫu sẽ tạo cơ sở dữ liệu IMDb trên MySQL và tải các tệp tập dữ liệu công khai IMDb vào cơ sở dữ liệu. Bạn có thể tìm hiểu thêm về tập dữ liệu IMDb bên trong [Khám phá Mô hình Nguồn](https://catalog.workshops.aws/dynamodb-labs/en-US/hands-on-labs/rdbms-migration/migration-chapter03)

Tài nguyên phiên bản CloudFormation DMS:

- DMS VPC: VPC di chuyển ở khu vực Bắc Virginia. VPC này sẽ lưu trữ phiên bản sao chép DMS.
- Phiên bản sao chép: Phiên bản sao chép DMS sẽ hỗ trợ di chuyển cơ sở dữ liệu từ máy chủ MySQL nguồn trên EC2 sang Amazon DynamoDB

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration-environment.png)