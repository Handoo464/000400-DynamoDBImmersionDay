---
title : "Thêm/Sửa dữ liệu"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.2.4. </b> "
---

## Chèn Dữ Liệu

DynamoDB cung cấp [API PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html) để tạo một mục mới hoặc thay thế hoàn toàn các mục hiện có bằng một mục mới. Nó được gọi bằng [lệnh CLI put-item](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/put-item.html).

Giả sử chúng ta muốn chèn một mục mới vào bảng **Reply**:

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

## Cập Nhật Dữ Liệu

DynamoDB cung cấp [API UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html) để tạo một mục mới hoặc thay thế hoàn toàn các mục hiện có bằng một mục mới. Nó được gọi bằng [lệnh CLI update-item](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/update-item.html). API này yêu cầu bạn phải chỉ định đầy đủ Khóa Chính và có thể tùy chọn sửa đổi các thuộc tính cụ thể mà không thay đổi các thuộc tính khác (bạn không cần phải truyền toàn bộ mục).

Lệnh gọi API `update-item` cũng cho phép bạn chỉ định một **ConditionExpression**, nghĩa là yêu cầu Cập nhật sẽ chỉ được thực thi nếu ConditionExpression được thỏa mãn. Để biết thêm thông tin, vui lòng xem [Condition Expressions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html) trong Hướng dẫn Dành cho Nhà Phát triển.

Giả sử chúng ta muốn cập nhật mục **Forum** cho DynamoDB để ghi nhận rằng hiện có 5 tin nhắn thay vì 4, và chúng ta chỉ muốn thay đổi đó được thực thi nếu không có luồng xử lý nào khác đã cập nhật mục đó trước. Điều này cho phép chúng ta tạo ra các sửa đổi idempotent (không bị lặp lại). Để biết thêm thông tin về các thay đổi idempotent, vui lòng xem [Làm Việc Với Các Mục](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithItems.html#WorkingWithItems.ConditionalUpdate) trong Hướng dẫn Dành cho Nhà Phát triển.

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

Lưu ý rằng nếu bạn chạy lại lệnh chính xác này, bạn sẽ thấy lỗi sau:

```text
An error occurred (ConditionalCheckFailedException) when calling the UpdateItem operation: The conditional request failed
```

Bởi vì thuộc tính **Messages** đã được tăng lên **5** trong lệnh `update-item` trước đó, yêu cầu thứ hai sẽ thất bại với lỗi **ConditionalCheckFailedException**.