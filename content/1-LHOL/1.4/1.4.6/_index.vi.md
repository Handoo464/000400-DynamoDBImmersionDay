---
title : "Dọn dẹp tài nguyên"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 1.4.6. </b> "
---

#### Cleaning Up The Resources
##### Step 1: Delete restored AWS resources

Xóa ba bảng DynamoDB đã khôi phục bằng lệnh sau.
```bash
aws dynamodb delete-table \
--table-name ProductCatalogODRestore

aws dynamodb delete-table \
--table-name ProductCatalogRestored

aws dynamodb delete-table \
--table-name ProductCatalogPITR
```
### Step 2: Delete the backup plan

Thực hiện các bước sau để xóa một kế hoạch sao lưu:

1. Trong AWS Management Console, điều hướng đến **Dịch vụ -> AWS Backup**. Trong bảng điều hướng bên, chọn Kế hoạch sao lưu. Trên trang Kế hoạch sao lưu, chọn _dbBackupPlan_. Điều này sẽ đưa bạn đến trang chi tiết.

![Backup Plan Delete 1](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/hands-on-labs/backup/backup_plan_delete_1.png)

2. Để xóa các phân bổ tài nguyên cho kế hoạch của bạn, chọn nút radio bên cạnh bảng dynamodbTable dưới **Phân bổ tài nguyên**, sau đó chọn **Xóa**.

![Backup Plan Delete 2](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/hands-on-labs/backup/backup_plan_delete_2.png)

3. Để xóa kế hoạch sao lưu, chọn **Xóa** ở góc trên bên phải của trang.

![Backup Plan Delete 3](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/hands-on-labs/backup/backup_plan_delete_3.png)

4. Trên trang xác nhận, nhập _dbBackupPlan_, và chọn **Xóa kế hoạch**.

![Backup Plan Delete 4](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/hands-on-labs/backup/backup_plan_delete_4.png)
### Step 3: Delete the recovery points

1. Trên AWS Backup console, trong bảng điều hướng bên, chọn Backup vaults.
2. Trên trang Backup vaults, chọn _dynamodb-backup-vault_. Kiểm tra điểm khôi phục và chọn **Delete**.
    

![Restore Points Delete 1](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/hands-on-labs/backup/restore_point_delete_1.png)

3. Nếu bạn đang xóa hơn một recovery point, tiến hành theo các bước sau: 
    
    a. Xem lại danh sách các điểm khôi phục mà bạn đang xóa.
    
    b. Nếu bạn muốn chỉnh sửa danh sách, chọn Chỉnh sửa lựa chọn.
    
    c. Nếu danh sách của bạn chứa một bản sao liên tục, hãy chọn xem bạn sẽ giữ lại hay xóa dữ liệu sao lưu liên tục.
    
    d. Để xóa tất cả các điểm khôi phục trong danh sách, nhập "delete", và sau đó chọn Xóa các điểm khôi phục.
    

Giữ tab trình duyệt của bạn mở cho đến khi bạn thấy thông báo thành công màu xanh ở đầu trang.

Việc đóng tab này quá sớm sẽ kết thúc quá trình xóa và có thể để lại một số điểm khôi phục mà bạn muốn xóa.
### Step 4: Delete the backup vault

1. Chọn backup vault _dynamodb-backup-vault_ và chọn **Delete**.

![Backup Vault Delete 1](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/hands-on-labs/backup/backup_vault_delete_1.png)

2. Trên trang xác nhận, nhập _dynamodb-backup-vault_, và chọn **Delete Backup vault**.

![Backup Vault Delete 2](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/hands-on-labs/backup/backup_vault_delete_2.png)