---
title : "Inserting/Updating Data"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.2.4. </b> "
---

## Inserting Data

The DynamoDB [PutItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)  is used to create a new item or to replace existing items completely with a new item. It is invoked using the [put-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/put-item.html) .

Let's say we wanted to insert a new item into the _Reply_ table:

```bash
aws dynamodb put-item \
    --table-name Reply \
    --item '{
        "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
        "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"},
        "Message" : {"S": "DynamoDB Thread 2 Reply 3 text"},
        "PostedBy" : {"S": "User C"}
    }' \
    --return-consumed-capacity TOTAL
```

We can see in the response that this request consume 1 WCU:

```json
{
    "ConsumedCapacity": {
        "TableName": "Reply",
        "CapacityUnits": 1.0
    }
}
```

## Updating Data


The DynamoDB [UpdateItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)  is used to create a new item or to replace existing items completely with a new item. It is invoked using the [update-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/update-item.html) . This API requires you to specify the full Primary Key and can selectively modify specific attributes without changing others(you don't need to pass in the full item).

The `update-item` API call also allows you to specify a ConditionExpression, meaning the Update request will only execute if the ConditionExpression is satisfied. For more information please see [Condition Expressions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html)  in the Developer Guide.

Let's say we want to update the Forum item for DynamoDB to note that there are 5 messages how instead of 4, we only want that change to execute if no other processing thread has updated the item first. This allows us to create idempotent modifications. For more information on idempotent changes please see [Working With Items](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithItems.html#WorkingWithItems.ConditionalUpdate)  in the Developer Guide.

To do this from the CLI, we would run:

```bash
aws dynamodb update-item \
    --table-name Forum \
    --key '{
        "Name" : {"S": "Amazon DynamoDB"}
    }' \
    --update-expression "SET Messages = :newMessages" \
    --condition-expression "Messages = :oldMessages" \
    --expression-attribute-values '{
        ":oldMessages" : {"N": "4"},
        ":newMessages" : {"N": "5"}
    }' \
    --return-consumed-capacity TOTAL
```

Note that if you run this exact same command again you will see this error:

```text
An error occurred (ConditionalCheckFailedException) when calling the UpdateItem operation: The conditional request failed
```

Because the _Messages_ attribute had already been incremented to _5_ in the previous `update-item` call, the second request fails with a _ConditionalCheckFailedException_.

