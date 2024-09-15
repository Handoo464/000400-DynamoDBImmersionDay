---
title : "Xem các game trước"
date : "r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 7.6. </b> "
---

Trong module này, bạn sẽ xử lý mẫu truy cập cuối cùng — tìm tất cả các game đã chơi cho một người dùng. Người dùng trong ứng dụng có thể muốn xem lại các game mà họ đã chơi để xem lại trận đấu, hoặc họ có thể muốn xem các game của bạn bè.

### Mẫu chỉ mục đảo ngược (Inverted index pattern)

Bạn có thể nhớ rằng có một mối quan hệ nhiều-nhiều giữa thực thể `Game` và các thực thể `User` liên quan, và mối quan hệ này được biểu diễn bằng thực thể `UserGameMapping`.

Thường thì bạn muốn truy vấn cả hai phía của một mối quan hệ. Với cấu hình khóa chính hiện tại, bạn có thể tìm tất cả các thực thể `User` trong một `Game`. Bạn có thể cho phép truy vấn tất cả các thực thể `Game` cho một `User` bằng cách sử dụng một _chỉ mục đảo ngược_ (inverted index).

Trong DynamoDB, một chỉ mục đảo ngược là một chỉ mục thứ cấp toàn cục (GSI) mà trong đó khóa phân vùng và khóa sắp xếp của bạn được hoán đổi vị trí. Khóa sắp xếp trở thành khóa phân vùng và ngược lại. Mẫu này lật ngược bảng của bạn và cho phép bạn truy vấn phía bên kia của các mối quan hệ nhiều-nhiều.

Trong các bước tiếp theo, bạn sẽ thêm một chỉ mục đảo ngược vào bảng và xem cách sử dụng nó để truy xuất tất cả các thực thể `Game` cho một `User` cụ thể.

