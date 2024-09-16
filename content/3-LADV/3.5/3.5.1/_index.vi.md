---
title : "Bước 1 - Tạo bảng employees cho global secondary index key quá tải"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.5.1. </b> "
---
Chạy lệnh AWS CLI sau để tạo bảng.`employees`

```bash
aws dynamodb create-table --table-name employees \
--attribute-definitions AttributeName=PK,AttributeType=S AttributeName=SK,AttributeType=S \
AttributeName=GSI_1_PK,AttributeType=S AttributeName=GSI_1_SK,AttributeType=S \
--key-schema AttributeName=PK,KeyType=HASH AttributeName=SK,KeyType=RANGE \
--provisioned-throughput ReadCapacityUnits=100,WriteCapacityUnits=100 \
--tags Key=workshop-design-patterns,Value=targeted-for-cleanup \
--global-secondary-indexes "IndexName=GSI_1,\
KeySchema=[{AttributeName=GSI_1_PK,KeyType=HASH},{AttributeName=GSI_1_SK,KeyType=RANGE}],\
Projection={ProjectionType=ALL},\
ProvisionedThroughput={ReadCapacityUnits=100,WriteCapacityUnits=100}"
```

Chạy lệnh sau để đợi cho đến khi bảng hoạt động.

```bash
aws dynamodb wait table-exists --table-name employees
```

Hãy xem xét kỹ hơn lệnh `create-table`. Bạn đang tạo một bảng có tên là `employees`. Khóa phân vùng trên bảng là `PK` và nó chứa ID của nhân viên. Khóa sắp xếp là `SK`, chứa một giá trị được tạo ra mà bạn chọn trong script Python. (Chúng ta sẽ quay lại điểm này trong chốc lát.) Bạn sẽ tạo một chỉ mục phụ toàn cầu trên bảng này và đặt tên là `GSI_1`; Đây sẽ là một chỉ mục phụ toàn cầu được nạp chồng. Khóa phân vùng trên chỉ mục phụ toàn cầu là `GSI_1_PK`, và chứa cùng giá trị với khóa sắp xếp `SK` trên bảng cơ sở. Giá trị khóa sắp xếp `GSI_1` là `name`, và tên thuộc tính của nó là `GSI_1_SK`.

#### Bảng: `employees`

- Sơ đồ khóa: HASH, RANGE (khóa phân vùng và khóa sắp xếp)
- Số đơn vị dung lượng đọc (RCUs) của bảng = 100
- Số đơn vị dung lượng ghi (WCUs) của bảng = 100
- Chỉ mục phụ toàn cầu:
  - GSI_1 (100 RCUs, 100 WCUs) - Cho phép truy vấn theo địa chỉ IP của host.

|Tên thuộc tính (Loại)|Thuộc tính đặc biệt?|Trường hợp sử dụng thuộc tính|Giá trị mẫu của thuộc tính|
|---|---|---|---|
|PK (STRING)|Khóa phân vùng|ID nhân viên|`e#129`|
|SK (STRING)|Khóa sắp xếp|Giá trị được tạo ra|`root`, `state#MI`|
|GSI_1_PK (STRING)|Khóa phân vùng GSI_1|Giá trị được tạo ra|`root`, `state#MI`|
|GSI_1_SK (STRING)|Khóa sắp xếp GSI_1|Tên nhân viên|`Christine Milsted`|