---
title : "Bước 3 - Kiểm tra cài đặt boto3"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 3.1.4. </b> "
---

Boto3 là Bộ công cụ phát triển phần mềm (SDK) của Amazon Web Services (AWS) dành cho Python, cho phép các nhà phát triển Python xây dựng ứng dụng sử dụng các dịch vụ của AWS.

Trong cửa sổ shell của EC2, chạy `python` để mở một bảng điều khiển tương tác với lệnh đầu tiên, sau đó sao chép và dán đoạn mã Python sau:

```bash
# Mở Python:
python
```

```py
# Chạy đoạn mã này:
import boto3
ddb = boto3.client('dynamodb')
ddb.describe_limits()
```

Bạn sẽ thấy kết quả sau:

```txt
{u'TableMaxWriteCapacityUnits': 40000, u'TableMaxReadCapacityUnits': 40000, u'AccountMaxReadCapacityUnits': 80000, 'ResponseMetadata': {'RetryAttempts': 0, 'HTTPStatusCode': 200, 'RequestId': 'BFMGAS4P48I3DJTP5NU22QRDDJVV4KQNSO5AEMVJF66Q9ASUAAJG', 'HTTPHeaders': {'x-amzn-requestid': 'BFMGAS4P48I3DJTP5NU22QRDDJVV4KQNSO5AEMVJF66Q9ASUAAJG', 'content-length': '143', 'server': 'Server', 'connection': 'keep-alive', 'x-amz-crc32': '3062975651', 'date': 'Tue, 31 Dec 2020 00:00:00 GMT', 'content-type': 'application/x-amz-json-1.0'}}, u'AccountMaxWriteCapacityUnits': 80000}
```

Để đóng bảng điều khiển Python, gõ:

```py
quit()
```