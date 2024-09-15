---
title : "Tạo Zero-ETL Pipeline"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.3.2. </b> "
---

Amazon DynamoDB cung cấp tích hợp zero-ETL với Amazon OpenSearch Service thông qua plugin DynamoDB cho OpenSearch Ingestion. Amazon OpenSearch Ingestion cung cấp một trải nghiệm không cần mã (no-code) được quản lý hoàn toàn để nhập dữ liệu vào Amazon OpenSearch Service.

1. Mở [OpenSearch Service Ingestion Pipelines](https://us-west-2.console.aws.amazon.com/aos/home?region=us-west-2#opensearch/ingestion-pipelines)
    
2. Nhấp vào "Create pipeline"
    
   ![Create pipeline](/images/2/2.3/1.jpg)
    
3. Đặt tên cho pipeline của bạn và bao gồm cấu hình sau cho pipeline. Cấu hình này chứa nhiều giá trị cần được cập nhật. Các giá trị cần thiết được cung cấp trong phần Outputs của CloudFormation Stack với tên "Region", "Role", "S3Bucket", "DdbTableArn", và "OSDomainEndpoint".

    ```yaml
      version: "2"
      dynamodb-pipeline:
        source:
          dynamodb:
            acknowledgments: true
            tables:
              # BẮT BUỘC: Cung cấp ARN của bảng DynamoDB
              - table_arn: "{DDB_TABLE_ARN}"
                stream:
                  start_position: "LATEST"
                export:
                  # BẮT BUỘC: Chỉ định tên của một S3 bucket đã tồn tại để DynamoDB ghi các tệp dữ liệu xuất vào
                  s3_bucket: "{S3BUCKET}"
                  # BẮT BUỘC: Chỉ định khu vực của S3 bucket
                  s3_region: "{REGION}"
                  # Tùy chọn thiết lập tên của một tiền tố mà các tệp dữ liệu xuất của DynamoDB được ghi vào trong bucket.
                  s3_prefix: "pipeline"
            aws:
              # BẮT BUỘC: Cung cấp vai trò có các quyền cần thiết cho DynamoDB, OpenSearch và S3.
              sts_role_arn: "{ROLE}"
              # BẮT BUỘC: Cung cấp khu vực
              region: "{REGION}"
        sink:
          - opensearch:
              hosts:
                  # BẮT BUỘC: Cung cấp một endpoint AWS OpenSearch, bao gồm cả https://
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
                # BẮT BUỘC: Cung cấp vai trò có các quyền cần thiết cho DynamoDB, OpenSearch và S3.
                sts_role_arn: "{ROLE}"
                # BẮT BUỘC: Cung cấp khu vực
                region: "{REGION}"
    ```

4. Dưới phần Network, chọn "Public access", sau đó nhấp vào "Next".
    
   ![Create pipeline](/images/2/2.3/2.jpg)
    
5. Nhấp vào "Create pipeline".
    
   ![Create pipeline](/images/2/2.3/3.jpg)
    
6. **Chờ cho đến khi pipeline được tạo xong**. Điều này sẽ mất khoảng 5 phút hoặc hơn.

Sau khi pipeline được tạo, sẽ mất thêm một thời gian để thực hiện xuất ban đầu từ DynamoDB và nhập vào OpenSearch Service. Sau khi bạn chờ thêm vài phút, bạn có thể kiểm tra xem các mục đã được nhân bản vào OpenSearch hay chưa bằng cách thực hiện truy vấn trong Dev Tools trên OpenSearch Dashboards.

Để mở Dev Tools, nhấp vào menu ở góc trên bên trái của OpenSearch Dashboards, cuộn xuống phần `Management`, sau đó nhấp vào `Dev Tools`. Nhập truy vấn sau vào ngăn bên trái, sau đó nhấp vào mũi tên "play".

```text
GET /product-details-index-en/_search
```

Bạn có thể gặp phải một số loại kết quả:

- Nếu bạn thấy lỗi 404 loại _index_not_found_exception_, thì bạn cần đợi cho đến khi pipeline ở trạng thái `Active`. Khi đó, lỗi này sẽ biến mất.
- Nếu truy vấn của bạn không có kết quả, hãy chờ thêm vài phút cho quá trình nhân bản ban đầu hoàn tất và thử lại.

![Create pipeline](/images/2/2.3/4.jpg)

Chỉ tiếp tục khi bạn thấy kết quả như trên, với nội dung phản hồi. Kết quả tìm kiếm có thể khác nhau.