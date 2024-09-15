---
title : "Exercise 3: Global Secondary Index Write Sharding"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 3.4. </b> "
---

Khóa chính của một bảng DynamoDB hoặc một chỉ mục phụ toàn cầu bao gồm một khóa phân vùng và một khóa sắp xếp tùy chọn. Cách bạn thiết kế nội dung của các khóa này rất quan trọng đối với cấu trúc và hiệu suất của cơ sở dữ liệu. Các giá trị khóa phân vùng xác định các phân vùng logic mà dữ liệu của bạn được lưu trữ. Do đó, việc chọn một giá trị khóa phân vùng phân phối đồng đều khối lượng công việc trên tất cả các phân vùng trong bảng hoặc chỉ mục phụ toàn cầu là rất quan trọng. Để thảo luận về cách chọn khóa phân vùng phù hợp, hãy xem bài viết trên blog của chúng tôi có tiêu đề [Choosing the Right DynamoDB Partition Key](https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/).

Trong bài tập này, bạn sẽ tìm hiểu về phương pháp ghi phân mảnh (write sharding) trên chỉ mục phụ toàn cầu, đây là một mẫu thiết kế hiệu quả để truy vấn chọn lọc các mục được phân tán trên các phân vùng logic khác nhau trong một bảng rất lớn. Hãy xem lại ví dụ nhật ký truy cập máy chủ từ [Bài tập 1]{href="/design-patterns/ex1capacity"}, dựa trên nhật ký truy cập dịch vụ Apache. Lần này, bạn sẽ truy vấn các mục có mã phản hồi `4xx`. Lưu ý rằng các mục có mã phản hồi `4xx` chiếm một tỷ lệ rất nhỏ trong tổng số dữ liệu và không có sự phân bố đồng đều theo mã phản hồi. Ví dụ, mã phản hồi `200 OK` có nhiều bản ghi hơn các mã khác, điều này là mong đợi cho bất kỳ ứng dụng web nào.

Biểu đồ sau đây cho thấy sự phân bố của các bản ghi nhật ký theo mã phản hồi cho tệp mẫu, `logfile_medium1.csv`.

![Bản ghi nhật ký theo mã phản hồi](/images/3/3.4/1.jpg)

Bạn sẽ tạo một chỉ mục phụ toàn cầu ghi phân mảnh trên một bảng để ngẫu nhiên hóa các lượt ghi trên nhiều giá trị khóa phân vùng logic. Việc này sẽ tăng thông lượng ghi và đọc của ứng dụng. Để áp dụng mẫu thiết kế này, bạn có thể tạo một số ngẫu nhiên từ một tập cố định (ví dụ: 1 đến 10) và sử dụng số này làm khóa phân vùng logic cho chỉ mục phụ toàn cầu. Vì bạn đang ngẫu nhiên hóa khóa phân vùng, các lượt ghi vào bảng sẽ được phân phối đồng đều trên tất cả các giá trị khóa phân vùng, không phụ thuộc vào bất kỳ thuộc tính nào. Điều này sẽ mang lại khả năng song song tốt hơn và thông lượng tổng thể cao hơn. Ngoài ra, nếu ứng dụng cần truy vấn các bản ghi nhật ký theo một mã phản hồi cụ thể vào một ngày cụ thể, bạn có thể tạo một khóa sắp xếp tổng hợp bằng cách kết hợp mã phản hồi và ngày.

Trong bài tập này, bạn sẽ tạo một chỉ mục phụ toàn cầu sử dụng các giá trị ngẫu nhiên cho khóa phân vùng và khóa tổng hợp `responsecode#date#hourofday` làm khóa sắp xếp. Bảng `logfile_scan` mà bạn đã tạo và nạp dữ liệu trong giai đoạn chuẩn bị của workshop đã có hai thuộc tính này. Nếu bạn chưa hoàn thành các bước thiết lập, hãy quay lại [Thiết lập - Bước 6](https://catalog.workshops.aws/dynamodb-labs/en-US/design-patterns/setup/Step6) và hoàn thành bước này. Các thuộc tính này đã được tạo bằng đoạn mã sau:

```py
SHARDS = 10
newitem['GSI_1_PK'] = "shard#{}".format((newitem['requestid'] % SHARDS) + 1)
newitem['GSI_1_SK'] = row[7] + "#" + row[2] + "#" + row[3]
```