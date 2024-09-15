---
title : "LCDC: Change Data Capture for Amazon DynamoDB"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 4. </b> "
---

Trong hội thảo này, bạn sẽ tìm hiểu cách thực hiện thu thập dữ liệu thay đổi đối với các thay đổi ở cấp độ mục trên bảng DynamoDB bằng cách sử dụng [Luồng Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)  và [Amazon Kinesis Data Streams](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/kds.html) . Kỹ thuật này cho phép bạn phát triển các giải pháp theo sự kiện được khởi tạo bằng những thay đổi được thực hiện đối với dữ liệu cấp mục được lưu trữ trong DynamoDB.

Đây là những gì hội thảo này bao gồm:

1. Bắt đầu
2. Tổng quan kịch bản
3. Thay đổi Thu thập dữ liệu bằng DynamoDB Streams
4. Thay đổi tính năng Thu thập dữ liệu bằng Kinesis Data Streams
5. Tóm tắt và dọn dẹp

### Đối tượng mục tiêu

Hội thảo này được thiết kế cho các nhà phát triển, kỹ sư và quản trị viên cơ sở dữ liệu tham gia thiết kế và duy trì các ứng dụng DynamoDB.

### Yêu cầu

#### Kiến thức cơ bản về dịch vụ AWS

- Trong số các dịch vụ khác, phòng thí nghiệm này sẽ hướng dẫn bạn sử dụng [Đám mây AWS9](https://aws.amazon.com/cloud9/)  và [AWS Lambda](https://aws.amazon.com/lambda/) .
#### Hiểu biết cơ bản về DynamoDB


- Nếu bạn không quen thuộc với DynamoDB hoặc không tham gia phòng thực hành này như một phần của sự kiện AWS, hãy cân nhắc xem lại tài liệu về "[Amazon DynamoDB là gì?](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) "

### Đề xuất nghiên cứu trước khi dùng phòng thí nghiệm

Nếu bạn không tham gia sự kiện AWS và gần đây bạn chưa xem xét các khái niệm thiết kế của DynamoDB, chúng tôi khuyên bạn nên xem video này trên [Mẫu thiết kế nâng cao cho DynamoDB](https://www.youtube.com/watch?v=xfxBhvGpoa0) , thời lượng khoảng một giờ.
