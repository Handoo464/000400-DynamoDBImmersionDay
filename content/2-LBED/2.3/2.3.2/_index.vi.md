---
title : "Tạo Zero-ETL Pipeline"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.3.2. </b> "
---

Amazon Bedrock là một dịch vụ được quản lý toàn phần, cung cấp lựa chọn các mô hình nền tảng (FM) hiệu suất cao từ các công ty AI hàng đầu như AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI và Amazon thông qua một API duy nhất, cùng với một loạt các khả năng bạn cần để xây dựng các ứng dụng AI tổng quát với bảo mật, quyền riêng tư và AI có trách nhiệm.

1. Mở [Đường ống thu nạp dịch vụ OpenSearch](https://us-west-2.console.aws.amazon.com/aos/home?region=us-west-2#opensearch/ingestion-pipelines) 
    
2. Nhấp vào "Tạo đường ống"
    
    ![Tạo quy trình](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl13.jpg)
    
3. Đặt tên cho quy trình của bạn và bao gồm thông tin sau cho cấu hình quy trình của bạn. Cấu hình chứa nhiều giá trị cần được cập nhật. Các giá trị cần thiết được cung cấp trong Đầu ra ngăn xếp CloudFormation dưới dạng "Khu vực", "Vai trò", "S3Bucket", "DdbTableArn" và "OSDomainEndpoint".
    
    ```yaml
      version: "2"
      dynamodb-pipeline:
        source:
          dynamodb:
            acknowledgments: true
            tables:
              # REQUIRED: Supply the DynamoDB table ARN
              - table_arn: "{DDB_TABLE_ARN}"
                stream:
                  start_position: "LATEST"
                export:
                  # REQUIRED: Specify the name of an existing S3 bucket for DynamoDB to write export data files to
                  s3_bucket: "{S3BUCKET}"
                  # REQUIRED: Specify the region of the S3 bucket
                  s3_region: "{REGION}"
                  # Optionally set the name of a prefix that DynamoDB export data files are written to in the bucket.
                  s3_prefix: "pipeline"
            aws:
              # REQUIRED: Provide the role to assume that has the necessary permissions to DynamoDB, OpenSearch, and S3.
              sts_role_arn: "{ROLE}"
              # REQUIRED: Provide the region
              region: "{REGION}"
        sink:
          - opensearch:
              hosts:
                  # REQUIRED: Provide an AWS OpenSearch endpoint, including https://
                [
                  "{OS_DOMAIN_ENDPOINT}"
                ]
              index: "product-details-index-en"
              index_type: custom
              template_type: "index-template"
              template_content: |
                {
                  "template": {
                    "settings": {
                      "index.knn": true,
                      "default_pipeline": "product-en-nlp-ingest-pipeline"
                    },
                    "mappings": {
                      "properties": {
                        "ProductID": {
                          "type": "keyword"
                        },
                        "ProductName": {
                          "type": "text"
                        },
                        "Category": {
                          "type": "text"
                        },
                        "Description": {
                          "type": "text"
                        },
                        "Image": {
                           "type": "text"
                        },
                        "combined_field": {
                          "type": "text"
                        },
                        "product_embedding": {
                          "type": "knn_vector",
                          "dimension": 1536,
                          "method": {
                            "engine": "nmslib",
                            "name": "hnsw",
                            "space_type": "l2"
                          }
                        }
                      }
                    }
                  }
                }
              aws:
                # REQUIRED: Provide the role to assume that has the necessary permissions to DynamoDB, OpenSearch, and S3.
                sts_role_arn: "{ROLE}"
                # REQUIRED: Provide the region
                region: "{REGION}"
    ```
    
4. Under Network, select "Public access", then click "Next".
    
    ![Create pipeline](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl14.jpg)
    
5. Click "Create pipeline".
    
    ![Create pipeline](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl15.jpg)
    
6. **Wait until the pipeline has finished creating**. This will take 5 minutes or more.
    

After the pipeline is created, it will take some additional time for the initial export from DynamoDB and import into OpenSearch Service. After you have waited several more minutes, you can check if items have replicated into OpenSearch by making a query in Dev Tools in the OpenSearch Dashboards.

To open Dev Tools, click on the menu in the top left of OpenSearch Dashboards, scroll down to the section, then click on . Enter the following query in the left pane, then click the "play" arrow.`Management``Dev Tools`

```text
GET /product-details-index-en/_search
```

You may encounter a few types of results:

- If you see a 404 error of type _index_not_found_exception_, then you need to wait until the pipeline is . Once it is, this exception will go away.`Active`
- If your query does not have results, wait a few more minutes for the initial replication to finish and try again.

![Tạo quy trình](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl16.jpg)

Chỉ tiếp tục khi bạn thấy sự trở lại như trên, với một cơ thể phản hồi. Số lần truy cập của bạn có thể khác nhau.