---
title : "Làm việc với Table Scans"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.3.3. </b> "
---

[API Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html) có thể được gọi bằng [lệnh CLI scan](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html). Lệnh Scan sẽ quét toàn bộ bảng và trả về các mục theo từng khối 1MB.

API Scan tương tự như API Query, ngoại trừ việc chúng ta muốn quét toàn bộ bảng thay vì chỉ một Item Collection duy nhất, do đó không có Biểu thức Điều kiện Khóa (Key Condition Expression) cho lệnh Scan. Tuy nhiên, bạn có thể chỉ định một [Biểu thức Lọc (Filter Expression)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.FilterExpression) để giảm kích thước của bộ kết quả (mặc dù nó sẽ không giảm lượng dung lượng đã tiêu thụ).

Hãy xem dữ liệu trong bảng **Reply** có cả Khóa Phân Vùng và Khóa Sắp Xếp. Chọn **Explore items** từ thanh menu bên trái.  
![Console Menu Item Explorer](/images/1/1.3/11.png)  
Bạn có thể cần nhấp vào biểu tượng menu hình hamburger để mở rộng menu bên trái nếu nó bị ẩn.  
![Console Menu Hamburger Icon](/images/1/1.3/12.png)  

Khi bạn vào phần **Explore Items**, bạn cần chọn bảng **Reply** và sau đó mở rộng hộp **Scan/Query items**.

![Item Explorer Expand Tables](/images/1/1.3/13.png)

Ví dụ, chúng ta có thể tìm tất cả các phản hồi trong bảng Reply được đăng bởi User A.

![Item Explorer Scan Reply 1](/images/1/1.3/14.png)

Bạn sẽ thấy 3 mục **Reply** được đăng bởi User A.