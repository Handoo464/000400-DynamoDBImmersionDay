---
title : "On-Demand Backup"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 1.4.3. </b> "
---

Bạn có thể sử dụng tính năng sao lưu theo yêu cầu của DynamoDB để tạo các bản sao lưu đầy đủ cho các bảng của mình nhằm lưu giữ lâu dài và lưu trữ cho các yêu cầu tuân thủ quy định. Bạn có thể sao lưu và khôi phục dữ liệu bảng bất kỳ lúc nào chỉ với một cú nhấp chuột trên AWS Management Console hoặc thông qua một lệnh API. Các hành động sao lưu và khôi phục hoạt động mà không ảnh hưởng đến hiệu suất hoặc khả năng sử dụng của bảng.

1. Đầu tiên, hãy truy cập vào [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/)  nhấp vào _Tables_ từ menu bên. Chọn ProductCatalog bảng. Trên tab **Backups** của bảng ProductCatalog chọn **Create backup**.

![OD Backup 1](/images/1/1.4/7.png)

2. Đảm bảo ProductCatalog là tên bảng nguồn. Chọn **Customize settings** và sau đó chọn **Backup with DynamoDB**. Nhập tên `ProductCatalogBackup`. Chọn **Create backup** để tạo bản sao lưu.

![OD Backup 2](/images/1/1.4/8.png)

Khi bản sao lưu đang được tạo, trạng thái bản sao lưu sẽ được thiết lập là **Creating**. Sau khi bản sao lưu hoàn tất, trạng thái bản sao lưu sẽ thay đổi thành **Available**.

### Restore Backup

1. ĐI đến [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/)  và click vào _Tables_ từ menu bên. Chọn bảng ProductCatalog table. Chọn tab **Backups**. Trong danh sách các bản sao lưu, chọn ProductCatalogBackup chọn **Restore**.

![OD Backup 3](/images/1/1.4/9.png)

2. Nhập `ProductCatalogODRestore` làm tên bảng mới. Xác nhận tên bản sao lưu và các chi tiết khác về bản sao lưu. Chọn "Restore" để bắt đầu quá trình khôi phục. Bảng đang được khôi phục sẽ hiển thị với trạng thái **Creating**. Sau khi quá trình khôi phục hoàn tất, trạng thái của bảng **ProductCatalogODRestore** sẽ thay đổi thành **Active**.

![OD Backup 4](/images/1/1.4/10.png)
### To delete a backup

Quy trình sau đây cho thấy cách sử dụng console để xóa ProductCatalogBackup. Bạn chỉ có thể xóa bản sao lưu sau khi bảng `ProductCatalogODRestore` đã hoàn thành quá trình khôi phục.

1. Đi đến [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/)  and click on _Tables_ from the side menu
2. Choose ProductCatalog table.
3. Choose **Backups** tab.
4. In the list of backups, choose ProductCatalogBackup.
5. Click **Delete**:

![OD Backup 5](/images/1/1.4/11.png)

Finally, type the world `Delete` and click **Delete** to delete the backup.

![OD Backup 6](/images/1/1.4/12.png)
