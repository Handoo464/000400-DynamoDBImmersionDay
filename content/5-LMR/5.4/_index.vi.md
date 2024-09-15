---
title : "Module 3: Tương tác với Giao diện Globalflix"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

### Điều hướng đến Ứng dụng Web Globalflix

Nhấp vào liên kết dưới đây để mở ứng dụng web Globalflix, hoặc nếu bạn vẫn còn mở ứng dụng từ mô-đun 2, hãy nhấp vào logo Globalflix ở góc trên bên phải. [https://amazon-dynamodb-labs.com/static/global-serverless-application/web/globalflix.html](https://amazon-dynamodb-labs.com/static/global-serverless-application/web/globalflix.html) 

Nếu bạn đã tải thành công các URL API trong mô-đun trước, bạn sẽ thấy một lưới gồm 12 hình thu nhỏ của video. Đây là kết quả của một truy vấn chống lại DynamoDB cho dữ liệu mẫu mà bạn đã tải trong mô-đun 1.

Khu vực API mà bạn đã thiết lập trong mô-đun trước sẽ được hiển thị ở góc trên bên phải, với khu vực "đang hoạt động" hiện tại được đánh dấu màu cam đậm.

1. Chọn khu vực được viền để thực hiện "chuyển đổi" cục bộ sang khu vực thứ hai

Để tiết kiệm thời gian trong mô-đun này, chúng ta sẽ thực hiện chuyển đổi khu vực trong ứng dụng web, nhưng trong sản xuất, một mô hình phổ biến hơn là sử dụng [Amazon Route 53 Application Recovery Controller](https://aws.amazon.com/route53/application-recovery-controller/) để quản lý các kiểm tra sức khỏe, chuyển đổi và khôi phục các dịch vụ khu vực của bạn.

![globalflix](/images/5/5.4/1.png)
### Xem một video để bắt đầu tải các điểm đánh dấu vào cơ sở dữ liệu

2. Nhấp vào bất kỳ video nào để tải trang trình phát video.

Trên trang này, video đã chọn sẽ bắt đầu phát ở giữa với các chỉ số sau được hiển thị xung quanh (từ trái sang phải):

- Tiến trình Video: Dấu thời gian hiện tại của tiến trình qua video như được thấy bởi trình duyệt web của bạn
- Độ trễ ghi: Thời gian tính bằng mili giây để ghi điểm đánh dấu cuối cùng vào cơ sở dữ liệu
- Tiến trình Khu vực 1 và 2: Dấu thời gian hiện tại của tiến trình qua video khi đọc mục điểm đánh dấu từ mỗi khu vực

Bên dưới trình phát, bạn có thể thấy nhật ký của mỗi thao tác ghi được thực hiện, lưu ý đến khu vực đang được sử dụng.

![player](/images/5/5.4/2.png)

### Mô phỏng sự cố Khu vực

3. Quay lại bảng điều khiển AWS và tìm kiếm "Lambda" bằng thanh tìm kiếm ở trên cùng
4. Một hàm có tên "global-serverless-dev" sẽ được liệt kê trên trang các hàm, nhấp vào tên hàm đó. Nếu bạn không thấy nó được liệt kê, hãy kiểm tra để đảm bảo bạn đang ở một trong hai khu vực mà bạn đã triển khai với Chalice ở góc trên bên phải của trang
5. Sử dụng nút **Throttle** ở góc trên bên phải của trang để đặt mức độ đồng thời tối đa của hàm Lambda thành 0, dừng bất kỳ lời gọi nào trong tương lai của hàm trong khu vực này.

![lambda_throttle](/images/5/5.4/3.png)

6. Quay lại trình phát video Globalflix và quan sát rằng một lỗi API trong khu vực đó đã được phát hiện

![ui_error](/images/5/5.4/4.png)

7. Chờ ứng dụng chờ đợi để chuyển đổi khu vực

![ui_failover](/images/5/5.4/5.png)

Mặc dù ngăn xếp ứng dụng trong khu vực đó hiện không phản hồi, vì chúng ta đang sử dụng Bảng Toàn cầu DynamoDB, các cập nhật dữ liệu vẫn được sao chép vào khu vực đó. Khi dịch vụ khôi phục, chúng ta không cần lo lắng về việc mất dữ liệu trong thời gian ngừng hoạt động.

Bạn có thể xác minh điều này nếu muốn bằng cách chạy truy vấn chống lại Bảng DynamoDB "global-serverless" ở mỗi khu vực của bạn

```bash
aws dynamodb query \
    --table-name global-serverless \
    --region us-west-2 \
    --key-condition-expression "PK = :PK" \
    --expression-attribute-values '{":PK": {"S": "user10"}}' \
    --query 'Items[*].bookmark.S' \
    --output text | awk '{print $1": us-west-2"}'
aws dynamodb query \
    --table-name global-serverless \
    --region eu-west-1 \
    --key-condition-expression "PK = :PK" \
    --expression-attribute-values '{":PK": {"S": "user10"}}' \
    --query 'Items[*].bookmark.S' \
    --output text | awk '{print $1": eu-west-1"}'
```

8. Quay lại bảng điều khiển Lambda và nhấp vào "Edit concurrency" ở góc trên bên phải

![lambda_unthrottle](/images/5/5.4/6.png)

9. Chọn nút "Use unreserved account concurrency" và sau đó nhấp vào Save

![lambda_concurrency](/images/5/5.4/7.png)

Ứng dụng linh hoạt của bạn đã thành công xử lý sự cố ngăn xếp khu vực, chuyển đổi sang khu vực thay thế, và chuyển đổi trở lại, tất cả mà không mất dữ liệu hoặc ảnh hưởng đến trải nghiệm người dùng.