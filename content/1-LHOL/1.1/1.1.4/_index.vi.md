---
title : "Xóa tài nguyên"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 1.1.4. </b> "
---

Nếu bạn đã sử dụng tài khoản của riêng mình, vui lòng xóa các tài nguyên sau:
- Bốn bảng DynamoDB đã được tạo trong phần Bắt đầu của lab:
```bash
aws dynamodb delete-table \
    --table-name ProductCatalog

aws dynamodb delete-table \
    --table-name Forum

aws dynamodb delete-table \
    --table-name Thread

aws dynamodb delete-table \
    --table-name Reply
```

- Mẫu CloudFormation đã được khởi động trong phần bắt đầu. Điều hướng đến bảng điều khiển CloudFormation, chọn stack `amazon-dynamodb-labs` và nhấp vào `Delete`.

![](/images/1/9.png)

Điều này sẽ hoàn tất quá trình dọn dẹp.
