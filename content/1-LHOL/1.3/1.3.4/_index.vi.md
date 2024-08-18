---
title : "Điều chỉnh dữ liệu"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 1.3.4. </b> "
---

### Inserting Data

The DynamoDB [PutItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)  được sử dụng để tạo một mục mới hoặc thay thế hoàn toàn các mục hiện có bằng một mục mới. Nó được gọi bằng [put-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/put-item.html) .

Giả sử chúng ta muốn chèn một mục mới vào bảng **Reply** từ bảng điều khiển. Đầu tiên, điều hướng đến bảng **Reply** và nhấn nút **Create Item**.

![Console Create Item 1](/images/1/1.3/15.png)
Nhấn vào chế độ **JSON view**, đảm bảo rằng tùy chọn **View DynamoDB JSON** không được chọn, sau đó dán JSON sau và nhấn **Create Item** để chèn mục mới.

```json
{
        "Id" : "Amazon DynamoDB#DynamoDB Thread 2",
        "ReplyDateTime" : "2021-04-27T17:47:30Z",
        "Message" : "DynamoDB Thread 2 Reply 3 text",
        "PostedBy" : "User C"
}
```

![Console Create Item 2](/images/1/1.3/16.png)
## Updating or Deleting Data
DynamoDB [UpdateItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)  được sử dụng để tạo một mục mới hoặc để thay thế hoàn toàn các mục hiện có bằng một mục mới. Nó được gọi bằng [update-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/update-item.html) . API này yêu cầu bạn chỉ định đầy đủ Khóa chính và có thể sửa đổi chọn lọc các thuộc tính cụ thể mà không thay đổi các thuộc tính khác (bạn không cần phải cung cấp toàn bộ mục).

The DynamoDB [DeleteItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html)  được sử dụng để xóa một mục. Nó được gọi bằng [delete-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/delete-item.html) .

Bạn có thể dễ dàng chỉnh sửa hoặc xóa một mục bằng cách chọn ô kiểm bên cạnh mục quan tâm, nhấn vào menu **Actions** và thực hiện thao tác mong muốn.

![Console Delete Item](/images/1/1.3/17.png)

