---
title : "Load DynamoDB Data"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.2.3. </b> "
---

Next, you'll load example product data into your DynamoDB Table. Pipelines will move this data into OpenSearch Service in later steps.

## Load and Review Data

Return to the Cloud9 IDE. If you accidentally closed the IDE, you may search for the service in the AWS Management Console or use the Cloud9IDE URL found in the `Outputs` section of the CloudFormation stack.

Load the sample data into your DynamoDB Table.

```bash
cd ~/environment/OpenSearchPipeline
aws dynamodb batch-write-item --request-items=file://product_en.json
```

![CloudFormation Outputs](/images/2/2.2/10.jpg)

Next, navigate to the DynamoDB section of the AWS Management Console and click `Explore items` and then select the `ProductDetails` table. This is where the product information for this exercise originates from. Review the product names to get an idea for what kind of natural language searches you might want to provide later at the end of the lab.

![DynamoDB Console](/images/2/2.2/11.jpg)