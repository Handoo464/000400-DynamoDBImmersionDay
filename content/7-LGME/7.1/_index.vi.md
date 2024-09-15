---
title : "Chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 7.1. </b> "
---

{{%notice warning%}}
Nửa đầu của hướng dẫn thiết lập này giống hệt cho LADV, LHOL, LMR, LBED, và LGME - tất cả đều sử dụng cùng một mẫu Cloud9. Chỉ thực hiện phần này một lần, và chỉ khi bạn đang chạy nó trên tài khoản của riêng mình. Nếu bạn đã khởi chạy Cloud9 stack trong một lab khác, hãy bỏ qua phần này và chuyển đến mục **Khởi chạy zETL CloudFormation stack**.
{{%/notice%}}

{{%notice tip%}}
Chỉ hoàn thành phần này nếu bạn đang tự chạy workshop. Nếu bạn đang tham gia một sự kiện do AWS tổ chức (như re:Invent, Immersion Day, v.v.), hãy đi tới [Tại một sự kiện AWS](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/aws-ws-event).
{{%/notice%}}

## Khởi chạy Cloud9 CloudFormation stack

{{%notice tip%}}
Trong quá trình thực hiện lab, bạn sẽ tạo các tài nguyên có thể gây ra chi phí lên đến hàng chục đô la mỗi ngày. Đảm bảo bạn xóa CloudFormation stack ngay khi hoàn thành lab và xác minh tất cả các tài nguyên đã được xóa.
{{%/notice%}}

1. Khởi chạy mẫu CloudFormation ở khu vực US West 2 để triển khai các tài nguyên trong tài khoản của bạn: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBID&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)  
   _Ngoài ra, bạn có thể tải xuống [tệp mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml) và khởi chạy theo cách riêng của bạn._

2. Nhấp vào _Next_ trên hộp thoại đầu tiên.

3. Trong phần Parameters, lưu ý rằng _Timeout_ được đặt thành 0. Điều này có nghĩa là instance Cloud9 sẽ không ngủ; bạn có thể thay đổi thủ công thành một giá trị như 60 để tránh chi phí không mong muốn nếu bạn quên xóa stack khi kết thúc.  
   Giữ nguyên tham số _WorkshopZIP_ và nhấp vào _Next_.

![Tham số CloudFormation](/images/2/2.1/2.png)

4. Cuộn xuống dưới cùng và nhấp vào _Next_, sau đó xem lại _Template_ và _Parameters_. Khi bạn đã sẵn sàng để tạo stack, cuộn xuống dưới cùng, đánh dấu vào ô xác nhận việc tạo tài nguyên IAM và nhấp vào _Submit_.

![Xác nhận khả năng IAM role](/images/2/2.1/3.png) Stack sẽ tạo một instance Cloud9 lab, một vai trò cho instance và một vai trò cho hàm AWS Lambda được sử dụng sau này trong lab. Nó sẽ sử dụng Systems Manager để cấu hình instance Cloud9.

5. Sau khi CloudFormation stack có trạng thái `CREATE_COMPLETE`, tiếp tục với stack tiếp theo.


