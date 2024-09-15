---
title : "Bước 6 - Sau khi tăng dung lượng của bảng, tải thêm dữ liệu"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 3.2.6. </b> "
---
Sau khi bạn tăng dung lượng của bảng, hãy chạy lại tập lệnh Python sau để điền bảng bằng cách sử dụng `logfile_medium2.csv` Nhập tệp dữ liệu có cùng số hàng như khi bạn chạy lệnh này trước đó. Lưu ý rằng việc thực hiện lệnh xảy ra nhanh hơn lần này.

```bash
python load_logfile.py logfile ./data/logfile_medium2.csv
```

Đầu ra sẽ như thế này:

```txt
row: 100 in 0.9451174736022949
row: 200 in 0.8512668609619141
...
row: 1900 in 0.8499886989593506
row: 2000 in 0.8817043304443359
RowCount: 2000, Total seconds: 17.13607406616211
```

{{%notice info%}}
Với công suất mới, tổng thời gian tải thấp hơn.
{{%/notice%}}
