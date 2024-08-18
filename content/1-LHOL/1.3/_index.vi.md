---
title : "Khám phá DynamoDB Console"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 1.3. </b> "
---

Chúng ta sẽ khám phá DynamoDB với AWS CLI thông qua [AWS cloud9 management Console](https://console.aws.amazon.com/cloud9/home). Nếu bạn chưa làm, hãy chọn *open IDE* để khởi động môi trường AWS Cloud9. Bạn có thể đóng Welcome screen và điều chỉnh terminal của mình để tăng diện tích màn hình, hoặc đóng tất cả các cửa sổ và điều hướng đến "Cửa sổ" -> "Terminal Mới" để mở một cửa sổ terminal mới.

Cấp độ trừu tượng cao nhất trong DynamoDB là *Bảng* (không có khái niệm về "Cơ sở dữ liệu" chứa nhiều bảng bên trong như trong các dịch vụ NOSQL hoặc RDBMS khác). Bên trong một Table, bạn sẽ chèn các Items, tương tự với những gì bạn có thể nghĩ đến như một hàng trong các dịch vụ khác. Các Item là một tập hợp các *Attributes* (Thuộc tính), tương tự với các cột. Mỗi mục phải có một *Primary Key*, sẽ xác định duy nhất hàng đó (hai mục không thể chứa cùng một Khóa Chính). Tối thiểu, khi bạn tạo một bảng, bạn phải chọn một thuộc tính làm *Partition Key* (hay còn gọi là Hash Key) và bạn có thể tùy chọn chỉ định một thuộc tính khác làm Sort Key.

Nếu bảng của bạn chỉ có Partition Key, thì Partition Key là *Primary Key* và phải xác định duy nhất mỗi mục. Nếu bảng của bạn có cả Partition Key và Sort Key, thì có thể có nhiều mục có cùng Partition Key, nhưng sự kết hợp của Partition Key và Sort Key sẽ là Primary Key và xác định duy nhất hàng đó. Nói cách khác, bạn có thể có nhiều mục có cùng Partition Key miễn là các Khóa Sắp xếp của chúng khác nhau. Các mục có cùng Partition Key được nói là thuộc về cùng một *Item Collection*.

Để biết thêm thông tin, vui lòng đọc về các [Core Concepts in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html).

Các thao tác trong DynamoDB tiêu tốn dung lượng từ bảng. Khi bảng đang sử dụng dung lượng kiểu On-demand, các thao tác đọc sẽ tiêu tốn Đơn vị yêu cầu đọc (*Read Request Units* - RRUs) và các thao tác ghi sẽ tiêu tốn Đơn vị yêu cầu ghi (*Write Request Units* - WRUs). Khi bảng đang sử dụng Dung lượng Cung cấp sẵn(Provisioned Capacity), các thao tác đọc sẽ tiêu tốn Đơn vị dung lượng đọc (*Read Capacity Units* - RCUs) và các thao tác ghi sẽ tiêu tốn Đơn vị dung lượng ghi (*Write Capacity Units* - WCUs). Để biết thêm thông tin, vui lòng xem phần  [Read/Write Capacity Mode](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html) trong DynamoDB Developer Guide.

Bây giờ hãy cùng bước vào shell và khám phá DynamoDB Console.