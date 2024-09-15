---
title : "Bắt đầu"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 2.1. </b> "
---

## Nền tảng

Hãy tưởng tượng bạn đang xây dựng một cửa hàng trực tuyến. Hầu hết các mẫu truy cập của bạn phù hợp rất tốt với thế mạnh của DynamoDB. Tra cứu sản phẩm theo SKU, tìm nội dung giỏ hàng, và xem lịch sử đơn hàng của khách hàng đều là các truy vấn khóa giá trị khá đơn giản.

Tuy nhiên, khi ứng dụng của bạn phát triển, bạn có thể muốn thêm một số mẫu truy cập bổ sung. Tìm kiếm? Lọc? Còn về "AI Tạo sinh" (Generative AI) mà bạn đã nghe rất nhiều thì sao? Đó có thể là một yếu tố khác biệt thú vị cho ứng dụng của bạn.

Trong lab này, chúng ta sẽ học cách tích hợp DynamoDB với Amazon OpenSearch Service để hỗ trợ các mẫu truy cập mới đó.

{{%notice warning%}}
Phần đầu của các hướng dẫn cài đặt này giống hệt cho LADV, LHOL, LMR, LBED, và LGME - tất cả đều sử dụng cùng một mẫu Cloud9. Chỉ hoàn thành phần này một lần, và chỉ khi bạn chạy nó trên tài khoản của riêng mình. Nếu bạn đã khởi chạy stack Cloud9 trong một lab khác, hãy bỏ qua phần này và chuyển đến phần **Launch the zETL CloudFormation stack**.
{{%/notice%}}

{{%notice tip%}}
Chỉ hoàn thành phần này nếu bạn tự chạy workshop. Nếu bạn đang ở một sự kiện AWS tổ chức (như re:Invent, Immersion Day, v.v.), hãy truy cập [At an AWS hosted Event](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/aws-ws-event)
{{%/notice%}}

## Khởi chạy Cloud9 CloudFormation stack

{{%notice tip%}}
Trong quá trình làm lab, bạn sẽ tạo các tài nguyên có thể phát sinh chi phí hàng chục USD mỗi ngày. Đảm bảo xóa CloudFormation stack ngay khi hoàn thành lab và xác minh tất cả các tài nguyên đã bị xóa.
{{%/notice%}}

1. Khởi chạy mẫu CloudFormation ở khu vực US West 2 để triển khai các tài nguyên trong tài khoản của bạn:  
   [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBID&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)  
   _Hoặc, bạn có thể tải xuống [mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml) và khởi chạy theo cách của riêng bạn._

2. Nhấp vào _Next_ trên hộp thoại đầu tiên.

3. Trong phần Parameters, lưu ý rằng _Timeout_ được đặt về 0. Điều này có nghĩa là phiên bản Cloud9 sẽ không ngủ; bạn có thể thay đổi thủ công thành một giá trị như 60 để tránh các chi phí không mong muốn nếu bạn quên xóa stack vào cuối quá trình.  
   Giữ nguyên tham số _WorkshopZIP_ và nhấp vào _Next_.

![CloudFormation parameters](/images/2/2.1/2.png)

4. Cuộn xuống cuối trang và nhấp vào _Next_, sau đó xem lại phần _Template_ và _Parameters_. Khi bạn đã sẵn sàng tạo stack, cuộn xuống cuối trang, chọn hộp xác nhận tạo tài nguyên IAM và nhấp vào _Submit_.

![Acknowledge IAM role capabilities](/images/2/2.1/3.png)  
Stack sẽ tạo một phiên bản Cloud9 lab, một vai trò cho phiên bản, và một vai trò cho hàm AWS Lambda được sử dụng sau này trong lab. Nó sẽ sử dụng Systems Manager để cấu hình phiên bản Cloud9.

5. Sau khi CloudFormation stack có trạng thái `CREATE_COMPLETE`, tiếp tục với stack tiếp theo.

## Khởi chạy zETL CloudFormation stack

_Không tiếp tục nếu mẫu CloudFormation Cloud9 chưa hoàn tất triển khai._

1. Khởi chạy mẫu CloudFormation ở khu vực US West 2 để triển khai các tài nguyên trong tài khoản của bạn:  
   [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBzETL&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/dynamodb-opensearch-setup.yaml)  
   _Hoặc, bạn có thể tải xuống [mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/dynamodb-opensearch-setup.yaml) và khởi chạy theo cách của riêng bạn._

2. Nhấp vào _Next_ trên hộp thoại đầu tiên.

3. Trong phần Parameters, tùy chọn thay đổi giá trị OpenSearchClusterName để đặt một tên cụ thể cho cụm OpenSearch của bạn hoặc giữ nguyên tham số này. Nhấp vào _Next_.

![CloudFormation parameters](/images/2/2.1/4.png)

4. Cuộn xuống cuối trang và nhấp vào _Next_, sau đó xem lại phần _Template_ và _Parameters_. Khi bạn đã sẵn sàng tạo stack, cuộn xuống cuối trang và nhấp vào _Submit_.

5. Sau khi CloudFormation stack có trạng thái `CREATE_COMPLETE`, [tiếp tục kết nối với Cloud9](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/Step1).