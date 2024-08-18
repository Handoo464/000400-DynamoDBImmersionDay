---
title : "LBED: Generative AI with DynamoDB zero-ETL to OpenSearch integration and Amazon Bedrock"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2. </b> "
---

Trong hội thảo này, bạn sẽ có kinh nghiệm thực tế thiết lập tích hợp zero-ETL của DynamoDB với Amazon OpenSearch Service để hỗ trợ truy vấn ngôn ngữ tự nhiên của danh mục sản phẩm. Bạn sẽ tạo kênh dẫn từ bảng DynamoDB đến OpenSearch Service, tạo Amazon Bedrock Connector trong OpenSearch Service và truy vấn Bedrock tận dụng OpenSearch Service làm kho lưu trữ vector. Vào cuối bài học này, bạn sẽ cảm thấy tự tin vào khả năng tích hợp DynamoDB với OpenSearch Service để hỗ trợ các ứng dụng suy luận nhận biết ngữ cảnh.

Ghép nối Amazon DynamoDB với Amazon OpenSearch Service là một mẫu kiến trúc phổ biến cho các ứng dụng cần kết hợp khả năng thay đổi quy mô và hiệu năng cao của DynamoDB cho khối lượng công việc giao dịch với khả năng tìm kiếm và phân tích mạnh mẽ của OpenSearch.

DynamoDB là cơ sở dữ liệu NoSQL được thiết kế để có độ sẵn sàng, hiệu năng và khả năng thay đổi quy mô cao, đồng thời tập trung vào các hoạt động khóa/giá trị. OpenSearch Service cung cấp các tính năng tìm kiếm nâng cao như tìm kiếm toàn văn bản, tìm kiếm theo khía cạnh và khả năng truy vấn phức tạp. Kết hợp lại, hai dịch vụ này có thể đáp ứng nhiều trường hợp sử dụng ứng dụng khác nhau.

Hội thảo này sẽ cho phép bạn thiết lập một trường hợp sử dụng như vậy. DynamoDB sẽ là nguồn thông tin xác thực cho danh mục sản phẩm và OpenSearch sẽ cung cấp khả năng tìm kiếm vector để cho phép Amazon Bedrock (một dịch vụ AI tổng quát) đưa ra các đề xuất sản phẩm.

{{%notice warning%}}
_Phòng thực hành này tạo các tài nguyên OpenSearch Service, DynamoDB và Secrets Manager. Nếu chạy trong tài khoản của riêng bạn, các tài nguyên này sẽ phải chịu phí khoảng 30 đô la một tháng. Hãy nhớ xóa CloudFormation Stack sau khi hoàn thành phòng thực hành._
{{%/notice%}}


![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl.png)