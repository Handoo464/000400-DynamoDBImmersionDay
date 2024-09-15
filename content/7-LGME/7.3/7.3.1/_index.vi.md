---
title : "Thiết kế khóa chính"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 7.3.1. </b> "
---

Hãy xem xét các thực thể khác nhau như đã đề cập trong phần giới thiệu trước. Trong ứng dụng trò chơi, bạn có các thực thể sau:

- `User` (Người dùng)
  
- `Game` (Trò chơi)
  
- `UserGameMapping` (Bản đồ Người dùng - Trò chơi)

Thực thể `UserGameMapping` là một bản ghi cho biết một người dùng đã tham gia vào một trò chơi. Có một mối quan hệ nhiều-nhiều giữa `User` và `Game`.

Việc có một bản đồ nhiều-nhiều thường cho thấy rằng bạn muốn thỏa mãn hai mẫu `Query` (truy vấn), và ứng dụng trò chơi này cũng không ngoại lệ. Bạn có một mẫu truy cập cần tìm tất cả người dùng đã tham gia vào một trò chơi, cũng như một mẫu khác để tìm tất cả các trò chơi mà một người dùng đã chơi.

Nếu mô hình dữ liệu của bạn có nhiều thực thể với mối quan hệ giữa chúng, bạn thường sử dụng **khóa chính tổng hợp** ([composite primary key](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html#HowItWorks.CoreComponents.PrimaryKey)) với cả giá trị _partition key_ và _sort key_. Khóa chính tổng hợp cho phép bạn sử dụng khả năng `Query` trên _partition key_ để thỏa mãn một trong những mẫu truy vấn mà bạn cần. Trong tài liệu DynamoDB, _partition key_ cũng được gọi là `HASH` và _sort key_ được gọi là `RANGE`.

Hai thực thể dữ liệu khác — `User` và `Game` — không có thuộc tính tự nhiên cho giá trị _sort key_ vì các mẫu truy cập trên `User` hoặc `Game` chỉ là tra cứu khóa-giá trị. Vì giá trị _sort key_ là bắt buộc, bạn có thể cung cấp một giá trị điền vào cho _sort key_.

Với ý tưởng này, hãy sử dụng mẫu sau cho các giá trị _partition key_ và _sort key_ cho mỗi loại thực thể.

|Thực thể|Partition Key|Sort Key|
|---|---|---|
|User|USER#<USERNAME>|#METADATA#<USERNAME>|
|Game|GAME#<GAME_ID>|#METADATA#<GAME_ID>|
|UserGameMapping|GAME#<GAME_ID>|USER#<USERNAME>|

Hãy cùng đi qua bảng trên.

Đối với thực thể `User`, giá trị _partition key_ là `USER#<USERNAME>`. Lưu ý rằng một tiền tố được sử dụng để xác định thực thể và ngăn ngừa bất kỳ xung đột nào có thể xảy ra giữa các loại thực thể.

Đối với giá trị _sort key_ trên thực thể `User`, sử dụng một tiền tố tĩnh `#METADATA#` theo sau là giá trị `USERNAME`. Đối với giá trị _sort key_, điều quan trọng là bạn có một giá trị đã biết, như `USERNAME`. Điều này cho phép thực hiện các hành động trên một mục duy nhất như `GetItem`, `PutItem`, và `DeleteItem`.

Tuy nhiên, bạn cũng muốn một giá trị _sort key_ có các giá trị khác nhau giữa các thực thể `User` khác nhau để cho phép [phân chia đồng đều](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.Partitions.html) nếu bạn sử dụng thuộc tính này làm _partition key_ cho một chỉ mục. Vì lý do này, bạn nối thêm `USERNAME`.

Thực thể `Game` có thiết kế khóa chính tương tự như thiết kế của thực thể `User`. Nó sử dụng một tiền tố khác (`GAME#`) và `GAME_ID` thay vì `USERNAME`, nhưng nguyên tắc vẫn giống nhau.

Cuối cùng, `UserGameMapping` sử dụng cùng partition key như thực thể `Game`. Điều này cho phép bạn truy xuất không chỉ siêu dữ liệu cho một `Game` mà còn tất cả người dùng trong một `Game` trong một truy vấn duy nhất. Bạn sau đó sử dụng thực thể `User` cho _sort key_ trên `UserGameMapping` để xác định người dùng nào đã tham gia vào một trò chơi cụ thể.

Trong bước tiếp theo, bạn sẽ tạo một bảng với thiết kế khóa chính này.