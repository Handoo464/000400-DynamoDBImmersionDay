---
title : "Sao lưu theo lịch"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.4.4. </b> "
---

**Sao lưu theo yêu cầu**

Bạn phải tạo ít nhất một vault (kho lưu trữ) trước khi tạo một kế hoạch sao lưu hoặc bắt đầu một công việc sao lưu.

1. Trong AWS Management Console, điều hướng đến **Services -> AWS Backup**. Nhấp vào **Create Backup vault** dưới **Backup vaults**.

![Sao lưu theo lịch trình 1](/images/1/1.4/1.4.4/1.png)

2. Cung cấp tên Backup vault theo ý muốn của bạn. AWS KMS encryption master key (khóa chính mã hóa AWS KMS). Mặc định, AWS Backup sẽ tạo một khóa chính với bí danh aws/backup cho bạn. Bạn có thể chọn khóa đó hoặc chọn bất kỳ khóa nào khác trong tài khoản của bạn. Nhấp vào **Create Backup vault**.

![Sao lưu theo lịch trình 2](/images/1/1.4/1.4.4/2.png)

Bạn có thể thấy Backup vault đã được tạo thành công.

![Sao lưu theo lịch trình 3](/images/1/1.4/1.4.4/3.png)

Bây giờ, chúng ta cần tạo một kế hoạch sao lưu.

3. Nhấp vào **Create Backup plan** dưới **Backup plans**.

![Sao lưu theo lịch trình 4](/images/1/1.4/1.4.4/4.png)

4. Chọn **Build a new plan**. Cung cấp **backup plan name** và **rule name**.

![Sao lưu theo lịch trình 5](/images/1/1.4/1.4.4/5.png)

5. Chọn **backup frequency** (tần suất sao lưu). Tần suất sao lưu xác định mức độ thường xuyên tạo một bản sao lưu. Sử dụng giao diện điều khiển, bạn có thể chọn **frequency** là mỗi 12 giờ, hàng ngày, hàng tuần, hoặc hàng tháng. Chọn **backup window** (cửa sổ sao lưu). Cửa sổ sao lưu bao gồm thời gian bắt đầu sao lưu và thời gian của cửa sổ tính bằng giờ. Công việc sao lưu sẽ bắt đầu trong cửa sổ này. Tôi đang cấu hình sao lưu bắt đầu vào lúc 6 giờ chiều UTC trong vòng 1 giờ và hoàn thành trong vòng 4 giờ.

   Tiếp theo, chọn **lifecycle** (vòng đời). Vòng đời xác định khi nào một bản sao lưu được chuyển sang lưu trữ lạnh và khi nào nó hết hạn. Tôi đang cấu hình sao lưu để chuyển sang lưu trữ lạnh sau 31 ngày và hết hạn sau 366 ngày.

![Sao lưu theo lịch trình 6](/images/1/1.4/1.4.4/6.png)

6. Chọn **backup vault** mà chúng ta đã tạo trước đó. Nhấp vào **Create plan**.

![Sao lưu theo lịch trình 7](/images/1/1.4/1.4.4/7.png)

_Lưu ý: Các bản sao lưu được chuyển sang lưu trữ lạnh phải được lưu trữ trong lưu trữ lạnh ít nhất 90 ngày._

Bây giờ, hãy gán tài nguyên cho kế hoạch sao lưu. Khi bạn gán một tài nguyên cho một kế hoạch sao lưu, tài nguyên đó sẽ được sao lưu tự động theo kế hoạch sao lưu.

7. Đặt tên cho Resource assignment (gán tài nguyên). Chọn vai trò mặc định. Chọn **Include specific resource types** dưới mục "1. Define resource selection".

![Sao lưu theo lịch trình 8](/images/1/1.4/1.4.4/8.png)

8. Dưới "2. Select specific resource types" (Chọn loại tài nguyên cụ thể), chọn loại tài nguyên **DynamoDB** từ menu thả xuống. Nhấp vào "Choose resources", bỏ chọn "All", và chọn bảng **ProductCatalog**. Nhấp vào **Assign resources**.

![Sao lưu theo lịch trình 9](/images/1/1.4/1.4.4/9.png)

9. Bạn có thể thấy trạng thái của công việc sao lưu trong phần "jobs" sau khoảng thời gian của cửa sổ sao lưu đã lên lịch. Bạn sẽ thấy sao lưu DynamoDB của mình đã hoàn thành.

![Sao lưu theo lịch trình 10](/images/1/1.4/1.4.4/10.png)

### Khôi phục một bản sao lưu:

Sau khi một tài nguyên đã được sao lưu ít nhất một lần, nó được coi là đã được bảo vệ và có thể được khôi phục bằng AWS Backup. Trong tài khoản của bạn, có thể một bản sao lưu chưa có sẵn. Nếu vậy, hãy xem qua các ảnh chụp màn hình thay vì thực hiện điều này trong tài khoản của riêng bạn.

1. Trên trang **Protected resources** (Tài nguyên được bảo vệ), bạn có thể xem chi tiết về các tài nguyên được sao lưu trong AWS Backup. Chọn tài nguyên bảng DynamoDB của chúng ta.

![Sao lưu theo lịch trình 11](/images/1/1.4/1.4.4/11.png)

2. Chọn ID điểm khôi phục của tài nguyên. Nhấp vào **Restore**. _Lưu ý: Nếu bạn không thấy điểm khôi phục, bạn có thể nhấp vào "Create an on-demand backup" và hoàn tất sao lưu. Đối với mục đích của bài thực hành này, bạn cần một bản sao lưu đã hoàn thành để tiếp tục và bạn có thể không muốn chờ đợi bản sao lưu đã lên lịch trong kế hoạch sao lưu của mình._

![Sao lưu theo lịch trình 12](/images/1/1.4/1.4.4/12.png)

3. Cung cấp tên bảng DynamoDB mới. Giữ tất cả các cài đặt mặc định và nhấp vào **Restore backup**.

![Sao lưu theo lịch trình 13](/images/1/1.4/1.4.4/13.png)

Bảng **Restore jobs** (Công việc khôi phục) xuất hiện. Một thông báo ở đầu trang cung cấp thông tin về công việc khôi phục. Bạn sẽ thấy trạng thái công việc đang chạy. Sau một thời gian, bạn sẽ thấy trạng thái thay đổi thành hoàn tất.

![Sao lưu theo lịch trình 14](/images/1/1.4/1.4.4/14.png)

Bạn cũng có thể giám sát tất cả các công việc sao lưu và khôi phục trong bảng điều khiển trung tâm.

![Sao lưu theo lịch trình 15](/images/1/1.4/1.4.4/15.png)

Để xem bảng đã khôi phục, truy cập [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/) và nhấp vào **_Tables_** từ menu bên. Chọn bảng **ProductCatalogRestored**. Bạn sẽ thấy bảng đã được khôi phục cùng với dữ liệu.

![Sao lưu theo lịch trình 16](/images/1/1.4/1.4.4/16.png)