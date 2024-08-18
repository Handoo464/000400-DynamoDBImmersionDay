---
title : "Service Configuration"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---

In this section, you will load data into your DynamoDB table and configure your OpenSearch Service resources.

Before beginning this section, make sure that [setup](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/) has been completed for whichever way you're running this lab. Setup will deploy several resources.

Dependencies from Cloud9 CloudFormation Template:

- S3 Bucket: Used to store the initial export of DynamoDB data for the Zero-ETL Pipeline.
- IAM Role: Used to grant permissions for pipeline integration and queries.
- Cloud9 IDE: Console for executing commands, building integrations, and running sample queries.

zETL CloudFormation Template Resources:

- DynamoDB Table: DynamoDB table to store product descriptions. Has Point-in-time Recovery (PITR) and DynamoDB Streams enabled.
- Amazon OpenSearch Service Domain: Single-node OpenSearch Service cluster to recieve data from DynamoDB and act as a vector database.

![Final Deployment Architecture](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl.png)