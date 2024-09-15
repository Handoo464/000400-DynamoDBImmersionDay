---
title : "Bài tập 7: Adjacency Lists"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 3.7. </b> "
---

Khi các thực thể khác nhau của một ứng dụng có mối quan hệ **nhiều-nhiều** (many-to-many), việc mô hình hóa mối quan hệ này dưới dạng một danh sách liền kề (adjacency list) sẽ dễ dàng hơn. Trong mô hình này, tất cả các thực thể cấp cao nhất (đồng nghĩa với các nút trong mô hình đồ thị) được biểu diễn như là khóa phân vùng (partition key). Mọi mối quan hệ với các thực thể khác (các cạnh trong đồ thị) được biểu diễn như một mục trong phân vùng bằng cách đặt giá trị của khóa sắp xếp (sort key) thành ID của thực thể mục tiêu (nút mục tiêu).

Ví dụ này sử dụng bảng `InvoiceAndBills` để minh họa mẫu thiết kế này. Trong kịch bản này, một khách hàng có thể có nhiều hóa đơn, do đó có mối quan hệ 1-nhiều giữa ID khách hàng và ID hóa đơn. Một hóa đơn chứa nhiều hóa đơn chi tiết, và một hóa đơn chi tiết có thể được chia nhỏ và liên kết với nhiều hóa đơn. Vì vậy, có mối quan hệ nhiều-nhiều giữa ID hóa đơn và ID hóa đơn chi tiết. Thuộc tính khóa phân vùng là ID hóa đơn, ID hóa đơn chi tiết, hoặc ID khách hàng.

Bạn sẽ mô hình hóa bảng để thực hiện các truy vấn sau:

- Sử dụng ID hóa đơn, truy xuất chi tiết hóa đơn cấp cao nhất, khách hàng và chi tiết hóa đơn liên quan.
- Truy xuất tất cả ID hóa đơn cho một khách hàng.
- Sử dụng ID hóa đơn chi tiết, truy xuất chi tiết hóa đơn chi tiết cấp cao nhất và chi tiết hóa đơn liên quan.

#### Bảng: `InvoiceAndBills`

- Lược đồ khóa: HASH, RANGE (khóa phân vùng và khóa sắp xếp)
- Đơn vị dung lượng đọc bảng (RCUs) = 100
- Đơn vị dung lượng ghi bảng (WCUs) = 100
- Chỉ số phụ toàn cục (GSI):
  - GSI_1 (100 RCUs, 100 WCUs) - Cho phép tra cứu ngược tới thực thể liên quan.

|Tên Thuộc Tính (Loại)|Thuộc Tính Đặc Biệt?|Trường Hợp Sử Dụng Thuộc Tính|Giá Trị Mẫu Của Thuộc Tính|
|---|---|---|---|
|PK (STRING)|Khóa phân vùng|Chứa ID của thực thể, có thể là hóa đơn chi tiết, hóa đơn, hoặc khách hàng|_B#3392_ hoặc _I#506_ hoặc _C#1317_|
|SK (STRING)|Khóa sắp xếp, khóa phân vùng của GSI_1|Chứa ID liên quan: có thể là hóa đơn chi tiết, hóa đơn, hoặc khách hàng|_B#1721_ hoặc _C#506_ hoặc _I#1317_|
