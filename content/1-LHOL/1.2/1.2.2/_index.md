---
title : "Reading Item Collections using Query"
date : "`r Sys.Date()`"
weight : 2.
chapter : false
pre : " <b> 1.2.2. </b> "
---

Item Collections are groups of Items that share a Partition Key. By definition, Item Collections can only exist in tables that have both a Partition Key and a Sort Key. We can read all or part of an Item Collection using the [Query API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html)  which can be invoked using the [query CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html) . It might seem confusing as the word "query" is generally used colloquially to mean "reading data from a database" but in DynamoDB "query" has a specific meaning: to read all or part of an Item Collection.

When we invoke the _Query_ API we must specify a [Key Condition Expression](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.KeyConditionExpressions) . If we were comparing this to SQL, we would say "this is the part of the WHERE clause that acts on the Partition Key and Sort Key attributes". This could take a couple of forms:

- Just the Partition Key value of our Item Collection. This indicates that we want to read ALL the items in the item collection.
- The Partition Key value and some kind of Sort Key Condition which will match a subset of the rows in item collection. Possible sort key conditions are =, <, >, <=, >=, BETWEEN, and BEGINS_WITH.

The Key Condition Expression will define the number of RRUs or RCUs that are consumed by our Query. DynamoDB will add up the size of all the rows matched by the Key Condition Expression, then divide that total size by 4KB to calculate the consumed capacity (and then it will divide that number in half if you're using an eventually consistent read).

We can optionally also specify a [Filter Expression](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.FilterExpression)  for our Query. If we were comparing this to SQL, we would say "this is the part of the WHERE clause that acts on the non-Key attributes". Filter Expressions act to remove some items from the Result Set returned by the Query, **but they do not affect the consumed capacity of the Query**. If your Key Condition Expression matches 1,000,000 items and your FilterExpression reduces the result set down to 100 items, you will still be charged to read all 1,000,000 items. But the Filter Expression reduces the amount of data returned from the network connection so there is still a benefit to our application in using Filter Expressions even if it doesn't affect the price of the Query.

The ProductCatalog table we used in the previous examples only has a Partition Key so let's look at the data in the **Reply** table which has both a Partition Key and a Sort Key:

```bash
aws dynamodb scan --table-name Reply
```

Data in this table has an Id attribute which references items in the Thread table. Our data has two threads, and each thread has 2 replies. Let's use the _query_ CLI to read just the items from thread 1:

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'Id = :Id' \
    --expression-attribute-values '{
        ":Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 1"}
    }' \
    --return-consumed-capacity TOTAL
```

Since the Sort Key in this table is a timestamp, we could specify a Key Condition Expression to return only the replies in a thread that were posted after a certain time by adding a sort key condition:

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'Id = :Id and ReplyDateTime > :ts' \
    --expression-attribute-values '{
        ":Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 1"},
        ":ts" : {"S": "2015-09-21"}
    }' \
    --return-consumed-capacity TOTAL
```

Remember we can use Filter Expressions if we want to limit our results based on non-key attributes. For example, we could find all the replies in Thread 1 that were posted by User B:

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'Id = :Id' \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 1"},
        ":user" : {"S": "User B"}
    }' \
    --return-consumed-capacity TOTAL
```

Note than in the response we see these lines:

```bash
"Count": 1,
"ScannedCount": 2,
```

This is telling us that the Key Condition Expression matched 2 items (ScannedCount) and thats what we were charged to read, but the Filter Expression reduced our result set size down to 1 item (Count).