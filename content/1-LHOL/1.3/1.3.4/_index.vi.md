---
title : "Điều chỉnh dữ liệu"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 1.3.4. </b> "
---

## Chèn Dữ Liệu

DynamoDB cung cấp [API PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html) để tạo một mục mới hoặc thay thế hoàn toàn các mục hiện có bằng một mục mới. Nó được gọi bằng [lệnh CLI put-item](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/put-item.html).

Giả sử chúng ta muốn chèn một mục mới vào bảng **Reply** từ giao diện điều khiển. Đầu tiên, điều hướng đến bảng **Reply** và nhấp vào nút **Create Item** (Tạo Mục).

![Tạo Mục trên Console 1](/images/1/1.3/15.png)

Nhấp vào **`JSON view`**, đảm bảo rằng **`View DynamoDB JSON`** chưa được chọn, dán JSON sau và sau đó nhấp vào **Create Item** để chèn mục mới.

```json
{
        "Id" : "Amazon DynamoDB#DynamoDB Thread 2",
        "ReplyDateTime" : "2021-04-27T17:47:30Z",
        "Message" : "DynamoDB Thread 2 Reply 3 text",
        "PostedBy" : "User C"
}
```

![Tạo Mục trên Console 2](/images/1/1.3/16.png)


## Cập Nhật hoặc Xóa Dữ Liệu

DynamoDB cung cấp [API UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html) để tạo một mục mới hoặc thay thế hoàn toàn các mục hiện có bằng một mục mới. Nó được gọi bằng [lệnh CLI update-item](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/update-item.html). API này yêu cầu bạn phải chỉ định đầy đủ Khóa Chính và có thể tùy chọn sửa đổi các thuộc tính cụ thể mà không thay đổi các thuộc tính khác (bạn không cần phải truyền toàn bộ mục).

DynamoDB cung cấp [API DeleteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html) để xóa một mục. Nó được gọi bằng [lệnh CLI delete-item](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/delete-item.html).

Bạn có thể dễ dàng sửa đổi hoặc xóa một mục bằng cách sử dụng giao diện điều khiển bằng cách chọn hộp kiểm bên cạnh mục bạn quan tâm, nhấp vào menu thả xuống **Actions** (Hành động) và thực hiện hành động mong muốn.

![Xóa Mục trên Console](/images/1/1.3/17.png)