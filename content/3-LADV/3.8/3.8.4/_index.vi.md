---
title : "Bước 4 - Truy vấn chi tiết Khách hàng và Chi tiết hóa đơn bằng cách sử dụng Index"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 3.8.4. </b> "
---
Truy vấn bằng ID khách hàng và xem lại kết quả hiển thị chi tiết khách hàng và danh sách các hóa đơn liên quan. Lưu ý cách đầu ra hiển thị tất cả các hóa đơn được liên kết với khách hàng.

```bash
python query_index_invoiceandbilling.py InvoiceAndBills 'C#1249'
```

Đây là một cái nhìn về đầu ra.

```txt
 =========================================================
 Invoice ID: I#661, Customer ID: C#1249


 Invoice ID: I#1249, Customer ID: C#1249
 =========================================================

```

Bây giờ, truy vấn bằng ID hóa đơn và xem lại đầu ra hiển thị chi tiết hóa đơn và chi tiết của các hóa đơn được liên kết. Lưu ý cách đầu ra hiển thị tất cả các hóa đơn được liên kết với một hóa đơn cụ thể.

Chạy tập lệnh Python sau:

```bash
python query_index_invoiceandbilling.py InvoiceAndBills 'B#3392'
```

Đây là một cái nhìn về đầu ra.

```txt
=========================================================
 Invoice ID: I#506, Bill ID: B#3392, BillAmount: $383,572.00 , BillBalance: $5,345,699.00


 Invoice ID: I#1721, Bill ID: B#3392, BillAmount: $401,844.00 , BillBalance: $25,408,787.00


 Invoice ID: I#390, Bill ID: B#3392, BillAmount: $581,765.00 , BillBalance: $11,588,362.00
 =========================================================
```
