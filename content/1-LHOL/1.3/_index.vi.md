---
title : "Khám phá DynamoDB Console"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 1.3. </b> "
---

Trong bài thực hành này, chúng ta sẽ khám phá [phần DynamoDB của AWS Management Console](https://console.aws.amazon.com/dynamodbv2/). Hiện có hai phiên bản của giao diện điều khiển và mặc dù bạn luôn có thể nhấp vào "Revert to the current console" (Quay lại giao diện điều khiển hiện tại), chúng ta sẽ làm việc với phiên bản V2 của giao diện điều khiển.

Cấp độ trừu tượng cao nhất trong DynamoDB là một **_Bảng_** (trong DynamoDB không có khái niệm về "Cơ sở dữ liệu" chứa nhiều bảng bên trong như ở các dịch vụ NoSQL hoặc RDBMS khác). Bên trong một Bảng, bạn sẽ chèn các **_Mục_** (Items), tương tự như các hàng (row) trong các dịch vụ khác. Các Mục là tập hợp của các **_Thuộc tính_** (Attributes), tương tự như các cột (column). Mỗi Mục phải có một **_Khóa Chính_** (Primary Key) để nhận diện duy nhất hàng đó (hai Mục không thể chứa cùng một Khóa Chính). Khi tạo bảng, ít nhất bạn phải chọn một thuộc tính làm **_Khóa Phân Vùng_** (Partition Key, hay còn gọi là Hash Key) và bạn có thể tùy chọn xác định một thuộc tính khác làm **_Khóa Sắp Xếp_** (Sort Key).

Nếu bảng của bạn chỉ có Khóa Phân Vùng, thì Khóa Phân Vùng là **_Khóa Chính_** và phải nhận diện duy nhất mỗi mục. Nếu bảng của bạn có cả Khóa Phân Vùng và Khóa Sắp Xếp, thì có thể có nhiều mục có cùng Khóa Phân Vùng, nhưng sự kết hợp giữa Khóa Phân Vùng và Khóa Sắp Xếp sẽ là Khóa Chính và nhận diện duy nhất hàng đó. Nói cách khác, bạn có thể có nhiều mục có cùng Khóa Phân Vùng miễn là Khóa Sắp Xếp của chúng khác nhau. Các mục có cùng Khóa Phân Vùng được gọi là thuộc về cùng một **_Bộ Sưu Tập Mục_** (Item Collection).

Để biết thêm thông tin, vui lòng đọc về [Các Khái Niệm Cơ Bản trong DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html).

Các thao tác trong DynamoDB tiêu thụ dung lượng từ bảng. Khi bảng sử dụng dung lượng Theo Yêu Cầu (On-Demand), các thao tác đọc sẽ tiêu thụ **_Đơn vị Yêu cầu Đọc (RRUs)_** và các thao tác ghi sẽ tiêu thụ **_Đơn vị Yêu cầu Ghi (WRUs)_**. Khi bảng sử dụng Dung lượng Cung cấp (Provisioned Capacity), các thao tác đọc sẽ tiêu thụ **_Đơn vị Dung lượng Đọc (RCUs)_** và các thao tác ghi sẽ tiêu thụ **_Đơn vị Dung lượng Ghi (WCUs)_**. Để biết thêm thông tin, vui lòng xem [Chế độ Dung lượng Đọc/Ghi](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html) trong Hướng dẫn Dành cho Nhà Phát triển DynamoDB.

Bây giờ hãy đi vào shell và khám phá DynamoDB Console.