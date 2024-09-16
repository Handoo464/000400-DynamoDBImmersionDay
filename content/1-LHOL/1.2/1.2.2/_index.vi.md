---
title : "Đọc Item Collections sử dụng Query"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 1.2.2. </b> "
---


**Item Collections** là các nhóm **Items** chia sẻ cùng một Khóa Phân Vùng (Partition Key). Theo định nghĩa, Item Collections chỉ có thể tồn tại trong các bảng có cả Khóa Phân Vùng và Khóa Sắp Xếp (Sort Key). Chúng ta có thể đọc toàn bộ hoặc một phần của một Item Collection bằng cách sử dụng [API Query](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html), được gọi bằng [lệnh CLI query](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html). Có thể hơi khó hiểu vì từ "query" thường được sử dụng để chỉ việc "đọc dữ liệu từ cơ sở dữ liệu", nhưng trong DynamoDB, "query" có một ý nghĩa cụ thể: đọc toàn bộ hoặc một phần của một Item Collection.

Khi gọi API **Query**, chúng ta phải chỉ định một [Biểu thức Điều kiện Khóa (Key Condition Expression)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.KeyConditionExpressions). Nếu so sánh với SQL, chúng ta có thể nói "đây là phần của câu lệnh WHERE áp dụng cho các thuộc tính Khóa Phân Vùng và Khóa Sắp Xếp". Biểu thức này có thể có một số dạng:

- Chỉ giá trị Khóa Phân Vùng của Item Collection. Điều này cho biết chúng ta muốn đọc TẤT CẢ các mục trong bộ sưu tập mục.
- Giá trị Khóa Phân Vùng và một số loại Điều kiện Khóa Sắp Xếp sẽ khớp với một tập hợp con các hàng trong bộ sưu tập mục. Các điều kiện khóa sắp xếp có thể là =, <, >, <=, >=, BETWEEN, và BEGINS_WITH.

Biểu thức Điều kiện Khóa sẽ xác định số lượng **RRUs** hoặc **RCUs** mà lệnh Query tiêu thụ. DynamoDB sẽ tính tổng kích thước của tất cả các hàng khớp với Biểu thức Điều kiện Khóa, sau đó chia tổng kích thước đó cho 4KB để tính toán dung lượng tiêu thụ (và sau đó sẽ chia số đó cho hai nếu bạn đang sử dụng chế độ đọc nhất quán cuối cùng).

Chúng ta cũng có thể tùy chọn chỉ định một [Biểu thức Lọc (Filter Expression)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.FilterExpression) cho lệnh Query của mình. Nếu so sánh với SQL, chúng ta có thể nói "đây là phần của câu lệnh WHERE áp dụng cho các thuộc tính không phải Khóa". Các Biểu thức Lọc sẽ loại bỏ một số mục khỏi Bộ Kết quả trả về bởi lệnh Query, **nhưng chúng không ảnh hưởng đến dung lượng tiêu thụ của lệnh Query**. Nếu Biểu thức Điều kiện Khóa của bạn khớp với 1.000.000 mục và Biểu thức Lọc giảm bộ kết quả xuống còn 100 mục, bạn vẫn sẽ bị tính phí để đọc tất cả 1.000.000 mục. Nhưng Biểu thức Lọc giảm lượng dữ liệu trả về từ kết nối mạng nên vẫn có lợi cho ứng dụng của chúng ta khi sử dụng Biểu thức Lọc ngay cả khi nó không ảnh hưởng đến giá của lệnh Query.

Bảng **ProductCatalog** mà chúng ta sử dụng trong các ví dụ trước chỉ có Khóa Phân Vùng, vì vậy hãy xem dữ liệu trong bảng **Reply** có cả Khóa Phân Vùng và Khóa Sắp Xếp:

```bash
aws dynamodb scan --table-name Reply
```

Dữ liệu trong bảng này có thuộc tính **Id** tham chiếu đến các mục trong bảng **Thread**. Dữ liệu của chúng ta có hai chủ đề (thread), và mỗi chủ đề có 2 phản hồi (replies). Hãy sử dụng lệnh **query** CLI để chỉ đọc các mục từ chủ đề 1:

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'Id = :Id' \
    --expression-attribute-values '{
        ":Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 1"}
    }' \
    --return-consumed-capacity TOTAL
```

Vì Khóa Sắp Xếp trong bảng này là dấu thời gian (timestamp), chúng ta có thể chỉ định Biểu thức Điều kiện Khóa để chỉ trả về các phản hồi trong một chủ đề được đăng sau một thời gian nhất định bằng cách thêm một điều kiện khóa sắp xếp:

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'Id = :Id and ReplyDateTime > :ts' \
    --expression-attribute-values '{
        ":Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 1"},
        ":ts" : {"S": "2015-09-21"}
    }' \
    --return-consumed-capacity TOTAL
```

Hãy nhớ rằng chúng ta có thể sử dụng Biểu thức Lọc nếu muốn giới hạn kết quả dựa trên các thuộc tính không phải Khóa. Ví dụ, chúng ta có thể tìm tất cả các phản hồi trong Chủ đề 1 được đăng bởi Người dùng B:

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'Id = :Id' \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 1"},
        ":user" : {"S": "User B"}
    }' \
    --return-consumed-capacity TOTAL
```

Lưu ý rằng trong phản hồi, chúng ta thấy các dòng này:

```bash
"Count": 1,
"ScannedCount": 2,
```

Điều này cho chúng ta biết rằng Biểu thức Điều kiện Khóa khớp với 2 mục (**ScannedCount**) và đó là số mục chúng ta phải trả phí để đọc, nhưng Biểu thức Lọc đã giảm kích thước bộ kết quả xuống còn 1 mục (**Count**).