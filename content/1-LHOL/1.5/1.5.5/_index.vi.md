---
title : "Explore Target Model"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 1.5.5. </b> "
---


Nền tảng Hệ thống quản lý cơ sở dữ liệu quan hệ (RDBMS) lưu trữ dữ liệu trong cấu trúc quan hệ chuẩn hóa. Cấu trúc này làm giảm cấu trúc dữ liệu phân cấp và lưu trữ dữ liệu trên nhiều bảng. Bạn thường có thể truy vấn dữ liệu từ nhiều bảng và tập hợp ở lớp bản trình bày. Tuy nhiên, điều đó không thích hợp hơn và sẽ không hiệu quả đối với khối lượng công việc có độ trễ đọc cực thấp. Để hỗ trợ các truy vấn lưu lượng truy cập cao với độ trễ cực thấp, việc thiết kế lược đồ để tận dụng lợi thế của hệ thống NoSQL thường có ý nghĩa kỹ thuật và kinh tế.

Để bắt đầu thiết kế mô hình dữ liệu đích trong Amazon DynamoDB có quy mô hiệu quả, bạn phải xác định các mẫu hình truy cập phổ biến. Đối với trường hợp sử dụng IMDb, chúng tôi đã xác định một tập hợp các mẫu hình truy cập như được mô tả bên dưới: ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration32.png)

Một cách tiếp cận phổ biến đối với thiết kế sơ đồ DynamoDB là xác định các thực thể lớp ứng dụng và sử dụng tính năng không chuẩn hóa và tổng hợp khóa tổng hợp để giảm độ phức tạp của truy vấn. Trong DynamoDB, điều này có nghĩa là sử dụng [Các phím sắp xếp tổng hợp](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-sort-keys.html) , [Chỉ số thứ cấp toàn cầu quá tải](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-gsi-overloading.html) và các mẫu thiết kế khác.

Trong trường hợp này, chúng tôi sẽ làm theo [Mẫu thiết kế danh sách liền kề](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-adjacency-graphs.html#bp-adjacency-lists)  với tình trạng quá tải khóa chính để lưu trữ dữ liệu quan hệ trong bảng DynamoDB. Ưu điểm của mẫu này bao gồm sao chép dữ liệu tối ưu và các mẫu truy vấn đơn giản hóa để tìm tất cả siêu dữ liệu liên quan đến từng bộ phim. Thông thường, mẫu danh sách liền kề lưu trữ dữ liệu trùng lặp dưới hai mục, mỗi mục đại diện cho một nửa mối quan hệ. Ví dụ: để liên kết tiêu đề với một khu vực, bạn sẽ viết một mục cho khu vực bên dưới tiêu đề và một mục bên dưới tiêu đề bên dưới khu vực, như thế này:

|Khóa phân vùng|Phím sắp xếp|Danh sách thuộc tính|
|---|---|---|
|TT0309377|REGN \|NZ|Đặt hàng, ngôn ngữ, khu vực, tiêu đề, loại|
|REGN \|NZ|TT0309377|Ngôn ngữ, khu vực, tiêu đề|

Tuy nhiên, trong phòng thí nghiệm này, chúng tôi sẽ chỉ làm việc với một mặt của mối quan hệ: dữ liệu nằm dưới tiêu đề. Khóa phân vùng của chúng tôi sẽ là và khóa sắp xếp của chúng tôi cho bảng phim. Mỗi phân vùng và khóa sắp xếp có tiền tố bằng các chữ cái để xác định loại thực thể và khóa sắp xếp sử dụng làm dấu phân cách giữa loại thực thể và giá trị. Khóa phân vùng có tiền tố (id phim duy nhất) và khóa sắp xếp bị quá tải để xác định loại thực thể.`mpkey``mskey``|``tt`

Các loại thực thể sau đây được tìm thấy trong bảng:

- `tt`: Một id phim duy nhất. Đây là loại thực thể của khóa phân vùng trong bảng cơ sở
- `nm`: Một mục duy nhất cho mỗi thành viên đoàn làm phim. Đây là loại thực thể của khóa phân vùng GSI
- `DETL`: Chứa thông tin diễn viên / đoàn làm phim cho mỗi bộ phim. Có 1: nhiều mối quan hệ giữa title_basics và title_principals. title_principals có tất cả thông tin về dàn diễn viên và đoàn làm phim được lưu trữ dưới dạng các hàng riêng biệt cho mỗi bộ phim, trong khi title_basics có siêu dữ liệu phim. Thông tin trong cả hai bảng được coi là tĩnh sau khi phim được xuất bản. Các mẫu hình truy cập yêu cầu các bộ phim và thông tin diễn viên / đoàn làm phim phải được tìm nạp cùng nhau. Trong quá trình mô hình hóa mục tiêu, mỗi diễn viên / thành viên đoàn làm phim (diễn viên, đạo diễn, nhà sản xuất, v.v.) siêu dữ liệu được không chuẩn hóa với thông tin phim và được lưu trữ với loại thực thể.`DETL`
- `REGN`: Chứa tất cả các vùng, ngôn ngữ và tiêu đề mà phim được xuất bản. Trong quá trình lập mô hình đích, dữ liệu được di chuyển sang bảng DynamoDB.
- `RTNG`: Chứa xếp hạng IMDb và số phiếu bầu. Đây được coi là những kỷ lục năng động và thường xuyên thay đổi cho một bộ phim. Để giảm I/O trong kịch bản cập nhật, bản ghi không bị hủy chuẩn hóa với các thông tin khác trong bảng DynamoDB.

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration33.png)

Một GSI mới được tạo trên bảng phim với phím partion mới: (duy nhất cho mỗi đoàn làm phim với loại thực thể) và phím sắp xếp: . Điều này sẽ giúp truy vấn mẫu hình truy cập theo thành viên phi hành đoàn (#6 bên trong bảng mẫu truy cập chung)`nconst``nm``startYear`

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration34.png)

[Nhấp vào đây để xem video minh họa cách đánh giá tất cả các mẫu hình truy cập này so với mô hình DynamoDB đích](https://www.amazondynamodblabs.com/static/rdbms-migration/migration36.mp4)
