---
title : "Bước 2 - Xem lại bảng InvoiceAndBills trên bảng điều khiển DynamoDB"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.8.2.</b> "
---
Trong bảng điều khiển DynamoDB, mở bảng **InvoiceAndBills** và chọn tùy chọn menu **Items**. Từ menu thả xuống, chọn `InvoiceAndBills GSI_1` và sau đó `Scan` bảng.

Trong kết quả đầu ra, chọn **PK** để sắp xếp dữ liệu theo thứ tự ngược lại. Chú ý các loại thực thể khác nhau trong cùng một bảng.

![Danh sách Liền kề](/images/3/3.8/1.png)

Trong các bước tiếp theo, bạn sẽ truy vấn bảng và truy xuất các loại thực thể khác nhau. Bạn có thể thực hiện các truy vấn này trong bảng điều khiển AWS ngay sau khi bạn truy vấn chúng bằng các script Python để có thêm cái nhìn sâu sắc.

