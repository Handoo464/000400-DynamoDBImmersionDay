---
title : "Global Secondary Indexes"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 1.2.7. </b> "
---

Chúng ta đã chú ý đến việc truy cập dữ liệu dựa trên các thuộc tính khóa cho đến nay. Nếu chúng ta muốn tìm kiếm các mục dựa trên các thuộc tính không phải khóa, chúng ta phải thực hiện quét toàn bộ bảng và sử dụng điều kiện lọc để tìm những gì chúng ta muốn, điều này vừa chậm vừa tốn kém cho các hệ thống hoạt động ở quy mô lớn.

DynamoDB cung cấp một tính năng gọi là **Global Secondary Indexes (GSIs)**, cho phép tự động chuyển đổi dữ liệu của bạn xung quanh các Khóa Phân vùng và Khóa Sắp xếp khác nhau. Dữ liệu có thể được nhóm lại và sắp xếp lại để cho phép nhiều mẫu truy cập hơn nhanh chóng được phục vụ thông qua các API **Query** và **Scan**.

Nhớ lại ví dụ trước đó, nơi chúng ta muốn tìm tất cả các phản hồi trong bảng **Reply** được viết bởi **User A**:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --return-consumed-capacity TOTAL
```

Khi thực hiện lệnh quét đó, chúng ta có thể thấy rằng số Count trả về khác với ScannedCount. Nếu có một tỷ mục **Reply** nhưng chỉ ba trong số đó được viết bởi **User A**, chúng ta sẽ phải trả phí (cả về thời gian và tiền bạc) để quét qua một tỷ mục chỉ để tìm ba mục mà chúng ta muốn.

Với kiến thức về GSIs, chúng ta giờ đây có thể tạo một GSI trên bảng **Reply** để phục vụ mẫu truy cập mới này. GSIs có thể được tạo và xóa bất kỳ lúc nào, ngay cả khi bảng đã có dữ liệu! GSI mới này sẽ sử dụng thuộc tính **PostedBy** làm khóa Phân vùng (HASH) và chúng ta vẫn giữ các tin nhắn được sắp xếp theo **ReplyDateTime** làm khóa Sắp xếp (RANGE). Chúng ta muốn tất cả các thuộc tính từ bảng được sao chép (dự đoán) vào GSI, vì vậy chúng ta sẽ sử dụng **ALL ProjectionType**. Lưu ý rằng tên của chỉ mục chúng ta tạo là **PostedBy-ReplyDateTime-gsi**.

```bash
aws dynamodb update-table \
    --table-name Reply \
    --attribute-definitions AttributeName=PostedBy,AttributeType=S AttributeName=ReplyDateTime,AttributeType=S \
    --global-secondary-index-updates '[{
        "Create":{
            "IndexName": "PostedBy-ReplyDateTime-gsi",
            "KeySchema": [
                {
                    "AttributeName" : "PostedBy",
                    "KeyType": "HASH"
                },
                {
                    "AttributeName" : "ReplyDateTime",
                    "KeyType": "RANGE"
                }
            ],
            "ProvisionedThroughput": {
                "ReadCapacityUnits": 5, "WriteCapacityUnits": 5
            },
            "Projection": {
                "ProjectionType": "ALL"
            }
        }
    }]'
```

Có thể mất một chút thời gian trong khi DynamoDB tạo GSI và sao chép dữ liệu từ bảng vào chỉ mục. Chúng ta có thể theo dõi điều này từ dòng lệnh và đợi cho đến khi **IndexStatus** trở thành **ACTIVE**:

```bash
# Lấy trạng thái ban đầu
aws dynamodb describe-table --table-name Reply --query "Table.GlobalSecondaryIndexes[0].IndexStatus"
# Theo dõi trạng thái với lệnh chờ (sử dụng Ctrl+C để thoát):
watch -n 5 "aws dynamodb describe-table --table-name Reply --query "Table.GlobalSecondaryIndexes[0].IndexStatus""
```

Khi GSI đã trở thành **ACTIVE**, hãy tiếp tục với bài tập dưới đây. Sử dụng Ctrl+C để thoát lệnh theo dõi.

