---
title : "Chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 7.3.2. </b> "
---

Bây giờ khóa chính đã được thiết kế, hãy tạo một bảng.

Mã mà bạn đã tải xuống trong các bước đầu tiên bao gồm một script Python trong thư mục **scripts/** có tên là **create_table.py**. Nội dung của script Python như sau.

```py
import boto3

dynamodb = boto3.client('dynamodb')

try:
    dynamodb.create_table(
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
        KeySchema=[
            {
                "AttributeName": "PK",
                "KeyType": "HASH"
            },
            {
                "AttributeName": "SK",
                "KeyType": "RANGE"
            }
        ],
        ProvisionedThroughput={
            "ReadCapacityUnits": 1,
            "WriteCapacityUnits": 1
        }
    )
    print("Table 'battle-royale' created successfully.")
except Exception as e:
    print("Could not create table. Error:")
    print(e)
```

### Thay đổi đơn vị dung lượng

Chỉnh sửa **scripts/create_table.py**, đặt cả `ReadCapacityUnits` và `WriteCapacityUnits` thành **100** cho bảng _battle-royale_ và lưu tệp.

Script trên sử dụng thao tác **CreateTable** ([CreateTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateTable.html)) bằng cách sử dụng **Boto 3** ([Boto 3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)), AWS SDK cho Python. Thao tác này khai báo hai định nghĩa thuộc tính, đó là các thuộc tính được nhập để sử dụng trong khóa chính. Mặc dù DynamoDB là [không có schema (schemaless)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SQLtoNoSQL.CreateTable.html), bạn phải khai báo tên và loại của các thuộc tính được sử dụng cho khóa chính. Các thuộc tính này phải được bao gồm trong mọi mục được ghi vào bảng và do đó phải được chỉ định khi bạn tạo bảng.

Bởi vì các thực thể khác nhau được lưu trữ trong một bảng duy nhất, bạn không thể sử dụng các tên thuộc tính khóa chính như `UserId`. Thuộc tính này mang ý nghĩa khác nhau tùy thuộc vào loại thực thể được lưu trữ. Ví dụ, khóa chính cho một người dùng có thể là `USERNAME`, và khóa chính cho một trò chơi có thể là `GAMEID`. Do đó, bạn sử dụng các tên chung chung cho các thuộc tính, như `PK` (cho partition key) và `SK` (cho sort key).

Sau khi cấu hình các thuộc tính trong sơ đồ khóa, bạn chỉ định **throughput được cấp phát (provisioned throughput)** ([provisioned throughput](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html)) cho bảng. DynamoDB có hai chế độ dung lượng: **provisioned** (cấp phát) và **on-demand** (theo yêu cầu). Trong chế độ dung lượng cấp phát, bạn chỉ định chính xác lượng throughput đọc và ghi bạn muốn. Bạn phải trả tiền cho dung lượng này dù bạn có sử dụng hay không.

Trong chế độ dung lượng theo yêu cầu của DynamoDB, bạn trả tiền cho mỗi yêu cầu. Chi phí cho mỗi yêu cầu cao hơn một chút so với khi bạn sử dụng throughput cấp phát đầy đủ, nhưng bạn không phải mất thời gian lập kế hoạch dung lượng hoặc lo lắng về việc bị giới hạn. Chế độ theo yêu cầu hoạt động tốt cho các khối lượng công việc có đột biến hoặc không dự đoán được. Trong lab này, chế độ dung lượng cấp phát được sử dụng.

Bạn có thể chọn chạy script Python `create_table.py` hoặc lệnh AWS CLI dưới đây. Cả hai đều được cung cấp để hiển thị các phương pháp khác nhau để tương tác với DynamoDB.

Bạn có thể chạy script Python bằng lệnh sau trong Terminal của Cloud9.

```sh
python scripts/create_table.py
```

Script sẽ trả về thông báo này:

```text
Table 'battle-royale' created successfully.
```

Ngoài ra, bạn có thể chạy lệnh AWS CLI từ Terminal của Cloud9.

```sh
aws dynamodb create-table \
--table-name battle-royale \
--attribute-definitions AttributeName=PK,AttributeType=S AttributeName=SK,AttributeType=S \
--key-schema AttributeName=PK,KeyType=HASH AttributeName=SK,KeyType=RANGE \
--provisioned-throughput ReadCapacityUnits=100,WriteCapacityUnits=100
```

Lệnh CLI sẽ trả về JSON sau:

```json
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
    "TableStatus": "CREATING",
    "CreationDateTime": "2023-12-06T13:52:52.187000-06:00",
    "ProvisionedThroughput": {
      "NumberOfDecreasesToday": 0,
      "ReadCapacityUnits": 1,
      "WriteCapacityUnits": 1
    },
    "TableSizeBytes": 0,
    "ItemCount": 0,
    "TableArn": "arn:aws:dynamodb:<AWS Region>:<Account ID>:table/battle-royale",
    "TableId": "<Unique Identifier>",
    "DeletionProtectionEnabled": false
  }
}
```