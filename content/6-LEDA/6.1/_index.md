---
title : "Getting Started"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.1. </b> "
---

{{%notice tip%}}
Only complete this section if you are running the workshop on your own. If you are at an AWS hosted event (such as re:Invent, Immersion Day, etc), go to [At an AWS hosted Event](https://catalog.workshops.aws/dynamodb-labs/en-US/event-driven-architecture/setup/start-here/aws-ws-event)
{{%/notice%}}

## Launch the CloudFormation stack

{{%notice warning%}}
During the course of the lab, you will make DynamoDB tables that will incur a cost that could approach tens or hundreds of dollars per day. Ensure you delete the DynamoDB tables using the DynamoDB console, and make sure you delete the CloudFormation stack as soon as the lab is complete.
{{%/notice%}}

1. Launch the CloudFormation template in US West 2 to deploy the resources in your account: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=amazon-dynamodb-labs&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/event-driven-cfn.yaml)

    1. _Optionally, download [the YAML template](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/event-driven-cfn.yaml)  and launch it your own way_
2. Click _Next_ on the first dialog.
    
3. Scroll to the bottom and click _Next_, and then review the _Template_. When you are ready to create the stack, scroll to the bottom, check the box acknowledging the creation of IAM resources, and click _Create stack_. ![CloudFormation parameters](/images/6/6.1/1.png) The stack will create DynamoDB tables, Lambda functions, Kinesis streams, and IAM roles and policies which will be used later on in the lab.
    
4. After the CloudFormation stack is `CREATE_COMPLETE`.