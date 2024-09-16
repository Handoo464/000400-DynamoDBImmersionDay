---
title : "LEDA: Xây dựng Kiến trúc Serverless Event Driven với DynamoDB"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 6. </b> "
---

Trong workshop này, bạn sẽ được giới thiệu với một pipeline tổng hợp dữ liệu dựa trên sự kiện không máy chủ. Pipeline này được xây dựng với [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html), [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html), và [Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/introduction.html).

Mặc dù bạn có thể vui lòng tham gia workshop này, hãy lưu ý rằng pipeline hiện đang bị lỗi và cần sự chú ý của bạn để hoạt động trở lại! Như bạn sẽ sớm phát hiện, workshop được thiết kế với một luồng đầu vào chứa các bản sao trùng lặp và các hàm Lambda ngẫu nhiên bị lỗi.

Trong suốt hai bài thực hành, bạn sẽ phải kết nối tất cả các thành phần của pipeline và sau đó cập nhật các hàm Lambda để tránh mất hoặc trùng lặp tin nhắn dưới các trường hợp lỗi ngẫu nhiên (được tạo ra).

Dưới đây là nội dung của workshop này:

- Bắt đầu từ đây: Khởi động
- Tổng quan
- Bài thực hành 1: Kết nối pipeline
- Bài thực hành 2: Đảm bảo khả năng chịu lỗi và xử lý chính xác từng lần
- Tóm tắt: Kết luận

### Đối tượng mục tiêu

Workshop này dành cho bất kỳ ai quan tâm đến việc hiểu cách xây dựng các pipeline xử lý dữ liệu không máy chủ. Kiến thức cơ bản về các dịch vụ AWS và kinh nghiệm lập trình Python là điều mong muốn nhưng không bắt buộc. Chúng tôi xếp loại workshop này ở mức độ 300, có nghĩa là bạn không cần phải là chuyên gia về bất kỳ dịch vụ nào trong ba dịch vụ được tập trung vào.

### Yêu cầu


#### Kiến thức cơ bản về các dịch vụ AWS    

- Trong các dịch vụ khác, bài thực hành này sẽ hướng dẫn bạn cách sử dụng Amazon Kinesis Data Streams và AWS Lambda.

#### Hiểu biết cơ bản về DynamoDB

- Nếu bạn chưa quen với DynamoDB hoặc không tham gia vào bài thực hành này như một phần của sự kiện AWS, hãy xem lại tài liệu về "[Amazon DynamoDB là gì?](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) "

#### Quen thuộc với Python/Boto3

- Bạn sẽ sao chép và dán mã để tập trung vào việc học về DynamoDB.
- Bạn sẽ có thể xem lại tất cả mã đã chạy trong các bài tập.

#### Thời lượng

Workshop yêu cầu khoảng 2 giờ để hoàn thành.

### Kết quả đạt được

- Sử dụng các ví dụ thực tế, hiểu cách kết nối các thành phần AWS không máy chủ vào một pipeline dựa trên sự kiện.
- Hiểu cách tận dụng các tính năng đặc biệt của DynamoDB và Lambda để xây dựng một pipeline xử lý dữ liệu đáng tin cậy với tính chính xác từng lần xử lý.
- Hiểu các chế độ lỗi khác nhau và cơ chế thử lại.