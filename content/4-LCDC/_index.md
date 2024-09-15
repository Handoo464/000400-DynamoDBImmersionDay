---
title : "LCDC: Change Data Capture for Amazon DynamoDB"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

In this workshop, you will learn how to perform change data capture of item level changes on DynamoDB tables using [Amazon DynamoDB Streams](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)  and [Amazon Kinesis Data Streams](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/kds.html) . This technique allows you to develop event-driven solutions that are initiated by alterations made to item-level data stored in DynamoDB.

Here is what this workshop includes:

1. Getting Started
2. Scenario Overview
3. Change Data Capture using DynamoDB Streams
4. Change Data Capture using Kinesis Data Streams
5. Summary and Clean Up

### Target audience


This workshop is designed for developers, engineers, and database administrators who are involved in designing and maintaining DynamoDB applications.
### Requirements

#### Basic knowledge of AWS services


- Among other services this lab will guide you through the use of [AWS Cloud9](https://aws.amazon.com/cloud9/)  and [AWS Lambda](https://aws.amazon.com/lambda/) .

#### Basic understanding of DynamoDB

- If you’re not familiar with DynamoDB or are not participating in this lab as part of an AWS event, consider reviewing the documentation on "[What is Amazon DynamoDB?](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) "

### Recommended study before taking the lab

If you're not part of an AWS event and you haven't recently reviewed DynamoDB design concepts, we suggest you watch this video on [Advanced Design Patterns for DynamoDB](https://www.youtube.com/watch?v=xfxBhvGpoa0) , which is about an hour in duration.
