---
title : "Tạo DynamoDB Tables"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 1.1.2. </b> "
---

Chúng ta bây giờ sẽ tạo các bảng (và trong bước tiếp theo, tải dữ liệu vào chúng) dựa trên [dữ liệu mẫu từ DynamoDB Developer Guide](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SampleData.html).

Sao chép các lệnh `create-table` bên dưới và dán chúng vào command prompt của AWS Cloud9, nhấn enter ở lệnh cuối cùng để thực thi nó. Sau đó, hãy sử dụng các lệnh chờ tương ứng bằng cách dán chúng vào cùng một terminal và chạy chúng.

```bash
aws dynamodb create-table \
    --table-name ProductCatalog \
    --attribute-definitions \
        AttributeName=Id,AttributeType=N \
    --key-schema \
        AttributeName=Id,KeyType=HASH \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5 \
    --query "TableDescription.TableStatus"
aws dynamodb create-table \
    --table-name Forum \
    --attribute-definitions \
        AttributeName=Name,AttributeType=S \
    --key-schema \
        AttributeName=Name,KeyType=HASH \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5 \
    --query "TableDescription.TableStatus"
aws dynamodb create-table \
    --table-name Thread \
    --attribute-definitions \
        AttributeName=ForumName,AttributeType=S \
        AttributeName=Subject,AttributeType=S \
    --key-schema \
        AttributeName=ForumName,KeyType=HASH \
        AttributeName=Subject,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5 \
    --query "TableDescription.TableStatus"
aws dynamodb create-table \
    --table-name Reply \
    --attribute-definitions \
        AttributeName=Id,AttributeType=S \
        AttributeName=ReplyDateTime,AttributeType=S \
    --key-schema \
        AttributeName=Id,KeyType=HASH \
        AttributeName=ReplyDateTime,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5 \
    --query "TableDescription.TableStatus"
```

Chạy các lệnh chờ. Khi tất cả chúng đều hoàn thành, bạn có thể tiếp tục:

```bash
aws dynamodb wait table-exists --table-name ProductCatalog
aws dynamodb wait table-exists --table-name Forum
aws dynamodb wait table-exists --table-name Thread
aws dynamodb wait table-exists --table-name Reply
```

