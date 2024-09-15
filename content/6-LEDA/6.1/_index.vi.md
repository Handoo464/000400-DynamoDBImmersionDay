---
title : "Chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 6.1. </b> "
---

{{%notice tip%}}
Chỉ hoàn thành phần này nếu bạn đang chạy workshop trên tài khoản của mình. Nếu bạn đang tham gia một sự kiện do AWS tổ chức (như re:Invent, Immersion Day, v.v.), hãy truy cập [Tại một sự kiện do AWS tổ chức](https://catalog.workshops.aws/dynamodb-labs/en-US/event-driven-architecture/setup/start-here/aws-ws-event)
{{%/notice%}}

## Khởi tạo CloudFormation Stack

{{%notice warning%}}
Trong quá trình thực hành, bạn sẽ tạo các bảng DynamoDB có thể phát sinh chi phí lên tới hàng chục hoặc hàng trăm đô la mỗi ngày. Hãy đảm bảo bạn xóa các bảng DynamoDB bằng cách sử dụng bảng điều khiển DynamoDB, và chắc chắn rằng bạn xóa CloudFormation stack ngay khi hoàn thành bài thực hành.
{{%/notice%}}

1. Khởi chạy mẫu CloudFormation ở vùng US West 2 để triển khai tài nguyên trong tài khoản của bạn: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=amazon-dynamodb-labs&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/event-driven-cfn.yaml)

    1. _Tùy chọn, tải xuống [mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/event-driven-cfn.yaml) và khởi chạy theo cách riêng của bạn_
2. Nhấp vào _Next_ trên hộp thoại đầu tiên.
    
3. Cuộn xuống cuối và nhấp vào _Next_, sau đó xem lại _Template_. Khi bạn đã sẵn sàng tạo stack, cuộn xuống dưới cùng, đánh dấu vào ô chấp nhận việc tạo tài nguyên IAM, và nhấp vào _Create stack_. ![CloudFormation parameters](/images/6/6.1/1.png) Stack sẽ tạo các bảng DynamoDB, hàm Lambda, luồng Kinesis, và các vai trò và chính sách IAM sẽ được sử dụng sau đó trong bài thực hành.
    
4. Sau khi CloudFormation stack là `CREATE_COMPLETE`.