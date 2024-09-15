---
title : Lập kế hoạch cho mô hình dữ liệu của bạn"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 7.2. </b> "
---
**Mô hình hóa dữ liệu** là quá trình thiết kế cách một ứng dụng lưu trữ dữ liệu trong một cơ sở dữ liệu nhất định. Với một cơ sở dữ liệu NoSQL như DynamoDB, mô hình hóa dữ liệu khác với mô hình hóa với một cơ sở dữ liệu quan hệ. Một cơ sở dữ liệu quan hệ được xây dựng để linh hoạt và có thể phù hợp với các ứng dụng phân tích. Trong mô hình hóa dữ liệu quan hệ, bạn bắt đầu với các thực thể của mình trước. Khi bạn có một mô hình quan hệ đã được [chuẩn hóa](https://vi.wikipedia.org/wiki/Chu%E1%BA%A9n_h%C3%B3a_c%C6%A1_s%E1%BB%9F_d%E1%BB%AF_li%E1%BB%87u), bạn có thể đáp ứng bất kỳ mẫu truy vấn nào bạn cần trong ứng dụng của mình.

Các cơ sở dữ liệu NoSQL được thiết kế để đạt tốc độ và khả năng mở rộng — không phải linh hoạt. Mặc dù hiệu suất của cơ sở dữ liệu quan hệ có thể giảm sút khi bạn mở rộng quy mô, các cơ sở dữ liệu mở rộng theo chiều ngang như DynamoDB cung cấp hiệu suất nhất quán ở bất kỳ quy mô nào. Một số người dùng DynamoDB có các bảng lớn hơn 100 TB, và hiệu suất đọc và ghi của các bảng này vẫn giống như khi các bảng có kích thước nhỏ hơn 1 GB.

Để đạt được kết quả tốt nhất với cơ sở dữ liệu NoSQL như DynamoDB, cần có sự thay đổi tư duy so với cơ sở dữ liệu quan hệ thông thường.

Hãy cùng xem qua một số phương pháp tốt nhất khi mô hình hóa dữ liệu với DynamoDB.