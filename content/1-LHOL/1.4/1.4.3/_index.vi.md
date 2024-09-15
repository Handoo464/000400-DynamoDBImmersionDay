---
title : "Sao lưu theo yêu cầu"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 1.4.3. </b> "
---

Bạn có thể sử dụng khả năng sao lưu theo yêu cầu của DynamoDB để tạo các bản sao lưu toàn bộ của các bảng để lưu giữ lâu dài và lưu trữ cho các nhu cầu tuân thủ quy định. Bạn có thể sao lưu và khôi phục dữ liệu bảng bất cứ lúc nào chỉ với một lần nhấp trên AWS Management Console hoặc với một lệnh gọi API duy nhất. Các hành động sao lưu và khôi phục được thực hiện mà không ảnh hưởng đến hiệu suất hoặc tính khả dụng của bảng.

1. Đầu tiên, truy cập vào [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/) và nhấp vào **_Tables_** từ menu bên. Chọn bảng **ProductCatalog**. Trong tab **Backups** của bảng ProductCatalog, chọn **Create backup**.

![OD Backup 1](/images/1/1.4/7.png)

2. Đảm bảo rằng **ProductCatalog** là tên bảng nguồn. Chọn **Customize settings** và sau đó chọn **Backup with DynamoDB**. Nhập tên `ProductCatalogBackup`. Nhấp vào **Create backup** để tạo bản sao lưu.

![OD Backup 2](/images/1/1.4/8.png)

Khi bản sao lưu đang được tạo, trạng thái sao lưu được đặt là **Creating**. Sau khi sao lưu hoàn tất, trạng thái sao lưu chuyển sang **Available**.

### Khôi phục sao lưu

1. Truy cập vào [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/) và nhấp vào **_Tables_** từ menu bên. Chọn bảng **ProductCatalog**. Chọn tab **Backups**. Trong danh sách các bản sao lưu, chọn **ProductCatalogBackup**. Chọn **Restore**.

![OD Backup 3](/images/1/1.4/9.png)

2. Nhập `ProductCatalogODRestore` làm tên bảng mới. Xác nhận tên bản sao lưu và các chi tiết khác của bản sao lưu. Chọn **Restore** để bắt đầu quá trình khôi phục. Bảng đang được khôi phục sẽ hiển thị với trạng thái **Creating**. Sau khi quá trình khôi phục hoàn tất, trạng thái của bảng `ProductCatalogODRestore` sẽ chuyển thành **Active**.

![OD Backup 4](/images/1/1.4/10.png)

### Để xóa một bản sao lưu

Quy trình sau đây cho thấy cách sử dụng giao diện điều khiển để xóa **ProductCatalogBackup**. Bạn chỉ có thể xóa bản sao lưu sau khi bảng `ProductCatalogODRestore` đã hoàn tất khôi phục.

1. Truy cập vào [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/) và nhấp vào **_Tables_** từ menu bên.
2. Chọn bảng **ProductCatalog**.
3. Chọn tab **Backups**.
4. Trong danh sách các bản sao lưu, chọn **ProductCatalogBackup**.
5. Nhấp vào **Delete**:

![OD Backup 5](/images/1/1.4/11.png)

Cuối cùng, nhập từ **`Delete`** và nhấp vào **Delete** để xóa bản sao lưu.

![OD Backup 6](/images/1/1.4/12.png)