---
title : "Truy vấn và kết luận"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.4. </b> "
---

Giờ đây, bạn đã tạo tất cả các kết nối và pipeline cần thiết, và dữ liệu đã được sao chép từ DynamoDB vào OpenSearch Service, bạn có nhiều lựa chọn để truy vấn dữ liệu của mình. Bạn có thể thực hiện tra cứu key/value trực tiếp với DynamoDB, thực hiện các truy vấn tìm kiếm trên OpenSearch và sử dụng Bedrock cùng với OpenSearch để đề xuất sản phẩm bằng ngôn ngữ tự nhiên.

Truy vấn này sẽ sử dụng OpenSearch như một cơ sở dữ liệu vector để tìm sản phẩm phù hợp nhất với ý định của bạn. Nội dung của chỉ mục OpenSearch được tạo thông qua kết nối DynamoDB Zero ETL. Khi các bản ghi được thêm vào DynamoDB, kết nối này tự động chuyển chúng vào OpenSearch. OpenSearch sau đó sử dụng mô hình Titan Embeddings để bổ sung dữ liệu đó.

Script này xây dựng một truy vấn tìm kiếm chỉ mục OpenSearch để tìm các sản phẩm phù hợp nhất với văn bản đầu vào của bạn. Điều này được thực hiện bằng truy vấn "neural", sử dụng các embeddings được lưu trữ trong OpenSearch để tìm các sản phẩm có nội dung văn bản tương tự. Sau khi truy xuất các sản phẩm liên quan, script sử dụng Bedrock để tạo ra một phản hồi tinh vi hơn thông qua mô hình Claude. Điều này bao gồm việc tạo ra một prompt kết hợp truy vấn ban đầu của bạn với dữ liệu đã truy xuất và gửi prompt này đến Bedrock để xử lý.

1. Quay lại Cloud9 IDE Console.
    
2. Đầu tiên, chúng ta sẽ thực hiện một yêu cầu trực tiếp đến DynamoDB
    
    ```bash
    aws dynamodb get-item \
        --table-name ProductDetails \
        --key '{"ProductID": {"S": "S020"}}'
    ```
    
    Đây là ví dụ về tra cứu key/value mà DynamoDB rất xuất sắc. Nó trả về chi tiết sản phẩm cho một sản phẩm cụ thể, được xác định bằng ProductID của nó.
    
3. Tiếp theo, chúng ta sẽ thực hiện một truy vấn tìm kiếm đến OpenSearch. Chúng ta sẽ tìm những chiếc váy có chứa "Spandex" trong mô tả.

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
    
    Hãy thử thay đổi `Spandex` thành `Polyester` và xem kết quả thay đổi như thế nào.
    
4. Cuối cùng, chúng ta sẽ yêu cầu Bedrock cung cấp một số đề xuất sản phẩm bằng cách sử dụng một trong các script đã cung cấp trong lab.

    Truy vấn này sẽ sử dụng OpenSearch như một cơ sở dữ liệu vector để tìm sản phẩm phù hợp nhất với ý định của bạn. Nội dung của chỉ mục OpenSearch được tạo thông qua kết nối DynamoDB Zero ETL. Khi các bản ghi được thêm vào DynamoDB, kết nối này tự động chuyển chúng vào OpenSearch. OpenSearch sau đó sử dụng mô hình Titan Embeddings để bổ sung dữ liệu đó.

    Script này xây dựng một truy vấn tìm kiếm chỉ mục OpenSearch để tìm các sản phẩm phù hợp nhất với văn bản đầu vào của bạn. Điều này được thực hiện bằng truy vấn "neural", sử dụng các embeddings được lưu trữ trong OpenSearch để tìm các sản phẩm có nội dung văn bản tương tự. Sau khi truy xuất các sản phẩm liên quan, script sử dụng Bedrock để tạo ra một phản hồi tinh vi hơn thông qua mô hình Claude. Điều này bao gồm việc tạo ra một prompt kết hợp truy vấn ban đầu của bạn với dữ liệu đã truy xuất và gửi prompt này đến Bedrock để xử lý.

    Trong console, thực hiện script Python được cung cấp để thực hiện truy vấn đến Bedrock và trả về kết quả sản phẩm.

    ```bash
    python bedrock_query.py product_recommend en "I need a warm winter coat" $METADATA_AWS_REGION $OPENSEARCH_ENDPOINT $MODEL_ID | jq .
    ```
    
   ![Kết quả truy vấn](/images/2/2.4/1.jpg)
    
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
    
6. Thử sửa đổi lệnh DynamoDB `get-item` ở trên để truy xuất mục mới của bạn. Tiếp theo, thử sửa đổi truy vấn OpenSearch để tìm kiếm "Socks" có chứa "Wool". Cuối cùng, yêu cầu Bedrock "I need warm socks for hiking in winter". Liệu nó có đề xuất mục mới của bạn không?
    
{{%notice tip%}}
Tiếp tục truy vấn!
Đừng dừng lại ở đây với các truy vấn của bạn. Hãy thử yêu cầu quần áo cho mùa đông (liệu nó có đề xuất các sản phẩm có len không?) hoặc cho giờ đi ngủ. Lưu ý rằng có một danh mục sản phẩm rất nhỏ để nhúng, vì vậy thuật ngữ tìm kiếm của bạn nên giới hạn dựa trên những gì bạn đã thấy khi xem bảng DynamoDB.
{{%/notice%}}

Chúc mừng! Bạn đã hoàn thành bài lab.
{{%notice warning%}}
_Nếu bạn đang chạy trên tài khoản của riêng mình, hãy nhớ xóa CloudFormation Stack sau khi hoàn thành bài lab để tránh các chi phí không mong muốn._
{{%/notice%}}