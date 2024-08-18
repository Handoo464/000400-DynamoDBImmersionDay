---
title : "Tải DynamoDB Data"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.2.3. </b> "
---

Amazon Bedrock là một dịch vụ được quản lý toàn phần, cung cấp lựa chọn các mô hình nền tảng (FM) hiệu suất cao từ các công ty AI hàng đầu như AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI và Amazon thông qua một API duy nhất, cùng với một loạt các khả năng bạn cần để xây dựng các ứng dụng AI tổng quát với bảo mật, quyền riêng tư và AI có trách nhiệm.


## Tải và xem lại dữ liệu


Quay lại IDE Cloud9. Nếu vô tình đóng IDE, bạn có thể tìm kiếm dịch vụ trong Bảng điều khiển quản lý AWS hoặc sử dụng URL Cloud9IDE có trong phần của ngăn xếp CloudFormation.`Outputs`

Tải dữ liệu mẫu vào Bảng DynamoDB của bạn.

```bash
cd ~/environment/OpenSearchPipeline
aws dynamodb batch-write-item --request-items=file://product_en.json
```

![Đầu ra CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl11.jpg)

Tiếp theo, điều hướng đến phần DynamoDB của Bảng điều khiển quản lý AWS và nhấp vào rồi chọn bảng. Đây là nơi thông tin sản phẩm cho bài tập này bắt nguồn. Xem lại tên sản phẩm để có ý tưởng về loại tìm kiếm ngôn ngữ tự nhiên mà bạn có thể muốn cung cấp sau này vào cuối phòng thí nghiệm.`Explore items``ProductDetails`

![Bảng điều khiển DynamoDB](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl19.jpg)