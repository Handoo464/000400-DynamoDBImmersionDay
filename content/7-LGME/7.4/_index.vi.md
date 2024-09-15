---
title : "Tìm trò chơi đang mở"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 7.4. </b> "
---

Một trong những điều chỉnh lớn nhất cho người dùng mới với DynamoDB và NoSQL là cách mô hình hóa dữ liệu để lọc trên toàn bộ bộ dữ liệu. Ví dụ, trong ứng dụng trò chơi, bạn cần tìm các trò chơi còn chỗ trống để có thể hiển thị cho người dùng các trò chơi mà họ có thể tham gia.

Trong cơ sở dữ liệu quan hệ, bạn có thể viết một số câu lệnh SQL để truy vấn dữ liệu.

```sql
SELECT * 
FROM games 
WHERE status = "OPEN"
```

DynamoDB có thể [lọc kết quả](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.FilterExpression) trên một thao tác `Query` hoặc `Scan`, nhưng DynamoDB không hoạt động như một cơ sở dữ liệu quan hệ. Bộ lọc DynamoDB áp dụng **sau khi** các mục ban đầu khớp với thao tác `Query` hoặc `Scan` đã được truy xuất. Bộ lọc giảm kích thước của payload được gửi từ dịch vụ DynamoDB, nhưng số lượng mục được truy xuất ban đầu phụ thuộc vào [giới hạn kích thước của DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html#limits-api).

May mắn thay, có một số cách bạn có thể cho phép truy vấn có lọc trên bộ dữ liệu của bạn trong DynamoDB. Để cung cấp các bộ lọc hiệu quả trên bảng DynamoDB của bạn, bạn cần lập kế hoạch cho các bộ lọc này vào mô hình dữ liệu của bảng từ đầu. Hãy nhớ bài học bạn đã học ở các mô-đun trước của lab này: Xem xét các mẫu truy cập của bạn, sau đó thiết kế bảng của bạn.

Trong các bước tiếp theo, bạn sẽ sử dụng **global secondary index (chỉ mục phụ toàn cầu)** để tìm các trò chơi còn chỗ trống. Cụ thể, bạn sẽ sử dụng kỹ thuật [sparse index (chỉ mục thưa)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-indexes-general-sparse-indexes.html) để xử lý mẫu truy cập này.