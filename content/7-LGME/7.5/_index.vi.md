---
title : "Tham gia và đóng trò chơi"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 7.5. </b> "
---

Qua các module trước trong bài lab này, bạn đã thỏa mãn các mẫu truy cập cho việc tạo và truy xuất các thực thể cốt lõi trong ứng dụng game, chẳng hạn như `Users` và `Games`. Bạn cũng đã sử dụng một GSI thưa (sparse GSI) để tìm các game mở mà người dùng có thể tham gia.

Trong module này, bạn sẽ thỏa mãn hai mẫu truy cập:

- Tham gia game cho một người dùng (Write)
- Bắt đầu game (Write)

Lưu ý rằng cả hai mẫu truy cập này đều ghi dữ liệu vào DynamoDB, trái ngược với các mẫu đọc nhiều mà bạn đã thực hiện từ trước trong bài lab này.

## Giao dịch DynamoDB

Để thỏa mãn mẫu truy cập "Tham gia game cho một người dùng" trong các bước của module này, bạn sẽ sử dụng [giao dịch DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html). Giao dịch rất phổ biến trong các hệ thống quan hệ cho các thao tác ảnh hưởng đến nhiều phần tử dữ liệu cùng một lúc. Ví dụ, hãy tưởng tượng bạn đang điều hành một ngân hàng. Một khách hàng chuyển $100 cho một khách hàng khác. Khi ghi lại giao dịch này, bạn sẽ sử dụng giao dịch để đảm bảo các thay đổi được áp dụng cho cả hai tài khoản của khách hàng, thay vì chỉ một người.

Giao dịch DynamoDB giúp dễ dàng hơn trong việc xây dựng các ứng dụng thay đổi nhiều mục (items) trong một thao tác duy nhất. Với giao dịch, bạn có thể thao tác lên đến 100 mục như một phần của yêu cầu giao dịch duy nhất. Trong một cuộc gọi API [TransactWriteItems](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TransactWriteItems.html), bạn có thể sử dụng các thao tác sau:

- **Put**: Để chèn hoặc ghi đè một mục.
- **Update**: Để cập nhật một mục hiện có.
- **Delete**: Để xóa một mục.
- **ConditionCheck**: Để khẳng định một điều kiện trên một mục hiện có mà không thay đổi mục đó.

Trong các bước tiếp theo, bạn sẽ sử dụng giao dịch DynamoDB khi thêm người dùng mới vào game và ngăn không cho game bị đầy quá mức.

