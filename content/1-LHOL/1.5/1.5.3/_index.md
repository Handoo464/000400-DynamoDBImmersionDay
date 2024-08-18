---
title : "Create DMS Resources"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.5.3. </b> "
---

Let's create the DMS resources for the workshop.

1. Go to IAM console > Roles > Create Role
2. Under “Select trusted entity” select “AWS service” then under “Use case” select “DMS” from the pulldown list and click the “DMS” radio button. Then click “Next”
3. Under “Add permissions” use the search box to find the “AmazonDMSVPCManagementRole” policy and select it, then click “Next”
4. Under “Name, review, and create” add the role name as exactly `dms-vpc-role` and click Create role

_Do not continue unless you have made the IAM role._

1. Launch the CloudFormation template in US West 2 to deploy the resources in your account: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=dynamodbmigration&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-dms-setup.yaml)
    1. _Optionally, download [the YAML template](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-dms-setup.yaml)  and launch it your own way_
2. Click Next
3. Confirm the Stack Name _dynamodbmigration_ and keep the default parameters (modify if necessary) ![Final Deployment Architecture](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration18.jpg)
4. Click “Next” twice
5. Check “I acknowledge that AWS CloudFormation might create IAM resources with custom names.”
6. Click Submit. The CloudFormation template will take about 15 minutes to build a replication environment. You should continue the lab while the stack creates in the background. ![Final Deployment Architecture](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration19.jpg)

{{%notice tip%}}
Do not wait for the stack to complete creation. Please continue the lab and allow it to create in the background
{{%/notice%}}
