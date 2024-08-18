---
title : "Modifying Data"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.3.4. </b> "
---


## Inserting Data

The DynamoDB [PutItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)  is used to create a new item or to replace existing items completely with a new item. It is invoked using the [put-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/put-item.html) .

Let's say we wanted to insert a new item into the _Reply_ table from the console. First, navigate to the **Reply** table click the **Create Item** button.

![Console Create Item 1](/images/1/1.3/15.png)

Click `JSON view`, ensure `View DynamoDB JSON` is deselected, paste the following JSON, and then click **Create Item** to insert the new item.

```json
{
        "Id" : "Amazon DynamoDB#DynamoDB Thread 2",
        "ReplyDateTime" : "2021-04-27T17:47:30Z",
        "Message" : "DynamoDB Thread 2 Reply 3 text",
        "PostedBy" : "User C"
}
```

![Console Create Item 2](/images/1/1.3/16.png)


## Updating or Deleting Data

The DynamoDB [UpdateItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)  is used to create a new item or to replace existing items completely with a new item. It is invoked using the [update-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/update-item.html) . This API requires you to specify the full Primary Key and can selectively modify specific attributes without changing others(you don't need to pass in the full item).

The DynamoDB [DeleteItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html)  is used to create a new item or to replace existing items completely with a new item. It is invoked using the [delete-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/delete-item.html) .

You can easily modify or delete an item using the console by selecting the checkbox next to the item of interest, clicking the **Actions** dropdown and performing the desired action.

![Console Delete Item](/images/1/1.3/17.png)