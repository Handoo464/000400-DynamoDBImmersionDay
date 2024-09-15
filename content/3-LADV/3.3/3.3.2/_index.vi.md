---
title : "Bước 2 - Thực hiện Quét song song"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2.2. </b> "
---

Để thực hiện một thao tác `Scan` song song, mỗi worker ứng dụng gửi yêu cầu `Scan` của riêng mình với các tham số sau:

- `Segment`: Đoạn sẽ được quét bởi một worker ứng dụng cụ thể. Mỗi worker nên sử dụng một giá trị khác nhau cho `Segment`.
- `TotalSegments`: Tổng số đoạn cho quá trình quét song song. Giá trị này phải giống với số worker mà ứng dụng của bạn sẽ sử dụng.

Hãy xem qua khối mã sau đây cho việc quét song song, từ tệp `scan_logfile_parallel.py`.

```py
  fe = "responsecode <> :f"
  eav = {":f": 200}
  response = table.scan(
    FilterExpression=fe,
    ExpressionAttributeValues=eav,
    Limit=pageSize,
    TotalSegments=totalsegments,
    Segment=threadsegment,
    ProjectionExpression='bytessent'
    )
```

Sau lần quét đầu tiên, bạn có thể tiếp tục quét bảng cho đến khi `LastEvaluatedKey` bằng null.

```py
  while 'LastEvaluatedKey' in response:
    response = table.scan(
        FilterExpression=fe,
        ExpressionAttributeValues=eav,
        Limit=pageSize,
        TotalSegments=totalsegments,
        Segment=threadsegment,
        ExclusiveStartKey=response['LastEvaluatedKey'],
        ProjectionExpression='bytessent')
    for i in response['Items']:
        totalbytessent += i['bytessent']
```

Để chạy đoạn mã này, thực thi lệnh AWS CLI sau:

```bash
python scan_logfile_parallel.py logfile_scan 2
```

**Tham số:**

1. Tên bảng: `logfile_scan`
2. Số luồng: `2` (đây là số luồng sẽ được thực thi song song và cũng sẽ được sử dụng cho số đoạn).

Kết quả đầu ra sẽ trông như sau:

```txt
Scanning 1 million rows of the `logfile_scan` table to get the total of bytes sent

Total bytessent 6054250 in 8.544446229934692 seconds
```

{{%notice info%}}
Thời gian thực thi khi sử dụng quét song song sẽ ngắn hơn so với thời gian thực thi cho quét tuần tự. Sự khác biệt về thời gian thực thi sẽ càng lớn hơn đối với các bảng lớn.
{{%/notice%}}

#### Tóm tắt

Trong bài tập này, chúng ta đã minh họa việc sử dụng hai phương pháp quét bảng DynamoDB: tuần tự và song song, để đọc các mục từ một bảng hoặc chỉ mục phụ. Sử dụng `Scan` một cách cẩn trọng vì nó có thể tiêu thụ một lượng lớn tài nguyên dung lượng. Đôi khi `Scan` là phù hợp (như khi quét một bảng nhỏ) hoặc không thể tránh khỏi (như khi thực hiện xuất dữ liệu hàng loạt). Tuy nhiên, theo nguyên tắc chung, bạn nên thiết kế ứng dụng của mình để tránh thực hiện quét.