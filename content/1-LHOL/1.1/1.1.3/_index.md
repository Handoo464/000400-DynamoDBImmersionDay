---
title : "Load Sample Data"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 1.1.3. </b> "
---
Execute the command `sudo su`

Download and unzip the sample data:

```bash
curl -O https://amazon-dynamodb-labs.com/static/hands-on-labs/sampledata.zip

unzip sampledata.zip
```

Load the sample data using the `batch-write-item` CLI:

```bash
aws dynamodb batch-write-item --request-items file://ProductCatalog.json

aws dynamodb batch-write-item --request-items file://Forum.json

aws dynamodb batch-write-item --request-items file://Thread.json

aws dynamodb batch-write-item --request-items file://Reply.json
```

After each data load, you will receive a message indicating that there are no unprocessed items.

```json
    {
        "UnprocessedItems": {}
    }
```

#### Sample output
![](/images/1/1.1/7.png)
![](/images/1/1.1/8.png)