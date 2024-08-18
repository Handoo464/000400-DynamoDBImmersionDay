---
title : "Thêm/Sửa dữ liệu"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.2.4. </b> "
---


## Thêm dữ liệu
[PutItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html) của DynamoDB được sử dụng để tạo một mục mới hoặc thay thế hoàn toàn các mục hiện có bằng một mục mới. Nó có thể được gọi bằng  [put-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/put-item.html)

Giả sử chúng ta muốn chèn một mục mới vào bảng `Reply`:

```bash
aws dynamodb put-item \
    --table-name Reply \
    --item '{
        "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
        "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"},
        "Message" : {"S": "DynamoDB Thread 2 Reply 3 text"},
        "PostedBy" : {"S": "User C"}
    }' \
    --return-consumed-capacity TOTAL
```

Chúng ta có thể thấy trong phản hồi rằng yêu cầu này tiêu thụ 1 WCU:

```json
{
    "ConsumedCapacity": {
        "TableName": "Reply",
        "CapacityUnits": 1.0
    }
}
```

## Sửa dữ liệu
 [UpdateItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html) của DynamoDB được sử dụng để tạo một mục mới hoặc thay thế các mục hiện có hoàn toàn bằng một mục mới. Nó có thể được gọi bằng [update-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/update-item.html). API này yêu cầu bạn chỉ định đầy đủ Khóa chính và có thể chọn lọc thay đổi các thuộc tính cụ thể mà không cần thay đổi các thuộc tính khác (bạn không cần phải cung cấp toàn bộ mục).

`Update-item` API call cũng cho phép bạn chỉ định một `ConditionExpression`, nghĩa là Yêu cầu cập nhật chỉ được thực hiện nếu `ConditionExpression` được thỏa mãn. Để biết thêm thông tin bạn có thể tham khảo tại [Condition Expressions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html)  trong the Developer Guide.

Giả sử chúng ta muốn cập nhật mục `Forum` cho DynamoDB để ghi nhận rằng hiện có 5 tin nhắn thay vì 4, và chúng ta chỉ muốn thay đổi đó được thực hiện nếu không có luồng xử lý nào khác đã cập nhật mục này trước đó. Điều này cho phép chúng ta tạo ra các thay đổi idempotent.

Để thực hiện điều này từ CLI, chúng ta sẽ chạy:

```bash
aws dynamodb update-item \
    --table-name Forum \
    --key '{
        "Name" : {"S": "Amazon DynamoDB"}
    }' \
    --update-expression "SET Messages = :newMessages" \
    --condition-expression "Messages = :oldMessages" \
    --expression-attribute-values '{
        ":oldMessages" : {"N": "4"},
        ":newMessages" : {"N": "5"}
    }' \
    --return-consumed-capacity TOTAL
```

Lưu ý rằng nếu bạn chạy lệnh này một lần nữa, bạn sẽ thấy lỗi:

```
An error occurred (ConditionalCheckFailedException) when calling the UpdateItem operation: The conditional request failed
```

Bởi vì thuộc tính `Messages` đã được tăng lên 5 trong cuộc gọi `update-item` trước đó, yêu cầu thứ hai sẽ thất bại với `ConditionalCheckFailedException`.

## Bài Tập
Cập nhật mục `ProductCatalog` nơi `Id=201` để thêm các màu mới "Blue" và "Yellow" vào danh sách màu sắc cho loại xe đạp đó. Sau đó, sử dụng API để xóa những mục "Blue" và "Yellow" trở lại trạng thái ban đầu.

**Gợi ý:** Trang `Update Expressions` trong Hướng dẫn phát triển có các phần về việc thêm và xóa các phần tử trong danh sách.

**Giải pháp có thể mở rộng nhưng hãy cố gắng tự mình tìm ra trước khi tiếp tục.**

#### Xem Mục Đầu
Đầu tiên, chúng ta cần xem mục trông như thế nào:

```bash
aws dynamodb get-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"201"}}'
```

Phản hồi sẽ như sau:

```json
{
    "Item": {
        "Title": {
            "S": "18-Bike-201"
        },
        "Price": {
            "N": "100"
        },
        "Brand": {
            "S": "Mountain A"
        },
        "Description": {
            "S": "201 Description"
        },
        "Color": {
            "L": [
                {
                    "S": "Red"
                },
                {
                    "S": "Black"
                }
            ]
        },
        "ProductCategory": {
            "S": "Bicycle"
        },
        "Id": {
            "N": "201"
        },
        "BicycleType": {
            "S": "Road"
        }
    }
}
```

Chúng ta thấy rằng có một thuộc tính danh sách gọi là `Color` và nó đã có hai màu là Red và Black. Chúng ta sẽ sử dụng hàm `list_append()` trong `UpdateExpression` để thêm màu Blue.

```bash
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{
        "Id" : {"N": "201"}
    }' \
    --update-expression "SET #Color = list_append(#Color, :values)" \
    --expression-attribute-names '{"#Color": "Color"}' \
    --expression-attribute-values '{
        ":values" : {"L": [{"S" : "Blue"}, {"S" : "Yellow"}]}
    }' \
    --return-consumed-capacity TOTAL
```

Bây giờ khi chúng ta muốn xóa hai màu này, chúng ta phải tham chiếu đến số chỉ số trong danh sách. Các danh sách trong DynamoDB bắt đầu từ 0, có nghĩa là các phần tử thứ 3 và thứ 4 mà chúng ta đã thêm vào có chỉ số danh sách là 2 và 3. Để xóa chúng, chúng ta có thể sử dụng lệnh sau:

```bash
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{
        "Id" : {"N": "201"}
    }' \
    --update-expression "REMOVE #Color[2], #Color[3]" \
    --expression-attribute-names '{"#Color": "Color"}' \
    --return-consumed-capacity TOTAL
```

Bạn có thể sử dụng lệnh `get-item` để xác minh rằng các thay đổi đã được thực hiện sau mỗi bước.