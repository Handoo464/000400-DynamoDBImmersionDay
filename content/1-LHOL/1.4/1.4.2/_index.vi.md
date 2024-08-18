---
title : "Point-In-Time Recovery Backup"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 1.4.2. </b> "
---

[DynamoDB Point-in-time recovery](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/PointInTimeRecovery.html)  hay còn gọi là "PITR", giúp bảo vệ các bảng DynamoDB của bạn khỏi các thao tác ghi hoặc xóa không mong muốn. Với khôi phục theo thời gian thực, bạn không cần lo lắng về việc tạo, duy trì hoặc lập lịch sao lưu theo yêu cầu. Ví dụ, giả sử rằng một script thử nghiệm đã vô tình ghi vào bảng DynamoDB sản xuất. Với khôi phục theo thời gian thực, bạn có thể khôi phục bảng đó về bất kỳ thời điểm nào trong vòng 35 ngày qua. DynamoDB lưu giữ các bản sao lưu gia tăng của bảng của bạn. Theo mặc định, PITR là tắt.

### How to enable PITR

1. Đầu tiên, hãy truy cập vào [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/)  và nhấp vào "Tables" từ menu bên. Trong danh sách các bảng, chọn bảng **ProductCatalog**. Trên tab "Backups" của bảng ProductCatalog, trong phần "Point-in-time recovery", chọn "Edit".

![PITR Backup 1](/images/1/1.4/1.png)

2. Chọn "Enable Point-in-time recovery" và chọn "Save changes".

![PITR Backup 2](/images/1/1.4/2.png)
### To restore a table to a point in time
Giả sử rằng chúng ta có một số bản ghi không mong muốn trong bảng ProductCatalog như đã được làm nổi bật dưới đây.

![PITR Unwanted Records](/images/1/1.4/3.png)

Follow the below steps to restore ProductCatalog using Point-in-time-recovery.

1. Đăng nhập vào AWS Management Console và mở console DynamoDB. Trong phần điều hướng bên trái của console, chọn **Tables**. Trong danh sách các bảng, chọn bảng ProductCatalog table. Trên tab **Backups** của bảng ProductCatalog, trong phần **Point-in-time recovery** chọn **Restore to point-in-time**.

![PITR Restore 1](/images/1/1.4/4.png)

2. Đối với tên bảng mới, nhập ProductCatalogPITR. Để xác nhận thời gian khôi phục, thiết lập **Restore date and time** thành **Latest restore date**. Chọn **Restore** để bắt đầu quá trình khôi phục.

![PITR Restore 2](/images/1/1.4/5.png)

_Note : Bạn có thể khôi phục bảng về cùng một AWS Region hoặc sang một Region khác nơi mà bản sao lưu đang tồn tại. Bạn cũng có thể loại trừ các chỉ mục phụ khỏi việc được tạo trên bảng được khôi phục mới. Ngoài ra, bạn có thể chỉ định một chế độ mã hóa khác.

Bảng đang được khôi phục sẽ hiển thị với trạng thái **Restoring**. Sau khi quá trình khôi phục hoàn tất, trạng thái của b.ảng **ProductCatalogPITR** sẽ thay đổi thành **Active**.

![PITR Restore 3](/images/1/1.4/6.png)