---
title : "Tổng quan bài tập"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.5.1. </b> "
---

Trong module này, bạn sẽ tạo một môi trường để lưu trữ cơ sở dữ liệu MySQL trên Amazon EC2. Phiên bản này sẽ được sử dụng để lưu trữ cơ sở dữ liệu nguồn và mô phỏng phía tại chỗ (on-premise) của kiến trúc di chuyển. Tất cả các tài nguyên để cấu hình cơ sở hạ tầng nguồn được triển khai thông qua mẫu [Amazon CloudFormation](https://aws.amazon.com/cloudformation/). Có hai mẫu CloudFormation được sử dụng trong bài tập này, mỗi mẫu sẽ triển khai các tài nguyên sau:

**Tài nguyên của CloudFormation MySQL Template:**

- **OnPrem VPC:** Nguồn VPC sẽ đại diện cho môi trường nguồn tại chỗ trong khu vực N. Virginia. VPC này sẽ lưu trữ cơ sở dữ liệu MySQL nguồn trên Amazon EC2.
- **Amazon EC2 MySQL Database:** Phiên bản Amazon EC2 với Amazon Linux 2 AMI được cài đặt và chạy MySQL.
- **Load IMDb dataset:** Mẫu sẽ tạo cơ sở dữ liệu IMDb trên MySQL và tải các tệp dữ liệu công khai IMDb vào cơ sở dữ liệu. Bạn có thể tìm hiểu thêm về bộ dữ liệu IMDb trong [Explore Source Model](https://catalog.workshops.aws/dynamodb-labs/en-US/hands-on-labs/rdbms-migration/migration-chapter03).

**Tài nguyên của CloudFormation DMS Instance:**

- **DMS VPC:** VPC di chuyển ở khu vực N. Virginia. VPC này sẽ lưu trữ phiên bản sao chép DMS.
- **Replication Instance:** Phiên bản sao chép DMS sẽ hỗ trợ quá trình di chuyển cơ sở dữ liệu từ máy chủ MySQL nguồn trên EC2 sang Amazon DynamoDB.

![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/1.png)