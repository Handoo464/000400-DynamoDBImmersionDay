---
title : "Cleaning Up The Resources"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 1.4.6. </b> "
---

#### Cleaning Up The Resources
##### Step 1: Delete restored AWS resources


Delete three DynamoDB restored tables using following command.

```bash
aws dynamodb delete-table \
--table-name ProductCatalogODRestore

aws dynamodb delete-table \
--table-name ProductCatalogRestored

aws dynamodb delete-table \
--table-name ProductCatalogPITR


```

### Step 2: Delete the backup plan


Follow these steps to delete a backup plan:

1. In the AWS Management Console, navigate to **Services -> AWS Backup.** In the navigation pane, choose Backup plans.On the Backup plans page, choose _dbBackupPlan_. This takes you to the details page.

![Backup Plan Delete 1](/images/1/1.4/1.4.6/1.png)

2. To delete the resource assignments for your plan, choose the radio button next to the dynamodbTable under **Resource assignments**, and then choose **Delete**.

![Backup Plan Delete 2](/images/1/1.4/1.4.6/2.png)

3. To delete the backup plan, choose **Delete** in the upper-right corner of the page.

![Backup Plan Delete 3](/images/1/1.4/1.4.6/3.png)

4. On the confirmation page, enter _dbBackupPlan_, and choose **Delete plan**.

![Backup Plan Delete 4](/images/1/1.4/1.4.6/4.png)

### Step 3: Delete the recovery points


1. On the AWS Backup console, in the navigation pane, choose Backup vaults.
    
2. On the Backup vaults page, choose the _dynamodb-backup-vault_. Check the recovery point and choose **Delete**.
    

![Restore Points Delete 1](/images/1/1.4/1.4.6/5.png)

3. If you are deleting more than one recovery point, follow these steps:
    
    a. Review the list of recovery points that you are deleting.
    
    b. If you want to edit the list, choose Modify selection.
    
    c. If your list contains a continuous backup, choose whether to keep or delete your continuous backup data.
    
    d. To delete all the recovery points listed, enter delete, and then choose Delete recovery points.
    

Keep your browser tab open until you see the green success banner at the top of the page.

Prematurely closing this tab will end the deletion process and might leave behind some of the recovery points you wanted to delete.

### Step 4: Delete the backup vault


1. Select the backup vault _dynamodb-backup-vault_ and choose **Delete**.

![Backup Vault Delete 1](/images/1/1.4/1.4.6/6.png)

2. On the confirmation page, enter _dynamodb-backup-vault_, and choose **Delete Backup vault**.

![Backup Vault Delete 2](/images/1/1.4/1.4.6/7.png)