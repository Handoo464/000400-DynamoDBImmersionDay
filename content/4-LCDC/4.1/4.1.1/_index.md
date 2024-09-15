---
title : "Start with Cloud9"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1.1. </b> "
---
## Create a Cloud9 Environment

To complete the steps in these labs, you need an IAM role that has the privileges to create, update and delete AWS Cloud9 environments, Lambda functions, DynamoDB tables, IAM roles, Kinesis Data Streams and DynamoDB Streams

- Log into the AWS Management Console, go to the AWS Cloud9 service dashboard then select **Create environment**.

![Create Cloud9 environment](/images/4/4.1/1.png)

- Give your new environment a name - **DynamoDB Labs** then provide an optional description for the environment.

![Name Cloud9 environment](/images/4/4.1/2.png)

- Select **t2.small** as your instance type, leave all other fields as the default values then select **Create**.

![Select Cloud9 instance](/images/4/4.1/3.png)

- Wait for creation of your Cloud9 environment to complete then select **Open** to launch your Cloud9 evironment.

![Launch Cloud9 environment](/images/4/4.1/4.png)

Start a command line terminal in Cloud9 and set up the `Region` and `Account ID` environment variables.

```bash
export REGION={your aws region} &&
export ACCOUNT_ID={your aws account ID}
```

Install jq on your AWS Cloud9 environment using the command below.

```bash
sudo yum install jq -y
```

{{%notice tip%}}
_After completing the workshop, remember to complete the [Clean Up](https://catalog.workshops.aws/dynamodb-labs/en-US/change-data-capture/clean-up) section to remove AWS resources that you no longer require._
{{%/notice%}}

Now that your environment is set up, continue on to the [2. Scenario Overview](https://catalog.workshops.aws/dynamodb-labs/en-US/change-data-capture/overview).
