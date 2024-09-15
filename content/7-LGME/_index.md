---
title : "LGME: Modeling Game Player Data with Amazon DynamoDB"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

In this workshop, you will learn advanced data modeling patterns in [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) . When using DynamoDB, it is important to consider how you will access your data (your access patterns) before you model your data. You will go through an example multiplayer gaming application, learn about the access patterns in the gaming application, and see how to design a DynamoDB table to handle the access patterns by using secondary indexes and transactions.

Read up more on how DynamoDB is used by existing customers in GameTech particularly [https://aws.amazon.com/dynamodb/gaming/](https://aws.amazon.com/dynamodb/gaming/) 

Here's what this workshop includes:

- 1. Getting Started
- 2. Plan your data model
- 3. Core usage: user profiles and games
- 4. Find open games
- 5. Join and close games
- 6. View past games
- Summary & Cleanup

### Target audience

This workshop is designed for developers, engineers, and database administrators who are involved in designing and maintaining DynamoDB applications.