---
title : "Thêm chỉ mục đảo ngược"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 7.6.1. </b> "
---

Trong bước này, bạn thêm một chỉ mục đảo ngược vào bảng. Chỉ số đảo ngược được tạo giống như bất kỳ chỉ số phụ toàn cầu (GSI) nào khác.

Trong mã bạn đã tải xuống, một tập lệnh **add_inverted_index.py** nằm trong thư mục **scripts/**. Tập lệnh Python này thêm một chỉ mục đảo ngược vào bảng của bạn.

```python
import boto3

dynamodb = boto3.client('dynamodb')

try:
    dynamodb.update_table(
        TableName='battle-royale',
        AttributeDefinitions=[
            {
                "AttributeName": "PK",
                "AttributeType": "S"
            },
            {
                "AttributeName": "SK",
                "AttributeType": "S"
            }
        ],
        GlobalSecondaryIndexUpdates=[
            {
                "Create": {
                    "IndexName": "InvertedIndex",
                    "KeySchema": [
                        {
                            "AttributeName": "SK",
                            "KeyType": "HASH"
                        },
                        {
                            "AttributeName": "PK",
                            "KeyType": "RANGE"
                        }
                    ],
                    "Projection": {
                        "ProjectionType": "ALL"
                    },
                    "ProvisionedThroughput": {
                        "ReadCapacityUnits": 1,
                        "WriteCapacityUnits": 1
                    }
                }
            }
        ],
    )
    print("Table 'battle-royale' updated successfully.")
except Exception as e:
    print("Could not update table. Error:")
    print(e)
```

Chỉnh sửa **tập lệnh / add_inverted_index.py**, đặt cả hai và **thành 100** cho .`ReadCapacityUnits``WriteCapacityUnits``InvertedIndex`

Trong tập lệnh này, bạn gọi một phương thức trên máy khách DynamoDB. Trong phương pháp này, bạn chuyển thông tin chi tiết về chỉ mục phụ mà bạn muốn tạo, bao gồm lược đồ khóa cho chỉ mục, thông lượng được cung cấp và các thuộc tính để chiếu vào chỉ mục.`update_table()`

Bạn có thể chọn chạy tập lệnh python hoặc lệnh AWS CLI bên dưới. Cả hai đều được cung cấp để hiển thị các phương pháp tương tác khác nhau với DynamoDB.`add_inverted_index.py`

Chạy tập lệnh bằng cách nhập lệnh sau vào thiết bị đầu cuối của bạn:

```sh
python scripts/add_inverted_index.py
```

Thiết bị đầu cuối của bạn sẽ hiển thị kết quả cho thấy chỉ mục của bạn đã được tạo thành công.

```text
Table 'battle-royale' updated successfully.
```

Ngoài ra, bạn có thể tạo GSI bằng cách chạy lệnh AWS CLI bên dưới:`InvertedIndex`

```sh
aws dynamodb update-table \
--table-name battle-royale \
--attribute-definitions AttributeName=PK,AttributeType=S AttributeName=SK,AttributeType=S \
--global-secondary-index-updates \
"[
  {
    \"Create\": {
      \"IndexName\": \"InvertedIndex\",
      \"KeySchema\": [
        {
          \"AttributeName\": \"SK\",
          \"KeyType\": \"HASH\"
        },
        {
          \"AttributeName\": \"PK\",
          \"KeyType\": \"RANGE\"
        }
      ],
      \"Projection\": {
        \"ProjectionType\": \"ALL\"
      },
      \"ProvisionedThroughput\": {
        \"ReadCapacityUnits\": 100,
        \"WriteCapacityUnits\": 100
      }
    }
  }
]"
```

Nếu bạn chọn chạy lệnh AWS CLI, đầu ra sẽ chứa mô tả đầy đủ về bảng bao gồm các chỉ mục hiện có và mới tạo. Bạn sẽ nhận thấy IndexStatus cho chỉ mục sẽ hiển thị dưới dạng **CREATING.**`battle-royale``InvertedIndex`

Sẽ mất vài phút để chỉ số phụ mới được điền vào. Bạn cần đợi cho đến khi chỉ mục phụ hoạt động.

Bạn có thể tìm hiểu trạng thái hiện tại của bảng và các chỉ mục của nó bằng một trong hai cách:

- Kiểm tra trong **Dịch vụ**, **Cơ sở dữ liệu**, **DynamoDB** trong bảng điều khiển AWS.
    
- Chạy lệnh bên dưới trong Thiết bị đầu cuối Cloud9:
    
    ```sh
    aws dynamodb describe-table --table-name battle-royale --query "Table.GlobalSecondaryIndexes[].IndexStatus"
    ```
    
    Bạn cũng có thể viết kịch bản lệnh để chạy cứ sau 5 giây bằng cách sử dụng .`watch`
    
    ```bash
    # Watch checks every 5 seconds by default
    watch -n 5 "aws dynamodb describe-table --table-name battle-royale --query \"Table.GlobalSecondaryIndexes[].IndexStatus\""
    ```
    
    Nhấn **Ctrl + C** để kết thúc sau khi chỉ mục phụ toàn cục đã được tạo.`watch`
