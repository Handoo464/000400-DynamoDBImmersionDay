---
title : "Tạo VPC "
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> </b> "
---

{{%notice info%}}
Bạn có thể sử dụng Cloud9 hoặc Session Manager.
{{%/notice%}}

Trong bài lab này, bạn sẽ sử dụng các script Bash và Python để tương tác với các dịch vụ AWS. Chạy các lệnh sau trong terminal của bạn để tải xuống và giải nén mã của bài lab này.

```bash
cd ~/environment
curl -sL https://amazon-dynamodb-labs.com/assets/OpenSearchPipeline.zip -o OpenSearchPipeline.zip && unzip -oq OpenSearchPipeline.zip && rm OpenSearchPipeline.zip
```

Bạn sẽ thấy một thư mục trong trình khám phá tệp của AWS Cloud9 có tên **OpenSearchPipeline**:

Thư mục _OpenSearchPipeline_ chứa các mục ví dụ sẽ được tải vào một bảng DynamoDB, một script Bash để đơn giản hóa việc quản lý thông tin xác thực khi ký yêu cầu cho OpenSearch, và một script Python để thực hiện truy vấn đến Bedrock.

Bây giờ bạn đã sẵn sàng để bắt đầu lab. Trong module tiếp theo, bạn sẽ hoàn tất cài đặt cho từng dịch vụ trong số ba dịch vụ được sử dụng trong bài lab này trước khi tiến hành tích hợp chúng.