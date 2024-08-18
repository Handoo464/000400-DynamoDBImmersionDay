---
title : "Kích hoạt mô hình Amazon Bedrock"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2.2. </b> "
---

Amazon Bedrock là một dịch vụ được quản lý toàn phần, cung cấp lựa chọn các mô hình nền tảng (FM) hiệu suất cao từ các công ty AI hàng đầu như AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI và Amazon thông qua một API duy nhất, cùng với một loạt các khả năng bạn cần để xây dựng các ứng dụng AI tổng quát với bảo mật, quyền riêng tư và AI có trách nhiệm.

Trong ứng dụng này, Bedrock sẽ được sử dụng để thực hiện các truy vấn đề xuất sản phẩm ngôn ngữ tự nhiên bằng cách sử dụng OpenSearch Service làm cơ sở dữ liệu vector.

Bedrock yêu cầu các FM khác nhau được kích hoạt trước khi chúng được sử dụng.

1. Mở [Quyền truy cập mô hình Amazon Bedrock](https://us-west-2.console.aws.amazon.com/bedrock/home?region=us-west-2#/modelaccess) 
    
2. Nhấp vào "Quản lý quyền truy cập mô hình"
    
    ![Quản lý quyền truy cập mô hình](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/ddb-os-zetl10.jpg)
    
3. Chọn "Titan Embeddings G1 - Text" và "Claude", sau đó nhấp vào `Request model access`
    
4. Đợi cho đến khi bạn được cấp quyền truy cập vào cả hai mô hình trước khi tiếp tục. _Trạng thái Truy cập_ phải cho biết _Quyền truy cập được cấp_ trước khi tiếp tục.
    
{{%notice tip%}}
_Không tiếp tục trừ khi các mô hình cơ sở "Claude" và "Titan Embeddings G1 - Văn bản" được cấp cho tài khoản của bạn._
{{%/notice%}}