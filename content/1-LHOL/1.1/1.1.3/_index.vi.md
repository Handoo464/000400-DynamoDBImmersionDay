---
title : "Tải Data mẫu"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 1.1.3. </b> "
---
Thực hiện lệnh `sudo su`

Download và unzip data mẫu:

```bash
curl -O https://amazon-dynamodb-labs.com/static/hands-on-labs/sampledata.zip

unzip sampledata.zip
```

Load data mẫu sử dụng the `batch-write-item` CLI:

```bash
aws dynamodb batch-write-item --request-items file://ProductCatalog.json

aws dynamodb batch-write-item --request-items file://Forum.json

aws dynamodb batch-write-item --request-items file://Thread.json

aws dynamodb batch-write-item --request-items file://Reply.json
```

Sau mỗi lần tải dữ liệu, bạn sẽ nhận được thông báo này cho biết không có mục nào chưa được xử lý.
```json
    {
        "UnprocessedItems": {}
    }
```

#### Ví dụ output
![](/images/1/7.png)
![](/images/1/8.png)