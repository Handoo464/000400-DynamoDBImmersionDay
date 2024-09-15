---
title : "Step 2 - Review the InvoiceAndBills table on the DynamoDB console"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.8.2. </b> "
---
In the DynamoDB console, open the **InvoiceAndBills** table and choose the **Items** menu option. From the dropdown menu, choose `InvoiceAndBills GSI_1` and then `Scan` the table.

In the output, choose **PK** to sort the data in reverse. Notice the different entity types in the same table.

![Adjacency Lists](/images/3/3.8/1.png)

In the following steps you will query the table and retrieve different entity types. Optionally consider performing the queries in the AWS console right after you query them with the Python scripts for extra insight.