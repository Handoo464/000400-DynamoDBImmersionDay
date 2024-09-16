---
title : "LMR: Xây dựng và triển khai ứng dụng serverless toàn cầu với Amazon DynamoDB"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

Trong buổi workshop này, bạn sẽ học cách xây dựng và triển khai một ứng dụng serverless phân phối toàn cầu và có kinh nghiệm sử dụng Amazon DynamoDB Global Tables để sao chép dữ liệu trên các vùng (AWS Regions).

Bạn sẽ sử dụng các thành phần serverless để xây dựng một API hỗ trợ cho ứng dụng web trình phát video. Dịch vụ API này được thiết kế để lưu trữ các bản ghi bookmark cho bất kỳ chương trình video nào mà khách hàng xem, để ứng dụng có thể nhớ nơi người dùng đã dừng lại trong một phiên xem.

Đối tượng mục tiêu cho buổi workshop này là bất kỳ nhà phát triển hoặc kiến trúc sư ứng dụng nào cần hiểu về kiến trúc khả dụng dữ liệu đa vùng (multi-region data availability architectures) cho ứng dụng của họ. Kết thúc buổi workshop này, bạn sẽ:

- Cảm thấy thoải mái khi tạo một DynamoDB Global Table trong nhiều vùng
- Hiểu cách DynamoDB Global Tables Replication hoạt động
- Có thể giải thích ý nghĩa của "eventual consistency" trong ngữ cảnh sao chép của DynamoDB Global Tables

Bạn phải mang theo laptop của mình để tham gia và thời lượng dự kiến của buổi workshop là 50 phút.

Buổi workshop bao gồm các chương sau:

- Bắt đầu
- Module 1: Khởi chạy ứng dụng
- Module 2: Khám phá Global Tables
- Module 3: Tương tác với Giao diện Globalflix
- Các chủ đề thảo luận về Global Tables