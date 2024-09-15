---
title : "Tạo sparse GSI"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 7.4.2. </b> "
---

Dưới đây là bản dịch sang tiếng Việt, giữ nguyên các từ chuyên ngành tiếng Anh:

---

Trong bước này, bạn tạo chỉ mục thứ cấp toàn cục thưa (global secondary index - GSI) cho các game mở (những game chưa đầy người chơi).

Tạo GSI tương tự như tạo bảng. Trong mã mà bạn đã tải xuống, bạn sẽ tìm thấy tệp script trong thư mục **scripts/** có tên **add_secondary_index.py**.

```python
import boto3

dynamodb = boto3.client('dynamodb')

try:
    dynamodb.update_table(
        TableName='battle-royale',
        AttributeDefinitions=[
            {
                "AttributeName": "map",
                "AttributeType": "S"
            },
            {
                "AttributeName": "open_timestamp",
                "AttributeType": "S"
            }
        ],
        GlobalSecondaryIndexUpdates=[
            {
                "Create": {
                    "IndexName": "OpenGamesIndex",
                    "KeySchema": [
                        {
                            "AttributeName": "map",
                            "KeyType": "HASH"
                        },
                        {
                            "AttributeName": "open_timestamp",
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
    print("Bảng 'battle-royale' đã được cập nhật thành công.")
except Exception as e:
    print("Không thể cập nhật bảng. Lỗi:")
    print(e)
```

Thay đổi đơn vị dung lượng

Chỉnh sửa **scripts/add_secondary_index.py**, đặt cả `ReadCapacityUnits` và `WriteCapacityUnits` thành **100** cho `OpenGamesIndex`. Sau đó, lưu lại tệp.

Khi các thuộc tính được sử dụng trong khóa chính (primary key) cho bảng hoặc chỉ mục thứ cấp (secondary index), chúng phải được định nghĩa trong `AttributeDefinitions`. Sau đó, bạn `Create` một GSI mới trong thuộc tính `GlobalSecondaryIndexUpdates`. Đối với GSI này, bạn cần chỉ định tên chỉ mục, sơ đồ của khóa chính, thông lượng được cấp phát (provisioned throughput), và các thuộc tính mà bạn muốn đưa vào (projection).

Lưu ý rằng bạn không cần phải chỉ định rằng GSI được sử dụng như một chỉ mục thưa. Điều này chỉ phụ thuộc vào dữ liệu mà bạn đưa vào. Nếu bạn ghi các mục vào bảng mà không có các thuộc tính cho chỉ mục thứ cấp, chúng sẽ không được đưa vào chỉ mục thứ cấp của bạn.

Bạn có thể chọn chạy script Python `add_secondary_index.py` hoặc lệnh AWS CLI dưới đây. Cả hai đều được cung cấp để cho thấy các phương pháp khác nhau để tương tác với DynamoDB.

Tạo GSI của bạn bằng cách chạy lệnh sau:

```sh
python scripts/add_secondary_index.py
```

Bạn sẽ thấy thông báo sau trong bảng điều khiển:

```text
Bảng 'battle-royale' đã được cập nhật thành công.
```

Ngoài ra, bạn có thể tạo GSI bằng cách chạy lệnh AWS CLI dưới đây:

```sh
aws dynamodb update-table \
--table-name battle-royale \
--attribute-definitions AttributeName=map,AttributeType=S AttributeName=open_timestamp,AttributeType=S \
--global-secondary-index-updates \
"[
  {
    \"Create\": {
      \"IndexName\": \"OpenGamesIndex\",
      \"KeySchema\": [
        {
          \"AttributeName\": \"map\",
          \"KeyType\": \"HASH\"
        },
        {
          \"AttributeName\": \"open_timestamp\",
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

Nếu bạn chọn chạy lệnh AWS CLI, bạn sẽ thấy đầu ra như sau:  
Lưu ý rằng trạng thái của bảng sẽ hiển thị là **UPDATING** và trạng thái của chỉ mục sẽ hiển thị là **CREATING**

```text
{
  "TableDescription": {
    "AttributeDefinitions": [
      {
        "AttributeName": "PK",
        "AttributeType": "S"
      },
      {
        "AttributeName": "SK",
        "AttributeType": "S"
      },
      {
        "AttributeName": "map",
        "AttributeType": "S"
      },
      {
        "AttributeName": "open_timestamp",
        "AttributeType": "S"
      }
    ],
    "TableName": "battle-royale",
    "KeySchema": [
      {
        "AttributeName": "PK",
        "KeyType": "HASH"
      },
      {
        "AttributeName": "SK",
        "KeyType": "RANGE"
      }
    ],
    "TableStatus": "UPDATING",
    "CreationDateTime": "2023-12-06T14:48:31.246000-06:00",
    "ProvisionedThroughput": {
      "NumberOfDecreasesToday": 0,
      "ReadCapacityUnits": 100,
      "WriteCapacityUnits": 100
    },
    "TableSizeBytes": 0,
    "ItemCount": 0,
    "TableArn": "arn:aws:dynamodb:<AWS Region>:<Account ID>:table/battle-royale",
    "TableId": "<Unique ID>",
    "BillingModeSummary": {
      "BillingMode": "PROVISIONED",
      "LastUpdateToPayPerRequestDateTime": "2023-12-07T11:11:34.932000-06:00"
    },
    "GlobalSecondaryIndexes": [
      {
        "IndexName": "OpenGamesIndex",
        "KeySchema": [
          {
            "AttributeName": "map",
            "KeyType": "HASH"
          },
          {
            "AttributeName": "open_timestamp",
            "KeyType": "RANGE"
          }
        ],
        "Projection": {
          "ProjectionType": "ALL"
        },
        "IndexStatus": "CREATING",
        "Backfilling": false,
        "ProvisionedThroughput": {
          "NumberOfDecreasesToday": 0,
          "ReadCapacityUnits": 100,
          "WriteCapacityUnits": 100
        },
        "IndexSizeBytes": 0,
        "ItemCount": 0,
        "IndexArn": "arn:aws:dynamodb:<AWS Region>:<Account ID>:table/battle-royale/index/OpenGamesIndex"
      }
    ],
    "DeletionProtectionEnabled": false
  }
}
```

Sẽ mất vài phút để GSI mới được hoàn thiện. Bạn cần chờ cho đến khi GSI trở nên **active**. Bạn có thể kiểm tra trạng thái hiện tại của bảng và các chỉ mục của nó bằng cách:

- Kiểm tra dưới mục **Services**, **Database**, **DynamoDB** trong AWS console.
    
- Chạy lệnh sau trong Cloud9 Terminal:
    
    ```sh
    aws dynamodb describe-table --table-name battle-royale --query "Table.GlobalSecondaryIndexes[].IndexStatus"
    ```

    Bạn cũng có thể viết lệnh để chạy tự động mỗi 5 giây bằng cách sử dụng `watch`.
    
    ```bash
    # Watch kiểm tra mỗi 5 giây theo mặc định
    watch -n 5 "aws dynamodb describe-table --table-name battle-royale --query \"Table.GlobalSecondaryIndexes[].IndexStatus\""
    ```

    Nhấn **Ctrl + C** để dừng `watch` sau khi chỉ mục thứ cấp toàn cục đã được tạo.

---

Hy vọng bản dịch này giúp bạn dễ dàng hơn trong việc thực hiện quy trình trên.