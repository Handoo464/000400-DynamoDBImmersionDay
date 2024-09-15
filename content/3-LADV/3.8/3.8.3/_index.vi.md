---
title : "Bước 3 - Truy vấn chi tiết hóa đơn của bảng"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.1. </b> "
---
Chạy tập lệnh sau để truy vấn chi tiết hóa đơn của bảng.

```bash
python query_invoiceandbilling.py InvoiceAndBills 'I#1420'
```

```txt
=========================================================
 Invoice ID:I#1420, BillID:B#2485, BillAmount:$135,986.00 , BillBalance:$28,322,352.00

 Invoice ID:I#1420, BillID:B#2823, BillAmount:$592,769.00 , BillBalance:$8,382,270.00

 Invoice ID:I#1420, Customer ID:C#1420

 Invoice ID:I#1420, InvoiceStatus:Cancelled, InvoiceBalance:$28,458,338.00 , InvoiceDate:10/31/17, InvoiceDueDate:11/20/17
 =========================================================
```

Xem lại chi tiết hóa đơn, chi tiết khách hàng và chi tiết hóa đơn. Lưu ý cách kết quả hiển thị mối quan hệ giữa ID hóa đơn, ID khách hàng và thực thể ID hóa đơn.