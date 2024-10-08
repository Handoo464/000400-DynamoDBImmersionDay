---
title : "Configure Integrations"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.3.1. </b> "
---

In this section you'll configure ML and Pipeline connectors in OpenSearch Service. These configurations are set up by a series of POST and PUT requests that are authenticated with AWS Signature Version 4 (sig-v4). Sigv4 is the standard authentication mechanism used by AWS services. While in most cases an SDK abstracts away sig-v4 but in this case we will be building the requests ourselves with curl.

Building a sig-v4 signed request requires a session token, access key, and secret access key. You'll first retrieve these from your Cloud9 Instance metadata with the provided "credentials.sh" script which exports required values to environmental variables. In the following steps, you'll also export other values to environmental variables to allow for easy substitution into listed commands.

1. Edit the credentials.sh

You can make edits on Cloud9/Session Manager/VSCode.

On VSCode, you need to log in using the command `sudo su`, then execute the command `vim credentials.sh` with the following content:

    ```bash
    # Lấy token từ metadata service
    TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

    # Lấy IAM role
    INSTANCE_ROLE=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/)

    # Lấy thông tin chi tiết về IAM role
    ROLE_DETAILS=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/${INSTANCE_ROLE})

    # Trích xuất thông tin từ JSON
    AccessKeyId=$(echo $ROLE_DETAILS | jq -r '.AccessKeyId')
    SecretAccessKey=$(echo $ROLE_DETAILS | jq -r '.SecretAccessKey')
    Token=$(echo $ROLE_DETAILS | jq -r '.Token')
    Expiration=$(echo $ROLE_DETAILS | jq -r '.Expiration')
    Region=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/region)
    Role=$(aws sts get-caller-identity | jq -r '.Arn | sub("sts";"iam") | sub("assumed-role";"role") | sub("/i-[a-zA-Z0-9]+$";"")')

    # Xuất các biến môi trường
    export METADATA_AWS_ACCESS_KEY_ID=${AccessKeyId}
    export METADATA_AWS_SECRET_ACCESS_KEY=${SecretAccessKey}
    export METADATA_AWS_SESSION_TOKEN=${Token}
    export METADATA_AWS_REGION=${Region}
    export METADATA_AWS_ROLE=${Role}
    export METADATA_AWS_EXPIRATION=${Expiration}

    # Hiển thị thông tin
    echo "METADATA_AWS_ACCESS_KEY_ID: $AccessKeyId"
    echo "METADATA_AWS_SECRET_ACCESS_KEY: $SecretAccessKey"
    echo "METADATA_AWS_SESSION_TOKEN: $Token"
    echo "METADATA_AWS_REGION: $Region"
    echo "METADATA_AWS_ROLE: $Role"
    echo "METADATA_AWS_EXPIRATION: $Expiration"
    ```
1. Run the credentials.sh script to retrieve and export credentials. These credentials will be used to sign API requests to the OpenSearch cluster. Note the leading "." before "./credentials.sh", this must be included to ensure that the exported credentials are available in the currently running shell.
    
    ```bash
    . ./credentials.sh 
    ```
    
2. Next, export an environmental variable with the OpenSearch endpoint URL. This URL is listed in the CloudFormation Stack Outputs tab as "OSDomainEndpoint". This variable will be used in subsequent commands.
    
    ```bash
    export OPENSEARCH_ENDPOINT="https://search-ddb-os-xxxx-xxxxxxxxxxxxx.us-west-2.es.amazonaws.com"
    ```
    
3. Execute the following curl command to create the OpenSearch ML model connector.
    
    ```bash
    curl --request POST \
      ${OPENSEARCH_ENDPOINT}'/_plugins/_ml/connectors/_create' \
      --header 'Content-Type: application/json' \
      --header 'Accept: application/json' \
      --header "x-amz-security-token: ${METADATA_AWS_SESSION_TOKEN}" \
      --aws-sigv4 aws:amz:${METADATA_AWS_REGION}:es \
      --user "${METADATA_AWS_ACCESS_KEY_ID}:${METADATA_AWS_SECRET_ACCESS_KEY}" \
      --data-raw '{
      "name": "Amazon Bedrock Connector: embedding",
      "description": "The connector to bedrock Titan embedding model",
      "version": 1,
      "protocol": "aws_sigv4",
      "parameters": {
        "region": "'${METADATA_AWS_REGION}'",
        "service_name": "bedrock"
      },
      "credential": {
        "roleArn": "'${METADATA_AWS_ROLE}'"
      },
      "actions": [
        {
          "action_type": "predict",
          "method": "POST",
          "url": "https://bedrock-runtime.'${METADATA_AWS_REGION}'.amazonaws.com/model/amazon.titan-embed-text-v1/invoke",
          "headers": {
            "content-type": "application/json",
            "x-amz-content-sha256": "required"
          },
          "request_body": "{ \"inputText\": \"${parameters.inputText}\" }",
          "pre_process_function": "\n    StringBuilder builder = new StringBuilder();\n    builder.append(\"\\\"\");\n    String first = params.text_docs[0];\n    builder.append(first);\n    builder.append(\"\\\"\");\n    def parameters = \"{\" +\"\\\"inputText\\\":\" + builder + \"}\";\n    return  \"{\" +\"\\\"parameters\\\":\" + parameters + \"}\";",
          "post_process_function": "\n      def name = \"sentence_embedding\";\n      def dataType = \"FLOAT32\";\n      if (params.embedding == null || params.embedding.length == 0) {\n        return params.message;\n      }\n      def shape = [params.embedding.length];\n      def json = \"{\" +\n                 \"\\\"name\\\":\\\"\" + name + \"\\\",\" +\n                 \"\\\"data_type\\\":\\\"\" + dataType + \"\\\",\" +\n                 \"\\\"shape\\\":\" + shape + \",\" +\n                 \"\\\"data\\\":\" + params.embedding +\n                 \"}\";\n      return json;\n    "
        }
      ]
    }'
    ```
    
4. Note the "connector_id" returned in the previous command. Export it to an environmental variable for convenient substitution in future commands.
    
    ```bash
    export CONNECTOR_ID='xxxxxxxxxxxxxx'
    ```
    
5. Run the next curl command to create the model group.
    
    ```bash
    curl --request POST \
      ${OPENSEARCH_ENDPOINT}'/_plugins/_ml/model_groups/_register' \
      --header 'Content-Type: application/json' \
      --header 'Accept: application/json' \
      --header "x-amz-security-token: ${METADATA_AWS_SESSION_TOKEN}" \
      --aws-sigv4 aws:amz:${METADATA_AWS_REGION}:es \
      --user "${METADATA_AWS_ACCESS_KEY_ID}:${METADATA_AWS_SECRET_ACCESS_KEY}" \
      --data-raw '{
        "name": "remote_model_group",
        "description": "This is an example description"
    }'
    ```
    
6. Note the "model_group_id" returned in the previous command. Export it to an environmental variable for later substitution.
    
    ```bash
    1
    export MODEL_GROUP_ID='xxxxxxxxxxxxx'
    ```
    
7. The next curl command registers the connector with the model group.
    
    ```bash
    curl --request POST \
      ${OPENSEARCH_ENDPOINT}'/_plugins/_ml/models/_register' \
      --header 'Content-Type: application/json' \
      --header 'Accept: application/json' \
      --header "x-amz-security-token: ${METADATA_AWS_SESSION_TOKEN}" \
      --aws-sigv4 aws:amz:${METADATA_AWS_REGION}:es \
      --user "${METADATA_AWS_ACCESS_KEY_ID}:${METADATA_AWS_SECRET_ACCESS_KEY}" \
      --data-raw '{
      "name": "Bedrock embedding model",
      "function_name": "remote",
      "model_group_id": "'${MODEL_GROUP_ID}'",
      "description": "embedding model",
      "connector_id": "'${CONNECTOR_ID}'"
    }'
    ```
    
8. Note the "model_id" and export it.
    
    ```bash
    1
    export MODEL_ID='xxxxxxxxxxxxx'
    ```
    
9. Run the following command to verify that you have successfully exported the connector, model group, and model id.
    
    ```bash
    1
    echo -e "CONNECTOR_ID=${CONNECTOR_ID}\nMODEL_GROUP_ID=${MODEL_GROUP_ID}\nMODEL_ID=${MODEL_ID}"
    ```
    
10. Next, we'll deploy the model with the following curl.
    
    ```bash
    curl --request POST \
      ${OPENSEARCH_ENDPOINT}'/_plugins/_ml/models/'${MODEL_ID}'/_deploy' \
      --header 'Content-Type: application/json' \
      --header 'Accept: application/json' \
      --header "x-amz-security-token: ${METADATA_AWS_SESSION_TOKEN}" \
      --aws-sigv4 aws:amz:${METADATA_AWS_REGION}:es \
      --user "${METADATA_AWS_ACCESS_KEY_ID}:${METADATA_AWS_SECRET_ACCESS_KEY}"
    ```
    
    With the model created, OpenSearch can now use Bedrock's Titan embedding model for processing text. An embeddings model is a type of machine learning model that transforms high-dimensional data (like text or images) into lower-dimensional vectors, known as embeddings. These vectors capture the semantic or contextual relationships between the data points in a more compact, dense representation.
    
    The embeddings represent the semantic meaning of the input data, in this case product descriptions. Words with similar meanings are represented by vectors that are close to each other in the vector space. For example, the vectors for "sturdy" and "strong" would be closer to each other than to "warm".
    
11. Now we can test the model. If you recieve results back with a "200" status code, everything is working properly.
    
    ```bash
    curl --request POST \
      ${OPENSEARCH_ENDPOINT}'/_plugins/_ml/models/'${MODEL_ID}'/_predict' \
      --header 'Content-Type: application/json' \
      --header 'Accept: application/json' \
      --header "x-amz-security-token: ${METADATA_AWS_SESSION_TOKEN}" \
      --aws-sigv4 aws:amz:${METADATA_AWS_REGION}:es \
      --user "${METADATA_AWS_ACCESS_KEY_ID}:${METADATA_AWS_SECRET_ACCESS_KEY}" \
      --data-raw '{
      "parameters": {
        "inputText": "What is the meaning of life?"
      }
    }'
    ```
    
12. Next, we'll create the Details table mapping pipeline.
    
    ```bash
    curl --request PUT \
      ${OPENSEARCH_ENDPOINT}'/_ingest/pipeline/product-en-nlp-ingest-pipeline' \
      --header 'Content-Type: application/json' \
      --header 'Accept: application/json' \
      --header "x-amz-security-token: ${METADATA_AWS_SESSION_TOKEN}" \
      --aws-sigv4 aws:amz:${METADATA_AWS_REGION}:es \
      --user "${METADATA_AWS_ACCESS_KEY_ID}:${METADATA_AWS_SECRET_ACCESS_KEY}" \
      --data-raw '{
      "description": "A text embedding pipeline",
      "processors": [
        {
          "script": {
            "source": "def combined_field = \"ProductID: \" + ctx.ProductID + \", Description: \" + ctx.Description + \", ProductName: \" + ctx.ProductName + \", Category: \" + ctx.Category; ctx.combined_field = combined_field;"
          }
        },
        {
          "text_embedding": {
            "model_id": "'${MODEL_ID}'",
            "field_map": {
              "combined_field": "product_embedding"
            }
          }
        }
      ]
    }'
    ```
    
13. Followed by the Reviews table mapping pipeline. We won't use this in this version of the lab, but in a real system you will want to keep your embeddings indexes separate for different queries.
    
    ```bash
    curl --request PUT \
      ${OPENSEARCH_ENDPOINT}'/_ingest/pipeline/product-reviews-nlp-ingest-pipeline' \
      --header 'Content-Type: application/json' \
      --header 'Accept: application/json' \
      --header "x-amz-security-token: ${METADATA_AWS_SESSION_TOKEN}" \
      --aws-sigv4 aws:amz:${METADATA_AWS_REGION}:es \
      --user "${METADATA_AWS_ACCESS_KEY_ID}:${METADATA_AWS_SECRET_ACCESS_KEY}" \
      --data-raw '{
      "description": "A text embedding pipeline",
      "processors": [
        {
          "script": {
            "source": "def combined_field = \"ProductID: \" + ctx.ProductID + \", ProductName: \" + ctx.ProductName + \", Comment: \" + ctx.Comment + \", Timestamp: \" + ctx.Timestamp; ctx.combined_field = combined_field;"
          }
        },
        {
          "text_embedding": {
            "model_id": "m6jIgowBXLzE-9O0CcNs",
            "field_map": {
              "combined_field": "product_reviews_embedding"
            }
          }
        }
      ]
    }'
    ```
    
    These pipelines allow OpenSearch to preprocess and enrich data as it is written to the index by adding embeddings through the Bedrock connector.