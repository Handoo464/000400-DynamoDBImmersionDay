---
title : "Bước 2 - Truy vấn GSI bằng các phân đoạn"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.4.2. </b> "
---

Để lấy tất cả các bản ghi nhật ký có mã phản hồi 404, bạn cần truy vấn tất cả các phân vùng của chỉ mục phụ toàn cầu bằng cách sử dụng khóa sắp xếp. Bạn có thể làm điều này bằng cách sử dụng các luồng song song trong ứng dụng của mình và sử dụng khóa phân vùng và khóa sắp xếp.

```py
  if date:= "all":
    ke = Key('GSI_1_PK').eq("shard#{}".format(shardid)) & Key('GSI_1_SK').begins_with(responsecode)
  else:
    ke = Key('GSI_1_PK').eq("shard#{}".format(shardid)) & Key('GSI_1_SK').begins_with(responsecode+"#"+date)

  response = table.query(
    IndexName='GSI_1',
    KeyConditionExpression=ke
    )
```

Chạy script sau để lấy các mục từ chỉ mục phụ toàn cầu phân mảnh bằng cách chỉ sử dụng khóa phân vùng và mã phản hồi.

```bash
python query_responsecode.py logfile_scan 404
```

Điều này sẽ truy vấn bảng `logfile_scan` để lấy các mục có khóa sắp xếp bắt đầu bằng `404`. `begins_with` là một tham số trong [KeyConditionExpression của DynamoDB Query như được mô tả trong tài liệu của chúng tôi](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html#DDB-Query-request-KeyConditionExpression). Một truy vấn được chạy cho mỗi shard trên GSI và kết quả được đếm trên phía client. Kết quả đầu ra của script sẽ trông như sau:

```txt
Records with response code 404 in the shardid 0 = 0
Records with response code 404 in the shardid 1 = 1750
Records with response code 404 in the shardid 2 = 2500
Records with response code 404 in the shardid 3 = 1250
Records with response code 404 in the shardid 4 = 1000
Records with response code 404 in the shardid 5 = 1000
Records with response code 404 in the shardid 6 = 1750
Records with response code 404 in the shardid 7 = 1500
Records with response code 404 in the shardid 8 = 3250
Records with response code 404 in the shardid 9 = 2750
Number of records with responsecode 404 is 16750. Query time: 1.5092344284057617 seconds
```

Bạn cũng có thể truy vấn cùng một chỉ mục phụ toàn cầu cho cùng mã phản hồi và chỉ định một ngày cụ thể. Điều này sẽ truy vấn bảng `logfile_scan` để lấy các mục có khóa sắp xếp bắt đầu bằng `404#2017-07-21`.

```bash
python query_responsecode.py logfile_scan 404 --date 2017-07-21
```

Kết quả đầu ra sẽ trông như sau:

```txt
Records with response code 404 in the shardid 0 = 0
Records with response code 404 in the shardid 1 = 750
Records with response code 404 in the shardid 2 = 750
Records with response code 404 in the shardid 3 = 250
Records with response code 404 in the shardid 4 = 500
Records with response code 404 in the shardid 5 = 0
Records with response code 404 in the shardid 6 = 250
Records with response code 404 in the shardid 7 = 1000
Records with response code 404 in the shardid 8 = 1000
Records with response code 404 in the shardid 9 = 1000
Number of records with responsecode 404 is 5500. Query time: 1.190359354019165 seconds
```

#### Tóm tắt

Trong bài tập này, chúng ta đã sử dụng một chỉ mục phụ toàn cầu phân mảnh (GSI) để nhanh chóng truy xuất các kết quả đã được sắp xếp, sử dụng các khóa tổng hợp sẽ được đề cập chi tiết hơn trong [Bài tập 6](https://catalog.workshops.aws/dynamodb-labs/en-US/design-patterns/ex6compos) của phòng lab. Sử dụng phương pháp ghi phân mảnh GSI khi bạn cần một chỉ mục được sắp xếp có khả năng mở rộng.

Ví dụ về GSI phân mảnh sử dụng một phạm vi khóa từ 0 đến 9, nhưng trong ứng dụng của bạn, bạn có thể chọn bất kỳ phạm vi nào. Trong ứng dụng của bạn, bạn có thể thêm nhiều shard hơn khi số lượng mục được lập chỉ mục tăng lên. Trong mỗi shard, dữ liệu được sắp xếp trên đĩa theo khóa sắp xếp. Điều này cho phép chúng ta truy xuất nhật ký truy cập máy chủ được sắp xếp theo mã trạng thái và ngày, ví dụ: `404#2017-07-21`.

Để biết thêm thông tin về cách chọn số lượng shard phù hợp, hãy đọc bài viết [Chọn số lượng shard phù hợp cho bảng Amazon DynamoDB quy mô lớn của bạn](https://aws.amazon.com/blogs/database/choosing-the-right-number-of-shards-for-your-large-scale-amazon-dynamodb-table/) trên AWS Database Blog.