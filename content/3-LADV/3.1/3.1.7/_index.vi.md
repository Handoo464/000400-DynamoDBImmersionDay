---
title : "Kết nối đến máy chủ Public"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 3.1.7. </b> "
---

{{%notice info%}}
_Lưu ý: Tất cả các lệnh đều được thực thi trong bảng điều khiển shell kết nối với phiên bản EC2, không phải trên máy tính cục bộ của bạn. (Nếu bạn không chắc chắn, bạn luôn có thể xác nhận lại bằng cách quay lại [bước 1](https://catalog.workshops.aws/dynamodb-labs/en-US/design-patterns/setup/step1.html))_
{{%/notice%}}

Trong bước này, chúng ta sẽ nạp 1 triệu mục vào bảng để chuẩn bị cho bài tập này.

Chạy lệnh để tạo một bảng mới:

```bash
aws dynamodb create-table --table-name logfile_scan \
--attribute-definitions AttributeName=PK,AttributeType=S AttributeName=GSI_1_PK,AttributeType=S AttributeName=GSI_1_SK,AttributeType=S \
--key-schema AttributeName=PK,KeyType=HASH \
--provisioned-throughput ReadCapacityUnits=5000,WriteCapacityUnits=5000 \
--tags Key=workshop-design-patterns,Value=targeted-for-cleanup \
--global-secondary-indexes "IndexName=GSI_1,\
KeySchema=[{AttributeName=GSI_1_PK,KeyType=HASH},{AttributeName=GSI_1_SK,KeyType=RANGE}],\
Projection={ProjectionType=KEYS_ONLY},\
ProvisionedThroughput={ReadCapacityUnits=3000,WriteCapacityUnits=5000}"
```

Lệnh này sẽ tạo một bảng mới và một GSI với định nghĩa sau:

#### Bảng: logfile_scan

- Key schema: HASH
- Table RCU = 5000
- Table WCU = 5000
- GSI(s):
    - GSI_1 (3000 RCU, 5000 WCU) - _Cho phép quét song song hoặc tuần tự các nhật ký truy cập. Sắp xếp theo mã trạng thái và dấu thời gian._

|Tên thuộc tính (Loại)|Thuộc tính đặc biệt?|Trường hợp sử dụng thuộc tính|Giá trị mẫu của thuộc tính|
|---|---|---|---|
|PK (STRING)|Hash key|Lưu giữ request id cho nhật ký truy cập|_request#104009_|
|GSI_1_PK (STRING)|GSI 1 hash key|Khóa shard, với các giá trị từ 0-N, để cho phép tìm kiếm nhật ký|_shard#3_|
|GSI_1_SK (STRING)|GSI 1 sort key|Sắp xếp các nhật ký theo thứ bậc, từ mã trạng thái -> ngày -> giờ|_200#2019-09-21#01_|

Chạy lệnh để đợi cho đến khi bảng trở thành Active:

```bash
aws dynamodb wait table-exists --table-name logfile_scan
```

#### Nạp dữ liệu vào bảng

Chạy lệnh sau để tải dữ liệu nhật ký máy chủ vào bảng `logfile_scan`. Lệnh này sẽ nạp 1,000,000 dòng vào bảng.

```bash
nohup python load_logfile_parallel.py logfile_scan &
disown
```

`nohup` được sử dụng để chạy tiến trình trong nền, và `disown` cho phép tiến trình tải tiếp tục ngay cả khi bạn bị ngắt kết nối.

_Lệnh sau sẽ mất khoảng mười phút để hoàn thành. Nó sẽ chạy ở chế độ nền._

Chạy `pgrep -l python` để xác minh rằng script đang tải dữ liệu trong nền.

```bash
pgrep -l python
```

Kết quả:

```txt
3257 python
```

_Process id - số 4 chữ số trong ví dụ trên - sẽ khác nhau đối với mỗi người._

Script sẽ tiếp tục chạy trong nền trong khi bạn làm bài tập tiếp theo.

**Bạn đã hoàn thành phần THIẾT LẬP!**