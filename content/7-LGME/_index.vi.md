---
title : "LGME: Lập mô hình dữ liệu người chơi trò chơi với Amazon DynamoDB"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

Trong workshop này, bạn sẽ học các mẫu mô hình dữ liệu nâng cao trong [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html). Khi sử dụng DynamoDB, điều quan trọng là phải xem xét cách bạn sẽ truy cập dữ liệu của mình (các mẫu truy cập) trước khi mô hình hóa dữ liệu. Bạn sẽ đi qua một ví dụ về ứng dụng trò chơi nhiều người chơi, tìm hiểu về các mẫu truy cập trong ứng dụng trò chơi đó, và xem cách thiết kế một bảng DynamoDB để xử lý các mẫu truy cập bằng cách sử dụng chỉ mục phụ và giao dịch.

Đọc thêm về cách DynamoDB được sử dụng bởi các khách hàng hiện tại trong GameTech, đặc biệt tại: [https://aws.amazon.com/dynamodb/gaming/](https://aws.amazon.com/dynamodb/gaming/)

Nội dung của workshop này bao gồm:

1. Bắt đầu
2. Lập kế hoạch mô hình dữ liệu của bạn
3. Sử dụng cốt lõi: hồ sơ người dùng và trò chơi
4. Tìm trò chơi đang mở
5. Tham gia và đóng trò chơi
6. Xem lại các trò chơi trước đây
7. Tóm tắt & Dọn dẹp

### Đối tượng mục tiêu

Workshop này được thiết kế cho các nhà phát triển, kỹ sư, và quản trị viên cơ sở dữ liệu, những người tham gia vào việc thiết kế và duy trì các ứng dụng DynamoDB.