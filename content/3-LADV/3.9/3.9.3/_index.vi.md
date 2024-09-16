---
title : "Bước 3 - Tạo hàm Lambda"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.9.3. </b> "
---

Hàm AWS Lambda này sẽ được gắn vào DynamoDB Stream của bảng `logfile` để sao chép các thao tác thêm (put) và xóa (delete) mục vào bảng `logfile_replica`. Mã hàm Lambda đã được cung cấp cho bạn trong tệp `ddbreplica_lambda.py`. Bạn có thể xem nội dung của script nếu muốn với `vim` hoặc `less`.

Nén nội dung của script. Chúng ta sẽ tải nó lên AWS Lambda khi tạo hàm.

```bash
zip ddbreplica_lambda.zip ddbreplica_lambda.py lab_config.py
```

Lấy Amazon Resource Name (ARN) của vai trò IAM đã được tạo sẵn để bạn có thể liên kết nó với hàm Lambda. Chạy lệnh sau để lấy ARN của vai trò được tạo trong quá trình thiết lập lab.

```bash
cat ~/workshop/ddb-replication-role-arn.txt
```

Kết quả đầu ra sẽ trông giống như sau:

```txt
arn:aws:iam::<ACCOUNTID>:role/XXXXX-DDBReplicationRole-XXXXXXXXXXX
```

Bây giờ, chạy lệnh sau để tạo hàm Lambda.

```bash
aws lambda create-function \
--function-name ddbreplica_lambda --zip-file fileb://ddbreplica_lambda.zip \
--handler ddbreplica_lambda.lambda_handler --timeout 60 --runtime python3.7 \
--description "Sample lambda function for dynamodb streams" \
--role $(cat ~/workshop/ddb-replication-role-arn.txt)
```