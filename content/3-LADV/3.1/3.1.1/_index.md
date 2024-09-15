---
title : "Getting Started"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1.1. </b> "
---

{{%notice warning%}}
These setup instructions are identitical for LADV, LHOL, LMR, LBED, and LGME - all of which use the same Cloud9 template. Only complete this section once, and only if you're running it on your own account.
{{%/notice%}}

{{%notice tip%}}
Only complete this section if you are running the workshop on your own. If you are at an AWS hosted event (such as re:Invent, Immersion Day, etc), go to [At an AWS hosted Event](https://catalog.workshops.aws/dynamodb-labs/en-US/design-patterns/setup/aws-ws-event)
{{%/notice%}}

## Launch the CloudFormation stack

{{%notice tip%}}
During the course of the lab, you will make DynamoDB tables that will incur a cost that could approach tens or hundreds of dollars per day. Ensure you delete the DynamoDB tables using the DynamoDB console, and make sure you delete the CloudFormation stack as soon as the lab is complete.
{{%/notice%}}

1. Launch the CloudFormation template in US West 2 to deploy the resources in your account: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBID&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)
    
    1. _Optionally, download [the YAML template](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)  and launch it your own way_
2. Click _Next_ on the first dialog.
    
3. In the Parameters section, note the _Timeout_ is set to zero. This means the Cloud9 instance will not sleep; you may want to change this manually to a value such as 60 to protect against unexpected charges if you forget to delete the stack at the end.  
    Leave the _WorkshopZIP_ parameter unchanged and click _Next_ ![CloudFormation parameters](/images/3/3.1/1.png)
    
4. Scroll to the bottom and click _Next_, and then review the _Template_ and _Parameters_. When you are ready to create the stack, scroll to the bottom, check the box acknowledging the creation of IAM resources, and click _Create stack_. ![CloudFormation parameters](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/awsconsole2.png) The stack will create a Cloud9 lab instance, a role for the instance, and a role for the AWS Lambda function used later on in the lab. It will use Systems Manager to configure the Cloud9 instance.
    
5. After the CloudFormation stack is `CREATE_COMPLETE`, [continue onto Step 1](https://catalog.workshops.aws/dynamodb-labs/en-US/design-patterns/setup/Step1).