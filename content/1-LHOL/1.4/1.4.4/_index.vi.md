---
title : "Scheduled Backup"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.4.4. </b> "
---

Bạn phải tạo ít nhất một kho lưu trữ (vault) trước khi tạo kế hoạch sao lưu hoặc bắt đầu một công việc sao lưu.

1. Ở AWS Management Console, điều hướng tới **Services -> AWS Backup.** Chọn **Create Backup vault** dưới **Backup vaults**.

![Scheduled Backup 1](/images/1/1.4/13.png)

2. Cung cấp tên Backup vault tùy chọn. AWS KMS encryption master key. By default, AWS Backup tạo master key with alias aws/backup cho bạn. Bạn có thể chọn khóa đó hoặc chọn bất kỳ khóa nào khác trong tài khoản của bạn. Nhấp vào **Create Backup vault**

![Scheduled Backup 2](/images/1/1.4/14.png)

Bạn có thể thấy kho sao lưu đã được tạo thành công.

![Scheduled Backup 3](/images/1/1.4/15.png)

Bây giờ, chúng ta cần tạo kế hoạch sao lưu.

3. Nhấp vào **Create Backup plan** dưới **Backup plans**.

![Scheduled Backup 4](/images/1/1.4/16.png)

4. Chọn **Build a new plan**. Cung cấp **backup plan name** và **rule name**.

![Scheduled Backup 5](/images/1/1.4/17.png)

5. Chọn **backup frequency.** Tần suất sao lưu xác định tần suất sao lưu được tạo. Sử dụng giao diện, bạn có thể chọn **frequency** of every 12 hours, daily, weekly, hoặc monthly. Chọn **backup window**. Backup window bao gồm thời gian bắt đầu và thời gian kéo dài của khoảng thời gian tính bằng giờ. Ta đang cấu hình sao lưu bắt đầu lúc 6 giờ chiều UTC, bắt đầu trong vòng 1 giờ và hoàn thành trong vòng 4 giờ.
    
    Thêm vào đó, chọn **lifecycle**. Vòng đời xác định khi nào một bản sao lưu được chuyển sang lưu trữ lạnh và khi nào nó hết hạn. Tôi đang cấu hình sao lưu để chuyển sang lưu trữ lạnh sau 31 ngày và hết hạn sau 366 ngày.
    

![Scheduled Backup 6](/images/1/1.4/18.png)

6. Chọn **backup vault** mà chúng ta đã tạo trước đó. Nhấp vào **Create plan**.

![Scheduled Backup 7](/images/1/1.4/19.png)

_Note: Bản sao lưu được chuyển sang lưu trữ lạnh phải được lưu trữ ở đó trong tối thiểu 90 ngày.

Bây giờ, gán tài nguyên cho kế hoạch sao lưu. Khi bạn gán một tài nguyên cho kế hoạch sao lưu, tài nguyên đó sẽ được sao lưu tự động theo kế hoạch sao lưu.

7. Cung cấp tên gán tài nguyên. Chọn vai trò mặc định. Chọn **Include specific resource types** dưới "1. Xác định lựa chọn tài nguyên"

![Scheduled Backup 8](/images/1/1.4/20.png)

8. Dưới "2. Chọn các loại tài nguyên cụ thể", chọn loại tài nguyên **DynamoDB** trong menu xổ xuống. Nhấp vào resources, bỏ chọn All và chọn bảng **ProductCatalog** chọn **Assign resources**

![Scheduled Backup 9](/images/1/1.4/21.png)

9. Bạn có thể thấy trạng thái của công việc sao lưu trong phần công việc sau khoảng thời gian của cửa sổ sao lưu đã được lập lịch. Bạn có thể thấy sao lưu DynamoDB của mình đã hoàn thành.

![Scheduled Backup 10](/images/1/1.4/22.png)
### Restore a Backup:

Sau khi một tài nguyên được sao lưu ít nhất một lần, nó được coi là được bảo vệ và có thể được khôi phục bằng AWS Backup. Trong tài khoản của bạn, một bản sao lưu có thể chưa có sẵn. Nếu trường hợp này xảy ra, hãy xem các ảnh chụp màn hình thay vì thực hiện trong tài khoản của riêng bạn.

1. Trên trang **Protected resources**,bạn có thể khám phá chi tiết về các tài nguyên đã được sao lưu trong AWS Backup. Chọn tài nguyên bảng DynamoDB our DynamoDB table resource của chúng tôi. 

![Scheduled Backup 11](/images/1/1.4/23.png)

2. Chọn recovery point ID của resource. Chọn **Restore**. Nếu bạn không thấy một điểm phục hồi, bạn có thể nhấp vào "Create an on-demand backup" và hoàn thành việc sao lưu. Đối với mục đích của bài thực hành này, bạn cần một bản sao lưu đã hoàn thành để tiếp tục, và bạn có thể không muốn chờ kế hoạch sao lưu theo lịch của mình.

![Scheduled Backup 12](/images/1/1.4/24.png)

3. Cung cấp tên bảng DynamoDB mới. Để tất cả các cài đặt ở mặc định và nhấp vào **Restore backup**

![Scheduled Backup 13](/images/1/1.4/25.png)

Thanh **Restore jobs** xuất hiện. Một thông điệp ở đầu trang cung cấp thông tin về công việc phục hồi. Bạn có thể thấy trạng thái công việc đang chạy. Sau một thời gian, bạn có thể thấy trạng thái thay đổi thành hoàn thành.

![Scheduled Backup 14](/images/1/1.4/26.png)

Bạn cũng có thể theo dõi tất cả các công việc sao lưu và phục hồi trong bảng điều khiển trung tâm.

![Scheduled Backup 15](/images/1/1.4/27.png)

Để xem bảng đã được khôi phục, hãy đến [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/)  và nhấp vào _Tables_ từ menu bên. Chọn bảng ProductCatalogRestored. Bạn có thể thấy bảng đã được khôi phục cùng với dữ liệu.

![Scheduled Backup 16](/images/1/1.4/28.png)

