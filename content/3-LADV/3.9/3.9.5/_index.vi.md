---
title : "Bước 5 - Ánh xạ luồng nguồn tới hàm Lambda"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 3.9.5. </b> "
---

Đến nay, bạn đã có bảng nguồn với DynamoDB Streams được kích hoạt và hàm Lambda. Bây giờ bạn cần ánh xạ stream nguồn đến hàm Lambda. Bạn cần sao chép ARN từ bước trước và dán nó vào lệnh sau trước khi chạy lệnh.

```bash
aws lambda create-event-source-mapping \
--function-name ddbreplica_lambda --enabled --batch-size 100 --starting-position TRIM_HORIZON \
--event-source-arn YOUR_STREAM_ARN_HERE
```
{{%notice tip%}}
Bạn phải sao chép đầy đủ ARN của stream, bao gồm cả dấu thời gian ở cuối
{{%/notice%}}

Ví dụ:
```bash
aws lambda create-event-source-mapping \
--function-name ddbreplica_lambda --enabled --batch-size 100 --starting-position TRIM_HORIZON \
--event-source-arn arn:aws:dynamodb:<REGION>:<ACCOUNTID>:table/logfile/stream/2021-12-31T00:00:00.000
```

Kết quả dự kiến sẽ trông như sau:

```json
{
  "UUID": "0dcede66-709c-4073-a628-724d01b92095",
  "BatchSize": 100,
  "MaximumBatchingWindowInSeconds": 0,
  "ParallelizationFactor": 1,
  "EventSourceArn": "arn:aws:dynamodb:<REGION>:<ACCOUNTID>:table/logfile/stream/2021-12-31T00:00:00.000",
  "FunctionArn": "arn:aws:lambda:<REGION>:<ACCOUNTID>:function:ddbreplica_lambda",
  "LastModified": 1663286115.972,
  "LastProcessingResult": "No records processed",
  "State": "Creating",
  "StateTransitionReason": "User action",
  "DestinationConfig": {
    "OnFailure": {}
  },
  "MaximumRecordAgeInSeconds": -1,
  "BisectBatchOnFunctionError": false,
  "MaximumRetryAttempts": -1
}
```