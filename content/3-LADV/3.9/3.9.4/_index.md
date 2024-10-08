---
title : "Step 4 - Enable DynamoDB Streams"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 3.9.4. </b> "
---
Now that we have an AWS Lambda function created to process the DynamoDB Streams records, we need to enable the DynamoDB Stream on the `logfile` table. In the following step we will connect the stream with the function.

We will enable the stream on the `logfile` table. When a stream is enabled you can choose whether DynamoDB copies the new item, or the old item, or both old and new items, or just the partition and sort keys of an item that has been created, updated, or deleted. For more information on the different options [you can review the documentation on `StreamSpecification`](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_StreamSpecification.html) , which lists the options as follows: _NEW_IMAGE_, _OLD_IMAGE_, _NEW_AND_OLD_IMAGES_, or _KEYS_ONLY_.

Enable DynamoDB Streams for the `logfile` table with the _NEW_IMAGE_, which includes "The entire item, as it appears after it was modified, is written to the stream." according to the documentation linked above.

```bash
aws dynamodb update-table --table-name 'logfile' --stream-specification StreamEnabled=true,StreamViewType=NEW_IMAGE
```

Get the full ARN for DynamoDB Streams in the response. We will need this for the next step.

```bash
aws dynamodb describe-table --table-name 'logfile' --query 'Table.LatestStreamArn' --output text
```

The output will look like the following

```
arn:aws:dynamodb:<REGION>:<ACCOUNTID>:table/logfile/stream/2018-10-27T02:15:46.245
```

Note the ARN for use in the next step.