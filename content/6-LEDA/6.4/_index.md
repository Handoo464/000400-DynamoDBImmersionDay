---
title : "Lab 2: Ensure fault tolerance and exactly once processing"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 6.4. </b> "
---

{{%notice tip%}}
Points and scoreboard only apply when this lab is run during an AWS Event.
{{%/notice%}}

# Are you ready to start Lab 2?


Before proceeding to _Lab 2_ let's verify that _Lab 1_ was successfully completed. There are two phases to complete before continuing:

- First, you started to accumulate points on the scoreboard. If you have non-zero points then this phase is complete. Open the scoreboard and find your team. Find your account ID in the AWS Management Console on the top right of the console.
- Second, you need accumulate 300 points to continue. The workshop will automatically switch to _Lab 2_ when you reach this milestone, and this phase is complete.
- Once 300 points are accumulated, new failure modes will be introduced and all three Lambda functions (`StateLambda`, `MapLambda`, and `ReduceLambda`) will start failing randomly. This is a pre-programmed evolution of the workshop. In the Lambda console, click on any of the three Lambda functions, navigate to the `Monitor` tab and then to the `Metrics` sub-tab. You should expect to see a non-zero error rate on some of the graphs!

{{%notice tip%}}
If the dashboard has 300 points, then congratulations: you can start Lab 2!
{{%/notice%}}


![Architecture-1](/images/6/6.4/1.png)

# Let’s utilize different features of DynamoDB to ensure data integrity and fault tolerance


In _Lab 2_ we will achieve exactly once processing of the messages. To make sure that our pipeline can withstand different failure modes and achieve exactly once message processing.

![Architecture-3](/images/6/6.4/2.png)

Continue on to: [Step 1](https://catalog.workshops.aws/dynamodb-labs/en-US/event-driven-architecture/ex3fixbugs/step1)