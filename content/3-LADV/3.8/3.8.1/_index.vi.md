---
title : "Bước 1 - Tạo và tải bảng InvoiceandBilling"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.8.1. </b> "
---
Chạy lệnh AWS CLI để tạo bảng có tên và tạo GSI có tên được phân vùng theo thuộc tính của bảng mẹ:`InvoiceAndBilling``GSI_1``SK`

```bash
aws dynamodb create-table --table-name InvoiceAndBills \
--attribute-definitions AttributeName=PK,AttributeType=S AttributeName=SK,AttributeType=S \
--key-schema AttributeName=PK,KeyType=HASH AttributeName=SK,KeyType=RANGE \
--provisioned-throughput ReadCapacityUnits=100,WriteCapacityUnits=100 \
--tags Key=workshop-design-patterns,Value=targeted-for-cleanup \
--global-secondary-indexes "IndexName=GSI_1,\
KeySchema=[{AttributeName=SK,KeyType=HASH}],\
Projection={ProjectionType=ALL},\
ProvisionedThroughput={ReadCapacityUnits=100,WriteCapacityUnits=100}"
```

Đợi cho đến khi bảng hoạt động:

```bash
aws dynamodb wait table-exists --table-name InvoiceAndBills
```

Sau đó, tải dữ liệu vào bảng InvoiceAndBills:

```bash
python load_invoice.py InvoiceAndBills ./data/invoice-data.csv
```