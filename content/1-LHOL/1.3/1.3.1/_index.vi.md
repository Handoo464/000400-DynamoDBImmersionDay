---
title : "Xem Table Data"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.3.1. </b> "
---

Đầu tiên, hãy truy cập vào [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/) và nhấp vào **_Tables_** từ menu bên trái.

![Chọn Tables trên Console](/images/1/1.3/1.png)

Tiếp theo, chọn bảng `ProductCatalog` và nhấp vào **`Explore table items`** ở góc trên bên phải để xem các mục.

![Xem trước các mục của ProductCatalog trên Console](/images/1/1.3/2.png)

Chúng ta có thể thấy trực quan rằng bảng này có Khóa Phân Vùng là **_Id_** (thuộc loại `Number`), không có khóa sắp xếp, và có 8 mục trong bảng. Một số mục là Sách (Books) và một số mục là Xe đạp (Bicycles), và một số thuộc tính như **_Id_**, **_Price_**, **_ProductCategory_**, và **_Title_** tồn tại trong mọi Mục, trong khi các thuộc tính đặc trưng cho từng danh mục như **Authors** hoặc **Colors** chỉ tồn tại trên một số mục.

Nhấp vào thuộc tính **_Id_** có giá trị `101` để mở trình chỉnh sửa mục cho Mục đó. Chúng ta có thể xem và chỉnh sửa tất cả các thuộc tính cho mục này trực tiếp từ giao diện điều khiển. Hãy thử thay đổi **_Title_** thành "Book 101 Title New and Improved". Nhấp vào **Add new attribute** (Thêm thuộc tính mới) và đặt tên là **_Reviewers_** thuộc loại String set, sau đó nhấp vào **Insert a field** hai lần để thêm vài mục vào tập hợp đó. Khi bạn hoàn thành, nhấp vào **Save changes** (Lưu thay đổi).

![Trình chỉnh sửa mục của ProductCatalog trên Console](/images/1/1.3/3.png)

Bạn cũng có thể sử dụng trình chỉnh sửa mục trong định dạng JSON của DynamoDB (thay vì trình chỉnh sửa dạng Form mặc định) bằng cách nhấp vào **JSON** ở góc trên bên phải. Định dạng này sẽ trông quen thuộc nếu bạn đã thực hiện phần [Khám phá DynamoDB CLI](https://catalog.workshops.aws/dynamodb-labs/en-US/hands-on-labs/explore-cli.html) của bài thực hành. Định dạng JSON của DynamoDB được mô tả trong phần [API Cấp thấp của DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.LowLevelAPI.html) trong Hướng dẫn Dành cho Nhà Phát triển.

![Trình chỉnh sửa mục của ProductCatalog trong định dạng JSON trên Console](/images/1/1.3/4.png)