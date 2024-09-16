---
title : "Bước 4 - Bật luồng DynamoDB"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 3.9.4. </b> "
---

Bây giờ chúng ta đã tạo một hàm AWS Lambda để xử lý các bản ghi từ DynamoDB Streams, chúng ta cần kích hoạt DynamoDB Stream trên bảng `logfile`. Trong bước tiếp theo, chúng ta sẽ kết nối stream với hàm Lambda.

Chúng ta sẽ kích hoạt stream trên bảng `logfile`. Khi một stream được kích hoạt, bạn có thể chọn liệu DynamoDB sao chép mục mới, mục cũ, cả mục cũ và mới, hoặc chỉ khóa phân vùng và khóa sắp xếp của một mục đã được tạo, cập nhật, hoặc xóa. Để biết thêm thông tin về các tùy chọn khác nhau, [bạn có thể xem tài liệu về `StreamSpecification`](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_StreamSpecification.html), liệt kê các tùy chọn như sau: _NEW_IMAGE_, _OLD_IMAGE_, _NEW_AND_OLD_IMAGES_, hoặc _KEYS_ONLY_.

Kích hoạt DynamoDB Streams cho bảng `logfile` với tùy chọn _NEW_IMAGE_, bao gồm "Toàn bộ mục, như nó xuất hiện sau khi được sửa đổi, được ghi vào stream" theo tài liệu liên kết ở trên.

```bash
aws dynamodb update-table --table-name 'logfile' --stream-specification StreamEnabled=true,StreamViewType=NEW_IMAGE
```

Lấy ARN đầy đủ cho DynamoDB Streams trong kết quả. Chúng ta sẽ cần nó cho bước tiếp theo.

```bash
aws dynamodb describe-table --table-name 'logfile' --query 'Table.LatestStreamArn' --output text
```

Kết quả sẽ trông giống như sau:

```
arn:aws:dynamodb:<REGION>:<ACCOUNTID>:table/logfile/stream/2018-10-27T02:15:46.245
```

Ghi chú ARN này để sử dụng trong bước tiếp theo.