---
title : "Update IAM Role"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4.3.4. </b> "
---

Configure your lambda function to copy changed records from the Orders DynamoDB streams to the OrdersHistory table by doing the following.

1. Go to the IAM dashboard on the AWS Management Console and inspect the IAM policy, i.e. **AWSLambdaMicroserviceExecutionRole...**, created when you created the create-order-history-ddbs lambda function.

![AWS Lambda function console](/images/4/4.3/5.png)

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:DeleteItem",
                "dynamodb:GetItem",
                "dynamodb:PutItem",
                "dynamodb:Scan",
                "dynamodb:UpdateItem"
            ],
            "Resource": "arn:aws:dynamodb:{aws-region}:{aws-account-id}:table/*"
        }
    ]
}
```

2. Update the policy statement by editing and replacing the existing policy using the following IAM policy statement.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:DescribeStream",
                "dynamodb:GetRecords",
                "dynamodb:GetShardIterator",
                "dynamodb:ListStreams"
            ],
            "Resource": "arn:aws:dynamodb:{aws-region}:{aws-account-id}:table/Orders/stream/*"
        },
        {
            "Effect": "Allow",
            "Action": "dynamodb:PutItem",
            "Resource": "arn:aws:dynamodb:{aws-region}:{aws-account-id}:table/OrdersHistory"
        },
        {
            "Effect": "Allow",
            "Action": "sqs:SendMessage",
            "Resource": "arn:aws:sqs:{aws-region}:{aws-account-id}:orders-ddbs-dlq"
        }
    ]
}
```

The updated IAM policy gives the create-order-history-ddbs lambda function the permissions required to read events from the Orders DynamoDB stream, write new items to the OrdersHistory DynamoDB table and send messages to the orders-ddbs-dlq SQS queue.

{{%notice tip%}}
Replace **{aws-region}** and **{aws-account-id}** in the policy statement above with the correct value for your AWS region and your AWS account ID.
{{%/notice%}}


3. Go to the lambda function console editor. Select **Layers** then select **Add a Layer**.

![AWS Lambda function console](/images/4/4.3/6.png)

![AWS Lambda function console](/images/4/4.3/7.png)

4. Select **Specify an ARN**, enter the Lambda Layer ARN below.

```bash
1
arn:aws:lambda:{aws-region}:017000801446:layer:AWSLambdaPowertoolsPythonV2:58
```

5. Click **Verify** then select **Add**.

![AWS Lambda function console](/images/4/4.3/8.png)

Replace {aws-region} with ID for the AWS region that you are currently working on.

6. Go to the configuration section of the lambda console editor. Select **Environment variables** then select **Edit**.

![AWS Lambda function console](/images/4/4.3/9.png)

7. Add a new environment variable called **ORDERS_HISTORY_DB** and set its value to **OrdersHistory**.

![AWS Lambda function console](/images/4/4.3/10.png)

8. Select **Triggers** then select **Add trigger**.

![AWS Lambda function console](/images/4/4.3/11.png)

9. Select **DynamoDB** as the trigger source.
10. Select the **Orders** DynamoDB table.
11. Set the **Batch size** to **10** and leave all other values unchanged.

![AWS Lambda function console](/images/4/4.3/12.png)

12. Click **Additional settings** to expand the section.
13. Provide the ARN of the **orders-ddbs-dlq** SQS queue you created earlier.

```bash
arn:aws:sqs:{aws-region}:{aws-account-id}:orders-ddbs-dlq
```

14. Set the Retry attempts to 3.

![AWS Lambda function console](/images/4/4.3/13.png)

15. Select **Add**.