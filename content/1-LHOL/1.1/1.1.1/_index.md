---
title : "Prerequisites and Start"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.1.1. </b> "
---
## Prerequisites 
To run this lab, you'll need an AWS account, and a user identity with access to the following services:

- Amazon DynamoDB
- AWS Cloud9 Environment

You can use your own account, or an account provided through Workshop Studio Event Delivery as part of an AWS organized workshop. Using an account provided by Workshop Studio is the easier path, as you will have full access to all AWS services, and the account will terminate automatically when the event is over.

#### Account setup
#### Using an account provided to you by your lab instructor


If you are running this workshop using a link provided to you by your AWS instructor, please use that link and enter the access-code provided to you as part of the workshop. In the lab AWS account, the Cloud9 instance should already be provisioned. Please open the "AWS Cloud9" section of the AWS Management Console in the correct region and look for a lab instance called **DynamoDBC9**.

#### Using your own AWS account 

If you are using your own AWS account, be sure you have access to create and manage resources in Amazon DynamoDB and AWS Cloud9 environment

_After completing the workshop, remember to complete the [cleanup](https://catalog.workshops.aws/dynamodb-labs/en-US/hands-on-labs/cleanup.html) section to remove any unnecessary AWS resources._


## Launch the CloudFormation stack
{{% notice warning %}}
During the course of the lab, you will make DynamoDB tables that will incur a cost that could approach tens or hundreds of dollars per day. Ensure you delete the DynamoDB tables using the DynamoDB console, and make sure you delete the CloudFormation stack as soon as the lab is complete
{{% /notice %}}

1. Launch the CloudFormation template in US West 2 to deploy the resources in your account: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBID&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)
 ![CloudFormation](/images/1/1.1/1.png)  
    1. *Optionally, download [the YAML template](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml) and launch it your own way*

1. Click *Next* on the first dialog.

1. In the Parameters section, note the *Timeout* is set to zero. This means the Cloud9 instance will not sleep; you may want to change this manually to a value such as 60 to protect against unexpected charges if you forget to delete the stack at the end.  
    Leave the *WorkshopZIP* parameter unchanged and click *Next*. ![](/images/1/1.1/2.png)


1. Scroll to the bottom and click *Next*, and then review the *Template* and *Parameters*. When you are ready to create the stack, scroll to the bottom, check the box acknowledging the creation of IAM resources, and click *Create stack*.
![CloudFormation parameters](/images/1/1.1/3.png)
  The stack will create a Cloud9 lab instance, a role for the instance, and a role for the AWS Lambda function used later on in the lab. It will use Systems Manager to configure the Cloud9 instance.

1. After the CloudFormation stack is `CREATE_COMPLETE`, continue to the next step.

1. Access EC2 to search for the Instance that was created from the stack.
   ![](/images/1/1.1/4.png)
1. Execute "Connect to instance."
   ![](/images/1/1.1/5.png)
1. Run the command `aws sts get-caller-identity` just to verify that your AWS credentials have been configured correctly.
![](/images/1/1.1/6.png)