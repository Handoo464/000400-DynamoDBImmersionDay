---
title : "Mô-đun 2: Khám phá Global Tables"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

### Tổng quan về ứng dụng

Chúc mừng! Hiện tại bạn đã có một stack ứng dụng serverless chạy tại cả Oregon và Ireland. Các stack mà bạn triển khai với Chalice mỗi cái đều chứa các thành phần cốt lõi sau:

- **Dịch vụ web Amazon API Gateway** phản hồi các lệnh gọi HTTP GET và chuyển tiếp chúng đến hàm Lambda.
- **Hàm AWS Lambda** thực hiện các yêu cầu đọc và ghi đến bảng DynamoDB bằng Python.
- **Vai trò AWS IAM** để cấp các quyền cần thiết.

Sau đó, bạn đã sử dụng AWS Command Line Interface để triển khai một DynamoDB Global Table có tên **global-serverless** và điền vào đó một số mục. Những mục này đại diện cho các bản ghi bookmark có thể được thiết lập để ghi lại và truy xuất tiến trình mà khách hàng đã đạt được khi xem nội dung video. Ngoài ra còn có các mục đại diện cho một danh mục nội dung video có sẵn.

![Kiến trúc Serverless Toàn cầu](/images/5/5.3/1.png "Global Serverless Architecture")

### Chi tiết về DynamoDB Global Table

#### Thiết kế Bookmark

Bảng của chúng ta có khóa chính hai phần bao gồm một Partition Key và một Sort Key, được đặt tên là PK và SK. Mỗi bản ghi bookmark sẽ có UserID của người xem làm giá trị PK, và một giá trị ContentID trong SK để chỉ ra video mà họ đang xem. Thuộc tính thứ ba, gọi là Bookmark, sẽ ghi lại tiến trình. Ứng dụng sẽ thực hiện các cập nhật định kỳ cho thuộc tính Bookmark này khi chương trình được xem. Nếu người dùng dừng lại và sau đó quay lại sau, một lệnh gọi Get-Item có thể xác định vị trí bookmark, đọc giá trị Bookmark, và xếp hàng cho trình phát video đến đúng điểm để khách hàng tiếp tục xem.

#### Hiệu suất Sao chép

Khi stack ứng dụng ở Oregon thực hiện các lệnh gọi tới DynamoDB, nó kết nối tới bảng DynamoDB trong cùng vùng. Chúng ta có thể gọi bảng cục bộ này là một bản sao vùng vì nó tham gia vào sao chép Global Tables. Ghi vào bất kỳ bản sao vùng nào sẽ được dịch vụ DynamoDB phát hiện và hình ảnh mục mới sẽ được gửi và áp dụng cho tất cả các bản sao vùng khác. Mục tiêu của Global Tables là đưa tất cả các bản sao đến trạng thái giống nhau càng nhanh càng tốt. Người gọi như hàm Lambda của chúng ta ở Oregon không cần phải biết về các vùng khác và không kết nối đến bất kỳ điểm cuối toàn cầu nào vì không có điểm cuối nào như vậy.

Các thao tác ghi vào một bảng bản sao được xác nhận thành công với người gọi với hiệu suất tương tự như một bảng không phải là Global Table, thường là trong vòng 10 mili giây.

Khoảng cách giữa Oregon và Ireland là 4500 dặm (7000 km). Thông tin truyền với tốc độ ánh sáng sẽ vượt qua khoảng cách này trong 24 ms.

![Khoảng cách từ Oregon đến Ireland](/images/5/5.3/2.png "Distance Oregon to Ireland")

Thời gian DynamoDB cần để sao chép các thay đổi đến các vùng khác có thể thay đổi, nhưng thường là từ 1-2 giây. Thống kê **ReplicationLatency** trong Cloudwatch theo dõi thời gian cần thiết để sao chép các mục.

### Kiểm tra các trường hợp cạnh trong ứng dụng web

Hãy chứng minh rằng sao chép Global Tables đang hoạt động.

1. Nhấp vào nút cộng `+` trong vùng đầu tiên và chú ý rằng giá trị bookmark mới được hiển thị.
2. Nhấp vào Get-Item trong vùng thứ hai và so sánh giá trị với bookmark trong vùng đầu tiên. Nếu chúng giống nhau, điều đó có nghĩa là Global Tables đã áp dụng trạng thái mới cho tất cả các vùng.
3. Lặp lại các bước 1 và 2 nhanh nhất có thể.

![Độ trễ sao chép](/images/5/5.3/3.png "Replication Delay")

Mục tiêu là kiểm tra bookmark trong vùng thứ hai trước khi việc sao chép hoàn tất. Vì sao chép có thể diễn ra trong khoảng một giây, bạn sẽ phải nhanh chóng để phát hiện điều này. Nếu bạn làm được, thì nhấp lại vào Get-Item và giá trị đã đồng bộ hóa sẽ được hiển thị.  
Lưu ý ứng dụng giữ một bộ đếm thời gian sau mỗi lần cập nhật, do đó bạn có thể xem đã bao nhiêu giây trôi qua khi thực hiện đọc tiếp theo.

#### Tạo một xung đột

Có một trường hợp cạnh với Global Tables xảy ra khi các thao tác ghi vào cùng một mục diễn ra đồng thời ở các vùng khác nhau. Nếu các thao tác ghi xung đột trong khoảng 1-2 giây của quá trình sao chép đang diễn ra, DynamoDB sẽ phát hiện điều này là một Xung đột và đưa ra quyết định về thao tác ghi nào sẽ thắng trong xung đột. Các dấu thời gian của các bản cập nhật được so sánh và thao tác ghi sau trở thành người chiến thắng. Thao tác ghi trước đó bị bỏ qua như thể nó chưa từng xảy ra.

1. Ở vùng thứ nhất, nhấp vào Get-Item và ghi lại giá trị bookmark hiện tại.
2. Tiếp theo, nhấp vào nút cộng.
3. Ở vùng thứ hai, ngay lập tức nhấp vào nút trừ.
4. Kiểm tra kỹ đầu ra. Vùng thứ hai có thay đổi giá trị trở lại giá trị ban đầu không? Nếu có, thì không có xung đột. Bản cập nhật đầu tiên đã sao chép hoàn toàn trước khi bản cập nhật thứ hai bắt đầu. Nếu bản cập nhật thứ hai đã giảm bookmark thấp hơn giá trị ban đầu, điều đó cho thấy ĐÃ có xung đột và bản cập nhật đầu tiên đã bị hoàn tác.

![Xung đột sao chép](/images/5/5.3/4.png "Replication Conflict")

Bạn có thể đọc thêm về DynamoDB Global Tables trong chương cuối của buổi workshop này hoặc trong [Tài liệu về Global Tables](https://aws.amazon.com/dynamodb/global-tables/).