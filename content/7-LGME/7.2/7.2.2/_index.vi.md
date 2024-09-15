---
title : "Xây dựng entity-relationship diagram của bạn"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 7.2.2. </b> "
---

Bước đầu tiên của bất kỳ bài tập mô hình hóa dữ liệu nào là xây dựng một sơ đồ để hiển thị các thực thể trong ứng dụng của bạn và cách chúng liên quan với nhau.

Trong ứng dụng này, bạn có các thực thể sau:

- `User`
  
- `Game`
  
- `UserGameMapping`

Thực thể `User` đại diện cho một người dùng trong ứng dụng. Một người dùng có thể tạo nhiều thực thể `Game`, và người tạo trò chơi sẽ quyết định bản đồ nào được chơi và khi nào trò chơi bắt đầu. Một `User` có thể tạo nhiều thực thể `Game`, vì vậy có một mối quan hệ một-nhiều giữa `Users` và `Games`.

Cuối cùng, một `Game` chứa nhiều `Users` và một `User` có thể chơi trong nhiều `Games` khác nhau theo thời gian.

Do đó, có một mối quan hệ nhiều-nhiều giữa `Users` và `Games`. Bạn có thể biểu diễn mối quan hệ này bằng thực thể `UserGameMapping`.

Với các thực thể và mối quan hệ này, sơ đồ thực thể-mối quan hệ (ERD) được hiển thị dưới đây.

![ERD - Sơ đồ Thực thể-Mối quan hệ](/images/7/7.2/1.png)

Tiếp theo, chúng ta sẽ xem xét các mẫu truy cập mà mô hình dữ liệu cần hỗ trợ.