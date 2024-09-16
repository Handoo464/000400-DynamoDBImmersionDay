---
title : "Bước 1 - Tạo bảng bản sao"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.9.1. </b> "
---
![Luồng DynamoDB với Lambda](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/image6.jpg)

Hãy tạo một bảng có tên để chứa các hàng được sao chép. Lệnh này dựa trên lệnh để tạo bảng. Kết quả là, nó tạo ra một bảng có thể chứa các mục giống như bảng ngược dòng của nó.`logfile_replica``create-table``logfile`

```bash
aws dynamodb create-table --table-name logfile_replica \
--attribute-definitions AttributeName=PK,AttributeType=S AttributeName=GSI_1_PK,AttributeType=S \
--key-schema AttributeName=PK,KeyType=HASH --provisioned-throughput ReadCapacityUnits=10,WriteCapacityUnits=5 \
--tags Key=workshop-design-patterns,Value=targeted-for-cleanup \
--global-secondary-indexes "IndexName=GSI_1,KeySchema=[{AttributeName=GSI_1_PK,KeyType=HASH}],\
Projection={ProjectionType=INCLUDE,NonKeyAttributes=['bytessent', 'requestid', 'host']},\
ProvisionedThroughput={ReadCapacityUnits=10,WriteCapacityUnits=5}"
```

Lệnh này tạo một bảng mới và một chỉ mục phụ toàn cục được liên kết với định nghĩa sau.

#### Bàn: `logfile_replica`


- Lược đồ khóa: HASH (khóa phân vùng)
- Đơn vị dung lượng đọc bảng (RCU) = 10
- Đơn vị dung lượng ghi bảng (WCU) = 5
- • Chỉ số phụ toàn cầu:
    - GSI_1 (10 RCU, 5 WCU) - Cho phép truy vấn theo địa chỉ IP máy chủ.

|Tên thuộc tính (loại)|Thuộc tính đặc biệt?|Trường hợp sử dụng thuộc tính|Giá trị thuộc tính mẫu|
|---|---|---|---|
|PK (CHUỖI)|Khóa phân vùng|Giữ id yêu cầu|`request#104009`|
|GSI_1_PK (CHUỖI)|Khóa phân vùng GSI 1|Chủ nhà|`host#66.249.67.3`|

Chạy lệnh sau để đợi cho đến khi bảng hoạt động.

```bash
aws dynamodb wait table-exists --table-name logfile_replica
```