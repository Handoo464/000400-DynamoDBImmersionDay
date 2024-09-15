---
title : "Kết nối đến máy chủ Public"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.6.3. </b> "
---

# Bước 3 - Quét bảng employees để tìm quản lý bằng cách sử dụng sparse global secondary index

Bây giờ, hãy quét chỉ mục phụ toàn cầu mới `GSI_2` trên bảng `employees`. Khi sử dụng chỉ mục thưa mới, chúng ta kỳ vọng sẽ tiêu thụ ít đơn vị dung lượng đọc hơn cho các mục. Chúng ta sẽ sử dụng sparse index như một bộ lọc rất hiệu quả để cải thiện hiệu suất cho mẫu truy cập này.

```py
response = table.scan(
  Limit=pageSize,
  IndexName='GSI_2'
)
```

Chạy lệnh AWS CLI sau để thực hiện thao tác quét này bằng sparse index.

```bash
python scan_for_managers_gsi.py employees 100
```

**Tham số:**

1. Tên bảng = `employees`
2. Kích thước trang = `100` (đây là kích thước phân trang cho thao tác quét).

Kết quả đầu ra bao gồm số lượng mục đã quét và thời gian thực thi.

```txt
Number of managers: 84. # of records scanned: 84. Execution time: 0.287754058838 seconds
```

Quan sát số lượng mục đã quét và thời gian thực thi khi sử dụng sparse index. So sánh điều này với kết quả đạt được từ thao tác quét trên bảng cơ sở trong Bước 2. Sparse index có ít dữ liệu hơn và hiệu quả hơn.