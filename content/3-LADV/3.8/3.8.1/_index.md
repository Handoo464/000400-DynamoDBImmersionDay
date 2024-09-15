---
title : "Step 1 - Create and load the the InvoiceandBilling table"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.8.1. </b> "
---
Run the AWS CLI command to create the table named `InvoiceAndBilling` and create a GSI named `GSI_1` which is partitioned on `SK` attribute of the parent table:

```bash
aws dynamodb create-table --table-name InvoiceAndBills \
--attribute-definitions AttributeName=PK,AttributeType=S AttributeName=SK,AttributeType=S \
--key-schema AttributeName=PK,KeyType=HASH AttributeName=SK,KeyType=RANGE \
--provisioned-throughput ReadCapacityUnits=100,WriteCapacityUnits=100 \
--tags Key=workshop-design-patterns,Value=targeted-for-cleanup \
--global-secondary-indexes "IndexName=GSI_1,\
KeySchema=[{AttributeName=SK,KeyType=HASH}],\
Projection={ProjectionType=ALL},\
ProvisionedThroughput={ReadCapacityUnits=100,WriteCapacityUnits=100}"
```

Wait until the table becomes active:

```bash
aws dynamodb wait table-exists --table-name InvoiceAndBills
```

Then, load data into the InvoiceAndBills table:

```bash
python load_invoice.py InvoiceAndBills ./data/invoice-data.csv
```