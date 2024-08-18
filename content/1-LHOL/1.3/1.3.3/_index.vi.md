---
title : "Làm việc với Table Scans"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.3.3. </b> "
---


[Scan API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html)  có thể được gọi bằng [scan CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html) . Scan sẽ thực hiện một quét toàn bộ bảng và trả về các mục theo từng khối 1MB.

API Scan tương tự như API Query, ngoại trừ việc chúng ta muốn quét toàn bộ bảng chứ không chỉ một Tập hợp Mục (Item Collection) duy nhất, do đó không có Biểu thức Điều kiện Khóa cho một Scan. Tuy nhiên, bạn có thể chỉ định một [Filter Expression](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.FilterExpression)  để giảm kích thước của tập kết quả (mặc dù điều này sẽ không làm giảm lượng tài nguyên tiêu thụ).

Hãy xem dữ liệu trong bảng **Reply** mà có cả Partition Key và Sort Key. Select the left menu bar **Explore items**. 
![Console Menu Item Explorer](/images/1/1.3/11.png) 
Bạn có thể cần nhấn vào biểu tượng hamburger để mở rộng menu bên trái nếu nó bị ẩn. ![Console Menu Hamburger Icon](/images/1/1.3/12.png)

Một khi bạn vào Explore Items , bạn cần chọn bảng **Reply** và sau đó mở rộng hộp **Scan/Query items**.

![Item Explorer Expand Tables](/images/1/1.3/13.png)

Ví dụ, chúng ta có thể tìm tất cả các phản hồi trong bảng **Reply** được đăng bởi **User A**.

![Item Explorer Scan Reply 1](/images/1/1.3/14.png)

Bạn sẽ thấy 3 mục phản hồi được đăng bởi **User A**.