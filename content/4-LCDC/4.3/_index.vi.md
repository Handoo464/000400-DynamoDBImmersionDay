---
title : "Cập nhật IAM Role"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 4.3. </b> "
---

[Luồng DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)  ghi lại chuỗi thay đổi cấp độ mục theo thứ tự thời gian xảy ra trên bảng DynamoDB. Sau khi bật, thông tin về tất cả các thay đổi cấp mặt hàng sẽ được ghi lại và lưu trữ trong tối đa 24 giờ.

Các thay đổi đối với các mục trên bảng DynamoDB đã bật luồng được ghi lại gần như theo thời gian thực để có thể sử dụng các sự kiện làm trình kích hoạt cho các ứng dụng dựa trên sự kiện tiêu thụ dữ liệu từ luồng DynamoDB của bạn.

Trong chương này, bạn kích hoạt luồng DynamoDB trên bảng Đơn hàng và triển khai hàm AWS Lambda sao chép các thay đổi từ bảng Đơn hàng sang bảng Lịch sử đơn hàng mỗi khi một mục được cập nhật trên bảng Đơn hàng.

Các giải pháp kết quả được hiển thị trong hình dưới đây.

![Kiến trúc triển khai cuối cùng](/images/4/4.3/1.png)