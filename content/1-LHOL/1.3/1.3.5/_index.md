---
title : "Global Secondary Indexes"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 1.3.5. </b> "
---

We have concerned ourself hitherto with accessing data based on the key attributes. If we wanted to look for items based on non-key attributes we had to do a full table scan and use filter conditions to find what we wanted, which would be both very slow and very expensive for systems operating at large scale.

DynamoDB provides a feature called [Global Secondary Indexes (GSIs)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html)  which will automatically pivot your data around different Partition and Sort Keys. Data can be re-grouped and re-sorted to allow for more access patterns to be quickly served with the Query and Scan APIs.

Remember the previous example where we wanted to find all the replies in the **Reply** table that were posted by User A and needed to use a `Scan` operation? If there had been a billion **Reply** items but only three of them were posted by User A, we would have to pay (both in time and money) to scan through a billion items just to find the three we wanted.

Armed with this knowledge of GSIs, we can now create a GSI on the **Reply** table to service this new access pattern. GSIs can be created and removed at any time, even if the table has data in it already! This new GSI will use the _PostedBy_ attribute as the Partition (HASH) key and we will still keep the messages sorted by _ReplyDateTime_ as the Sort (RANGE) key. We want all the attributes from the table copied (projected) into the GSI so we will use the ALL ProjectionType. Note that the name of the index we create is `PostedBy-ReplyDateTime-gsi`.

Navigate to the **Reply** table, switch to the **Indexes** tab and click `Create Index`.

![Console Create GSI 1](/images/1/1.3/17.png)

Enter `PostedBy` as the Partition key, `ReplyDateTime` as the Sort key, and `PostedBy-ReplyDateTime-gsi` as the Index name. Leave the other settings as defaults and click `Create Index`. Once the index leaves the `Creating` state you can continue on to the exercise below.


### Cleanup

When you're done, make sure to remove the GSI. Return to the Indexes tab, select the `PostedBy-ReplyDateTime-gsi` index and click `Delete`.

![Console Delete GSI](/images/1/1.3/18.png)