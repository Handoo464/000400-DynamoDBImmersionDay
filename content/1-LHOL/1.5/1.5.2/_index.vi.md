---
title : "Định cấu hình môi trường MySQL"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 1.5.2. </b> "
---


Chương này sẽ tạo môi trường nguồn trên AWS như đã thảo luận trong Tổng quan về bài tập. Mẫu CloudFormation được sử dụng dưới đây sẽ tạo VPC nguồn, máy chủ MySQL lưu trữ EC2, cơ sở dữ liệu IMDb và tải tập dữ liệu công khai IMDb vào 6 bảng.

1. Khởi chạy mẫu CloudFormation ở US West 2 để triển khai các tài nguyên trong tài khoản của bạn: [![Sự hình thành đám mây](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=rdbmsmigration&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-env-setup.yaml)
    1. _Tùy chọn, tải xuống [mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-env-setup.yaml)  và khởi chạy nó theo cách riêng của bạn_
2. Nhấp vào Next
3. Xác nhận Tên ngăn xếp _rdbmsmigration_ và cập nhật các tham số nếu cần (để lại các tùy chọn mặc định nếu có thể) ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration6.jpg)
4. Nhấp vào "Tiếp theo" hai lần, sau đó chọn "Tôi xác nhận rằng AWS CloudFormation có thể tạo tài nguyên IAM có tên tùy chỉnh".
5. Nhấp vào "Gửi"
6. Ngăn xếp CloudFormation sẽ mất khoảng 5 phút để xây dựng môi trường ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration7.jpg)
7. Đi tới [Bảng điều khiển EC2](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:)  và đảm bảo cột Kiểm tra trạng thái được thông qua 2/2 lần kiểm tra trước khi chuyển sang bước tiếp theo. ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration8.jpg)

{{%notice tip%}}
Không tiếp tục trừ khi phiên bản MySQL vượt qua cả hai kiểm tra tình trạng, 2/2.
{{%/notice%}}