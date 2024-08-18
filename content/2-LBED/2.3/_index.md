---
title : "Integrations"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3. </b> "
---

In this section, you will configure integrations between services. You'll first set up ML and Pipeline connectors in OpenSearch Service followed by a zero ETL connector to move data written to DynamoDB to OpenSearch. Once these integrations are set up, you'll be able to write records to DynamoDB as your source of truth and then automatically have that data available to query in other services.