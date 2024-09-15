---
title : "Dọn dẹp tài nguyên"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 1.4.6. </b> "
---

#### Dọn dẹp tài nguyên
##### Bước 1: Xóa các tài nguyên AWS đã khôi phục

Xóa ba bảng DynamoDB đã khôi phục bằng lệnh sau.

```bash
aws dynamodb delete-table \
--table-name ProductCatalogODRestore

aws dynamodb delete-table \
--table-name ProductCatalogRestored

aws dynamodb delete-table \
--table-name ProductCatalogPITR
```

### Bước 2: Xóa kế hoạch sao lưu

Thực hiện các bước sau để xóa một kế hoạch sao lưu:

1. Trong AWS Management Console, điều hướng đến **Services -> AWS Backup**. Trong bảng điều hướng, chọn **Backup plans**. Trên trang **Backup plans**, chọn **_dbBackupPlan_**. Thao tác này sẽ đưa bạn đến trang chi tiết.

![Xóa kế hoạch sao lưu 1](/images/1/1.4/1.4.6/1.png)

2. Để xóa việc gán tài nguyên cho kế hoạch của bạn, chọn nút radio bên cạnh **dynamodbTable** trong phần **Resource assignments**, sau đó chọn **Delete**.

![Xóa kế hoạch sao lưu 2](/images/1/1.4/1.4.6/2.png)

3. Để xóa kế hoạch sao lưu, chọn **Delete** ở góc trên bên phải của trang.

![Xóa kế hoạch sao lưu 3](/images/1/1.4/1.4.6/3.png)

4. Trên trang xác nhận, nhập **_dbBackupPlan_**, và chọn **Delete plan**.

![Xóa kế hoạch sao lưu 4](/images/1/1.4/1.4.6/4.png)

### Bước 3: Xóa các điểm khôi phục

1. Trên AWS Backup console, trong bảng điều hướng, chọn **Backup vaults**.

2. Trên trang **Backup vaults**, chọn **_dynamodb-backup-vault_**. Kiểm tra điểm khôi phục và chọn **Delete**.

![Xóa điểm khôi phục 1](/images/1/1.4/1.4.6/5.png)

3. Nếu bạn đang xóa nhiều hơn một điểm khôi phục, hãy làm theo các bước sau:
    
    a. Xem lại danh sách các điểm khôi phục mà bạn đang xóa.
    
    b. Nếu bạn muốn chỉnh sửa danh sách, chọn **Modify selection**.
    
    c. Nếu danh sách của bạn chứa một bản sao lưu liên tục, chọn xem bạn muốn giữ lại hay xóa dữ liệu sao lưu liên tục của mình.
    
    d. Để xóa tất cả các điểm khôi phục được liệt kê, nhập **delete**, và sau đó chọn **Delete recovery points**.
    
Giữ tab trình duyệt của bạn mở cho đến khi bạn thấy biểu ngữ màu xanh lá cây thành công ở đầu trang.

Đóng tab này quá sớm sẽ kết thúc quá trình xóa và có thể để lại một số điểm khôi phục mà bạn muốn xóa.

### Bước 4: Xóa backup vault

1. Chọn backup vault **_dynamodb-backup-vault_** và chọn **Delete**.

![Xóa backup vault 1](/images/1/1.4/1.4.6/6.png)

2. Trên trang xác nhận, nhập **_dynamodb-backup-vault_**, và chọn **Delete Backup vault**.

![Xóa backup vault 2](/images/1/1.4/1.4.6/7.png)