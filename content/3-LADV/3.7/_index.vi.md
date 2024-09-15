---
title : "Bài tập 6: Composite Keys"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 3.7. </b> "
---

Việc lựa chọn cẩn thận thuộc tính khóa sắp xếp rất quan trọng vì nó có thể cải thiện đáng kể khả năng lựa chọn các mục được truy xuất bằng truy vấn. Giả sử bạn cần tạo một ứng dụng để truy vấn nhân viên theo vị trí địa lý (tiểu bang và thành phố) và theo phòng ban. Bạn có các thuộc tính: `state`, `city`, và `dept`. Bạn có thể tạo một global secondary index kết hợp các thuộc tính này để cho phép truy vấn theo vị trí/phòng ban. Trong DynamoDB, bạn có thể truy vấn các mục bằng cách sử dụng sự kết hợp của khóa phân vùng và khóa sắp xếp. Trong trường hợp này, tiêu chí truy vấn của bạn cần sử dụng nhiều hơn hai thuộc tính, vì vậy bạn sẽ tạo một cấu trúc khóa tổng hợp (composite-key) cho phép bạn truy vấn với nhiều hơn hai thuộc tính.

Trước đó (Bài tập 4, Bước 1) bạn đã chạy các lệnh để tạo bảng `employees` và nạp dữ liệu mẫu vào. Một trong những thuộc tính trong dữ liệu này được gọi là `state`, lưu trữ các chữ viết tắt hai chữ cái của các bang Hoa Kỳ. Ngoài ra, giá trị thuộc tính của `state` được thêm tiền tố `state#` và được lưu trữ dưới tên thuộc tính `GSI_3_PK`. Script cũng đã tạo thuộc tính `city_dept` đại diện cho một thuộc tính tổng hợp sử dụng các thuộc tính `city` và `dept`, được phân cách bằng ký tự `#` giữa các giá trị. Giá trị thuộc tính này sử dụng định dạng `city#dept` (ví dụ: `Seattle#Development`). Giá trị thuộc tính này được sao chép và lưu trữ dưới khóa `GSI_3_SK`.

#### GSI_3

|Tên Thuộc Tính (Loại)|Thuộc Tính Đặc Biệt?|Trường Hợp Sử Dụng Thuộc Tính|Giá Trị Mẫu của Thuộc Tính|
|---|---|---|---|
|`GSI_3_PK` (STRING)|Khóa phân vùng của GSI_3|Tiểu bang của nhân viên|`state#WA`|
|`GSI_3_SK` (STRING)|Khóa sắp xếp của GSI_3|Thành phố và phòng ban của nhân viên, được nối lại|`Seattle#Development`|

**Lưu ý**: Mặc dù bạn đang tạo một global secondary index mới cho truy vấn này, bạn vẫn có thể nạp chồng chỉ mục phụ toàn cầu này trong tương lai. Việc nạp chồng chỉ mục phụ toàn cầu mang lại cho bạn sự linh hoạt để đặt các loại thực thể khác nhau vào cùng một chỉ mục (ví dụ: nhân viên và tòa nhà). Để hỗ trợ sự phát triển trong tương lai, khóa phân vùng `GSI_3` được thêm hậu tố với loại thực thể, cho phép bạn chèn các hàng vào cùng một chỉ mục phụ toàn cầu sau này mà không cần kết hợp dữ liệu.