---
title : "Tích hợp"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3. </b> "
---

Trong phần này, bạn sẽ cấu hình tích hợp giữa các dịch vụ. Đầu tiên, bạn sẽ thiết lập các kết nối ML (Machine Learning) và Pipeline trong OpenSearch Service, sau đó thiết lập kết nối zero ETL để di chuyển dữ liệu đã ghi vào DynamoDB sang OpenSearch. Khi các tích hợp này đã được thiết lập, bạn có thể ghi các bản ghi vào DynamoDB làm nguồn dữ liệu chính của mình và sau đó tự động có dữ liệu đó sẵn sàng để truy vấn trong các dịch vụ khác.