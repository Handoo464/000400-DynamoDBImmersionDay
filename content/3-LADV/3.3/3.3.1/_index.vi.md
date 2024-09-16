---
title : "Bước 1 - Thực hiện quét tuần tự"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.3.1. </b> "
---

Đầu tiên, thực hiện một thao tác `Scan` đơn giản (tuần tự) để tính tổng số byte được gửi cho tất cả các bản ghi với `mã phản hồi khác 200` (`response code <> 200`). Bạn có thể chạy một thao tác `Scan` với biểu thức lọc (`filter expression`) để lọc ra các bản ghi không liên quan. Sau đó, worker ứng dụng sẽ tính tổng giá trị của tất cả các bản ghi trả về mà `mã phản hồi khác 200`.

Tệp Python, `scan_logfile_simple.py`, bao gồm lệnh để chạy một thao tác `Scan` với biểu thức lọc và sau đó tính tổng số byte đã gửi.

Khối mã sau đây quét bảng.

```py
  fe = "responsecode <> :f"
  eav = {":f": 200}
  response = table.scan(
      FilterExpression=fe,
      ExpressionAttributeValues=eav,
      Limit=pageSize,
      ProjectionExpression='bytessent')
```

Bạn có thể xem tệp này bằng cách chạy lệnh `vim ~/workshop/scan_logfile_simple.py`. Nhập `:q` và nhấn enter để thoát vim.

Lưu ý rằng có một tham số `Limit` được đặt trong lệnh `Scan`. Một thao tác `Scan` đơn lẻ sẽ đọc tối đa số mục được đặt (nếu sử dụng tham số `Limit`) hoặc tối đa 1 MB dữ liệu, sau đó áp dụng bất kỳ bộ lọc nào vào kết quả bằng cách sử dụng `FilterExpression`. Nếu tổng số mục đã quét vượt quá giới hạn tối đa được đặt bởi tham số `Limit` hoặc giới hạn kích thước tập dữ liệu là 1 MB, quá trình quét sẽ dừng lại và kết quả được trả về cho người dùng dưới dạng giá trị `LastEvaluatedKey`. Giá trị này có thể được sử dụng trong một thao tác tiếp theo để bạn có thể tiếp tục từ nơi đã dừng lại.

Trong đoạn mã sau, giá trị `LastEvaluatedKey` trong phản hồi được truyền cho phương thức `Scan` thông qua tham số `ExclusiveStartKey`.

```py
  while 'LastEvaluatedKey' in response:
    response = table.scan(
        FilterExpression=fe,
        ExpressionAttributeValues=eav,
        Limit=pageSize,
        ExclusiveStartKey=response['LastEvaluatedKey'],
        ProjectionExpression='bytessent')
    for i in response['Items']:
        totalbytessent += i['bytessent']
```

Khi trang cuối cùng được trả về, `LastEvaluatedKey` không còn là một phần của phản hồi, vì vậy bạn biết rằng quá trình quét đã hoàn tất.

Bây giờ, hãy thực thi đoạn mã này.

```bash
python scan_logfile_simple.py logfile_scan
```

**Tham số:** Tên bảng: `logfile_scan`

Kết quả đầu ra sẽ trông giống như sau:

```txt
Scanning 1 million rows of table logfile_scan to get the total of bytes sent
Total bytessent 6054250 in 16.325594425201416 seconds
```

Hãy ghi lại thời gian cần thiết để hoàn tất quá trình quét. Với tập dữ liệu trong bài tập này, một thao tác quét song song sẽ hoàn thành nhanh hơn so với quét tuần tự.