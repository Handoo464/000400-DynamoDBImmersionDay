---
title : "Query and Conclusion"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.4. </b> "
---

Amazon Bedrock là một dịch vụ được quản lý toàn phần, cung cấp lựa chọn các mô hình nền tảng (FM) hiệu suất cao từ các công ty AI hàng đầu như AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI và Amazon thông qua một API duy nhất, cùng với một loạt các khả năng bạn cần để xây dựng các ứng dụng AI tổng quát với bảo mật, quyền riêng tư và AI có trách nhiệm.

Truy vấn này sẽ sử dụng OpenSearch làm cơ sở dữ liệu vector để tìm sản phẩm phù hợp nhất với ý định mong muốn của bạn. Nội dung của chỉ mục OpenSearch được tạo thông qua trình kết nối DynamoDB Zero ETL. Khi bản ghi được thêm vào DynamoDB, trình kết nối sẽ tự động di chuyển chúng vào OpenSearch. OpenSearch sau đó sử dụng mô hình Titan Embeddings để trang trí dữ liệu đó.

Tập lệnh xây dựng một truy vấn tìm kiếm chỉ mục OpenSearch cho các sản phẩm có liên quan nhất đến văn bản nhập của bạn. Điều này được thực hiện bằng cách sử dụng truy vấn "thần kinh", tận dụng các nhúng được lưu trữ trong OpenSearch để tìm các sản phẩm có nội dung văn bản tương tự. Sau khi truy xuất các sản phẩm có liên quan, kịch bản sử dụng Bedrock để tạo ra phản hồi tinh vi hơn thông qua mô hình Claude. Điều này liên quan đến việc tạo một lời nhắc kết hợp truy vấn ban đầu của bạn với dữ liệu đã truy xuất và gửi lời nhắc này đến Bedrock để xử lý.

1. Quay lại Bảng điều khiển Cloud9 IDE.
    
2. Trước tiên, hãy gửi yêu cầu trực tiếp đến DynamoDB
    
    ```bash
      aws dynamodb get-item \
          --table-name ProductDetails \
          --key '{"ProductID": {"S": "S020"}}'
    ```
    
    Đây là ví dụ về tra cứu khóa/giá trị mà DynamoDB vượt trội. Nó trả về chi tiết sản phẩm cho một sản phẩm cụ thể, được xác định bằng ProductID của sản phẩm đó.
    
3. Tiếp theo, hãy thực hiện một truy vấn tìm kiếm đến OpenSearch. Chúng tôi sẽ tìm thấy váy bao gồm "Spandex" trong mô tả của họ.
    
    ```bash
    curl --request POST \
      ${OPENSEARCH_ENDPOINT}/product-details-index-en/_search \
      --header 'Content-Type: application/json' \
      --header 'Accept: application/json' \
      --header "x-amz-security-token: ${METADATA_AWS_SESSION_TOKEN}" \
      --aws-sigv4 aws:amz:${METADATA_AWS_REGION}:es \
      --user "${METADATA_AWS_ACCESS_KEY_ID}:${METADATA_AWS_SECRET_ACCESS_KEY}" \
      --data-raw '{
        "_source": {
          "excludes": ["product_embedding"]
        },
        "query": {
          "bool": {
            "must": [
              {
                "match": {
                  "Category": "Skirt"
                }
              },
              {
                "match_phrase": {
                  "Description": "Spandex"
                }
              }
            ]
          }
        }
      }' | jq .
    ```
    
    Hãy thử thay đổi và xem kết quả thay đổi như thế nào.`Spandex``Polyester`
    
4. Cuối cùng, hãy yêu cầu Bedrock cung cấp một số đề xuất sản phẩm bằng cách sử dụng một trong những kịch bản được cung cấp trong phòng thí nghiệm.
    
    Truy vấn này sẽ sử dụng OpenSearch làm cơ sở dữ liệu vector để tìm sản phẩm phù hợp nhất với ý định mong muốn của bạn. Nội dung của chỉ mục OpenSearch được tạo thông qua trình kết nối DynamoDB Zero ETL. Khi bản ghi được thêm vào DynamoDB, trình kết nối sẽ tự động di chuyển chúng vào OpenSearch. OpenSearch sau đó sử dụng mô hình Titan Embeddings để trang trí dữ liệu đó.
    
    Tập lệnh xây dựng một truy vấn tìm kiếm chỉ mục OpenSearch cho các sản phẩm có liên quan nhất đến văn bản nhập của bạn. Điều này được thực hiện bằng cách sử dụng truy vấn "thần kinh", tận dụng các nhúng được lưu trữ trong OpenSearch để tìm các sản phẩm có nội dung văn bản tương tự. Sau khi truy xuất các sản phẩm có liên quan, kịch bản sử dụng Bedrock để tạo ra phản hồi tinh vi hơn thông qua mô hình Claude. Điều này liên quan đến việc tạo một lời nhắc kết hợp truy vấn ban đầu của bạn với dữ liệu đã truy xuất và gửi lời nhắc này đến Bedrock để xử lý.
    
    Trong bảng điều khiển, thực thi tập lệnh python được cung cấp để thực hiện truy vấn đến Bedrock và trả về kết quả sản phẩm.
    
    ```bash
    python bedrock_query.py product_recommend en "I need a warm winter coat" $METADATA_AWS_REGION $OPENSEARCH_ENDPOINT $MODEL_ID | jq .
    ```
    
    ![Kết quả truy vấn](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl17.jpg)
    
5. Thử thêm một mục mới vào bảng DynamoDB của bạn.
    
    ```bash
    aws dynamodb put-item \
        --table-name ProductDetails \
        --item '{
            "ProductID": {"S": "S021"},
            "Category": {"S": "Socks"},
            "Description": {"S": "{\"Style\": \"Outdoor\", \"Pattern\": \"Striped\", \"Length\": \"Knee-High\", \"Type\": \"Thick\", \"Fabric\": \"Wool\", \"Composition\": \"80% Wool, 20% Nylon\", \"Care Instructions\": \"Hand wash cold, lay flat to dry\", \"Ideal For\": \"Outdoor Activities\", \"Stretch\": \"Moderate\", \"Opacity\": \"Opaque\", \"Lining\": \"No\", \"Pockets\": \"No Pockets\", \"Closure\": \"Pull Up\", \"Shoe Height\": \"Knee-High\", \"Occasion\": \"Outdoor\", \"Season\": \"Fall, Winter\"}"},
            "Image": {"S": "https://example.com/S021.jpg"},
            "ProductName": {"S": "Striped Wool Knee-High Socks"}
        }'
    ```
    
6. Hãy thử sửa đổi mục tải DynamoDB ở trên để truy xuất mục mới của bạn. Tiếp theo, hãy thử sửa đổi truy vấn OpenSearch để tìm kiếm "Socks" có chứa "Wool". Cuối cùng, nói với Bedrock "Tôi cần vớ ấm để đi bộ đường dài vào mùa đông". Nó có giới thiệu mặt hàng mới của bạn không?
    
{{%notice tip%}}
Tiếp tục truy vấn!
Đừng chỉ dừng lại ở đó với các truy vấn của bạn. Thử yêu cầu quần áo cho mùa đông (nó sẽ giới thiệu các sản phẩm có len?) hoặc trước khi đi ngủ. Lưu ý rằng có một danh mục sản phẩm rất nhỏ cần được nhúng, vì vậy cụm từ tìm kiếm của bạn nên bị giới hạn dựa trên những gì bạn thấy khi đánh giá bảng DynamoDB.
{{%/notice%}}

Chúc mừng! Bạn đã hoàn thành phòng thí nghiệm.

{{%notice warning%}}
_Nếu chạy trong tài khoản của riêng bạn, hãy nhớ xóa CloudFormation Stack sau khi hoàn thành phòng thực hành để tránh các khoản phí không mong muốn._
{{%/notice%}}