---
title : "Mô hình spare GSI"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 7.4.1. </b> "
---

[**Secondary indexes (Chỉ mục phụ)**](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html) là công cụ mô hình hóa dữ liệu quan trọng trong DynamoDB. Chúng cho phép bạn tái cấu trúc dữ liệu để phù hợp với các mẫu truy vấn thay thế. Để tạo một chỉ mục phụ, bạn chỉ định khóa chính của chỉ mục, giống như khi bạn đã tạo một bảng trước đó. Lưu ý rằng khóa chính cho **global secondary index (chỉ mục phụ toàn cầu)** không cần phải duy nhất cho mỗi mục. Sau đó, DynamoDB sao chép các mục vào chỉ mục dựa trên các thuộc tính được chỉ định, và bạn có thể truy vấn nó giống như bạn làm với bảng.

Sử dụng **sparse secondary indexes (chỉ mục phụ thưa)** là một chiến lược nâng cao trong DynamoDB. Với các chỉ mục phụ, DynamoDB chỉ sao chép các mục từ bảng gốc nếu chúng có các phần tử của khóa chính trong chỉ mục phụ. Các mục không có các phần tử khóa chính sẽ không được sao chép, đó là lý do tại sao các chỉ mục phụ này được gọi là "thưa".

Hãy xem cách điều này áp dụng cho chúng ta. Bạn có thể nhớ rằng bạn có hai mẫu truy cập để tìm các trò chơi còn chỗ trống:

- Tìm các trò chơi còn chỗ trống (Đọc)
- Tìm các trò chơi còn chỗ trống theo bản đồ (Đọc)

Bạn có thể tạo một **global secondary index (GSI)** bằng cách sử dụng khóa chính tổng hợp, trong đó _partition key (khóa phân vùng)_ là thuộc tính `map` của trò chơi và _sort key (khóa sắp xếp)_ là thuộc tính `open_timestamp` của trò chơi, chỉ ra thời điểm trò chơi được mở.

Phần quan trọng là khi một trò chơi trở nên đầy, thuộc tính `open_timestamp` sẽ bị xóa. Khi thuộc tính này bị xóa, trò chơi đã đầy sẽ bị loại khỏi GSI vì nó không có giá trị cho thuộc tính sort key. Đây là cách giữ cho chỉ mục thưa: nó chỉ bao gồm các trò chơi còn chỗ trống có thuộc tính `open_timestamp`.