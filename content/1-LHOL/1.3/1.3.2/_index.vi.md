---
title : "Đọc Item Collections sử dụng Query"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 1.3.2. </b> "
---

**Item Collections** là các nhóm **Items** chia sẻ cùng một Khóa Phân Vùng (Partition Key). Theo định nghĩa, Item Collections chỉ có thể tồn tại trong các bảng có cả Khóa Phân Vùng và Khóa Sắp Xếp (Sort Key). Chúng ta có thể đọc toàn bộ hoặc một phần của một Item Collection bằng cách sử dụng [API Query](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html), được gọi bằng [lệnh CLI query](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html). Có thể hơi khó hiểu vì từ "query" thường được sử dụng để chỉ việc "đọc dữ liệu từ cơ sở dữ liệu", nhưng trong DynamoDB, "query" có một ý nghĩa cụ thể: đọc toàn bộ hoặc một phần của một Item Collection.

Khi gọi API **Query**, chúng ta phải chỉ định một [Biểu thức Điều kiện Khóa (Key Condition Expression)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.KeyConditionExpressions). Nếu so sánh với SQL, chúng ta có thể nói "đây là phần của câu lệnh WHERE áp dụng cho các thuộc tính Khóa Phân Vùng và Khóa Sắp Xếp". Biểu thức này có thể có một số dạng:

- Chỉ giá trị Khóa Phân Vùng của Item Collection. Điều này cho biết chúng ta muốn đọc TẤT CẢ các mục trong bộ sưu tập mục.
- Giá trị Khóa Phân Vùng và một số loại Điều kiện Khóa Sắp Xếp. Hãy khám phá các tùy chọn khác trong Item explorer và tìm hiểu cách thực hiện truy vấn để trả về các phản hồi được sắp xếp từ mới nhất đến cũ nhất. >=, BETWEEN, và BEGINS_WITH.

Biểu thức Điều kiện Khóa sẽ xác định số lượng **RRUs** hoặc **RCUs** mà lệnh Query tiêu thụ. DynamoDB sẽ tính tổng kích thước của tất cả các hàng khớp với Biểu thức Điều kiện Khóa, sau đó chia tổng kích thước đó cho 4KB để tính toán dung lượng tiêu thụ (và sau đó sẽ chia số đó cho hai nếu bạn đang sử dụng chế độ đọc nhất quán cuối cùng).

Chúng ta cũng có thể tùy chọn chỉ định một [Biểu thức Lọc (Filter Expression)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.FilterExpression) cho lệnh Query của mình. Nếu so sánh với SQL, chúng ta có thể nói "đây là phần của câu lệnh WHERE áp dụng cho các thuộc tính không phải Khóa". Các Biểu thức Lọc sẽ loại bỏ một số mục khỏi Bộ Kết quả trả về bởi lệnh Query, **nhưng chúng không ảnh hưởng đến dung lượng tiêu thụ của lệnh Query**. Nếu Biểu thức Điều kiện Khóa của bạn khớp với 1.000.000 mục và Biểu thức Lọc giảm bộ kết quả xuống còn 100 mục, bạn vẫn sẽ bị tính phí để đọc tất cả 1.000.000 mục. Nhưng Biểu thức Lọc giảm lượng dữ liệu trả về từ kết nối mạng nên vẫn có lợi cho ứng dụng của chúng ta khi sử dụng Biểu thức Lọc ngay cả khi nó không ảnh hưởng đến giá của lệnh Query.

Bảng **ProductCatalog** mà chúng ta sử dụng trong các ví dụ trước chỉ có Khóa Phân Vùng, vì vậy hãy xem dữ liệu trong bảng **Reply** có cả Khóa Phân Vùng và Khóa Sắp Xếp. Chọn **Explore items** từ thanh menu bên trái trong phần Tables.  
![Console Menu Item Explorer](/images/1/1.3/5.png)  
Bạn có thể cần nhấp vào biểu tượng menu hình hamburger để mở rộng menu bên trái nếu nó bị ẩn.  
![Console Menu Hamburger Icon](/images/1/1.3/6.png)  

Khi bạn vào phần **Explore Items**, bạn cần chọn bảng **Reply** và sau đó mở rộng hộp **Scan/Query items**.

![Item Explorer Expand Tables](/images/1/1.3/7.png)

Dữ liệu trong bảng này có thuộc tính **Id** tham chiếu đến các mục trong bảng **Thread**. Dữ liệu của chúng ta có hai threads, và mỗi thread có 2 phản hồi. Hãy sử dụng chức năng **Query** để chỉ đọc các mục từ thread 1 bằng cách dán `Amazon DynamoDB#DynamoDB Thread 1` vào ô **_Id (Partition key)_** và sau đó nhấp vào **Run**.

Chúng ta có thể thấy rằng có hai mục **Reply** trong thread `DynamoDB Thread 1`.

![Item Explorer Query Reply 1](/images/1/1.3/8.png)

Vì Khóa Sắp Xếp trong bảng này là dấu thời gian (timestamp), chúng ta có thể chỉ định một Biểu thức Điều kiện Khóa để chỉ trả về các phản hồi trong một thread được đăng sau một thời gian nhất định bằng cách thêm một điều kiện khóa sắp xếp, nơi `ReplyDateTime` lớn hơn `2015-09-21` và nhấp vào **Run**.

![Item Explorer Query Reply 2](/images/1/1.3/9.png)

Hãy nhớ rằng chúng ta có thể sử dụng Biểu thức Lọc nếu muốn giới hạn kết quả dựa trên các thuộc tính không phải Khóa. Ví dụ, chúng ta có thể tìm tất cả các phản hồi trong Thread 1 được đăng bởi User B. Xóa điều kiện khóa sắp xếp, và nhấp vào **Add filter**, sau đó sử dụng `PostedBy` cho tên Thuộc tính, Điều kiện `Equals` và Giá trị `User B`, sau đó nhấp vào **Run**.

![Item Explorer Query Reply 3](/images/1/1.3/10.png)