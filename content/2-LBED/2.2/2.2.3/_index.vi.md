---
title : "Tải DynamoDB Data"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.2.3. </b> "
---

Tiếp theo, bạn sẽ tải dữ liệu sản phẩm mẫu vào bảng DynamoDB của mình. Các pipeline sẽ chuyển dữ liệu này vào OpenSearch Service ở các bước sau.

## Tải và Xem xét Dữ liệu

Quay lại Cloud9 IDE. Nếu bạn vô tình đóng IDE, bạn có thể tìm kiếm dịch vụ trong AWS Management Console hoặc sử dụng URL Cloud9IDE được tìm thấy trong phần `Outputs` của CloudFormation stack.

Tải dữ liệu mẫu vào bảng DynamoDB của bạn.

```bash
cd ~/environment/OpenSearchPipeline
aws dynamodb batch-write-item --request-items=file://product_en.json
```

![CloudFormation Outputs](/images/2/2.2/10.jpg)

Tiếp theo, điều hướng đến phần DynamoDB của AWS Management Console và nhấp vào `Explore items`, sau đó chọn bảng `ProductDetails`. Đây là nơi mà thông tin sản phẩm cho bài tập này xuất phát. Xem qua tên các sản phẩm để có ý tưởng về loại truy vấn ngôn ngữ tự nhiên mà bạn có thể muốn thực hiện sau khi kết thúc lab.

![DynamoDB Console](/images/2/2.2/11.jpg)