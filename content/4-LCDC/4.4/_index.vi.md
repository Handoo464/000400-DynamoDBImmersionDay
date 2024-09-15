---
title : "4. Thay đổi tính năng Thu thập dữ liệu bằng Kinesis Data Streams"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4.4. </b> "
---

[Amazon Kinesis Data Streams](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/kds.html) có thể được sử dụng để thu thập và xử lý các luồng dữ liệu lớn từ các ứng dụng tạo dữ liệu streaming theo thời gian thực. Ở mức cao, các nhà sản xuất dữ liệu (data producers) đẩy các bản ghi dữ liệu (data records) đến Amazon Kinesis Data Streams và người tiêu dùng (consumers) có thể đọc và xử lý dữ liệu trong thời gian thực.

Dữ liệu trên Amazon Kinesis Data Streams theo mặc định sẽ có sẵn trong 24 giờ sau khi dữ liệu được ghi vào luồng và thời gian lưu giữ (retention period) có thể được tăng lên tối đa 365 ngày.

Amazon DynamoDB có tích hợp tự nhiên với Kinesis streams, do đó Kinesis Data Streams cũng có thể được sử dụng để ghi lại các thay đổi ở cấp độ mục (item level changes) cho các bảng DynamoDB.

Trong chương này, bạn sẽ lặp lại quá trình thu thập các thay đổi ở cấp độ mục trên một bảng DynamoDB và ghi những thay đổi đó vào một bảng DynamoDB khác. Tuy nhiên, trong phần này, việc thu thập dữ liệu thay đổi (change data capture) sẽ được thực hiện bằng cách sử dụng Amazon Kinesis Data Streams.

Kiến trúc của giải pháp kết quả được hiển thị trong hình dưới đây.

![Kiến trúc Triển khai Cuối cùng](/images/4/4.4/1.png)