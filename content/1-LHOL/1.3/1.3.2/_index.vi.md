---
title : "Đọc Item Collections sử dụng Query"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 1.3.2. </b> "
---

Item Collections là các nhóm Mục có chung một Khóa phân vùng (Partition Key). Định nghĩa, Tập hợp Mục chỉ có thể tồn tại trong những bảng có cả Khóa phân vùng và Khóa sắp xếp (Sort Key). Chúng ta có thể đọc tất cả hoặc một phần của Tập hợp Mục bằng cách sử dụng [Query API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html)  có thể được gọi bằng [query CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html) . Có thể có phần nào đó gây nhầm lẫn vì từ "truy vấn" thường được sử dụng trong ngữ cảnh để chỉ việc "đọc dữ liệu từ một cơ sở dữ liệu", nhưng trong DynamoDB, có một ý nghĩa cụ thể: để đọc toàn bộ hoặc một phần của Tập hợp Mục.

Khi chúng ta gọi API Truy vấn, chúng ta phải chỉ định **Biểu thức Điều kiện Khóa** [Key Condition Expression](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.KeyConditionExpressions) . Nếu so sánh điều này với SQL, chúng ta có thể nói "đây là phần của câu WHERE áp dụng cho thuộc tính Khóa phân vùng và Khóa sắp xếp". Điều này có thể có một số hình thức:

- Chỉ giá trị Khóa phân vùng của Tập hợp Mục, điều này chỉ rõ rằng chúng ta muốn đọc TẤT CẢ các mục trong tập hợp mục.
- Giá trị Khóa phân vùng và một số điều kiện như **=, >, >=, BETWEEN, và BEGINS_WITH**.

Key Condition Expression(Biểu thức Điều kiện Khóa) sẽ xác định số lượng RRUs hoặc RCUs được tiêu thụ bởi Truy vấn của chúng ta. DynamoDB sẽ cộng kích thước của tất cả các hàng phù hợp với Key Condition Expression, sau đó chia tổng kích thước đó cho 4KB để tính toán năng lực tiêu thụ (và sau đó sẽ chia con số đó cho hai nếu bạn đang sử dụng đọc nhất quán không ngay lập tức).

Chúng ta cũng có thể chỉ định **Biểu thức Lọc** ([Filter Expression](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.FilterExpression))  cho Truy vấn. So với SQL, chúng ta có thể nói "đây là phần của câu WHERE áp dụng cho các thuộc tính không phải Khóa". 
**Biểu thức Lọc** có tác dụng loại bỏ một số mục khỏi Tập hợp Kết quả trả về bởi Truy vấn, nhưng chúng không ảnh hưởng đến năng lực tiêu thụ của Truy vấn. Nếu Biểu thức Điều kiện Khóa của bạn phù hợp với 1,000,000 mục và Biểu thức Lọc giảm tập kết quả xuống còn 100 mục, bạn sẽ vẫn bị tính phí để đọc tất cả 1,000,000 mục. Tuy nhiên, **Biểu thức Lọc** làm giảm lượng dữ liệu được trả về từ kết nối mạng, vì vậy vẫn có lợi cho ứng dụng của chúng ta khi sử dụng **Biểu thức Lọc**, mặc dù nó không ảnh hưởng đến giá của Truy vấn.
Bảng **ProductCatalog** mà chúng ta đã sử dụng trong các ví dụ trước chỉ có một Khóa phân vùng, vì vậy hãy xem dữ liệu trong bảng **Reply** có cả Khóa phân vùng và Khóa sắp xếp. Chọn menu bên trái, khám phám các mục dưới bảng.
![Console Menu Item Explorer](/images/1/1.3/5.png) 

![Console Menu Hamburger Icon](/images/1/1.3/6.png)

Sau khi vào **Khám Phá Mục**, bạn cần chọn bảng **Reply** và sau đó mở hộp **Scan/Query items**.

![Item Explorer Expand Tables](/images/1/1.3/7.png)

Dữ liệu trong bảng này có thuộc tính **Id** tham chiếu đến các mục trong bảng **Thread**. Dữ liệu của chúng ta có hai chủ đề (threads), và mỗi chủ đề có 2 phản hồi (replies). Hãy sử dụng chức năng **Truy vấn**
 để đọc chỉ các mục từ chủ đề 1 bằng cách dán "Amazon DynamoDB#DynamoDB Thread 1" vào ô **Id** (Khóa phân vùng) và sau đó nhấp vào **Run**. Chúng ta có thể thấy có hai mục phản hồi trong chủ đề "DynamoDB Thread 1".

![Item Explorer Query Reply 1](/images/1/1.3/8.png)

Do Khóa sắp xếp trong bảng này là dấu thời gian (timestamp), chúng ta có thể chỉ định một **Biểu thức Điều kiện Khóa** để chỉ trả về các phản hồi trong chủ đề được đăng sau một thời điểm nhất định bằng cách thêm điều kiện khóa sắp xếp nơi **ReplyDateTime** là lớn hơn "2015-09-21" và nhấp vào 

**Run**.

![Item Explorer Query Reply 2](/images/1/1.3/9.png)

Nhớ rằng chúng ta có thể sử dụng **Biểu thức Lọc** nếu muốn giới hạn kết quả của chúng ta dựa trên các thuộc tính không phải Khóa. Ví dụ, chúng ta có thể tìm tất cả các phản hồi trong chủ đề 1 được đăng bởi **User B**. Xóa điều kiện khóa sắp xếp và nhấp vào “Thêm bộ lọc”, sau đó sử dụng **PostedBy** cho **Tên thuộc tính**, điều kiện là **Bằng (Equals)** và giá trị là **User B**, sau đó nhấp vào **Run**.

![Item Explorer Query Reply 3](/images/1/1.3/10.png)

