---
title : "AWS Backup Recap"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.4.1. </b> "
---

AWS Backup được thiết kế để giúp bạn tập trung hóa và tự động hóa việc bảo vệ dữ liệu trên các dịch vụ AWS. AWS Backup cung cấp một dịch vụ dựa trên chính sách, tiết kiệm chi phí và hoàn toàn được quản lý, giúp đơn giản hóa việc bảo vệ dữ liệu ở quy mô lớn. AWS Backup cho phép bạn triển khai các chính sách sao lưu một cách tập trung để cấu hình, quản lý và giám sát hoạt động sao lưu của bạn trên các tài khoản và tài nguyên AWS trong tổ chức, bao gồm [Amazon EC2](https://aws.amazon.com/ec2/)  instances, [Amazon EBS](https://aws.amazon.com/ebs)  volumes, [Amazon RDS](https://aws.amazon.com/rds/)  databases, Amazon DynamoDB tables, [Amazon EFS](https://aws.amazon.com/efs/) , [Amazon FSx for Lustre](https://aws.amazon.com/fsx/lustre/) , [Amazon FSx for Windows File Server](https://aws.amazon.com/fsx/windows/) , and [AWS Storage Gateway volumes](https://aws.amazon.com/storagegateway/volume/) .

Hãy cùng hiểu một số thuật ngữ liên quan đến AWS Backup:

- **Backup vault**: là một container mà bạn tổ chức các bản sao lưu của mình trong đó.
- **Backup plan:** là một biểu thức chính sách định nghĩa khi nào và như thế nào bạn muốn sao lưu tài nguyên AWS của mình. Kế hoạch sao lưu được gắn vào một **backup vault**.
- **Resource assignment**: định nghĩa tài nguyên nào nên được sao lưu. Bạn có thể chọn tài nguyên theo thẻ (tags) hoặc theo ARN (Amazon Resource Name) của tài nguyên.
- **Recovery point:** là một bức ảnh chụp/bản sao lưu của một tài nguyên được sao lưu bởi AWS Backup. Mỗi điểm phục hồi có thể được phục hồi bằng AWS Backup.