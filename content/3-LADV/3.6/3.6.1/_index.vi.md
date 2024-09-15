---
title : "Bước 1 - Thêm global secondary index mới vào bảng nhân viên"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.6.1. </b> "
---

Thêm một global secondary index mới sử dụng thuộc tính `is_manager` làm khóa phân vùng cho chỉ mục phụ toàn cầu, được lưu trữ dưới thuộc tính có tên `GSI_2_PK`. Các chức danh công việc của nhân viên được lưu trữ dưới `GSI_2_SK`.

Chạy lệnh AWS CLI sau:

```bash
aws dynamodb update-table --table-name employees \
--attribute-definitions AttributeName=GSI_2_PK,AttributeType=S AttributeName=GSI_2_SK,AttributeType=S \
--global-secondary-index-updates file://gsi_manager.json
```

Đợi cho đến khi global secondary index được tạo. Việc này mất khoảng 5 phút.

Kiểm tra kết quả đầu ra của lệnh sau. Nếu `"IndexStatus"` là `"CREATING"`, bạn sẽ phải đợi cho đến khi `"IndexStatus"` là `"ACTIVE"` trước khi tiếp tục bước tiếp theo.

```bash
aws dynamodb describe-table --table-name employees --query "Table.GlobalSecondaryIndexes[].IndexStatus"
```

Kết quả đầu ra ban đầu sẽ trông như sau:

```json
[
   "CREATING", 
   "ACTIVE"
]
```

Bạn cũng có thể script lệnh này để chạy mỗi 2 giây bằng cách sử dụng `watch`.

```bash
# Watch mặc định kiểm tra mỗi 2 giây
watch -n 2 "aws dynamodb describe-table --table-name employees --query \"Table.GlobalSecondaryIndexes[].IndexStatus\""
```

Nhấn **Ctrl + C** để kết thúc `watch` sau khi global secondary index đã được tạo.

Đợi cho đến khi chỉ mục mới là `ACTIVE` trước khi tiếp tục.

```json
[
   "ACTIVE",
   "ACTIVE"
]
```

{{%notice warning%}}
Không tiếp tục cho đến khi `IndexStatus` là `ACTIVE` trên cả hai chỉ mục. Truy vấn chỉ mục trước khi nó `ACTIVE` sẽ dẫn đến lỗi truy vấn.
{{%/notice%}}