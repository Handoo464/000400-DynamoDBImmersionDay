---
title : "Kết nối đến máy chủ Public"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.1. </b> "
---
Bây giờ bạn đã tạo bảng, bạn có thể tải một số dữ liệu mẫu vào bảng bằng cách chạy tập lệnh Python sau.

```bash
cd /home/ubuntu/workshop
python load_logfile.py logfile ./data/logfile_small1.csv
```

Các tham số trong lệnh trước: 1) Tên bảng = 2) Tên tệp = `logfile``logfile_small1.csv`

Đầu ra sẽ giống như sau.

```txt
row: 100 in 0.780548095703125
row: 200 in 7.2669219970703125
row: 300 in 1.547729730606079
row: 400 in 3.9651060104370117
row: 500 in 3.98996901512146
RowCount: 500, Total seconds: 17.614499807357788
```

**Curious behavior:** Bạn có thể tự hỏi tại sao một trong những lần chạy mất hơn năm giây. Xem bước tiếp theo để biết giải thích.