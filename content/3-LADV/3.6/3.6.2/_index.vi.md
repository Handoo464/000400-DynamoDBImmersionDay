---
title : "Bước 2 - Quét bảng nhân viên để tìm người quản lý mà không cần sử dụng sparse global secondary index"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.6.2. </b> "
---

Mẫu sparse index (chỉ mục thưa) giúp giảm bớt số lượng dữ liệu cần tìm kiếm, giúp cho các tìm kiếm trên chỉ mục, bằng cách sử dụng API `Scan` hoặc `Query`, trở nên hiệu quả hơn. Thay vì phải duyệt qua tất cả dữ liệu trong bảng cơ sở DynamoDB, bạn có thể tạo một sparse index để chứa một phần nhỏ thông tin của bạn nhằm dễ dàng truy vấn và tìm kiếm. Để tìm hiểu thêm về định nghĩa của một [DynamoDB sparse index, vui lòng xem tài liệu về các phương pháp tốt nhất của chúng tôi](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-indexes-general-sparse-indexes.html).

Để bắt đầu, quét bảng để tìm tất cả các quản lý mà không sử dụng chỉ mục phụ toàn cầu. Thông lượng tiêu thụ sẽ cho chúng ta một cơ sở so sánh sau này trong bài tập. Trong trường hợp này, bạn cần sử dụng biểu thức lọc (filter expression) để chỉ trả về các mục mà thuộc tính `is_manager` bằng 1, như trong ví dụ mã sau:

```py
fe = "is_manager = :f"
eav = {":f": "1"}
response = table.scan(
  FilterExpression=fe,
  ExpressionAttributeValues=eav,
  Limit=pageSize
)
```

Chạy script Python sau để tìm tất cả các quản lý mà không sử dụng chỉ mục phụ toàn cầu.

```bash
python scan_for_managers.py employees 100
```

**Tham số:**

1. Tên bảng = `employees`
2. Kích thước trang = `100` (đây là kích thước phân trang cho thao tác quét).

Kết quả đầu ra bao gồm số lượng mục đã quét và thời gian thực thi.

```txt
Managers count: 84. # of records scanned: 4000. Execution time: 0.596132993698 seconds
```

Xem lại số lượng mục đã quét để trả về các giá trị. Giá trị số lượng bản ghi đã quét trong kết quả mẫu trên, 4000, phải khớp với số trong kết quả đầu ra của script của bạn. Nếu bạn nhận được lỗi hoặc sự không nhất quán, hãy đảm bảo bạn đã hoàn thành Bước 1 trong bài tập này và cả hai chỉ mục đều đang ở trạng thái `ACTIVE`. Hãy thử thay đổi kích thước trang thành một số lớn hơn như 1000. Thời gian thực thi sẽ giảm vì có ít chuyến đi khứ hồi hơn tới DynamoDB. Một cuộc gọi API `Scan` có thể trả về tối đa 1MB dữ liệu mỗi lần.