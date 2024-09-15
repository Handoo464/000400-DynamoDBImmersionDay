---
title : "Overview"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 6.2.1. </b> "
---

## Architecture

After setup, most of the components the data aggregation pipeline are already put in place for you, as shown in the diagram below. However, some of the links between neighboring components are missing or misconfigured! These connections are the crux of the problem you have to solve.

The workshop contains two labs. The objective of the first lab is to establish connections between these components and achieve end-to-end data processing, from the `IncomingDataStream` on the left to the `AggregateTable` at the end of the pipeline.

However, the pipeline you build in the first lab doesn't ensure exactly once message processing. Therefore, establishing exactly once processing is the goal of _Lab 2_.

![Architecture-1](/images/6/6.2/6.png)

## Lab 1

The diagram below outlines a set of steps that you will need to perform in order to connect all the AWS resources. The _Lab 1_ section will give you more information and will explain how to perform each step.

![Architecture-2](/images/6/6.2/7.png)

## Lab 2

In _Lab 2_, we will enhance the pipeline to ensure exactly once processing for any ingested message. To make sure that our pipeline can withstand different failure modes and achieve exactly once message processing we will modify two Lambda functions.

The _Lab 2_ section will give you more information and will explain how to perform each step. ![Architecture-3](/images/6/6.2/1.png)

## Next steps and competition

The _Pipeline deep dive with example_ subsection contains a detailed explanation of how data is processed in this pipeline. This information is recommended but not necessary to complete the workshop.

To make this workshop more exciting, when run at an AWS event all participants are rated on how many messages they can aggregate correctly using a scoreboard. The _Game rules_ subsection outlines the rules of the game, and how the `GeneratorLambda` function ingests data into the start of the pipeline!

Continue on to: [Game Rules](https://catalog.workshops.aws/dynamodb-labs/en-US/event-driven-architecture/ex1overview/step1).