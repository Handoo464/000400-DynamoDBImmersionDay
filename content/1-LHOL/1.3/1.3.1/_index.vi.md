---
title : "Xem Table Data"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.3.1. </b> "
---

Đầu tiên, hãy truy cập vào [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/)  và nhấp vào "Tables" (Bảng) từ menu bên.

![Console Pick Tables](/images/1/1.3/1.png)

Tiếp theo, chọn bảng `ProductCatalog` và nhấp vào "Explore table items" (Khám phá các mục trong bảng) ở góc trên bên phải để xem các mục.

![Console ProductCatalog Items Preview](/images/1/1.3/2.png)

Chúng ta có thể thấy rằng bảng có một Khóa phân vùng (Partition Key) là **Id** (loại Số), không có Khóa sắp xếp, và có 8 mục trong bảng. Một số mục là Sách và một số mục là Xe đạp; một số thuộc tính như **Id**, **Price**, **ProductCategory**, và **Title** tồn tại trong mọi Mục, trong khi các thuộc tính cụ thể của Danh mục như **Authors** hoặc **Colors** chỉ tồn tại trên một số mục.

Nhấp vào thuộc tính **Id** có giá trị 101 để mở trình chỉnh sửa Mục cho mục đó. Chúng ta có thể xem và chỉnh sửa tất cả các thuộc tính cho mục này ngay từ bảng điều khiển. Hãy thử thay đổi tiêu đề thành "Book 101 Title New and Improved". Nhấp vào "Add new attribute" (Thêm thuộc tính mới) có tên **Reviewers** với loại String set và sau đó nhấp vào "Insert a field" (Chèn một trường) hai lần để thêm một vài mục vào tập hợp đó. Khi bạn hoàn tất, hãy nhấp vào "Save changes" (Lưu thay đổi).

![Console ProductCatalog Items Editor Forms](/images/1/1.3/3.png)

Bạn cũng có thể sử dụng trình chỉnh sửa Mục theo định dạng JSON của DynamoDB (thay vì trình chỉnh sửa dựa trên biểu mẫu mặc định) bằng cách nhấp vào "JSON" ở góc trên bên phải. Định dạng này sẽ quen thuộc nếu bạn đã trải qua phần [Explore the DynamoDB CLI](https://catalog.workshops.aws/dynamodb-labs/en-US/hands-on-labs/explore-cli.html)trong bài lab. Định dạng JSON của DynamoDB được mô tả trong phần [DynamoDB Low-Level API](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.LowLevelAPI.html)  của Developer Guide.

![Console ProductCatalog Items Editor JSON](/images/1/1.3/4.png)

