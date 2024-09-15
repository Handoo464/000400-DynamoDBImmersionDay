---
title : "Step 1 - Create a new global secondary index for City-Department"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.7.1. </b> "
---

Chạy lệnh sau để tạo một global secondary index mới có tên là `GSI_3`:

```bash
aws dynamodb update-table --table-name employees \
--attribute-definitions AttributeName=GSI_3_PK,AttributeType=S AttributeName=GSI_3_SK,AttributeType=S \
--global-secondary-index-updates file://gsi_city_dept.json
```

Bạn phải chờ cho đến khi chỉ mục được tạo xong trước khi tiếp tục.

Lưu ý rằng lệnh sau sẽ hiển thị trạng thái của global secondary index là `CREATING`.

```bash
aws dynamodb describe-table --table-name employees --query "Table.GlobalSecondaryIndexes[].IndexStatus"
```

Kết quả đầu ra trông tương tự như sau. Thứ tự của các trạng thái bảng có thể khác nhau.

```json
[
   "ACTIVE",
   "ACTIVE",
   "CREATING"
]
```

Bạn có thể script lệnh này để chạy mỗi hai giây bằng cách sử dụng `watch`, để bạn dễ dàng thấy khi trạng thái bảng đã thay đổi thành `ACTIVE`.

```bash
# Watch mặc định kiểm tra mỗi hai giây
watch -n 2 "aws dynamodb describe-table --table-name employees --query \"Table.GlobalSecondaryIndexes[].IndexStatus\""
```

_Sử dụng Ctrl + C để kết thúc `watch` sau khi global secondary index đã được tạo._

Chờ cho đến khi chỉ mục mới là `ACTIVE` trước khi tiếp tục.

```json
[
   "ACTIVE",
   "ACTIVE",
   "ACTIVE"
]
```

Bây giờ bạn có thể sử dụng global secondary index mới để truy vấn bảng. Bạn phải sử dụng khóa phân vùng, và bạn có thể sử dụng khóa sắp xếp (nhưng không bắt buộc).

Đối với khóa sắp xếp, bạn có thể sử dụng biểu thức `begins_with` để truy vấn bắt đầu với thuộc tính bên trái nhất của khóa tổng hợp. Kết quả là, bạn có thể truy vấn tất cả nhân viên trong một thành phố hoặc trong một phòng ban cụ thể ở một thành phố.

`KeyConditionExpression` sẽ trông như sau:

```py
Key('GSI_3_PK').eq("state#{}".format('TX')) & Key('GSI_3_SK').begins_with('Austin')
```

{{%notice warning%}}
Chờ cho đến khi `IndexStatus` là `ACTIVE` trên tất cả các chỉ mục trước khi tiếp tục. Nếu bạn cố gắng truy vấn một GSI nhưng nó chưa hoàn thành việc tạo, bạn sẽ nhận được lỗi.
{{%/notice%}}