---
title : "Sử dụng cốt lõi: hồ sơ người dùng và trò chơi"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 7.3. </b> "
---

Trong mô-đun trước, các mẫu truy cập của ứng dụng trò chơi đã được xác định. Trong mô-đun này, **khóa chính** ([primary key](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html#HowItWorks.CoreComponents.PrimaryKey)) cho bảng DynamoDB được xác định và các mẫu truy cập cốt lõi được xử lý.

#### Khi thiết kế khóa chính cho một bảng DynamoDB, hãy ghi nhớ các phương pháp tốt nhất sau:

- **Bắt đầu với các thực thể khác nhau trong bảng của bạn.** Nếu bạn đang lưu trữ nhiều loại dữ liệu khác nhau trong một bảng duy nhất—chẳng hạn như [nhân viên, phòng ban, khách hàng, và đơn hàng](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-modeling-nosql-B.html)—hãy đảm bảo khóa chính của bạn có cách để xác định rõ ràng từng thực thể và cho phép thực hiện các hành động cốt lõi trên các mục riêng lẻ.
- **Sử dụng tiền tố để phân biệt giữa các loại thực thể.** Sử dụng tiền tố để phân biệt giữa các loại thực thể có thể ngăn chặn xung đột và hỗ trợ trong việc truy vấn. Ví dụ, nếu bạn có cả khách hàng và nhân viên trong cùng một bảng, khóa chính cho một khách hàng có thể là `CUSTOMER#<CUSTOMERID>`, và khóa chính cho một nhân viên có thể là `EMPLOYEE#<EMPLOYEEID>`.
- **Tập trung vào các hành động trên một mục đầu tiên, sau đó thêm các hành động trên nhiều mục nếu có thể.** Đối với một khóa chính, điều quan trọng là bạn có thể đáp ứng các tùy chọn đọc và ghi trên một mục duy nhất bằng cách sử dụng các API cho mục đơn lẻ: **GetItem** ([GetItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_GetItem.html)), **PutItem** ([PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)), **UpdateItem** ([UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)), và **DeleteItem** ([DeleteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html)). Bạn cũng có thể đáp ứng các mẫu đọc trên nhiều mục với khóa chính bằng cách sử dụng **Query** ([Query](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html)). Nếu không, bạn có thể thêm một chỉ mục phụ để xử lý các trường hợp sử dụng **Query**.

Với các phương pháp tốt nhất này, hãy thiết kế khóa chính cho bảng ứng dụng trò chơi và thực hiện một số hành động cơ bản.