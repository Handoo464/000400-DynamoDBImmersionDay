---
title : "Bài tập 1: DynamoDB Capacity Units và Partitioning"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2. </b> "
---

Trong bài tập này, bạn sẽ tải dữ liệu vào các bảng DynamoDB được cấp phát với các đơn vị dung lượng đọc/ghi khác nhau, và so sánh thời gian tải cho các bộ dữ liệu khác nhau. Đầu tiên, bạn sẽ tải một bộ dữ liệu nhỏ hơn vào một bảng và ghi lại thời gian thực thi nhanh. Tiếp theo, bạn sẽ tải một bộ dữ liệu lớn hơn vào một bảng bị cấp phát dưới mức để mô phỏng các ngoại lệ do bị giới hạn (throttling). Cuối cùng, bạn sẽ mô phỏng áp lực từ chỉ mục phụ toàn cầu (global secondary index) trên một bảng bằng cách tạo một bảng có dung lượng cấp phát cao hơn và một chỉ mục phụ toàn cầu chỉ có 1 đơn vị dung lượng ghi (WCU). Trong bài tập này, bạn sẽ sử dụng dữ liệu mẫu từ nhật ký truy cập máy chủ web, tương tự như dữ liệu nhật ký máy chủ web được tạo bởi Apache.