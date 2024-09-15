---
title : "Định cấu hình quyền OpenSearch Service"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.2.1. </b> "
---

Miền OpenSearch Service được triển khai bởi CloudFormation Template sử dụng kiểm soát truy cập chi tiết (Fine-grained access control). Kiểm soát truy cập chi tiết cung cấp thêm các cách kiểm soát truy cập vào dữ liệu của bạn trên Amazon OpenSearch Service. Để cấu hình tích hợp giữa OpenSearch Service, DynamoDB và Bedrock, một số quyền OpenSearch Service sẽ cần được ánh xạ với IAM Role đang được sử dụng.

Các liên kết đến OpenSearch Dashboards, thông tin đăng nhập, và các giá trị cần thiết được cung cấp trong phần Outputs của DynamoDBzETL [CloudFormation](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/) Template. Nên để tab Outputs mở trong một tab trình duyệt để dễ dàng tham khảo trong khi theo dõi bài lab.

Trong môi trường sản xuất, một thực hành tốt là cấu hình các vai trò với quyền tối thiểu cần thiết. Để đơn giản trong lab này, chúng ta sẽ sử dụng vai trò "all_access" của OpenSearch Service.

{{%notice tip%}}
_Không tiếp tục nếu CloudFormation Template chưa hoàn tất triển khai._
{{%/notice%}}

1. Mở tab "Outputs" của stack có tên `dynamodb-opensearch-setup` trong CloudFormation Console.
    
   ![CloudFormation Outputs](/images/2/2.2/1.jpg)
    
2. Mở liên kết "SecretConsoleLink" trong một tab mới. Điều này sẽ đưa bạn đến bí mật trong AWS Secrets Manager chứa thông tin đăng nhập cho OpenSearch. Nhấp vào nút `Retrieve secret value` để xem tên người dùng và mật khẩu cho OpenSearch Cluster.
    
3. Quay lại tab "Outputs" của CloudFormation Console và mở liên kết cho **OSDashboardsURL** trong một tab mới.
    
4. Đăng nhập vào Dashboards với tên người dùng và mật khẩu được cung cấp trong Secrets Manager.
    
   ![OpenSearch Service Dashboards](/images/2/2.2/2.jpg)
    
5. Khi được nhắc chọn tenant, chọn _Global_ và nhấp vào **Confirm**. Đóng mọi cửa sổ bật lên.
    
   ![OpenSearch Service Dashboards](/images/2/2.2/3.jpg)
    
6. Mở menu trên cùng bên trái và chọn **Security** trong phần _Management_.
    
   ![Security Settings](/images/2/2.2/4.jpg)
    
7. Mở tab "Roles", sau đó nhấp vào vai trò "all_access".
    
   ![Roles Settings](/images/2/2.2/5.jpg)
    
8. Mở tab "Mapped users", sau đó chọn "Manage mapping".
    
   ![Mapping Settings](/images/2/2.2/6.jpg)
    
9. Trong trường "Backend roles", nhập Arn được cung cấp trong CloudFormation Stack Outputs. Thuộc tính có tên "Role" cung cấp Arn chính xác.  
   Hãy chắc chắn rằng bạn đã xóa mọi ký tự khoảng trắng từ đầu và cuối của ARN để đảm bảo bạn không gặp vấn đề về quyền sau này. Nhấp vào "Map".
    
   ![Settings](/images/2/2.2/7.jpg)
    
10. Xác minh rằng vai trò "all_access" hiện có một "Backend role" được liệt kê.
    
   ![Settings](/images/2/2.2/8.jpg)