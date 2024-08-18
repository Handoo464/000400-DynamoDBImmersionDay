---
title : "Tạo VPC "
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 2.1.1 </b> "
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

##### Bài Tập
Tìm tất cả các phản hồi được viết bởi **User A** và sắp xếp chúng lại, sử dụng lệnh **query** thay vì lệnh **scan**.
**Gợi ý:** Khi sử dụng GSI trong DynamoDB, bạn phải chỉ định rõ ràng cả tên bảng và tên chỉ mục.

Thử chạy lệnh **get-item** chống lại GSI. Tại sao điều này không hoạt động?
Giải pháp sẽ được mở rộng bên dưới nhưng hãy cố gắng tìm ra trước khi tiếp tục.
##### Giải pháp
1. Chạy một truy vấn trên GSI (Chỉ mục phụ toàn cầu) không khác gì so với việc chạy truy vấn trên bảng, ngoại trừ việc chúng ta cũng cần chỉ định chỉ mục nào sẽ sử dụng bằng tùy chọn `--index-name` và chúng ta sẽ sử dụng các thuộc tính khóa của GSI trong `KeyConditionExpression`.

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'PostedBy = :pb' \
    --expression-attribute-values '{
        ":pb" : {"S": "User A"}
    }' \
    --index-name PostedBy-ReplyDateTime-gsi \
    --return-consumed-capacity TOTAL
```

Lưu ý rằng trong đầu ra:

```bash
"Count": 3,
"ScannedCount": 3,
```

Câu truy vấn không thể tối ưu hơn thế này. Ngay cả khi bảng có một tỷ mục phản hồi được viết bởi các người dùng khác, câu truy vấn này chỉ tốn chi phí để đọc ba mục mà chúng ta hy vọng sẽ trả về (không giống như khi sử dụng lệnh quét - scan).

2. Trong bảng cơ bản, Khóa chính xác định duy nhất hàng, có nghĩa là yêu cầu `get-item` sẽ chỉ khớp với TỐI ĐA một mục. Vì chúng ta có thể chọn bất kỳ thuộc tính nào làm Khóa cho một GSI, nên không có đảm bảo rằng các khóa của GSI sẽ xác định duy nhất một mục. Do đó, DynamoDB ngăn không cho bạn thực hiện một yêu cầu `get-item` chống lại một GSI.
### Dọn dẹp
Khi bạn hoàn tất, hãy chắc chắn xóa GSI.

```bash
aws dynamodb update-table \
    --table-name Reply \
    --global-secondary-index-updates '[{
        "Delete":{
            "IndexName": "PostedBy-ReplyDateTime-gsi"
        }
    }]'
```