---
title : "Kích hoạt mô hình Amazon Bedrock"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2.2. </b> "
---

Amazon Bedrock là một dịch vụ được quản lý hoàn toàn, cung cấp các mô hình nền tảng (Foundation Models - FMs) hiệu suất cao từ các công ty AI hàng đầu như AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI và Amazon thông qua một API duy nhất, cùng với một loạt các khả năng bạn cần để xây dựng các ứng dụng AI tạo sinh với tính bảo mật, quyền riêng tư và AI có trách nhiệm.

Trong ứng dụng này, Bedrock sẽ được sử dụng để thực hiện các truy vấn đề xuất sản phẩm bằng ngôn ngữ tự nhiên, sử dụng OpenSearch Service như một cơ sở dữ liệu vector.

Bedrock yêu cầu các mô hình nền tảng (FMs) khác nhau phải được kích hoạt trước khi chúng được sử dụng.

1. Mở [Amazon Bedrock Model Access](https://us-west-2.console.aws.amazon.com/bedrock/home?region=us-west-2#/modelaccess)
    
2. Nhấp vào "Manage model access"
    
   ![Manage model access](/images/2/2.2/9.jpg)
    
3. Chọn "Titan Embeddings G1 - Text" và "Claude", sau đó nhấp vào `Request model access`
    
4. Chờ cho đến khi bạn được cấp quyền truy cập vào cả hai mô hình trước khi tiếp tục. Trạng thái _Access status_ phải hiển thị _Access granted_ trước khi chuyển sang bước tiếp theo.
    
{{%notice tip%}}
_Không tiếp tục trừ khi các mô hình cơ sở "Claude" và "Titan Embeddings G1 - Text" đã được cấp cho tài khoản của bạn._
{{%/notice%}}