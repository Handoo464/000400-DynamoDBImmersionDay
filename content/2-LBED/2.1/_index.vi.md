---
title : "Bắt đầu"
date :  "`r Sys.Date()`" 
weight : 10
chapter : false
pre : " <b> 2.1. </b> "
---
{{%notice warning%}}
Nửa đầu của các hướng dẫn thiết lập này giống hệt nhau cho LADV, LHOL, LMR, LBED và LGME - tất cả đều sử dụng cùng một mẫu Cloud9. Chỉ hoàn thành phần này một lần và chỉ khi bạn đang chạy phần này trên tài khoản của riêng mình. Nếu bạn đã khởi chạy ngăn xếp Cloud9 trong một phòng thí nghiệm khác, hãy chuyển đến phần **Khởi chạy ngăn xếp zETL CloudFormation**
{{%/notice%}}

{{%notice tip%}}
Chỉ hoàn thành phần này nếu bạn đang tự điều hành hội thảo. Nếu bạn đang tham dự một sự kiện do AWS tổ chức (chẳng hạn như re:Invent, Immersion Day, v.v.), hãy truy cập [Tại sự kiện do AWS tổ chức](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/aws-ws-event)
{{%/notice%}}

## Khởi chạy Stack Cloud9 CloudFormation

{{%notice tip%}}
Trong quá trình phòng thí nghiệm, bạn sẽ tạo ra các tài nguyên sẽ phải chịu chi phí có thể lên tới hàng chục đô la mỗi ngày. Đảm bảo bạn xóa ngăn xếp CloudFormation ngay sau khi phòng thực hành hoàn tất và xác minh rằng tất cả tài nguyên đã bị xóa.
{{%/notice%}}

1. Khởi chạy mẫu CloudFormation ở Miền Tây Hoa Kỳ 2 để triển khai các tài nguyên trong tài khoản của bạn: [![Sự hình thành đám mây](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBID&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)  
    _Tùy chọn, tải xuống [mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)  và khởi chạy nó theo cách riêng của bạn_
    
2. Nhấp vào _Tiếp theo_ trên hộp thoại đầu tiên.
    
3. Trong phần Tham số, lưu ý _Thời gian chờ_ được đặt thành không. Điều này có nghĩa là phiên bản Cloud9 sẽ không ở chế độ ngủ; Bạn có thể muốn thay đổi giá trị này theo cách thủ công thành một giá trị chẳng hạn như 60 để bảo vệ khỏi các khoản phí không mong muốn nếu bạn quên xóa ngăn xếp ở cuối.  
    Giữ nguyên tham số _WorkshopZIP_ và nhấp vào _Tiếp theo_
    

![Tham số CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/awsconsole1.png)

1. Cuộn xuống dưới cùng và bấm _Tiếp theo_, sau đó xem lại _Mẫu_ và _Tham số_. Khi bạn đã sẵn sàng tạo ngăn xếp, hãy cuộn xuống dưới cùng, chọn hộp xác nhận việc tạo tài nguyên IAM và nhấp vào _Gửi_.

![Xác nhận khả năng vai trò IAM](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/awsconsole2.png) Ngăn xếp sẽ tạo một phiên bản phòng thí nghiệm Cloud9, một vai trò cho phiên bản và một vai trò cho hàm AWS Lambda được sử dụng sau này trong phòng thực hành. Nó sẽ sử dụng Systems Manager để cấu hình phiên bản Cloud9.

1. Sau ngăn xếp CloudFormation là , tiếp tục với ngăn xếp tiếp theo.`CREATE_COMPLETE`

## Khởi chạy Stack zETL CloudFormation


_Không tiếp tục trừ khi Mẫu Cloud9 CloudFormation đã triển khai xong._

1. Khởi chạy mẫu CloudFormation ở Miền Tây Hoa Kỳ 2 để triển khai các tài nguyên trong tài khoản của bạn: [![Sự hình thành đám mây](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBzETL&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/dynamodb-opensearch-setup.yaml)  
    _Tùy chọn, tải xuống [mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/dynamodb-opensearch-setup.yaml)  và khởi chạy nó theo cách riêng của bạn_
    
2. Nhấp vào _Tiếp theo_ trên hộp thoại đầu tiên.
    
3. Trong phần Tham số, tùy chọn thay đổi giá trị của OpenSearchClusterName để đặt tên cụ thể cho cụm OpenSearch của bạn hoặc giữ nguyên tham số. Nhấp vào _Tiếp theo_
    

![Tham số CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/zetl-cfn-console1.png)

1. Cuộn xuống dưới cùng và bấm _Tiếp theo_, sau đó xem lại _Mẫu_ và _Tham số_. Khi bạn đã sẵn sàng tạo ngăn xếp, hãy cuộn xuống dưới cùng và nhấp vào _Gửi_.
    
2. Sau ngăn xếp CloudFormation là , [tiếp tục kết nối với Cloud9](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/Step1).`CREATE_COMPLETE`