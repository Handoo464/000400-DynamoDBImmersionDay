---
title : "LBED: Generative AI với rích hợp DynamoDB zero-ETL vào OpenSearch và Amazon Bedrock"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2. </b> "
---

Trong workshop này, bạn sẽ có trải nghiệm thực hành thiết lập tích hợp DynamoDB với Amazon OpenSearch Service mà không cần ETL (zero-ETL) để hỗ trợ truy vấn ngôn ngữ tự nhiên của một danh mục sản phẩm. Bạn sẽ tạo một pipeline từ bảng DynamoDB đến OpenSearch Service, tạo một Amazon Bedrock Connector trong OpenSearch Service, và truy vấn Bedrock bằng cách sử dụng OpenSearch Service làm vector store. Cuối buổi học này, bạn sẽ tự tin trong khả năng tích hợp DynamoDB với OpenSearch Service để hỗ trợ các ứng dụng suy luận có ý thức ngữ cảnh.

Kết hợp Amazon DynamoDB với Amazon OpenSearch Service là một mẫu kiến trúc phổ biến cho các ứng dụng cần kết hợp khả năng mở rộng cao và hiệu suất của DynamoDB cho khối lượng công việc giao dịch với các khả năng tìm kiếm và phân tích mạnh mẽ của OpenSearch.

DynamoDB là một cơ sở dữ liệu NoSQL được thiết kế để có tính sẵn sàng cao, hiệu suất cao và khả năng mở rộng, tập trung vào các hoạt động khóa/giá trị. OpenSearch Service cung cấp các tính năng tìm kiếm nâng cao như tìm kiếm toàn văn, tìm kiếm theo khía cạnh và khả năng truy vấn phức tạp. Khi kết hợp, hai dịch vụ này có thể đáp ứng nhiều trường hợp sử dụng ứng dụng khác nhau.

Workshop này sẽ cho phép bạn thiết lập một trường hợp sử dụng như vậy. DynamoDB sẽ là nguồn dữ liệu chính xác cho thông tin danh mục sản phẩm và OpenSearch sẽ cung cấp khả năng tìm kiếm vector để cho phép Amazon Bedrock (một dịch vụ AI tạo sinh) đưa ra các đề xuất sản phẩm.

{{%notice warning%}}
_Lab này tạo ra các tài nguyên OpenSearch Service, DynamoDB, và Secrets Manager. Nếu bạn chạy trong tài khoản riêng của mình, các tài nguyên này sẽ phát sinh chi phí khoảng 30 USD mỗi tháng. Hãy nhớ xóa CloudFormation Stack sau khi hoàn thành lab._
{{%/notice%}}

![Final Deployment Architecture](/images/2/1.png)