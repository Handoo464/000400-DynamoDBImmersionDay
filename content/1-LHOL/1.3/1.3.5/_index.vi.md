---
title : "Global Secondary Indexes"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 1.3.5. </b> "
---


Chúng ta đã quan tâm đến việc truy cập dữ liệu dựa trên các thuộc tính khóa. Nếu muốn tìm các mục dựa trên các thuộc tính không phải khóa, chúng ta phải quét toàn bộ bảng và sử dụng điều kiện lọc để tìm những gì chúng ta muốn, điều này sẽ rất chậm và tốn kém đối với các hệ thống hoạt động ở quy mô lớn.

DynamoDB cung cấp một tính năng gọi là [Global Secondary Indexes (GSIs)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html) giúp tự động xoay chuyển dữ liệu của bạn xung quanh các Khóa Phân Vùng và Khóa Sắp Xếp khác nhau. Dữ liệu có thể được tái nhóm và tái sắp xếp để cho phép nhiều mẫu truy cập hơn được phục vụ nhanh chóng với các API Query và Scan.

Hãy nhớ ví dụ trước đó khi chúng ta muốn tìm tất cả các phản hồi trong bảng **Reply** được đăng bởi User A và cần sử dụng thao tác `Scan`? Nếu có một tỷ mục **Reply** nhưng chỉ có ba mục được đăng bởi User A, chúng ta sẽ phải trả giá (cả về thời gian và chi phí) để quét qua một tỷ mục chỉ để tìm ba mục mà chúng ta cần.

Với kiến thức về GSIs, chúng ta có thể tạo một GSI trên bảng **Reply** để phục vụ mẫu truy cập mới này. GSIs có thể được tạo và xóa bất kỳ lúc nào, ngay cả khi bảng đã có dữ liệu! GSI mới này sẽ sử dụng thuộc tính **PostedBy** làm khóa Phân Vùng (HASH) và chúng ta sẽ giữ các tin nhắn được sắp xếp theo **ReplyDateTime** làm khóa Sắp Xếp (RANGE). Chúng ta muốn tất cả các thuộc tính từ bảng được sao chép (projected) vào GSI nên chúng ta sẽ sử dụng **ALL ProjectionType**. Lưu ý rằng tên của chỉ mục chúng ta tạo là **PostedBy-ReplyDateTime-gsi**.

Điều hướng đến bảng **Reply**, chuyển sang tab **Indexes** và nhấp vào `Create Index`.

![Tạo GSI trên Console 1](/images/1/1.3/17.png)

Nhập `PostedBy` làm khóa Phân Vùng, `ReplyDateTime` làm khóa Sắp Xếp, và `PostedBy-ReplyDateTime-gsi` làm tên Chỉ mục. Giữ nguyên các thiết lập khác và nhấp vào `Create Index`. Khi chỉ mục thoát khỏi trạng thái `Creating`, bạn có thể tiếp tục với bài tập bên dưới.

### Dọn Dẹp

Khi bạn hoàn thành, hãy chắc chắn xóa GSI. Quay lại tab Indexes, chọn chỉ mục `PostedBy-ReplyDateTime-gsi` và nhấp vào `Delete`.

![Xóa GSI trên Console](/images/1/1.3/18.png)