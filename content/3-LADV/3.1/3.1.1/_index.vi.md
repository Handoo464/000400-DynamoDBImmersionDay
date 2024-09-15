---
title : "Bắt đầu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.1.1. </b> "
---

### Hướng dẫn thiết lập

{{%notice warning%}}
Các hướng dẫn thiết lập này áp dụng cho LADV, LHOL, LMR, LBED, và LGME - tất cả đều sử dụng cùng một mẫu Cloud9. Chỉ hoàn thành phần này một lần và chỉ khi bạn đang chạy nó trên tài khoản của riêng bạn.
{{%/notice%}}

{{%notice tip%}}
Chỉ hoàn thành phần này nếu bạn đang tự thực hiện workshop. Nếu bạn đang tham gia một sự kiện do AWS tổ chức (như re:Invent, Immersion Day, v.v.), hãy truy cập [At an AWS hosted Event](https://catalog.workshops.aws/dynamodb-labs/en-US/design-patterns/setup/aws-ws-event)
{{%/notice%}}

## Khởi chạy CloudFormation Stack

{{%notice tip%}}
Trong quá trình thực hiện lab, bạn sẽ tạo ra các bảng DynamoDB có thể gây ra chi phí lên đến hàng chục hoặc hàng trăm đô la mỗi ngày. Đảm bảo bạn xóa các bảng DynamoDB bằng cách sử dụng DynamoDB console và chắc chắn xóa CloudFormation stack ngay khi hoàn thành lab.
{{%/notice%}}

1. Khởi chạy mẫu CloudFormation trong US West 2 để triển khai các tài nguyên trong tài khoản của bạn: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBID&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)
    
    1. _Tuỳ chọn, tải xuống [tệp YAML mẫu](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml) và khởi chạy theo cách riêng của bạn._
2. Nhấn _Next_ trên hộp thoại đầu tiên.
    
3. Trong phần Parameters (Tham số), lưu ý rằng _Timeout_ được đặt là 0. Điều này có nghĩa là phiên bản Cloud9 sẽ không ngủ; bạn có thể muốn thay đổi thủ công giá trị này thành 60 để tránh các chi phí không mong muốn nếu bạn quên xóa stack khi kết thúc.  
    Giữ nguyên tham số _WorkshopZIP_ và nhấp vào _Next_. ![Tham số CloudFormation](/images/3/3.1/1.png)
    
4. Cuộn xuống cuối trang và nhấn _Next_, sau đó xem lại _Template_ và _Parameters_. Khi bạn đã sẵn sàng tạo stack, cuộn xuống cuối, đánh dấu vào ô xác nhận tạo các tài nguyên IAM và nhấp vào _Create stack_. ![Tham số CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/awsconsole2.png) Stack sẽ tạo một phiên bản Cloud9 lab, một vai trò cho phiên bản, và một vai trò cho hàm AWS Lambda được sử dụng sau này trong lab. Nó sẽ sử dụng Systems Manager để cấu hình phiên bản Cloud9.
    
5. Sau khi CloudFormation stack có trạng thái `CREATE_COMPLETE`, [tiếp tục sang Bước 1](https://catalog.workshops.aws/dynamodb-labs/en-US/design-patterns/setup/Step1).