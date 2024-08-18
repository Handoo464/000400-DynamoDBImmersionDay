---
title : "Viewing Table Data"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.3.1. </b> "
---

First, go to the [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/)  and click on _Tables_ from the side menu.

![Console Pick Tables](/images/1/1.3/1.png)

Next, choose the `ProductCatalog` table and click `Explore table items` on the top right to view the items.

![Console ProductCatalog Items Preview](/images/1/1.3/2.png)

We can see visually that the table has a Partition Key of _Id_ (which is the `Number` type), no sort key, and there are 8 items in the table. Some items are Books and some items are Bicycles and some attributes like _Id_, _Price_, _ProductCategory_, and _Title_ exist in every Item while other Category specific attributes like Authors or Colors exist only on some items.

Click on the _Id_ attribute `101` to pull up the Item editor for that Item. We can see and modify all the attributes for this item right from the console. Try changing the _Title_ to "Book 101 Title New and Improved". Click **Add new attribute** named _Reviewers_ of the String set type and then clicking **Insert a field** twice to add a couple of entries to that set. When you're done click **Save changes**

![Console ProductCatalog Items Editor Forms](/images/1/1.3/3.png)

You can also use the Item editor in DynamoDB JSON notation (instead of the default Form based editor) by clicking **JSON** in the top right corner. This notation should look familiar if you already went through the [Explore the DynamoDB CLI](https://catalog.workshops.aws/dynamodb-labs/en-US/hands-on-labs/explore-cli.html) portion of the lab. The DynamoDB JSON format is described in the [DynamoDB Low-Level API](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.LowLevelAPI.html)  section of the Developer Guide.

![Console ProductCatalog Items Editor JSON](/images/1/1.3/4.png)