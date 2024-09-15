---
title : "Tạo bảng DynamoDB"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 4.2.1. </b> "
---

Trong phần này, bạn sẽ tạo các bảng DynamoDB mà bạn sẽ sử dụng trong các bài lab cho buổi workshop này.

Trong các lệnh dưới đây, lệnh AWS CLI **create-table** được sử dụng để tạo hai bảng mới tên là Orders và OrdersHistory.

Lệnh này sẽ tạo bảng Orders với chế độ dung lượng được cung cấp (provisioned capacity mode) có 5 đơn vị dung lượng đọc (RCU), 5 đơn vị dung lượng ghi (WCU) và một khóa phân vùng tên là `id`.

Nó cũng sẽ tạo bảng OrdersHistory với chế độ dung lượng được cung cấp (provisioned capacity mode) có 5 RCU, 5 WCU, một khóa phân vùng tên là `pk` và một khóa sắp xếp tên là `sk`.

- Sao chép các lệnh **create-table** dưới đây và dán chúng vào terminal của bạn.
- Thực thi các lệnh để tạo hai bảng tên là Orders và OrdersHistory.

```bash
aws dynamodb create-table \
    --table-name Orders \
    --attribute-definitions \
        AttributeName=id,AttributeType=S \
    --key-schema \
        AttributeName=id,KeyType=HASH \
    --provisioned-throughput \
        ReadCapacityUnits=5,WriteCapacityUnits=5 \
    --query "TableDescription.TableStatus"

aws dynamodb create-table \
    --table-name OrdersHistory \
    --attribute-definitions \
        AttributeName=pk,AttributeType=S \
        AttributeName=sk,AttributeType=S \
    --key-schema \
        AttributeName=pk,KeyType=HASH \
        AttributeName=sk,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=5,WriteCapacityUnits=5 \
    --query "TableDescription.TableStatus"
```

Chạy lệnh dưới đây để xác nhận rằng cả hai bảng đã được tạo.

```bash
aws dynamodb wait table-exists --table-name Orders
aws dynamodb wait table-exists --table-name OrdersHistory
```