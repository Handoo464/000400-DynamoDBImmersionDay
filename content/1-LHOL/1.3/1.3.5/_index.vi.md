---
title : "Global Secondary Indexes"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 1.3.5. </b> "
---


Chúng ta đã chú ý đến việc truy cập dữ liệu dựa trên các thuộc tính khóa cho đến nay. Nếu chúng ta muốn tìm kiếm các mục dựa trên các thuộc tính không phải khóa, chúng ta phải thực hiện quét toàn bộ bảng và sử dụng điều kiện lọc để tìm những gì chúng ta muốn, điều này vừa chậm vừa tốn kém cho các hệ thống hoạt động ở quy mô lớn.

DynamoDB cung cấp một tính năng gọi là **Global Secondary Indexes (GSIs)**, cho phép tự động chuyển đổi dữ liệu của bạn xung quanh các Khóa Phân vùng và Khóa Sắp xếp khác nhau. Dữ liệu có thể được nhóm lại và sắp xếp lại để cho phép nhiều mẫu truy cập hơn nhanh chóng được phục vụ thông qua các API **Query** và **Scan**.

Nhớ lại ví dụ trước đó, nơi chúng ta muốn tìm tất cả các phản hồi trong bảng **Reply** được đăng bởi **User A** và cần sử dụng một thao tác **Scan**? Nếu có một tỷ mục **Reply** nhưng chỉ ba trong số đó được đăng bởi **User A**, chúng ta sẽ phải tốn (cả thời gian và tiền bạc) để quét qua một tỷ mục chỉ để tìm ba mục mong muốn.

Với kiến thức về **GSI (Global Secondary Index)**, chúng ta có thể tạo một GSI trên bảng **Reply** để phục vụ mô hình truy cập mới này. **GSI** có thể được tạo và xoá bất cứ lúc nào, ngay cả khi bảng đã có dữ liệu. GSI mới này sẽ sử dụng thuộc tính **PostedBy** làm khóa phân vùng (HASH) và chúng ta vẫn giữ các tin nhắn được sắp xếp theo **ReplyDateTime** làm khóa sắp xếp (RANGE). Chúng ta muốn tất cả các thuộc tính từ bảng được sao chép (dự kiến) vào GSI, vì vậy sẽ sử dụng **ALL ProjectionType**. Lưu ý rằng tên của chỉ mục mà chúng ta tạo là **PostedBy-ReplyDateTime-gsi**.

Điều hướng đến bảng **Reply**, chuyển sang tab **Indexes** và nhấn **Create Index**.

![Console Create GSI 1](/images/1/1.3/17.png)

Nhập **PostedBy** làm khóa phân vùng, **ReplyDateTime** làm khóa sắp xếp, và **PostedBy-ReplyDateTime-gsi** làm tên chỉ mục. Giữ các cài đặt khác ở mặc định và nhấn **Create Index**. Khi chỉ mục không còn trong trạng thái Creating, bạn có thể tiếp tục với bài tập bên dưới.

### Cleanup

Khi bạn hoàn tất, hãy đảm bảo xóa GSI. Trở lại tab **Indexes**, chọn chỉ mục **PostedBy-ReplyDateTime-gsi** và nhấn **Delete**.

![Console Delete GSI](/images/1/1.3/18.png)
