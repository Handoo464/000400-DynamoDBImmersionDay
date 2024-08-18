---
title : "Chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1.1.1. </b> "
---


**Khởi động stack CloudFormation**

Trong quá trình lab, bạn sẽ tạo các bảng DynamoDB có thể phát sinh chi phí lên đến hàng chục hoặc hàng trăm đô la mỗi ngày. Hãy đảm bảo xóa các bảng DynamoDB bằng cách sử dụng bảng điều khiển DynamoDB, và hãy chắc chắn rằng bạn xóa stack CloudFormation ngay sau khi hoàn thành lab.

1. Khởi động mẫu CloudFormation ở miền US West 2 để triển khai các tài nguyên trong tài khoản của bạn:  [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBID&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)
	1. *Tùy chọn, tải xuống [the YAML template](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml) và khởi động nó theo cách của riêng bạn.*

2. Nhấp vào *Next trên hộp thoại đầu tiên.

3. Trong phần Parameters, lưu ý rằng *Timeout* được đặt thành 0. Điều này có nghĩa là phiên Cloud9 sẽ không ngủ; bạn có thể muốn thay đổi giá trị này thành một giá trị như 60 để bảo vệ khỏi những khoản phí bất ngờ nếu bạn quên xóa stack khi kết thúc. Giữ nguyên tham số *WorkshopZIP và nhấp Next.
 
4. Cuộn xuống dưới cùng và nhấp **Next**, sau đó xem lại Mẫu và Tham số. Khi bạn sẵn sàng tạo stack, cuộn xuống dưới cùng, đánh dấu hộp xác nhận việc tạo các tài nguyên IAM, và nhấp vào **Create stack**. 
   ![CloudFormation parameters](/images/1/3.png)

Stack sẽ tạo một phiên bản Cloud9 lab, một vai trò cho phiên bản đó, và một vai trò cho hàm AWS Lambda được sử dụng sau này trong lab. Nó sẽ sử dụng Systems Manager để cấu hình phiên bản Cloud9.

Sau khi stack CloudFormation có trạng thái CREATE_COMPLETE, hãy tiếp tục đến phần tiếp theo.

Truy cập EC2 tìm kiếm Instance đã được tạo từ stack.

   ![](/images/1/4.png)
Thực hiện Connect to instance
   ![](/images/1/5.png)

Chạy lệnh `aws sts get-caller-identity` chỉ để xác minh rằng thông tin xác thực AWS của bạn đã được cấu hình đúng cách.
![](/images/1/6.png)

