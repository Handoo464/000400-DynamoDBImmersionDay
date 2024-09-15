---
title : "Bài tập 2: Sequential và Parallel Table Scans"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.3. </b> "
---

Bài tập này minh họa hai phương pháp quét bảng DynamoDB: tuần tự và song song.

Mặc dù DynamoDB phân phối các mục trên nhiều phân vùng vật lý, một thao tác `Scan` chỉ có thể đọc một phân vùng tại một thời điểm. Để tìm hiểu thêm, hãy đọc [trang tài liệu của chúng tôi về phân vùng và phân phối dữ liệu](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.Partitions.html). Vì lý do này, thông lượng của một thao tác `Scan` bị giới hạn bởi thông lượng tối đa của một phân vùng đơn lẻ.

Để tối đa hóa việc sử dụng dung lượng cấp phát ở mức bảng, hãy sử dụng `Scan` song song để chia bảng (hoặc chỉ mục phụ) thành nhiều đoạn logic và sử dụng nhiều worker của ứng dụng để quét các đoạn logic này song song. Mỗi worker của ứng dụng có thể là một luồng (thread) trong các ngôn ngữ lập trình hỗ trợ đa luồng (multithreading) hoặc một tiến trình (process) của hệ điều hành. Để tìm hiểu thêm về cách triển khai quét song song, hãy đọc [trang tài liệu dành cho nhà phát triển về quét song song của chúng tôi](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.ParallelScan). API `Scan` không phù hợp với tất cả các mô hình truy vấn, và để biết thêm thông tin về lý do tại sao quét kém hiệu quả hơn truy vấn, vui lòng đọc về [tác động hiệu suất của `Scan`](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-query-scan.html) trong tài liệu của chúng tôi.

Sơ đồ dưới đây cho thấy cách một ứng dụng đa luồng thực hiện một `Scan` song song với ba luồng worker ứng dụng. Ứng dụng tạo ra ba luồng và mỗi luồng gửi một yêu cầu `Scan`, quét đoạn được chỉ định của nó, lấy dữ liệu mỗi lần 1 MB, và trả dữ liệu về cho luồng chính của ứng dụng.

![Quét song song](/images/3/3.3/1.jpg)