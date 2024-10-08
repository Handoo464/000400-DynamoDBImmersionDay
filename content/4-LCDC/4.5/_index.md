---
title : "Summary and Clean Up"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 4.5. </b> "
---

Congratulations! You have made it to the end of the workshop.

In this workshop you explored capturing item level changes on a DynamoDB table using DynamoDB Streams and Kinesis Data Streams. In this instance, you wrote the previous version of updated items to a different DynamoDB table. By applying these same techniques, you can build complex event driven solutions that are triggered by changes to items you have stored on DynamoDB.

If you used an account provided by Workshop Studio, you do not need to do any cleanup. The account terminates when the event is over.

If you used your own account, please remove the following resources:

- The Lambda Function Event Source Mappings:

```bash
UUID_1=`aws lambda list-event-source-mappings --function-name create-order-history-kds --query 'EventSourceMappings[].UUID' --output text`
UUID_2=`aws lambda list-event-source-mappings --function-name create-order-history-ddbs --query 'EventSourceMappings[].UUID' --output text`
aws lambda delete-event-source-mapping --uuid ${UUID_1}
aws lambda delete-event-source-mapping --uuid ${UUID_2}
```

- The AWS Lambda functions created during the labs:

```bash
aws lambda delete-function --function-name create-order-history-ddbs
aws lambda delete-function --function-name create-order-history-kds
```

- The AWS Kinesis data stream created during the labs:

```bash
aws kinesis delete-stream --stream-name Orders
```

- The Amazon DynamoDB tables created in the Getting Started section of the lab:

```bash
aws dynamodb delete-table --table-name Orders
aws dynamodb delete-table --table-name OrdersHistory
```

- The Amazon SQS queues created during the labs:

```bash
aws sqs delete-queue --queue-url https://sqs.${REGION}.amazonaws.com/${ACCOUNT_ID}/orders-ddbs-dlq
aws sqs delete-queue --queue-url https://sqs.${REGION}.amazonaws.com/${ACCOUNT_ID}/orders-kds-dlq
```

- The IAM policies attached to the IAM execution roles you created:

![Delete IAM Policies](/images/4/4.5/1.png)

![Delete IAM Policies](/images/4/4.5/2.png)

- The AWS IAM execution roles created for the lambda functions:

![Delete IAM Roles](/images/4/4.5/3.png)

This should wrap up the cleanup process.