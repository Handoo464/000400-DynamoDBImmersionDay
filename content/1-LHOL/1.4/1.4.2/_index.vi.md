---
title : "Khôi phục sao lưu theo thời gian"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 1.4.2. </b> "
---

[Khôi phục theo thời gian của DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/PointInTimeRecovery.html), hay còn gọi là **"PITR"**, giúp bảo vệ các bảng DynamoDB của bạn khỏi các thao tác ghi hoặc xóa ngoài ý muốn. Với khôi phục theo thời gian, bạn không cần lo lắng về việc tạo, duy trì, hoặc lên lịch sao lưu theo yêu cầu. Ví dụ, giả sử một script thử nghiệm vô tình ghi vào bảng DynamoDB sản xuất. Với khôi phục theo thời gian, bạn có thể khôi phục bảng đó về bất kỳ thời điểm nào trong vòng 35 ngày gần nhất. DynamoDB duy trì các bản sao lưu gia tăng của bảng của bạn. Mặc định, PITR bị tắt.

### Cách bật PITR

1. Đầu tiên, truy cập vào [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/) và nhấp vào **_Tables_** từ menu bên. Trong danh sách các bảng, chọn bảng **ProductCatalog**. Trong tab **Backups** của bảng ProductCatalog, trong phần **Point-in-time recovery**, chọn **Edit**.

![Sao lưu PITR 1](/images/1/1.4/1.png)

2. Chọn **Enable Point-in-time-recovery** và nhấp vào **Save changes**.

![Sao lưu PITR 2](/images/1/1.4/2.png)


### Khôi phục một bảng về một thời điểm

Bây giờ giả sử chúng ta có một số bản ghi không mong muốn trong bảng ProductCatalog như được đánh dấu bên dưới.

![Bản ghi không mong muốn của PITR](/images/1/1.4/3.png)

Làm theo các bước dưới đây để khôi phục ProductCatalog bằng cách sử dụng Point-in-time-recovery.

1. Đăng nhập vào AWS Management Console và mở DynamoDB console. Trong bảng điều hướng ở phía bên trái của giao diện điều khiển, chọn **Tables**. Trong danh sách các bảng, chọn bảng **ProductCatalog**. Trong tab **Backups** của bảng ProductCatalog, trong phần **Point-in-time recovery**, chọn **Restore to point-in-time**.

![PITR Khôi phục 1](/images/1/1.4/4.png)

2. Đối với tên bảng mới, nhập **ProductCatalogPITR**. Để xác nhận thời gian có thể khôi phục, đặt **Restore date and time** thành **Latest restore date**. Chọn **Restore** để bắt đầu quá trình khôi phục.

![PITR Khôi phục 2](/images/1/1.4/5.png)

_Lưu ý: Bạn có thể khôi phục bảng về cùng một Khu vực AWS hoặc sang một Khu vực khác từ nơi bản sao lưu tồn tại. Bạn cũng có thể loại trừ các chỉ mục phụ không được tạo trên bảng khôi phục mới. Ngoài ra, bạn có thể chỉ định một chế độ mã hóa khác._

Bảng đang được khôi phục sẽ hiển thị với trạng thái **Restoring**. Sau khi quá trình khôi phục hoàn tất, trạng thái của bảng **ProductCatalogPITR** sẽ chuyển thành **Active**.

![PITR Khôi phục 3](/images/1/1.4/6.png)