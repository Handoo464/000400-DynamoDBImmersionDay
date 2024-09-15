---
title : "Xem lại Access Patterns"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 7.2.3. </b> "
---

Hãy cùng xem xét các mẫu truy cập khác nhau mà chúng ta cần mô hình hóa dữ liệu.

### Xem xét các mẫu truy cập hồ sơ người dùng

Người dùng của ứng dụng trò chơi cần tạo hồ sơ người dùng. Những hồ sơ này bao gồm dữ liệu như tên người dùng, ảnh đại diện, thống kê trò chơi, và các thông tin khác về mỗi người dùng. Trò chơi hiển thị các hồ sơ người dùng khi một người dùng đăng nhập. Các người dùng khác có thể xem hồ sơ của một người dùng để xem xét thống kê trò chơi và các chi tiết khác.

Khi một người dùng chơi các trò chơi, thống kê trò chơi sẽ được cập nhật để phản ánh số lượng trò chơi mà người dùng đã chơi, số lượng trò chơi mà người dùng đã thắng, và số lần tiêu diệt kẻ địch của người dùng.

Dựa trên thông tin này, bạn có ba mẫu truy cập:

- Tạo hồ sơ người dùng (Ghi)
- Cập nhật hồ sơ người dùng (Ghi)
- Lấy hồ sơ người dùng (Đọc)

### Xem xét các mẫu truy cập trước trò chơi

Trò chơi là một trò chơi trực tuyến nhiều người chơi tương tự như [Fortnite](https://www.epicgames.com/fortnite) hoặc [PUBG](https://www.pubg.com/). Người chơi có thể tạo một trò chơi trên một bản đồ cụ thể, và các người chơi khác có thể tham gia trò chơi đó. Khi đã có 50 người chơi tham gia, trò chơi sẽ bắt đầu và không thể thêm người chơi nào khác.

Khi tìm kiếm các trò chơi để tham gia, người chơi có thể muốn chơi trên một bản đồ cụ thể. Những người chơi khác có thể không quan tâm đến bản đồ và chỉ muốn duyệt các trò chơi mở trên tất cả các bản đồ.

Dựa trên thông tin này, bạn có bảy mẫu truy cập sau:

- Tạo trò chơi (Ghi)
- Tìm các trò chơi mở (Đọc)
- Tìm các trò chơi mở theo bản đồ (Đọc)
- Xem trò chơi (Đọc)
- Xem người chơi trong trò chơi (Đọc)
- Tham gia trò chơi cho một người dùng (Ghi)
- Bắt đầu trò chơi (Ghi)

### Các mẫu truy cập trong trò chơi và sau trò chơi

Cuối cùng, hãy xem xét các mẫu truy cập trong và sau khi kết thúc trò chơi.

Trong trò chơi, người chơi cố gắng tiêu diệt các người chơi khác với mục tiêu trở thành người chơi cuối cùng còn lại. Ứng dụng theo dõi số lần tiêu diệt của mỗi người chơi trong trò chơi cũng như thời gian sống sót của người chơi trong trò chơi. Nếu một người chơi là một trong ba người chơi cuối cùng sống sót trong trò chơi, người chơi sẽ nhận được huy chương vàng, bạc hoặc đồng cho trò chơi đó.

Sau đó, người chơi có thể muốn xem lại các trò chơi trước đây mà họ đã chơi hoặc các trò chơi mà người chơi khác đã chơi.

Dựa trên thông tin này, bạn có ba mẫu truy cập:

- Cập nhật trò chơi cho người dùng (Ghi)
- Cập nhật trò chơi (Ghi)
- Tìm tất cả các trò chơi trước đây cho một người dùng (Đọc)

### Đánh giá

Bây giờ bạn đã xác định được tất cả các mẫu truy cập cho ứng dụng trò chơi. Trong các mô-đun tiếp theo, bạn sẽ triển khai các mẫu truy cập này bằng cách sử dụng DynamoDB.

Lưu ý rằng giai đoạn lập kế hoạch có thể cần vài lần lặp lại. Bắt đầu với một ý tưởng tổng quát về các mẫu truy cập mà ứng dụng của bạn cần. Xác định khóa chính, chỉ mục phụ, và các thuộc tính trong bảng của bạn. Quay lại từ đầu và đảm bảo rằng tất cả các mẫu truy cập của bạn đều được đáp ứng. Khi bạn tự tin rằng giai đoạn lập kế hoạch đã hoàn tất, tiến tới giai đoạn triển khai.