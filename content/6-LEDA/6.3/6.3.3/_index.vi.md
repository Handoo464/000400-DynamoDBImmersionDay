---
title : "Bước 3: Kết nối ReduceLambda"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 6.3.3. </b> "
---

## Mục tiêu của bước cuối cùng trong _Lab 1_

Mục tiêu của bước cuối cùng trong _Lab 1_ là cấu hình chính xác hàm `ReduceLambda`, kết nối nó với DynamoDB stream của `ReduceTable`, và đảm bảo rằng tổng số liệu được ghi vào `AggregateTable`. Khi hoàn thành thành công bước này, bạn sẽ bắt đầu tích lũy điểm trên bảng xếp hạng.

![Kiến trúc-1](/images/6/6.3/10.png)

## Cấu hình Lambda concurrency

Chúng ta bắt đầu bằng cách thiết lập concurrency (đồng thời) của hàm `ReduceLambda` thành `1`. Điều này đảm bảo rằng chỉ có một instance hoạt động của hàm `ReduceLambda` tại bất kỳ thời điểm nào. Điều này cần thiết để tránh xung đột ghi, nơi mà nhiều instance cố gắng cập nhật `AggregateTable` cùng một lúc. Từ góc độ hiệu suất, một instance Lambda duy nhất có thể xử lý việc tổng hợp của toàn bộ pipeline vì các thông điệp đến đã được tổng hợp trước bởi các hàm `MapLambda`.

1. Điều hướng đến dịch vụ AWS Lambda trong AWS Management Console.
2. Nhấp vào hàm `ReduceLambda` để chỉnh sửa cấu hình của nó (xem hình bên dưới).
3. Mở tab `Configuration`, sau đó chọn `Concurrency` ở phía bên trái.
4. Nhấp vào nút `Edit` ở góc trên bên phải, chọn `Reserve concurrency` và nhập `1`.
5. Sau khi nhấp vào `Save`, cấu hình của bạn sẽ trông giống như hình dưới đây.

![Kiến trúc-1](/images/6/6.3/11.png)

## Kết nối ReduceLambda với stream của ReduceTable

Tiếp theo, chúng ta muốn kết nối hàm `ReduceLambda` với DynamoDB stream của `ReduceTable`.

1. Bảng tổng quan về hàm cho thấy hàm `ReduceLambda` chưa có trigger. Nhấp vào nút `Add trigger`. ![Kiến trúc-1](/images/6/6.3/12.png)
   
2. Cấu hình như sau:
   
   - Trong danh sách thả xuống, chọn `DynamoDB` làm nguồn dữ liệu.
   - Trong bảng DynamoDB, chọn `ReduceTable`.
   - Đặt `Batch size` là `1000`.
   
3. Nhấp vào nút `Add` ở góc dưới bên phải.

Bạn sẽ thấy một lỗi! Trước khi có thể kích hoạt trigger này, chúng ta cần thêm quyền IAM cho hàm Lambda này.

![Kiến trúc-1](/images/6/6.3/13.png)

## Thêm quyền IAM cần thiết

Thông báo lỗi trên cho biết hàm `ReduceLambda` không có đủ quyền để đọc từ stream của `ReduceTable`. Mặc dù chúng ta đã gán các vai trò IAM với các quyền cần thiết cho các hàm `StateLambda` và `MapLambda`, nhưng giờ là lúc bạn cần làm điều này cho hàm `ReduceLambda`:

1. Giữ tab Lambda console hiện tại mở trên trang bạn nhận được lỗi IAM khi cố gắng thêm trigger vào hàm `ReduceLambda`. Bạn sẽ cần nó mở để thử lại yêu cầu.
2. Mở một tab trình duyệt mới, đi đến dịch vụ AWS Lambda và chọn hàm `ReduceLambda`.
3. Điều hướng đến tab `Configuration` và nhấp vào `Permissions`. Bạn sẽ thấy vai trò thực thi Lambda có tên là `ReduceLambdaRole`. Nhấp vào vai trò này để chỉnh sửa nó.

![Kiến trúc-1](/images/6/6.3/14.png)

4. Bây giờ bạn đã được chuyển hướng đến dịch vụ IAM, nơi bạn thấy chi tiết của vai trò `ReduceLambdaRole`. Có một chính sách liên kết với vai trò này, đó là `ReduceLambdaPolicy`. Mở rộng để xem các quyền hiện tại của hàm `ReduceLambda`. Bây giờ, nhấp vào nút `Edit` để thêm các quyền bổ sung.

![Kiến trúc-1](/images/6/6.3/15.png)

### Chỉnh sửa chính sách IAM

{{%notice tip%}}
Đã có sẵn một quyền IAM cho DynamoDB: điều này cần thiết để đảm bảo workshop hoạt động như mong đợi. Đừng bị nhầm lẫn bởi điều này và xin đừng xóa các quyền mà chúng tôi đã cấp! Tất cả các hàm Lambda cần có quyền truy cập vào ParameterTable để kiểm tra tiến độ hiện tại của lab và các chế độ lỗi tương ứng.
{{%/notice%}}

- Trước tiên, chúng ta cần thêm quyền để hàm `ReduceLambda` có thể đọc các thông điệp từ stream của `ReduceTable`.
  - Nhấp vào `Add new statement` ![Kiến trúc-1](/images/6/6.3/16.png)
  - Đối với `Service`, chọn `DynamoDB`
  - Dưới `Access level - read`, chọn bốn ô sau: `DescribeStream`, `GetRecords`, `GetShardIterator`, và `ListStreams`

Bây giờ chúng ta cần liên kết các quyền này với các tài nguyên cụ thể (ví dụ: chúng ta muốn hàm `ReduceLambda` chỉ có thể đọc từ `ReduceTable`). Do đó, dưới `Add a resource`, nhấp vào `Add`. Sau đó trong `Resource type` chọn `stream`. Tiếp theo, điền vào các thông tin sau:

- `{Region}` - Lab mặc định sử dụng us-west-2, nhưng hãy xác nhận vùng của bạn và đảm bảo điền đúng
- `{Account}` - ID tài khoản AWS. Bạn có thể đặt dấu sao (*) nếu không muốn lấy ID tài khoản chính xác.
- `{TableName}` - Tên bảng phải là `ReduceTable`
- `{StreamLabel}` - Thêm dấu sao `*` để hỗ trợ bất kỳ nhãn stream nào. Nhãn stream là một định danh duy nhất cho một stream DynamoDB.
- Cuối cùng, nhấp vào `Add resource`. Bạn đã cấp quyền cho hàm `ReduceLambda` đọc từ stream của `ReduceTable`, nhưng vẫn còn nhiều việc cần làm.

{{%notice tip%}}
Hãy chắc chắn xóa tất cả các dấu ngoặc nhọn khỏi ARN của bạn trước khi nhấp vào `Add resource`
{{%/notice%}}

![Kiến trúc-1](/images/6/6.3/17.png)

Nếu chúng ta không thực hiện thêm thay đổi nào, hàm `ReduceLambda` sẽ không thể cập nhật kết quả cuối cùng trong `AggregateTable`. Chúng ta cần chỉnh sửa chính sách để thêm các quyền bổ sung cho phép truy cập `UpdateItem` cho hàm.

- Nhấp vào `Add new statement`
- Đối với `Service`, chọn `DynamoDB`
- Dưới `Access level - read or write`, chọn hộp kiểm `UpdateItem`
- Lại, chúng ta muốn liên kết các quyền này với một tài nguyên cụ thể: Chúng ta muốn hàm `ReduceLambda` chỉ có thể ghi vào `AggregateTable`. Do đó, nhấp vào `Add a resource` và trong danh sách thả xuống `Resource type` chọn `table`. Tiếp theo, nhập các giá trị cho `Region` (sử dụng cùng một vùng như trước), `Account` (cân nhắc sử dụng dấu sao `*`), và `TableName` (lần này là `AggregateTable`).
- Nhấp `Add resource`.

![Kiến trúc-1](/images/6/6.3/18.png)

- Cuối cùng, nhấp `Next` và sau đó `Save changes` ở góc dưới bên phải.

## Thử lại kết nối ReduceLambda với stream của ReduceTable

Nếu tất cả các bước trên được thực hiện đúng, bạn sẽ có thể kết nối hàm `ReduceLambda` với DynamoDB stream của `ReduceTable` bằng cách chuyển lại sang tab mở và thử nhấp vào `Add` một lần nữa. Bạn có thể cần đợi vài giây để các thay đổi chính sách IAM được truyền tải.

{{%notice tip%}}
Nếu bạn không thể thêm trigger, điều này có thể do cấu hình sai của chính sách IAM. Nếu bạn cần trợ giúp, hãy đi tới `Summary & Conclusions` ở bên trái, sau đó `Solutions`, và bạn sẽ thấy chính sách `ReduceLambdaPolicy` mong muốn.
{{%/notice%}}

## Làm sao để biết nó hoạt động?

Nếu mọi thứ được thực hiện đúng, thì DynamoDB stream của `ReduceTable` sẽ kích hoạt hàm `ReduceLambda`. Do đó, bạn sẽ thấy nhật ký cho mỗi lần gọi Lambda trong tab `Monitor` -> `Logs`.

Một cách khác để kiểm tra xem nó có hoạt động hay không là quan sát các mục được ghi bởi `ReduceLambda` vào bảng DynamoDB `AggregateTable`. Để làm điều đó, điều hướng đến dịch vụ DynamoDB trong AWS console, nhấp vào `Items` ở bên trái, và chọn `AggregateTable`. Tại thời điểm này, bạn sẽ thấy nhiều hàng tương tự như hình dưới đây.

![Các mục AggregateTable](/images/6/6.3/19.png)

{{%notice tip%}}
AWS Event: Nếu các bước 1, 2 và 3 của _Lab 1_ được hoàn thành thành công, bạn sẽ bắt đầu ghi điểm trong vòng một đến hai phút. Hãy kiểm tra bảng xếp hạng! Yêu cầu người điều hành phòng lab cung cấp liên kết đến bảng xếp hạng.
{{%/notice%}}