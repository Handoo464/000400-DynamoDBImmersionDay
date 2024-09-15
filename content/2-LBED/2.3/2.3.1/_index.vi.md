---
title : "Định cấu hình tích hợp"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.3.1. </b> "
---

Trong phần này, bạn sẽ cấu hình các kết nối ML và Pipeline trong OpenSearch Service. Các cấu hình này được thiết lập bằng một loạt các yêu cầu POST và PUT được xác thực bằng AWS Signature Version 4 (sig-v4). Sigv4 là cơ chế xác thực tiêu chuẩn được sử dụng bởi các dịch vụ AWS. Mặc dù trong hầu hết các trường hợp, một SDK sẽ tự động xử lý sig-v4, nhưng trong trường hợp này, chúng ta sẽ tự xây dựng các yêu cầu bằng curl.

Để tạo một yêu cầu có chữ ký sig-v4, bạn cần một mã thông báo phiên, khóa truy cập và khóa truy cập bí mật. Đầu tiên, bạn sẽ truy xuất các thông tin này từ metadata của Instance Cloud9 của mình bằng script "credentials.sh" đã cung cấp, script này sẽ xuất các giá trị cần thiết vào các biến môi trường. Trong các bước tiếp theo, bạn cũng sẽ xuất các giá trị khác vào các biến môi trường để dễ dàng thay thế trong các lệnh liệt kê.


1. Chỉnh sửa `credentials.sh`

Bạn có thể thực hiện chỉnh sửa trên Cloud9/Session manager/VSCode

Ở trên VSCode bạn cần đăng nhập bằng lệnh`sudo su` sau đó thực hiện lệnh `vim credentials.sh`. Với nội dung như dưới đây.

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
2. Chạy script `credentials.sh` để truy xuất và xuất các thông tin xác thực. Các thông tin xác thực này sẽ được sử dụng để ký các yêu cầu API tới cụm OpenSearch. Lưu ý dấu chấm (.) đứng trước "./credentials.sh", điều này phải được bao gồm để đảm bảo rằng các thông tin xác thực đã xuất sẽ có sẵn trong shell hiện đang chạy.

    ```bash
    . ./credentials.sh 
    ```

3. Tiếp theo, xuất một biến môi trường với URL endpoint OpenSearch. URL này được liệt kê trong tab Outputs của CloudFormation Stack với tên "OSDomainEndpoint". Biến này sẽ được sử dụng trong các lệnh tiếp theo.

    ```bash
    export OPENSEARCH_ENDPOINT="https://search-ddb-os-xxxx-xxxxxxxxxxxxx.us-west-2.es.amazonaws.com"
    ```

4. Thực thi lệnh curl sau để tạo kết nối mô hình ML cho OpenSearch.

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

5. Ghi lại "connector_id" trả về từ lệnh trước đó. Xuất nó vào một biến môi trường để dễ dàng thay thế trong các lệnh sau.

    ```bash
    export CONNECTOR_ID='xxxxxxxxxxxxxx'
    ```

6. Chạy lệnh curl tiếp theo để tạo nhóm mô hình (model group).

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

7. Ghi lại "model_group_id" trả về từ lệnh trước đó. Xuất nó vào một biến môi trường để sử dụng sau này.

    ```bash
    export MODEL_GROUP_ID='xxxxxxxxxxxxx'
    ```

8. Lệnh curl tiếp theo đăng ký kết nối với nhóm mô hình.

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

9. Ghi lại "model_id" và xuất nó.

    ```bash
    export MODEL_ID='xxxxxxxxxxxxx'
    ```

10. Chạy lệnh sau để xác minh rằng bạn đã xuất thành công connector, model group, và model id.

    ```bash
    echo -e "CONNECTOR_ID=${CONNECTOR_ID}\nMODEL_GROUP_ID=${MODEL_GROUP_ID}\nMODEL_ID=${MODEL_ID}"
    ```

11. Tiếp theo, chúng ta sẽ triển khai mô