---
title : "Tạo tài nguyên DMS"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.5.3. </b> "
---

Hãy tạo tài nguyên DMS cho hội thảo.

1. Chuyển đến bảng điều khiển IAM > Vai trò > Tạo vai trò
2. Trong "Chọn thực thể đáng tin cậy", chọn "Dịch vụ AWS", sau đó trong "Trường hợp sử dụng", chọn "DMS" từ danh sách kéo xuống và nhấp vào nút radio "DMS". Sau đó nhấp vào "Tiếp theo"
3. Trong "Thêm quyền", sử dụng hộp tìm kiếm để tìm chính sách "AmazonDMSVPCManagementRole" và chọn chính sách đó, sau đó nhấp vào "Tiếp theo"
4. Trong phần "Tên, xem xét và tạo", hãy thêm tên vai trò chính xác và nhấp vào Tạo vai trò`dms-vpc-role`

_Không tiếp tục trừ khi bạn đã thực hiện vai trò IAM._

1. Khởi chạy mẫu CloudFormation ở US West 2 để triển khai các tài nguyên trong tài khoản của bạn: [![Sự hình thành đám mây](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=dynamodbmigration&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-dms-setup.yaml)
    1. _Tùy chọn, tải xuống [mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-dms-setup.yaml)  và khởi chạy nó theo cách riêng của bạn_
2. Nhấp vào Tiếp theo
3. Xác nhận _dynamodbmigration_ Tên ngăn xếp và giữ các tham số mặc định (sửa đổi nếu cần) ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration18.jpg)
4. Nhấp vào "Tiếp theo" hai lần
5. Kiểm tra "Tôi xác nhận rằng AWS CloudFormation có thể tạo tài nguyên IAM với tên tùy chỉnh."
6. Nhấp vào Gửi. Mẫu CloudFormation sẽ mất khoảng 15 phút để xây dựng môi trường sao chép. Bạn nên tiếp tục phòng thí nghiệm trong khi ngăn xếp tạo ra trong nền. ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration19.jpg)

{{%notice tip%}}
Đừng đợi ngăn xếp hoàn thành việc tạo. Vui lòng tiếp tục phòng thí nghiệm và cho phép nó tạo trong nền.
{{%/notice%}}
