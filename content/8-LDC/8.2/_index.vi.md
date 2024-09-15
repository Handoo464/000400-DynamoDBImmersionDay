---
title : "Bank Payments Scenario"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 8.2. </b> "
---

## Một ngân hàng đã yêu cầu bạn phát triển một hệ thống backend mới để xử lý các khoản thanh toán đã được lên lịch.

Đây chủ yếu là một khối lượng công việc OLTP với các quy trình xử lý hàng loạt hàng ngày. Các mục trong bảng biểu thị các khoản thanh toán được lên lịch giữa các tài khoản. Khi các mục được chèn, chúng được lên lịch vào một ngày cụ thể để xử lý thanh toán. Mỗi ngày, các mục được gửi thường xuyên đến hệ thống giao dịch để xử lý, và trạng thái của chúng sẽ thay đổi thành `PENDING`. Khi giao dịch thành công, trạng thái của mục được đặt thành `PROCESSED` và được cập nhật với một mã giao dịch mới.

### Kích thước khối lượng công việc:

- Các tài khoản có thể có nhiều khoản thanh toán được lên lịch cho bất kỳ ngày nào trong tương lai.
- Các khoản thanh toán có các trường dữ liệu sau: _AccountID_, _ScheduledTime_, _Status_ (`SCHEDULED`, `PENDING`, hoặc `PROCESSED`), _DataBlob_ (kích thước mục <= 8 KB).
- Mỗi ngày, vào lúc 1:00 sáng, có một triệu khoản thanh toán tự động được thêm vào _cho ngày đó_, và cần hoàn thành trong 30 phút.
- Mỗi ngày có thêm một triệu khoản thanh toán với trạng thái `SCHEDULED`, chủ yếu trong khoảng thời gian từ 6:00 sáng đến 6:00 chiều.
- Trong suốt cả ngày, một công việc hàng loạt sẽ chạy thường xuyên để truy vấn các khoản thanh toán `SCHEDULED` của ngày hôm nay. Dịch vụ này sẽ gửi các mục `SCHEDULED` đến hệ thống giao dịch. Khi các mục được gửi đến hệ thống giao dịch, trạng thái thanh toán sẽ được thay đổi thành `PENDING`.
- Khi hệ thống giao dịch hoàn tất, trạng thái của mục được thay đổi thành `PROCESSED` và một mã giao dịch mới sẽ được thêm vào mục.
- Các mục cần được trả về cho một tài khoản cụ thể mà đã được lên lịch thanh toán trong 90 ngày tới.
- Hệ thống giao dịch phải truy xuất tất cả các mục cho một ngày cụ thể (ví dụ, hôm nay) trên tất cả các tài khoản. Nó phải có khả năng truy xuất các mục có trạng thái cụ thể là `SCHEDULED` hoặc `PENDING`.

#### **Thử thách của bạn:** Phát triển một mô hình dữ liệu NoSQL cho ngân hàng để đáp ứng các yêu cầu về thanh toán đã được lên lịch.

#### **Thử thách bổ sung:** Vào cuối mỗi ngày, tất cả các mục đã có trạng thái `PROCESSED` cần được chuyển sang một bảng lưu trữ lâu dài (do yêu cầu tuân thủ, dữ liệu cần phải nằm trong một bảng riêng biệt). Hãy thiết kế một mô hình dữ liệu thứ hai đáp ứng cùng các yêu cầu truy cập như trên, và thêm một yêu cầu khác là trả về một mục cụ thể liên quan đến mã giao dịch.

