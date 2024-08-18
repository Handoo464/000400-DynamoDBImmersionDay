---
title : "Backups"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.4. </b> "
---

[Amazon DynamoDB](https://aws.amazon.com/dynamodb/)  cung cấp hai loại sao lưu, [on-demand](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BackupRestore.html)  và [point-in-time recovery](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/PointInTimeRecovery.html)  (PITR). PITR có tính chất cuộn (rolling window), trong khi các sao lưu theo yêu cầu sẽ tồn tại mãi mãi (ngay cả sau khi bảng bị xóa) cho đến khi ai đó yêu cầu DynamoDB xóa các sao lưu. Khi bạn bật PITR, DynamoDB tự động sao lưu dữ liệu bảng của bạn với độ chính xác theo giây. Thời gian giữ lại là cố định 35 ngày (5 tuần lịch) và không thể thay đổi. Tuy nhiên, những khách hàng lớn tại các doanh nghiệp thường quen thuộc với việc triển khai các giải pháp sao lưu truyền thống trong các trung tâm dữ liệu của họ muốn có một giải pháp sao lưu tập trung có thể lập lịch sao lưu qua các "công việc" và xử lý các tác vụ như hết hạn/xóa sao lưu cũ, theo dõi trạng thái của các sao lưu đang diễn ra, xác minh tuân thủ và tìm kiếm/kho phục hồi sao lưu, tất cả thông qua một bảng điều khiển trung tâm. Đồng thời, họ không muốn quản lý thủ công các sao lưu của mình, tạo các công cụ riêng của họ thông qua các kịch bản hoặc chức năng [AWS Lambda](https://aws.amazon.com/lambda/) , hay sử dụng một giải pháp khác cho từng ứng dụng mà họ cần bảo vệ. Họ muốn có khả năng quản lý sao lưu theo cách chuẩn hóa ở quy mô lớn cho các tài nguyên trong tài khoản AWS của họ.

[AWS Backup](https://aws.amazon.com/backup/)  aims to be the single point of centralize backup management and source of truth that customers can rely on. You can schedule periodic or future backups by using AWS Backup, The backup plans include schedules and retention policies for your resources. AWS Backup creates the backups and deletes prior backups based on your retention schedule. Backups of resources are always required in case of disaster recovery.

AWS Backup nhằm trở thành điểm quản lý sao lưu tập trung và nguồn thông tin đáng tin cậy mà khách hàng có thể dựa vào. Bạn có thể lên lịch sao lưu định kỳ hoặc trong tương lai bằng cách sử dụng AWS Backup. Các kế hoạch sao lưu bao gồm lịch trình và chính sách giữ lại cho các tài nguyên của bạn. AWS Backup thực hiện việc sao lưu và xóa các sao lưu trước đó dựa trên lịch trình giữ lại của bạn. Sao lưu tài nguyên luôn là cần thiết trong trường hợp khôi phục thảm họa.

AWS Backup loại bỏ những công việc nặng nhọc không phân loại của việc tạo và xóa sao lưu theo yêu cầu bằng cách tự động hóa lịch trình và việc xóa cho bạn. Trong bài lab này, chúng ta sẽ khám phá cách thiết lập sao lưu định kỳ của một bảng DynamoDB bằng AWS Backup. Tôi sẽ tạo một kế hoạch sao lưu mà tôi thực hiện sao lưu hàng ngày và giữ lại trong một tháng. Tiếp theo, tôi sẽ thiết lập một quy tắc sao lưu để chuyển đổi sao lưu sang kho lạnh sau 31 ngày và tự động xóa sao lưu sau 366 ngày kể từ ngày tạo sao lưu.

Ngoài ra, tôi sẽ cho bạn thấy cách bạn có thể hạn chế người trong tổ chức của mình xóa sao lưu từ AWS Backup và bảng điều khiển DynamoDB, trong khi vẫn có thể thực hiện các thao tác khác như tạo sao lưu, tạo bảng, v.v.

Bây giờ, hãy cùng khám phá các tùy chọn sao lưu khác nhau của DynamoDB.