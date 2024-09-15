---
title : "Bước 4 - Xem số liệu CloudWatch trên table của bạn"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 3.2.4. </b> "
---

Để xem các số liệu Amazon CloudWatch cho bảng của bạn:

1. Điều hướng đến phần DynamoDB trong bảng điều khiển quản lý AWS.

2. Như hình dưới đây, trong bảng điều hướng, chọn **Tables**. Chọn bảng `logfile`, và trong ngăn bên phải, chọn tab **Metrics**.

   ![Mở số liệu CloudWatch cho bảng](/images/3/3.2/1.png)

Các số liệu CloudWatch sẽ trông như hình dưới đây.

![Số liệu CloudWatch cho bảng cơ sở](/images/3/3.2/2.png)

Bạn có thể không thấy dữ liệu dung lượng được cấp phát trong các biểu đồ dung lượng đọc hoặc ghi, được hiển thị dưới dạng các đường màu đỏ. Việc DynamoDB tạo ra các số liệu CloudWatch về dung lượng được cấp phát mất thời gian, đặc biệt là đối với các bảng mới.

Các số liệu CloudWatch sẽ trông như hình dưới đây đối với chỉ mục phụ toàn cầu (GSI).

![Số liệu CloudWatch cho GSI](/images/3/3.2/3.png)

**Bạn có thể đang tự hỏi:** Tại sao có các sự kiện giới hạn (throttling) trên bảng cơ sở nhưng không có trên chỉ mục phụ toàn cầu? Lý do là bảng cơ sở nhận các ghi chép ngay lập tức và tiêu thụ dung lượng ghi khi thực hiện, trong khi dung lượng của chỉ mục phụ toàn cầu (GSI) được tiêu thụ không đồng bộ sau một thời gian kể từ khi ghi thành công vào bảng cơ sở. Để hệ thống này hoạt động bên trong dịch vụ DynamoDB, có một bộ đệm giữa một bảng DynamoDB cơ sở và một chỉ mục phụ toàn cầu (GSI). Một bảng cơ sở sẽ nhanh chóng gặp phải giới hạn nếu dung lượng bị cạn kiệt, trong khi chỉ một sự mất cân đối trong thời gian dài trên GSI mới làm đầy bộ đệm, dẫn đến việc tạo ra giới hạn. Tóm lại, GSI khoan dung hơn trong trường hợp mô hình truy cập không cân đối.

Tiếp tục bài tập này để xem điều gì xảy ra khi bạn tăng thêm dung lượng ghi cho bảng cơ sở DynamoDB.