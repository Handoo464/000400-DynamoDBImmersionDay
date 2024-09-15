---
title : "Bài tập 8: Amazon DynamoDB Streams và AWS Lambda"
date :  "`r Sys.Date()`" 
weight : 9
chapter : false
pre : " <b> 3.9. </b> "
---

Kết hợp DynamoDB Streams ([tài liệu giải thích dịch vụ](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)) với AWS Lambda mang lại nhiều mẫu thiết kế mạnh mẽ. Trong bài tập này, bạn sẽ sao chép các mục từ một bảng DynamoDB sang một bảng khác bằng cách sử dụng DynamoDB Streams và các hàm Lambda.

DynamoDB Streams ghi lại một chuỗi các thay đổi cấp mục theo thứ tự thời gian trong bất kỳ bảng DynamoDB nào và lưu trữ thông tin này trong một bản ghi (log) lên đến 24 giờ. Các ứng dụng có thể truy cập vào bản ghi này và xem các mục dữ liệu như chúng đã xuất hiện trước và sau khi được sửa đổi, gần như thời gian thực. DynamoDB Streams cung cấp các trường hợp sử dụng sau:

- Một trò chơi nhiều người chơi toàn cầu có kiến trúc cơ sở dữ liệu nhiều lãnh đạo, lưu trữ dữ liệu ở nhiều khu vực AWS (AWS Regions). Mỗi khu vực luôn đồng bộ hóa bằng cách tiêu thụ và phát lại các thay đổi xảy ra ở các khu vực từ xa. (Thực tế, DynamoDB Global Tables dựa vào DynamoDB Streams để thực hiện việc sao chép toàn cầu.)
- Một khách hàng mới thêm dữ liệu vào một bảng DynamoDB. Sự kiện này kích hoạt một hàm AWS Lambda để sao chép dữ liệu sang một bảng DynamoDB riêng biệt để lưu trữ lâu dài (Điều này rất giống với bài tập này, nơi chúng ta sao chép dữ liệu từ một bảng DynamoDB sang một bảng khác bằng cách sử dụng DynamoDB Streams.)

Bạn sẽ tái sử dụng bảng `logfile` mà bạn đã tạo trong Bài tập 1. Bạn sẽ kích hoạt DynamoDB Streams trên bảng `logfile`. Mỗi khi có sự thay đổi trong bảng `logfile`, thay đổi này sẽ xuất hiện ngay lập tức trong một stream. Tiếp theo, bạn gắn một hàm Lambda vào stream. Mục đích của hàm Lambda là truy vấn DynamoDB Streams để lấy các bản cập nhật cho bảng `logfile` và ghi các bản cập nhật này vào một bảng mới được tạo tên là `logfile_replica`. Sơ đồ sau đây cho thấy tổng quan về triển khai này.

![DynamoDB stream với Lambda](/images/3/3.9/1.jpg)