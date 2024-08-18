---
title : "Working with Table Scans"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.3.3. </b> "
---

The [Scan API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html)  which can be invoked using the [scan CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html) . Scan will do a full table scan and return the items in 1MB chunks.

The Scan API is similar to the Query API except that since we want to scan the whole table and not just a single Item Collection, there is no Key Condition Expression for a Scan. However, you can specify a [Filter Expression](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.FilterExpression)  which will reduce the size of the result set (even though it will not reduce the amount of capacity consumed).

Let us look at the data in the **Reply** table which has both a Partition Key and a Sort Key. Select the left menu bar **Explore items**. ![Console Menu Item Explorer](/images/1/1.3/11.png) You may need to click the hamburger menu icon to expand the left menu if its hidden. ![Console Menu Hamburger Icon](/images/1/1.3/12.png)

Once you enter the Explore Items you need to select the **Reply** table and then expand the Scan/Query items box.

![Item Explorer Expand Tables](/images/1/1.3/13.png)

For example, we could find all the replies in the Reply that were posted by User A.

![Item Explorer Scan Reply 1](/images/1/1.3/14.png)

You should see 3 **Reply** items posted by User A.