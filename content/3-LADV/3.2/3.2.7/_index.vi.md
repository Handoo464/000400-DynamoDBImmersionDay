---
title : "Bước 7 - Tạo bảng mới với global secondary index dung lượng thấp"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 3.2.7. </b> "
---

Bây giờ, hãy tạo một bảng mới với các đơn vị dung lượng khác nhau. Chỉ mục phụ toàn cầu (GSI) của bảng mới chỉ có 1 đơn vị dung lượng ghi (WCU) và 1 đơn vị dung lượng đọc (RCU).

Để tạo bảng mới, chạy lệnh AWS CLI sau:

```bash
aws dynamodb create-table --table-name logfile_gsi_low \
--attribute-definitions AttributeName=PK,AttributeType=S AttributeName=GSI_1_PK,AttributeType=S \
--key-schema AttributeName=PK,KeyType=HASH \
--provisioned-throughput ReadCapacityUnits=1000,WriteCapacityUnits=1000 \
--tags Key=workshop-design-patterns,Value=targeted-for-cleanup \
--global-secondary-indexes "IndexName=GSI_1,\
KeySchema=[{AttributeName=GSI_1_PK,KeyType=HASH}],\
Projection={ProjectionType=INCLUDE,NonKeyAttributes=['bytessent']},\
ProvisionedThroughput={ReadCapacityUnits=1,WriteCapacityUnits=1}"
```

Chạy lệnh AWS CLI sau để đợi cho đến khi bảng chuyển sang trạng thái `ACTIVE`:

```bash
aws dynamodb wait table-exists --table-name logfile_gsi_low
```

Lệnh đầu tiên tạo một bảng mới và một chỉ mục phụ toàn cầu với định nghĩa sau:

#### Bảng: `logfile_gsi_low`

- Sơ đồ khóa: HASH (partition key)
- Số đơn vị dung lượng đọc (RCUs) của bảng = 1000
- Số đơn vị dung lượng ghi (WCUs) của bảng = 1000
- Chỉ mục phụ toàn cầu:
  - GSI_1 (**1 RCU, 1 WCU**) - Cho phép truy vấn theo địa chỉ IP của host

|Tên thuộc tính (Loại)|Thuộc tính đặc biệt?|Trường hợp sử dụng thuộc tính|Giá trị mẫu của thuộc tính|
|---|---|---|---|
|PK (STRING)|Partition key|Lưu giữ request ID cho nhật ký truy cập|_request#104009_|
|GSI_1_PK (STRING)|GSI 1 partition key|Host cho yêu cầu, một địa chỉ IPv4|_host#66.249.67.3_|

Hãy nạp dữ liệu lớn vào bảng này. Bạn sẽ sử dụng phiên bản script tải dữ liệu Python có nhiều luồng để mô phỏng nhiều lần ghi mỗi giây vào bảng DynamoDB. Điều này sẽ tạo ra sự tranh chấp cho dung lượng cấp phát để mô phỏng một đợt tăng lưu lượng truy cập trên một bảng bị cấp phát dưới mức.

```bash
python load_logfile_parallel.py logfile_gsi_low
```

Sau vài phút, việc thực thi script này sẽ bị giới hạn và hiển thị một thông báo lỗi tương tự như lỗi sau. Điều này cho thấy bạn nên tăng dung lượng cấp phát của bảng DynamoDB hoặc kích hoạt tính năng tự động mở rộng của DynamoDB nếu bạn chưa làm như vậy (đọc thêm về [tự động mở rộng DynamoDB trong tài liệu AWS](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.ProvisionedThroughput.AutoScaling)).

```txt
ProvisionedThroughputExceededException: An error occurred (ProvisionedThroughputExceededException) when calling the BatchWriteItem operation (reached max retries: 9): The level of configured provisioned throughput for one or more global secondary indexes of the table was exceeded. Consider increasing your provisioning level for the under-provisioned global secondary indexes with the UpdateTable API
```

Bạn có thể tạm dừng hoạt động bằng cách nhấn Ctrl+Z (Ctrl+C nếu bạn sử dụng Mac).
{{%notice warning%}}
Bảng mới này có nhiều RCUs (1,000) và WCUs (1,000), nhưng bạn vẫn gặp lỗi và thời gian tải tăng lên.
{{%/notice%}}

**Chủ đề thảo luận:** Bạn có thể giải thích hành vi của bài kiểm tra không? Một ngoại lệ có tên `ProvisionedThroughputExceededException` đã được DynamoDB trả về với thông báo ngoại lệ đề xuất tăng dung lượng cấp phát của GSI. Đây là một lỗi quan trọng và cần phải được xử lý. Nói ngắn gọn, nếu bạn muốn 100% các lần ghi trên bảng cơ sở DynamoDB được sao chép vào GSI, thì GSI nên được cấp phát với 100% (cùng mức) dung lượng trên bảng cơ sở, trong ví dụ này là 1,000 WCU. Đơn giản là GSI đã bị cấp phát dưới mức.

#### Xem lại bảng trong AWS Console

Hãy xem lại các số liệu Amazon CloudWatch cho bài kiểm tra này trong bảng điều khiển quản lý AWS của Amazon DynamoDB. Chúng ta cần xem các số liệu nào đã được gửi tới CloudWatch trong khoảng thời gian ghi bị giới hạn này.

Mở bảng điều khiển AWS, hoặc chuyển sang tab trình duyệt của bạn với bảng điều khiển AWS, để xem các số liệu cho bảng `logfile_gsi_low`. Những số liệu này được tìm thấy dưới phần DynamoDB của bảng điều khiển quản lý AWS trong chế độ xem bảng. Nếu bạn không thấy bảng, hãy nhớ nhấn nút làm mới ở góc trên bên phải của bảng điều khiển DynamoDB.

Hình ảnh sau đây hiển thị số liệu dung lượng ghi cho bảng `logfile_gsi_low`. Lưu ý rằng các lần ghi đã tiêu thụ (đường màu xanh) thấp hơn so với các lần ghi cấp phát (đường màu đỏ) cho bảng trong bài kiểm tra. Điều này cho thấy bảng cơ sở có đủ dung lượng ghi cho đợt tăng yêu cầu.

{{%notice tip%}}
Có thể mất vài phút để dung lượng cấp phát (đường màu đỏ) hiển thị trên các biểu đồ. Các số liệu dung lượng cấp phát là tổng hợp và có thể có độ trễ từ năm đến mười phút cho đến khi chúng hiển thị sự thay đổi.
{{%/notice%}}

![Số liệu dung lượng ghi cho bảng](/images/3/3.2/4.png)

Hình ảnh sau đây hiển thị số liệu dung lượng ghi cho chỉ mục phụ toàn cầu. Lưu ý rằng các lần ghi đã tiêu thụ (đường màu xanh) cao hơn so với các lần ghi cấp phát (đường màu đỏ) cho chỉ mục phụ toàn cầu trong bài kiểm tra. Điều này cho thấy GSI đã bị cấp phát dưới mức đáng kể cho các yêu cầu mà nó nhận được.

![Số liệu dung lượng ghi cho GSI](/images/3/3.2/5.png)

Hình ảnh sau đây hiển thị các yêu cầu ghi bị giới hạn cho bảng `logfile_gsi_low`. Lưu ý rằng bảng có các yêu cầu ghi bị giới hạn, mặc dù bảng cơ sở đã được cấp phát đủ WCUs. Mỗi yêu cầu API bị giới hạn trên DynamoDB tạo ra một điểm dữ liệu cho số liệu `ThrottledRequests`. Trong hình này, khoảng 20 yêu cầu API đã bị giới hạn bởi DynamoDB. Tuy nhiên, bảng có một GSI và chúng ta chưa biết nếu GSI hoặc bảng cơ sở là nguồn của việc giới hạn. Chúng ta cần tiếp tục điều tra. ![Các lần ghi bị giới hạn cho bảng](/images/3/3.2/6.png)

Để xác định nguồn gốc của các yêu cầu ghi bị giới hạn này, hãy xem lại số liệu sự kiện ghi bị giới hạn. Nếu bảng cơ sở DynamoDB là nguồn giới hạn, nó sẽ có `WriteThrottleEvents`. Tuy nhiên, nếu GSI có dung lượng ghi không đủ, nó cũng sẽ có `WriteThrottleEvents`.

Khi bạn xem xét các sự kiện giới hạn cho GSI, bạn sẽ thấy nguồn gốc của các lần giới hạn! Chỉ có GSI có 'Throttled write events', điều này có nghĩa là nó là nguồn gây ra giới hạn trên bảng, và là nguyên nhân của các yêu cầu ghi Batch bị giới hạn.

![Các lần ghi bị giới hạn cho GSI](/images/3/3.2/7.png)
{{%notice warning%}}
Có thể mất một thời gian để các sự kiện ghi bị giới hạn xuất hiện trên biểu đồ sự kiện ghi bị giới hạn của GSI. Nếu bạn không thấy ngay lập tức các số liệu, hãy chạy lại lệnh trên để nạp dữ liệu vào DynamoDB và để nó tiếp tục trong vài phút để tạo ra nhiều sự kiện giới hạn.
{{%/notice%}}

Khi việc giới hạn ghi của chỉ mục phụ toàn cầu (GSI) của DynamoDB đủ để tạo ra các yêu cầu bị giới hạn, hành vi này được gọi là áp lực ngược của GSI (GSI back pressure). Các yêu cầu bị giới hạn là lỗi `ProvisionedThroughputExceededException` trong các SDK của AWS, tạo ra các số liệu `ThrottledRequests` trong CloudWatch và