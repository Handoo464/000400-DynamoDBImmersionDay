---
title : "Bài tập 5: Sparse Global Secondary Indexes"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 3.6. </b> "
---

Bạn có thể sử dụng một sparse global secondary index (chỉ mục phụ toàn cầu thưa) để xác định các mục bảng có một thuộc tính không phổ biến. Để làm điều này, bạn tận dụng thực tế rằng các mục bảng không chứa thuộc tính của chỉ mục phụ toàn cầu sẽ không được lập chỉ mục.

Truy vấn như vậy cho các mục bảng có một thuộc tính không phổ biến có thể rất hiệu quả vì số lượng mục trong chỉ mục nhỏ hơn đáng kể so với số lượng mục trong bảng. Ngoài ra, càng ít thuộc tính bảng bạn chiếu vào chỉ mục, thì càng ít đơn vị dung lượng ghi và đọc bạn tiêu thụ từ chỉ mục đó.

