---
title : "Bước 1 - Tạo GSI"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.4.1. </b> "
---

Chỉ mục phụ toàn cầu cho bài tập này đã được tạo trong giai đoạn thiết lập của workshop. Bạn có thể xem mô tả của chỉ mục phụ toàn cầu bằng cách thực thi lệnh AWS CLI sau:

```bash
aws dynamodb describe-table --table-name logfile_scan --query "Table.GlobalSecondaryIndexes"
```

Mô tả của các chỉ mục phụ toàn cầu sẽ trông như sau:

```json
{
  "GlobalSecondaryIndexes": [
    {
        "IndexName": "GSI_1",
        "KeySchema": [
            {
                "AttributeName": "GSI_1_PK",
                "KeyType": "HASH"
            },
            {
                "AttributeName": "GSI_1_SK",
                "KeyType": "RANGE"
            }
        ],
        "Projection": {
            "ProjectionType": "KEYS_ONLY"
        },
        "IndexStatus": "ACTIVE",
        "ProvisionedThroughput": {
            "NumberOfDecreasesToday": 0,
            "ReadCapacityUnits": 3000,
            "WriteCapacityUnits": 5000
        },
        "IndexSizeBytes": 0,
        "ItemCount": 0,
        "IndexArn": "arn:aws:dynamodb:(region):(accountid):table/logfile_scan/index/GSI_1"
    }
]
}
```

Trong ví dụ này, `ItemCount` của DynamoDB là 0. DynamoDB tính tổng số mục đã thấy trong API này nhiều lần trong ngày, và có thể bạn sẽ thấy một con số khác so với kết quả đầu ra ở trên. Điều này là bình thường.