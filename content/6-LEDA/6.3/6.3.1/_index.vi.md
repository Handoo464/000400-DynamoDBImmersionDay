---
title : "Bước 1: Kết nối StateLambda"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 6.3.1. </b> "
---

Mục tiêu của bước này là kết nối hàm `StateLambda` với luồng Kinesis `IncomingDataStream` như minh họa trong sơ đồ dưới đây. Khi các thông điệp mới có sẵn trong luồng Kinesis, hàm `StateLambda` sẽ được kích hoạt để xử lý các bản ghi luồng. Mỗi bản ghi luồng chứa một giao dịch đơn lẻ.

![Architecture-1](/images/6/6.3/2.png)

## Kết nối hàm StateLambda với Kinesis Data Stream

1. Sử dụng AWS Management Console và điều hướng đến dịch vụ AWS Lambda trong bảng điều khiển.
2. Nhấp vào hàm `StateLambda` để chỉnh sửa cấu hình của nó. Xem hình dưới đây.
3. Tổng quan hàm cho thấy rằng hàm `StateLambda` chưa có bất kỳ trình kích hoạt nào. Nhấp vào nút `Add trigger`.

![Architecture-1](/images/6/6.3/3.png)

4. Chỉ định cấu hình sau (xem hình bên dưới để biết chi tiết):
   - Trong menu thả xuống đầu tiên, chọn `Kinesis` làm nguồn dữ liệu.
   - Đối với luồng Kinesis, chọn `IncomingDataStream`.
   - Đặt `Batch size` thành `100`.

![Architecture-1](/images/6/6.3/4.png)

5. Nhấp `Add` ở góc dưới bên phải để tạo và kích hoạt một ánh xạ nguồn sự kiện trên hàm Lambda.

Tại thời điểm này, bạn đã cấu hình một kết nối dựa trên sự kiện giữa Kinesis Data Streams và AWS Lambda. Hàm `StateLambda` sẽ được kích hoạt mỗi khi có thông điệp mới xuất hiện trong `IncomingDataStream`. Các thông điệp sẽ được truyền đến hàm Lambda theo từng lô tối đa 100 thông điệp một lần.

## Làm thế nào để biết nó đang hoạt động?

Nếu tất cả đã được thực hiện đúng, thì hàm `StateLambda` sẽ được kích hoạt với các bản ghi luồng từ luồng Kinesis. Do đó, trong vòng một đến hai phút, bạn sẽ có thể thấy các bản ghi từ các lần gọi Lambda dưới mục `Monitor` trong tab `Logs`.

![Architecture-1](/images/6/6.3/5.png)

Bạn cũng có thể quan sát đầu ra của `StateLambda` để xác minh kết nối bằng cách xem phần `Items` trong bảng điều khiển DynamoDB. Để làm điều đó, điều hướng đến dịch vụ DynamoDB trong bảng điều khiển AWS, nhấp vào `Items` ở bên trái, và chọn `StateTable`.

Tại giai đoạn này, bạn sẽ thấy nhiều hàng tương tự như hình dưới đây. Số lượng mục trả về có thể khác nhau. Bạn có thể nhấp vào nút màu cam `Run` nếu muốn làm mới các mục.

![Architecture-1](/images/6/6.3/6.png)

