---
title : "Tạo tài nguyên DMS"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.5.3. </b> "
---

Hãy tạo các tài nguyên DMS cho workshop.

1. Truy cập vào IAM console > Roles > Create Role
2. Dưới phần "Select trusted entity", chọn “AWS service”, sau đó trong mục “Use case”, chọn “DMS” từ danh sách thả xuống và nhấp vào nút radio “DMS”. Sau đó nhấp “Next”.
3. Trong phần "Add permissions", sử dụng hộp tìm kiếm để tìm chính sách “AmazonDMSVPCManagementRole” và chọn nó, sau đó nhấp “Next”.
4. Trong mục "Name, review, and create", thêm tên vai trò chính xác là `dms-vpc-role` và nhấp vào Create role.

{{%notice tip%}}
_Không tiếp tục nếu bạn chưa tạo vai trò IAM._
{{%/notice%}}

1. Khởi chạy mẫu CloudFormation tại khu vực US West 2 để triển khai các tài nguyên vào tài khoản của bạn: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=dynamodbmigration&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-dms-setup.yaml)
   1. _Tùy chọn, tải xuống [mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-dms-setup.yaml) và khởi chạy theo cách của bạn._
2. Nhấp vào Next
3. Xác nhận tên Stack là _dynamodbmigration_ và giữ nguyên các tham số mặc định (chỉnh sửa nếu cần thiết)  
   ![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/5.jpg)
4. Nhấp “Next” hai lần
5. Chọn “I acknowledge that AWS CloudFormation might create IAM resources with custom names.”
6. Nhấp vào Submit. Mẫu CloudFormation sẽ mất khoảng 15 phút để xây dựng một môi trường sao chép. Bạn nên tiếp tục làm lab trong khi stack đang được tạo trong nền.  
   ![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/6.jpg)

{{%notice tip%}}
Đừng chờ đợi quá trình tạo stack hoàn tất. Hãy tiếp tục làm lab và để nó tự tạo trong nền.
{{%/notice%}}