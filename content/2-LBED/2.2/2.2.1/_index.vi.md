---
title : "Định cấu hình quyền OpenSearch Service"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.2.1. </b> "
---

Miền OpenSearch Service được triển khai bởi Mẫu CloudFormation sử dụng Kiểm soát truy cập chi tiết. Kiểm soát truy cập chi tiết cung cấp các cách bổ sung để kiểm soát quyền truy cập vào dữ liệu của bạn trên Amazon OpenSearch Service. Để cấu hình tích hợp giữa OpenSearch Service, DynamoDB và Bedrock, một số quyền OpenSearch Service nhất định sẽ cần được ánh xạ tới Vai trò IAM đang được sử dụng.

Liên kết đến Bảng thông tin OpenSearch, thông tin xác thực và các giá trị cần thiết được cung cấp trong Đầu ra của DynamoDBzETL [Sự hình thành đám mây](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/)  Mẫu. Bạn nên để Đầu ra mở trong một tab trình duyệt để dễ dàng tham khảo trong khi theo dõi qua phòng thí nghiệm.

Trong môi trường sản xuất như một phương pháp hay nhất, bạn sẽ định cấu hình các vai trò với ít đặc quyền nhất cần thiết. Để đơn giản trong phòng thực hành này, chúng tôi sẽ sử dụng vai trò OpenSearch Service "all_access".

_Không tiếp tục trừ khi Mẫu CloudFormation đã triển khai xong._

1. Mở tab "Đầu ra" của ngăn xếp có tên trong Bảng điều khiển CloudFormation.`dynamodb-opensearch-setup`
    
    ![Đầu ra CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl3.jpg)
    
2. Mở liên kết cho SecretConsoleLink trong một tab mới. Thao tác này sẽ đưa bạn đến bí mật AWS Secrets Manager chứa thông tin đăng nhập cho OpenSearch. Nhấp vào nút để xem tên người dùng và mật khẩu cho OpenSearch Cluster.`Retrieve secret value`
    
3. Quay lại Bảng điều khiển CloudFormation "Đầu ra" và mở liên kết cho **OSDashboardsURL** trong tab mới.
    
4. Đăng nhập vào Trang tổng quan bằng tên người dùng và mật khẩu được cung cấp trong Trình quản lý bí mật.
    
    ![Bảng thông tin OpenSearch Service](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl4.jpg)
    
5. Khi được nhắc chọn đối tượng thuê của bạn, hãy chọn _Toàn cầu_ và nhấp vào **Xác nhận**. Loại bỏ bất kỳ cửa sổ bật lên nào.
    
    ![Bảng thông tin OpenSearch Service](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl18.jpg)
    
6. Mở menu trên cùng bên trái và chọn **Bảo mật** trong phần _Quản lý_.
    
    ![Cài đặt bảo mật](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl5.jpg)
    
7. Mở tab "Vai trò", sau đó nhấp vào vai trò "all_access".
    
    ![Cài đặt vai trò](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl6.jpg)
    
8. Mở tab "Người dùng được ánh xạ", sau đó chọn "Quản lý ánh xạ".
    
    ![Cài đặt ánh xạ](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl7.jpg)
    
9. Trong trường "Vai trò phụ trợ", hãy nhập Arn được cung cấp trong Đầu ra ngăn xếp CloudFormation. Thuộc tính có tên "Vai trò" cung cấp Arn chính xác.  
    Hoàn toàn chắc chắn rằng bạn đã xóa bất kỳ ký tự khoảng trắng nào khỏi đầu và cuối ARN để đảm bảo bạn không gặp vấn đề về quyền sau này. Nhấp vào "Bản đồ".
    
    ![Cài đặt](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl8.jpg)
    
10. Xác minh rằng Vai trò "all_access" hiện có "Vai trò phụ trợ" được liệt kê.
    
    ![Cài đặt](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl9.jpg)