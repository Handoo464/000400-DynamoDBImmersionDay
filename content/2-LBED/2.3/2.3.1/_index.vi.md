---
title : "Định cấu hình tích hợp"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.3.1. </b> "
---

Amazon Bedrock là một dịch vụ được quản lý toàn phần, cung cấp lựa chọn các mô hình nền tảng (FM) hiệu suất cao từ các công ty AI hàng đầu như AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI và Amazon thông qua một API duy nhất, cùng với một loạt các khả năng bạn cần để xây dựng các ứng dụng AI tổng quát với bảo mật, quyền riêng tư và AI có trách nhiệm.

Việc xây dựng yêu cầu đã ký sig-v4 yêu cầu mã thông báo phiên, khóa truy cập và khóa truy cập bí mật. Trước tiên, bạn sẽ truy xuất các dữ liệu này từ siêu dữ liệu Phiên bản Cloud9 bằng tập lệnh "credentials.sh" được cung cấp để xuất các giá trị bắt buộc sang các biến môi trường. Trong các bước sau, bạn cũng sẽ xuất các giá trị khác sang các biến môi trường để cho phép dễ dàng thay thế vào các lệnh được liệt kê.

1. Chạy tập lệnh credentials.sh để truy xuất và xuất thông tin đăng nhập. Các thông tin xác thực này sẽ được sử dụng để ký các yêu cầu API vào cụm OpenSearch. Lưu ý dấu "." đứng đầu trước "./credentials.sh", điều này phải được bao gồm để đảm bảo rằng thông tin đăng nhập đã xuất có sẵn trong trình bao hiện đang chạy.
    
    ```bash
    . ./credentials.sh 
    ```
    
1. Edit the credentials.sh

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
    
2. Tiếp theo, xuất một biến môi trường với URL điểm cuối OpenSearch. URL này được liệt kê trong tab Đầu ra ngăn xếp CloudFormation là "OSDomainEndpoint". Biến này sẽ được sử dụng trong các lệnh tiếp theo.
    
    ```bash
    export OPENSEARCH_ENDPOINT="https://search-ddb-os-xxxx-xxxxxxxxxxxxx.us-west-2.es.amazonaws.com"
    ```
    
3. Thực hiện lệnh curl sau để tạo trình kết nối mô hình OpenSearch ML.
    
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
    
4. Lưu ý "connector_id" được trả về trong lệnh trước. Xuất nó sang một biến môi trường để thay thế thuận tiện trong các lệnh trong tương lai.
    
    ```bash
    export CONNECTOR_ID='xxxxxxxxxxxxxx'
    ```
    
5. Chạy lệnh curl tiếp theo để tạo nhóm model.
    
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
    
6. Lưu ý "model_group_id" được trả về trong lệnh trước. Xuất nó sang một biến môi trường để thay thế sau này.
    
    ```bash
    export MODEL_GROUP_ID='xxxxxxxxxxxxx'
    ```
    
7. Lệnh curl tiếp theo đăng ký trình kết nối với nhóm model.
    
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
    
8. Lưu ý "model_id" và xuất nó.
    
    ```bash
    export MODEL_ID='xxxxxxxxxxxxx'
    ```
    
9. Chạy lệnh sau để xác minh rằng bạn đã xuất thành công trình kết nối, nhóm mô hình và id mô hình.
    
    ```bash
    echo -e "CONNECTOR_ID=${CONNECTOR_ID}\nMODEL_GROUP_ID=${MODEL_GROUP_ID}\nMODEL_ID=${MODEL_ID}"
    ```
    
10. Tiếp theo, chúng ta sẽ deploy model với curl sau.
    
    ```bash
    curl --request POST \
      ${OPENSEARCH_ENDPOINT}'/_plugins/_ml/models/'${MODEL_ID}'/_deploy' \
      --header 'Content-Type: application/json' \
      --header 'Accept: application/json' \
      --header "x-amz-security-token: ${METADATA_AWS_SESSION_TOKEN}" \
      --aws-sigv4 aws:amz:${METADATA_AWS_REGION}:es \
      --user "${METADATA_AWS_ACCESS_KEY_ID}:${METADATA_AWS_SECRET_ACCESS_KEY}"
    ```
    
    Với mô hình được tạo, OpenSearch giờ đây có thể sử dụng mô hình nhúng Titan của Bedrock để xử lý văn bản. Mô hình nhúng là một loại mô hình học máy chuyển đổi dữ liệu chiều cao (như văn bản hoặc hình ảnh) thành các vectơ chiều thấp hơn, được gọi là nhúng. Các vectơ này nắm bắt các mối quan hệ ngữ nghĩa hoặc ngữ cảnh giữa các điểm dữ liệu trong một biểu diễn dày đặc, nhỏ gọn hơn.
    
    Việc nhúng đại diện cho ý nghĩa ngữ nghĩa của dữ liệu đầu vào, trong trường hợp này là mô tả sản phẩm. Các từ có nghĩa tương tự được biểu diễn bằng các vectơ gần nhau trong không gian vector. Ví dụ, các vectơ cho "mạnh mẽ" và "mạnh mẽ" sẽ gần nhau hơn là "ấm".
    
11. Bây giờ chúng ta có thể kiểm tra mô hình. Nếu bạn nhận được kết quả trở lại với mã trạng thái "200", mọi thứ đều hoạt động bình thường.
    
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
    
12. Tiếp theo, chúng ta sẽ tạo Details table mapping pipeline.
    
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
    
13. Tiếp theo là quy trình ánh xạ bảng Đánh giá. Chúng tôi sẽ không sử dụng điều này trong phiên bản này của phòng thực hành, nhưng trong một hệ thống thực, bạn sẽ muốn giữ các chỉ mục nhúng của mình riêng biệt cho các truy vấn khác nhau.
    
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
    
    Các quy trình này cho phép OpenSearch xử lý trước và làm phong phú dữ liệu khi nó được ghi vào chỉ mục bằng cách thêm nhúng thông qua trình kết nối Bedrock.