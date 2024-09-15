---
title : "Bước 1 - Tạo DynamoDB table"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.2.1. </b> "
---
Chạy lệnh AWS CLI sau để tạo bảng DynamoDB đầu tiên có tên `logfile`:

```bash
aws dynamodb create-table --table-name logfile \
--attribute-definitions AttributeName=PK,AttributeType=S AttributeName=GSI_1_PK,AttributeType=S \
--key-schema AttributeName=PK,KeyType=HASH \
--provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5 \
--tags Key=workshop-design-patterns,Value=targeted-for-cleanup \
--global-secondary-indexes "IndexName=GSI_1,\
KeySchema=[{AttributeName=GSI_1_PK,KeyType=HASH}],\
Projection={ProjectionType=INCLUDE,NonKeyAttributes=['bytessent']},\
ProvisionedThroughput={ReadCapacityUnits=5,WriteCapacityUnits=5}"
```

Bảng bạn vừa tạo sẽ có cấu trúc như sau.

#### Bàn:`logfile`

- Lược đồ khóa: HASH (khóa phân vùng)
- Đơn vị dung lượng đọc bảng (RCU) = 5
- Đơn vị dung lượng ghi bảng (WCU) = 5
- Chỉ số phụ toàn cầu (GSI):
    - GSI_1 (5 RCU, 5 WCU) - Cho phép truy vấn theo địa chỉ IP máy chủ.

| Tên thuộc tính (loại) | Thuộc tính đặc biệt? | Trường hợp sử dụng thuộc tính             | Giá trị thuộc tính mẫu |
| --------------------- | -------------------- | ----------------------------------------- | ---------------------- |
| PK (CHUỖI)            | Khóa phân vùng       | Giữ id yêu cầu cho nhật ký truy cập       | _Yêu cầu#104009_       |
| GSI_1_PK (CHUỖI)      | Khóa phân vùng GSI 1 | Máy chủ lưu trữ cho yêu cầu, địa chỉ IPv4 | _Máy chủ#66.249.67.3_  |

Các thuộc tính đặc biệt bao gồm các thuộc tính được đặt tên để xác định khóa chính của một bảng DynamoDB hoặc một chỉ mục phụ toàn cầu (GSI). Chỉ mục phụ toàn cầu có các khóa chính giống như các bảng DynamoDB. Trong DynamoDB, partition key (khóa phân vùng) giống như hash key (khóa băm), và sort key (khóa sắp xếp) giống như range key (khóa phạm vi). Các API của DynamoDB sử dụng thuật ngữ hash và range, trong khi tài liệu của AWS sử dụng thuật ngữ partition và range. Dù bạn sử dụng thuật ngữ nào, hai khóa này cùng nhau tạo thành khóa chính. Để tìm hiểu thêm, vui lòng xem hướng dẫn cho nhà phát triển AWS của chúng tôi về [phần khóa chính trong Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html#HowItWorks.CoreComponents.PrimaryKey).

Chạy lệnh AWS CLI sau để đợi cho đến khi bảng chuyển sang trạng thái `ACTIVE`.

```bash
aws dynamodb wait table-exists --table-name logfile
```

Bạn cũng có thể chạy lệnh AWS CLI sau để chỉ nhận trạng thái bảng.

```bash
aws dynamodb describe-table --table-name logfile --query "Table.TableStatus"
```