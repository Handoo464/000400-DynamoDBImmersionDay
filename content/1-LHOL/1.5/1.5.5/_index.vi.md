---
title : "Explore Target Model"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 1.5.5. </b> "
---

Hệ thống quản lý cơ sở dữ liệu quan hệ (Relational Database Management System - RDBMS) lưu trữ dữ liệu trong một cấu trúc quan hệ đã chuẩn hóa. Cấu trúc này giúp giảm bớt các cấu trúc dữ liệu dạng phân cấp và lưu trữ dữ liệu trên nhiều bảng. Bạn thường có thể truy vấn dữ liệu từ nhiều bảng và tập hợp chúng tại lớp trình bày. Tuy nhiên, điều này không phải lúc nào cũng phù hợp và sẽ không hiệu quả cho các khối lượng công việc yêu cầu độ trễ đọc cực thấp (ultra-low read latency workloads). Để hỗ trợ các truy vấn có lưu lượng cao với độ trễ cực thấp, việc thiết kế một schema để tận dụng hệ thống NoSQL thường có ý nghĩa cả về mặt kỹ thuật lẫn kinh tế.

Để bắt đầu thiết kế mô hình dữ liệu mục tiêu trong Amazon DynamoDB sao cho mở rộng hiệu quả, bạn cần xác định các mẫu truy cập phổ biến (common access patterns). Đối với trường hợp sử dụng IMDb, chúng tôi đã xác định một tập hợp các mẫu truy cập như mô tả dưới đây: ![Final Deployment Architecture](/images/1/1.5/16.png)

Một cách tiếp cận phổ biến để thiết kế schema cho DynamoDB là xác định các thực thể ở tầng ứng dụng và sử dụng việc không chuẩn hóa (denormalization) và tổng hợp khóa tổng hợp (composite key aggregation) để giảm độ phức tạp của truy vấn. Trong DynamoDB, điều này có nghĩa là sử dụng [composite sort keys (khóa sắp xếp tổng hợp)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-sort-keys.html), [overloaded global secondary indexes (chỉ mục thứ cấp toàn cầu quá tải)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-gsi-overloading.html), và các mẫu thiết kế khác.

Trong kịch bản này, chúng ta sẽ áp dụng [Adjacency List Design Pattern (mẫu thiết kế danh sách kề)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-adjacency-graphs.html#bp-adjacency-lists) với việc sử dụng khóa chính quá tải (primary key overloading) để lưu trữ dữ liệu quan hệ trong bảng DynamoDB. Lợi thế của mẫu thiết kế này bao gồm việc tối ưu hóa sự sao chép dữ liệu và đơn giản hóa các mẫu truy vấn để tìm tất cả metadata liên quan đến mỗi bộ phim. Thông thường, mẫu thiết kế danh sách kề lưu trữ dữ liệu trùng lặp dưới hai mục, mỗi mục đại diện cho một nửa của mối quan hệ. Để liên kết một tiêu đề với một vùng (region), ví dụ, bạn sẽ ghi một mục cho vùng dưới tiêu đề và một mục cho tiêu đề dưới vùng, như sau:

|Partition Key|Sort Key|Attribute List|
|---|---|---|
|tt0309377|REGN\|NZ|ordering, language, region, title, types|
|REGN\|NZ|tt0309377|language, region, title|

Tuy nhiên, trong bài tập này, chúng ta chỉ làm việc với một bên của mối quan hệ: dữ liệu nằm dưới tiêu đề. Khóa phân vùng (partition key) của chúng ta sẽ là `mpkey` và khóa sắp xếp (sort key) sẽ là `mskey` cho bảng phim. Mỗi khóa phân vùng và khóa sắp xếp được tiền tố bằng các chữ cái để xác định loại thực thể, và khóa sắp xếp sử dụng `|` như một dấu phân cách giữa loại thực thể và giá trị. Khóa phân vùng được tiền tố bằng `tt` (mã phim duy nhất) và khóa sắp xếp được quá tải để xác định loại thực thể.

Các loại thực thể sau được tìm thấy trong bảng:

- `tt`: Mã phim duy nhất. Đây là loại thực thể của khóa phân vùng trong bảng cơ sở.
- `nm`: Một mục duy nhất cho mỗi thành viên đoàn làm phim. Đây là loại thực thể của khóa phân vùng GSI.
- `DETL`: Chứa thông tin diễn viên/đoàn làm phim cho mỗi bộ phim. Có mối quan hệ 1: nhiều giữa `title_basics` và `title_principals`. `title_principals` chứa tất cả thông tin diễn viên và đoàn làm phim được lưu trữ dưới dạng các dòng riêng biệt cho mỗi bộ phim, trong khi `title_basics` chứa metadata về bộ phim. Thông tin trong cả hai bảng này được coi là tĩnh sau khi một bộ phim được công bố. Các mẫu truy cập yêu cầu thông tin phim và diễn viên/đoàn làm phim phải được truy xuất cùng nhau. Trong quá trình mô hình hóa mục tiêu, mỗi metadata của thành viên diễn viên/đoàn làm phim (diễn viên, đạo diễn, nhà sản xuất, v.v.) được không chuẩn hóa với thông tin phim và lưu trữ với loại thực thể `DETL`.
- `REGN`: Chứa tất cả các vùng, ngôn ngữ và tiêu đề mà một bộ phim được công bố. Trong quá trình mô hình hóa mục tiêu, dữ liệu được chuyển đổi nguyên bản vào bảng DynamoDB.
- `RTNG`: Chứa xếp hạng IMDb và số phiếu bầu. Đây được coi là các bản ghi động và thường xuyên thay đổi cho một bộ phim. Để giảm I/O trong kịch bản cập nhật, bản ghi này không được không chuẩn hóa với các thông tin khác trong bảng DynamoDB.

![Final Deployment Architecture](/images/1/1.5/17.png)

Một chỉ mục GSI mới được tạo trên bảng phim với khóa phân vùng mới là `nconst` (duy nhất cho mỗi thành viên đoàn làm phim với loại thực thể `nm`) và khóa sắp xếp là `startYear`. Điều này sẽ giúp truy vấn mẫu truy cập theo thành viên đoàn làm phim (số 6 trong bảng mẫu truy cập chung).

![Final Deployment Architecture](/images/1/1.5/18.png)

[Nhấp vào đây để xem video minh họa cách tất cả các mẫu truy cập này được đánh giá đối với mô hình DynamoDB mục tiêu](/images/1/1.5/1.mp4).