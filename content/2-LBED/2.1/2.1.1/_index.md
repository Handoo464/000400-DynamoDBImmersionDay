---
title : "Obtain & Review Code"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> </b> "
---
{{%notice info%}}
You can use cloud9 or session manager. 
{{%/notice%}}

In this lab, you use Bash and Python scripts to interact with AWS services. Run the following commands in your terminal to download and unpack this lab’s code.

```bash
cd ~/environment
curl -sL https://amazon-dynamodb-labs.com/assets/OpenSearchPipeline.zip -o OpenSearchPipeline.zip && unzip -oq OpenSearchPipeline.zip && rm OpenSearchPipeline.zip
```

You should see a directory in the AWS Cloud9 file explorer **OpenSearchPipeline**:

The _OpenSearchPipeline_ directory contains example items that will be loaded into a DynamoDB table, as Bash script to simplify managing credentials when signing requests for OpenSearch, and a python script for executing a query to Bedrock.

You are now ready to start the lab. In the next module, you will complete setup for each of the three services used in this lab before moving on to integrate them.
