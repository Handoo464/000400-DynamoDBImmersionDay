---
title : "Sao lưu"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.4. </b> "
---

[Amazon DynamoDB](https://aws.amazon.com/dynamodb/) cung cấp hai loại sao lưu: [sao lưu theo yêu cầu (on-demand)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BackupRestore.html) và [khôi phục theo thời gian (point-in-time recovery - PITR)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/PointInTimeRecovery.html). PITR hoạt động theo cửa sổ thời gian cuốn chiếu, còn sao lưu theo yêu cầu sẽ tồn tại mãi mãi (ngay cả khi bảng bị xóa) cho đến khi có người yêu cầu DynamoDB xóa các bản sao lưu. Khi bạn bật PITR, DynamoDB tự động sao lưu dữ liệu bảng của bạn với độ chính xác đến từng giây. Thời gian lưu trữ là cố định trong 35 ngày (5 tuần lịch) và không thể thay đổi. Tuy nhiên, các khách hàng doanh nghiệp lớn, những người đã quen với việc triển khai các giải pháp sao lưu truyền thống trong các trung tâm dữ liệu của họ, thường muốn một giải pháp sao lưu tập trung có thể lên lịch sao lưu thông qua các “công việc” và xử lý các tác vụ như hết hạn/xóa các bản sao lưu cũ theo thời gian, giám sát trạng thái của các bản sao lưu đang diễn ra, xác minh tuân thủ, và tìm kiếm/khôi phục các bản sao lưu, tất cả đều sử dụng một giao diện điều khiển trung tâm. Đồng thời, họ không muốn quản lý các bản sao lưu của mình thủ công, tạo công cụ riêng thông qua các script hoặc chức năng [AWS Lambda](https://aws.amazon.com/lambda/), hoặc sử dụng một giải pháp khác cho mỗi ứng dụng mà họ phải bảo vệ. Họ muốn có khả năng quản lý sao lưu của mình một cách tiêu chuẩn hóa ở quy mô lớn cho các tài nguyên trong tài khoản AWS của họ.

[AWS Backup](https://aws.amazon.com/backup/) nhằm mục đích trở thành điểm quản lý sao lưu tập trung duy nhất và là nguồn thông tin đáng tin cậy mà khách hàng có thể tin cậy. Bạn có thể lên lịch sao lưu định kỳ hoặc sao lưu trong tương lai bằng cách sử dụng AWS Backup. Các kế hoạch sao lưu bao gồm lịch trình và chính sách lưu giữ cho các tài nguyên của bạn. AWS Backup tạo các bản sao lưu và xóa các bản sao lưu trước đó dựa trên lịch lưu giữ của bạn. Các bản sao lưu của tài nguyên luôn cần thiết trong trường hợp khôi phục sau thảm họa.

AWS Backup loại bỏ công việc thủ công nặng nề của việc tạo và xóa các bản sao lưu theo yêu cầu bằng cách tự động hóa lịch trình và xóa cho bạn. Trong bài thực hành này, chúng ta sẽ khám phá cách lên lịch sao lưu định kỳ cho một bảng DynamoDB bằng cách sử dụng AWS Backup. Tôi sẽ tạo một kế hoạch sao lưu mà trong đó tôi thực hiện sao lưu hàng ngày và giữ nó trong một tháng. Tiếp theo, tôi thiết lập một quy tắc sao lưu để chuyển bản sao lưu sang lưu trữ lạnh sau 31 ngày và tự động xóa bản sao lưu sau 366 ngày kể từ ngày tạo bản sao lưu.

Ngoài ra, tôi sẽ chỉ cho bạn cách bạn có thể hạn chế người trong tổ chức của mình xóa các bản sao lưu từ AWS Backup và DynamoDB console trong khi vẫn có thể thực hiện các thao tác khác như tạo bản sao lưu, tạo bảng, v.v.

Bây giờ hãy cùng đi sâu vào các tùy chọn sao lưu DynamoDB khác nhau.