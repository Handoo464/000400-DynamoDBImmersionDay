---
title : "Tóm tắt về AWS Backup"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.4.1. </b> "
---

AWS Backup được thiết kế để giúp bạn tập trung hóa và tự động hóa việc bảo vệ dữ liệu trên các dịch vụ AWS. AWS Backup cung cấp một dịch vụ dựa trên chính sách, được quản lý hoàn toàn và có chi phí hợp lý, giúp đơn giản hóa hơn nữa việc bảo vệ dữ liệu ở quy mô lớn. AWS Backup cho phép bạn triển khai các chính sách sao lưu từ trung tâm để cấu hình, quản lý và kiểm soát hoạt động sao lưu trên các tài khoản AWS và tài nguyên trong tổ chức của bạn, bao gồm các phiên bản [Amazon EC2](https://aws.amazon.com/ec2/), các ổ đĩa [Amazon EBS](https://aws.amazon.com/ebs), các cơ sở dữ liệu [Amazon RDS](https://aws.amazon.com/rds/), các bảng Amazon DynamoDB, [Amazon EFS](https://aws.amazon.com/efs/), [Amazon FSx for Lustre](https://aws.amazon.com/fsx/lustre/), [Amazon FSx for Windows File Server](https://aws.amazon.com/fsx/windows/), và các ổ đĩa [AWS Storage Gateway](https://aws.amazon.com/storagegateway/volume/).

Hãy hiểu một số thuật ngữ trong AWS Backup:

- **Backup vault**: một container (kho lưu trữ) mà bạn tổ chức các bản sao lưu của mình.

- **Backup plan**: một biểu thức chính sách xác định thời điểm và cách thức bạn muốn sao lưu các tài nguyên AWS của mình. Kế hoạch sao lưu được liên kết với một **backup vault**.

- **Resource assignment**: định nghĩa những tài nguyên nào cần được sao lưu. Bạn có thể chọn tài nguyên theo thẻ (tags) hoặc theo ARN của tài nguyên.

- **Recovery point**: một snapshot/bản sao lưu của một tài nguyên được sao lưu bởi AWS Backup. Mỗi điểm khôi phục có thể được khôi phục lại với AWS Backup.