---
title : "Getting Started"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---
{{%notice warning%}}
The first half of these setup instructions are identitical for LADV, LHOL, LMR, LBED, and LGME - all of which use the same Cloud9 template. Only complete this section once, and only if you're running it on your own account. If you have already launched the Cloud9 stack in a different lab, skip to the **Launch the zETL CloudFormation stack** section
{{%/notice%}}

{{%notice tip%}}
Only complete this section if you are running the workshop on your own. If you are at an AWS hosted event (such as re:Invent, Immersion Day, etc), go to [At an AWS hosted Event](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/aws-ws-event)
{{%/notice%}}

## Launch the Cloud9 CloudFormation stack

{{%notice tip%}}
During the course of the lab, you will create resources that will incur a cost that could approach tens of dollars per day. Ensure you delete the CloudFormation stack as soon as the lab is complete and verify all resources are deleted.
{{%/notice%}}


1. Launch the CloudFormation template in US West 2 to deploy the resources in your account: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBID&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)  
    _Optionally, download [the YAML template](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)  and launch it your own way_
    
2. Click _Next_ on the first dialog.
    
3. In the Parameters section, note the _Timeout_ is set to zero. This means the Cloud9 instance will not sleep; you may want to change this manually to a value such as 60 to protect against unexpected charges if you forget to delete the stack at the end.  
    Leave the _WorkshopZIP_ parameter unchanged and click _Next_
    

![CloudFormation parameters](/images/2/2.1/2.png)

1. Scroll to the bottom and click _Next_, and then review the _Template_ and _Parameters_. When you are ready to create the stack, scroll to the bottom, check the box acknowledging the creation of IAM resources, and click _Submit_.

![Acknowledge IAM role capabilities](/images/2/2.1/3.png) The stack will create a Cloud9 lab instance, a role for the instance, and a role for the AWS Lambda function used later on in the lab. It will use Systems Manager to configure the Cloud9 instance.

1. After the CloudFormation stack is `CREATE_COMPLETE`, continue with the next stack.

## Obtain & Review Code

In this lab, you use Python scripts to interact with the DynamoDB API. Run the following commands in your AWS Cloud9 terminal to download and unpack this lab’s code.

```bash
cd ~/environment
curl -sL https://amazon-dynamodb-labs.com/assets/battle-royale.zip -o battle-royal.zip && unzip -oq battle-royal.zip && rm battle-royal.zip
```

You should see two directories in the AWS Cloud9 file explorer:

- **application**: The _application_ directory contains example code for reading and writing data in your table. This code is similar to code you would have in your real gaming application.
    
- **scripts**: The _scripts_ directory contains administrator-level scripts, such as for creating a table, adding a secondary index, or deleting a table.
    

You are now ready to start the lab. With DynamoDB, it is important to plan your data model up front so that you have fast, consistent performance in your application. In the next module, you will learn about planning your data model.