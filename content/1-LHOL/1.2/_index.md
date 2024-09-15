---
title : "Explore DynamoDB with the CLI"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 1.2. </b> "
---

We will be exploring DynamoDB with the AWS CLI using the AWS cloud9 management Console or Session manager

The highest level of abstraction in DynamoDB is a _Table_ (there isn't a concept of a "Database" that has a bunch of tables inside of it like in other NOSQL or RDBMS services). Inside of a Table you will insert _Items_, which are analogous to what you might think of as a row in other services. Items are a collection of _Attributes_, which are analogous to columns. Every item must have a _Primary Key_ which will uniquely identify that row (two items may not contain the same Primary Key). At a minimum when you create a table you must choose an attribute to be the _Partition Key_ (aka the Hash Key) and you can optionally specify another attribute to be the _Sort Key_.

If your table is a Partition Key only table, then the Partition Key is the _Primary Key_ and must uniquely identify each item. If your table has both a Partition Key and Sort Key, then it is possible to have multiple items that have the same Partition Key, but the combination of the Partition Key and Sort Key will be the Primary Key and uniquely identify the row. In other words, you can have multiple items that have the same Partition Key as long as their Sort Keys are different. Items with the same Partition Key are said to belong to the same _Item Collection_.

For more information please read about [Core Concepts in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html) .

Operations in DynamoDB consume capacity from the table. When the table is using On-Demand capacity, read operations will consume _Read Request Units (RRUs)_ and write operations will consume _Write Request Units (WRUs)_. When the table is using Provisioned Capacity, read operations will consume _Read Capacity Units (RCUs)_ and write operations will consume _Write Capacity Units (WCUs)_. For more information please see the [Read/Write Capacity Mode](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html)  in the DynamoDB Developer Guide.

Now lets dive into the shell and explore DynamoDB with the AWS CLI.