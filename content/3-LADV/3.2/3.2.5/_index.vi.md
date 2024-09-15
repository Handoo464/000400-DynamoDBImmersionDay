---
title : "Bước 5 - Tăng dung lượng của bảng"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 3.2.5. </b> "
---
Chạy lệnh AWS CLI sau để tăng đơn vị dung lượng ghi và đơn vị dung lượng đọc từ 5 lên 100.

```bash
aws dynamodb update-table --table-name logfile \
--provisioned-throughput ReadCapacityUnits=100,WriteCapacityUnits=100
```

Chạy lệnh để đợi cho đến khi bảng trở thành Hoạt động.

```bash
time aws dynamodb wait table-exists --table-name logfile
```

**Chủ đề thảo luận:** Mất bao lâu để tăng công suất? Nó nhanh hơn, hay lâu hơn bạn mong đợi? Thông thường, khi bạn cần thêm dung lượng từ bảng thông lượng được cung cấp, bảng thông lượng này sẽ sẵn dùng chỉ trong vài chục giây.