---
title : "Exercise Overview"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.5.1. </b> "
---

In this module, you will create an environment to host the MySQL database on Amazon EC2. This instance will be used to host source database and simulate on-premise side of migration architecture. All the resources to configure source infrastructure are deployed via [Amazon CloudFormation](https://aws.amazon.com/cloudformation/)  template. There are two CloudFormation templates used in this exercise which will deploy following resources.

CloudFormation MySQL Template Resources:

- OnPrem VPC: Source VPC will represent an on-premise source environment in the N. Virginia region. This VPC will host source MySQL database on Amazon EC2
- Amazon EC2 MySQL Database: Amazon EC2 Amazon Linux 2 AMI with MySQL installed and running
- Load IMDb dataset: The template will create IMDb database on MySQL and load IMDb public dataset files into database. You can learn more about IMDb dataset inside [Explore Source Model](https://catalog.workshops.aws/dynamodb-labs/en-US/hands-on-labs/rdbms-migration/migration-chapter03)

CloudFormation DMS Instance Resources:

- DMS VPC: Migration VPC on in the N. Virginia region. This VPC will host DMS replication instance.
- Replication Instance: DMS Replication instance that will facilitate database migration from source MySQL server on EC2 to Amazon DynamoDB

![Final Deployment Architecture](/images/1/1.5/1.png)
