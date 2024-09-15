---
title : "Working with Table Scans"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.2.3. </b> "
---

As mentioned previously, the [Scan API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html)  which can be invoked using the [scan CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html) . Scan will do a full table scan and return the items in 1MB chunks.

The Scan API is similar to the Query API except that since we want to scan the whole table and not just a single Item Collection, there is no Key Condition Expression for a Scan. However, you can specify a [Filter Expression](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.FilterExpression)  which will reduce the size of the result set (even though it will not reduce the amount of capacity consumed).

For example, we could find all the replies in the Reply that were posted by User A:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --return-consumed-capacity TOTAL
```

Note than in the response we see these lines:

```
"Count": 3,
"ScannedCount": 4,
```

This is telling us that the Scan scanned all 4 items (ScannedCount) in the table and thats what we were charged to read, but the Filter Expression reduced our result set size down to 3 items (Count).

Sometimes when scanning data there will be more data than will fit in the response if the scan hits the 1MB limit on the server side, or there may be more items left than our specified _--max-items_ parameter. In that case, the scan response will include a _NextToken_ which we can then issue to a subsequent scan call to pick up where we left off. For example in the previous scan we know that 3 items were in the result set. Let's run it again but limit the max items to 2:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --max-items 2 \
    --return-consumed-capacity TOTAL
```

We can see in the response that there is a

```text
1
"NextToken": "eyJFeGNsdXNpdmVTdGFydEtleSI6IG51bGwsICJib3RvX3RydW5jYXRlX2Ftb3VudCI6IDJ9"
```

So we can invoke the scan request again, this time passing that NextToken value into the _--starting-token_ parameter:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --max-items 2 \
    --starting-token eyJFeGNsdXNpdmVTdGFydEtleSI6IG51bGwsICJib3RvX3RydW5jYXRlX2Ftb3VudCI6IDJ9 \
    --return-consumed-capacity TOTAL
```

