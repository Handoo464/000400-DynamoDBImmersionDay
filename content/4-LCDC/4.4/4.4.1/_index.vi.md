---
title : "Bật Kinesis Data Streams"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 4.4.1. </b> "
---

Tắt DynamoDB streams cho bảng Orders bằng cách sử dụng các lệnh AWS CLI dưới đây.

```bash
aws dynamodb update-table \
    --table-name Orders \
    --stream-specification \
        StreamEnabled=false \
    --query "Table.StreamSpecification.StreamEnabled"
```

Xác nhận rằng DynamoDB streams đã bị vô hiệu hóa bằng cách sử dụng các lệnh AWS CLI dưới đây.

```bash
aws dynamodb describe-table \
    --table-name Orders \
    --query "Table.StreamSpecification.StreamEnabled"
```

Kết quả đầu ra sẽ trả về một giá trị boolean như dưới đây.

```
null
```

Tạo một luồng dữ liệu Kinesis có tên **Orders** bằng cách sử dụng lệnh sau.

```bash
aws kinesis create-stream --stream-name Orders --shard-count 2
```

Xác nhận rằng luồng đang hoạt động bằng cách sử dụng lệnh sau.

```bash
aws kinesis describe-stream \
    --stream-name Orders \
    --query "StreamDescription.[StreamStatus, StreamARN]"
```

Ví dụ về kết quả đầu ra:

```
[
    "ACTIVE",
    "arn:aws:kinesis:${REGION}:${ACCOUNT_ID}:stream/Orders"
]
```

Kích hoạt streaming Kinesis cho bảng DynamoDB Orders bằng cách sử dụng lệnh sau. Sao chép ARN từ lệnh trước đó vào tham số _--stream-arn_.

```bash
aws dynamodb enable-kinesis-streaming-destination \
    --table-name Orders \
    --stream-arn arn:aws:kinesis:${REGION}:${ACCOUNT_ID}:stream/Orders
```

Ví dụ về kết quả đầu ra:

```
{
    "TableName": "Orders",
    "StreamArn": "arn:aws:kinesis:${REGION}:${ACCOUNT_ID}:stream/Orders",
    "DestinationStatus": "ENABLING",
    "EnableKinesisStreamingConfiguration": {}
}
```

Xác nhận rằng streaming Kinesis đang hoạt động trên bảng Orders bằng cách sử dụng lệnh sau.

```bash
aws dynamodb describe-kinesis-streaming-destination \
    --table-name Orders \
    --query "KinesisDataStreamDestinations[0].DestinationStatus"
```

Ví dụ về kết quả đầu ra sẽ bao gồm tên bảng, ARN của luồng dữ liệu Kinesis và trạng thái streaming.

```
"ACTIVE"
```