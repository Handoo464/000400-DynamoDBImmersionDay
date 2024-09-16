---
title : "Bước 3 - Tải tệp lớn hơn để so sánh thời gian thực thi"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.2.3. </b> "
---
Chạy lại tập lệnh, nhưng lần này sử dụng tệp dữ liệu đầu vào lớn hơn.

```bash
python load_logfile.py logfile ./data/logfile_medium1.csv
```

**Tham số:** 1) Tên bảng = 2) Tên tệp = `logfile``logfile_medium1.csv`

Đầu ra sẽ giống như sau. Nó sẽ chạy chậm hơn về cuối và mất từ một đến ba phút để hoàn thành, tùy thuộc vào tốc độ bạn chạy lệnh này sau Bước 2.

```txt
row: 100 in 0.490761995316
...
row: 2000 in 3.188856363296509
RowCount: 2000, Total seconds: 75.0764648914
```

HOẶC:

```txt
row: 100 in 0.490761995316
...
row: 2000 in 18.479122161865234
RowCount: 2000, Total seconds: 133.84829711914062
```

**Xem xét kết quả đầu ra:** Bạn sẽ nhận thấy rằng thời gian tải cho mỗi lô 100 hàng thường xuyên vượt quá năm giây. Điều này xảy ra vì trong mỗi lô kéo dài nhiều giây, bạn đang thấy các giới hạn (throttles) khiến SDK Boto3 giảm tốc độ chèn dữ liệu (còn được gọi là exponential backoff - giãn cách theo cấp số nhân). SDK Boto3 đang chờ DynamoDB bổ sung dung lượng cho bảng DynamoDB, việc này xảy ra mỗi giây đối với các bảng có thông lượng được cấp phát. Trong Amazon CloudWatch, các giới hạn này xuất hiện dưới tên chỉ số `WriteThrottleEvents`.