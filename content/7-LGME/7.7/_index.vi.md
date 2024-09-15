---
title : "Tóm tắt và dọn dẹp tài nguyên"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 7.7. </b> "
---


Dưới đây là bản dịch sang tiếng Việt, giữ nguyên các thuật ngữ chuyên ngành:

---

Trong các module trước, các mẫu truy cập trong ứng dụng game đã được xử lý như sau:

- Tạo hồ sơ người dùng (Write)
- Cập nhật hồ sơ người dùng (Write)
- Lấy hồ sơ người dùng (Read)
- Tạo game (Write)
- Tìm game mở (Read)
- Xem game (Read)
- Tham gia game cho người dùng (Write)
- Bắt đầu game (Write)
- Cập nhật game cho người dùng (Write)
- Cập nhật game (Write)
- Tìm game cho người dùng (Read)

Các chiến lược được sử dụng để thỏa mãn các mẫu này bao gồm:

- Thiết kế bảng đơn để kết hợp nhiều loại thực thể trong một bảng.
- Khóa chính tổng hợp cho phép mối quan hệ nhiều-nhiều.
- Chỉ mục thứ cấp toàn cục thưa (GSI) để lọc trên một trong các trường.
- Giao dịch DynamoDB để xử lý các mẫu ghi phức tạp trên nhiều thực thể.
- Chỉ mục đảo ngược (GSI) để cho phép tra cứu ngược trên thực thể nhiều-nhiều.

#### Chúc mừng! Bạn đã hoàn thành khóa học này.

Vui lòng dành vài phút để chia sẻ phản hồi của bạn với chúng tôi qua liên kết mà bạn đã nhận được từ người hướng dẫn lab.

## Dọn dẹp

Nếu bạn đang chạy lab này trong Tài khoản AWS của riêng mình (không phải sự kiện do AWS tổ chức), đừng quên dọn dẹp tài nguyên bằng cách xóa CloudFormation stack hoặc các tài nguyên (trong trường hợp không có CloudFormation stack) mà bạn đã sử dụng trong quá trình thiết lập.

{{%notice warning%}}
Nếu bạn đang thực hiện lab trong Tài khoản AWS của mình, bạn sẽ tạo các bảng DynamoDB mà sẽ phát sinh chi phí có thể lên đến hàng chục hoặc hàng trăm đô la mỗi ngày. **Hãy đảm bảo xóa các bảng DynamoDB bằng cách sử dụng bảng điều khiển DynamoDB và chắc chắn rằng bạn [xóa môi trường Cloud9](https://docs.aws.amazon.com/cloud9/latest/user-guide/delete-environment.html) ngay khi hoàn thành lab.**
{{%/notice%}}

---

Hy vọng bản dịch này sẽ giúp bạn dễ dàng hơn trong việc dọn dẹp các tài nguyên AWS sau khi hoàn thành lab.