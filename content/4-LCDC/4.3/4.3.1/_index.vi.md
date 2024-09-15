---
title : "Bật luồng DynamoDB"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 4.1 </b> "
---

Khi [kích hoạt DynamoDB streams](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html#Streams.Enabling) trên một bảng, bạn có thể chọn ghi lại một trong các tùy chọn sau:

- **Key attributes** chỉ - chỉ các thuộc tính khóa của mục đang được cập nhật
- **New image** - hình ảnh mới của một mục sau khi nó được cập nhật
- **Old image** - hình ảnh ban đầu của một mục trước khi nó được cập nhật
- **New and old images** - hình ảnh trước và sau của một mục sau khi cập nhật.

Vì yêu cầu cho kịch bản này là chỉ giữ bản hiện tại của các đơn hàng trên bảng Orders, bạn sẽ thiết lập DynamoDB stream cho bảng để chỉ lưu trữ **old image** của các mục khi thực hiện các cập nhật. **New image** không cần được ghi vào stream vì không cần thiết.

Kích hoạt DynamoDB streams trên bảng Orders bằng cách chạy lệnh AWS CLI dưới đây.

```bash
aws dynamodb update-table \
    --table-name Orders \
    --stream-specification \
        StreamEnabled=true,StreamViewType=OLD_IMAGE \
    --query "TableDescription.LatestStreamArn"
```

Xác nhận rằng DynamoDB streams đã được kích hoạt bằng cách sử dụng các lệnh AWS CLI dưới đây.

```bash
aws dynamodb describe-table \
    --table-name Orders \
    --query "Table.StreamSpecification.StreamEnabled"
```

Kết quả đầu ra sẽ trả về một giá trị boolean như dưới đây.

```
true
```