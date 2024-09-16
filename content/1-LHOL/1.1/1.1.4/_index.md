---
title : "Cleanup"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 1.1.4. </b> "
---

If you used an account provided by Workshop Studio Event Delivery, you do not need to do any cleanup. The account terminates when the event is over.

If you used your own account, please remove the following resources:

The four DynamoDB tables created in the Getting Started section of the lab:
```bash
aws dynamodb delete-table \
    --table-name ProductCatalog

aws dynamodb delete-table \
    --table-name Forum

aws dynamodb delete-table \
    --table-name Thread

aws dynamodb delete-table \
    --table-name Reply
```

The Cloudformation template that was launched during the getting started section. Navigate to the Cloudformation console, select the `amazon-dynamodb-labs` stack and click `Delete`.

![](/images/1/1.1/9.png)

This should wrap up the cleanup process.
