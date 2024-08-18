---
title : "Configure MySQL Environment"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 1.5.2. </b> "
---


This chapter will create source environment on AWS as discussed during Exercise Overview. The CloudFormation template used below will create Source VPC, EC2 hosting MySQL server, IMDb database and load IMDb public dataset into 6 tables.

1. Launch the CloudFormation template in US West 2 to deploy the resources in your account: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=rdbmsmigration&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-env-setup.yaml)
    1. _Optionally, download [the YAML template](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/migration-env-setup.yaml)  and launch it your own way_
2. Click Next
3. Confirm the Stack Name _rdbmsmigration_ and update parameters if necessary (leave the default options if at all possible) ![Final Deployment Architecture](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration6.jpg)
4. Click “Next” twice then check “I acknowledge that AWS CloudFormation might create IAM resources with custom names.”
5. Click "Submit"
6. The CloudFormation stack will take about 5 minutes to build the environment ![Final Deployment Architecture](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration7.jpg)
7. Go to [EC2 Dashboard](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:)  and ensure the Status check column is 2/2 checks passed before moving to the next step. ![Final Deployment Architecture](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration8.jpg)

{{%notice tip%}}
Do not continue unless the MySQL instance is passing both health checks, 2/2.
{{%/notice%}}