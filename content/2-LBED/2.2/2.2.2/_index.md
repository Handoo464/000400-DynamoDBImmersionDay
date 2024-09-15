---
title : "Enable Amazon Bedrock Models"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2.2. </b> "
---

Amazon Bedrock is a fully managed service that offers a choice of high-performing foundation models (FMs) from leading AI companies like AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI, and Amazon via a single API, along with a broad set of capabilities you need to build generative AI applications with security, privacy, and responsible AI.

In this application, Bedrock will be used to make natural language product recommendation queries using OpenSearch Service as a vector database.

Bedrock requires different FMs to be enabled before they are used.

1. Open [Amazon Bedrock Model Access](https://us-west-2.console.aws.amazon.com/bedrock/home?region=us-west-2#/modelaccess) 
    
2. Click on "Manage model access"
    
    ![Manage model access](/images/2/2.2/9.jpg)
    
3. Select "Titan Embeddings G1 - Text" and "Claude", then click `Request model access`
    
4. Wait until you are granted access to both models before continuing. The _Access status_ should say _Access granted_ before moving on.
    
{{%notice tip%}}
_Do not continue unless the base models "Claude" and "Titan Embeddings G1 - Text" are granted to your account._
{{%/notice%}}