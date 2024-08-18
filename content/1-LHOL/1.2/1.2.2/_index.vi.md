---
title : "Đọc Item Collections sử dụng Query"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.1.1 </b> "
---


**Bộ sưu tập Mục (Item Collections)** là các nhóm Item chia sẻ Khóa Phân vùng (Partition Key). Theo định nghĩa, Item Collections có thể tồn tại trong các bảng có cả Khóa Phân vùng (Partition Key) và Khóa Sắp xếp (Sort Key). Chúng ta có thể đọc toàn bộ hoặc một phần của Item Collection bằng cách sử dụng [Query API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html), có thể được gọi bằng  [query CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html). Có thể thấy hơi khó hiểu vì từ "query" thường được sử dụng phổ biến để nghĩa là "đọc dữ liệu từ cơ sở dữ liệu", nhưng trong DynamoDB "query" có một ý nghĩa cụ thể: để đọc toàn bộ hoặc một phần của một Item Collection.

Khi chúng ta sử dụng _Query_ API, chúng ta phải chỉ định một **Biểu thức Điều kiện Khóa (Key Condition Expression)**. Nếu so sánh điều này với SQL, chúng ta có thể nói "đây là phần của điều kiện WHERE mà hoạt động trên các thuộc tính Partition Key và Sort Key". Điều này có thể có một vài hình thức:

-  Chỉ giá trị Partition Key của Item Collection của chúng ta. Điều này cho thấy rằng chúng ta muốn đọc **TẤT CẢ** các item trong item collection.
-  Giá trị Khóa Phân vùng và một điều kiện Khóa Sắp xếp nào đó sẽ khớp với một tập hợp con của các hàng trong bộ sưu tập mục. Các điều kiện khóa sắp xếp có thể là =, <, >, <=, >=, BETWEEN và BEGINS_WITH.

**Biểu thức Điều kiện Khóa** (Key Condition Expression) sẽ xác định số RCU hoặc RRU mà được tiêu thụ bởi truy vấn của chúng ta. DynamoDB sẽ cộng dồn kích thước của tất cả các hàng phù hợp với Biểu thức Điều kiện Khóa, sau đó chia tổng kích thước đó cho 4KB để tính dung lượng tiêu thụ (và sau đó nó sẽ chia số đó cho hai nếu bạn đang sử dụng một lần đọc nhất quán cuối cùng).

Chúng ta cũng có thể tùy chọn chỉ định một [**Biểu thức Lọc (Filter Expression]( [Filter Expression](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.FilterExpression)))** cho truy vấn của mình. Nếu so sánh điều này với SQL, chúng ta có thể nói "đây là phần của điều kiện WHERE mà hoạt động trên các thuộc tính không phải Khóa". Biểu thức Lọc hoạt động để loại bỏ một số mục khỏi Result Set được trả về bởi Query, nhưng chúng không ảnh hưởng đến dung lượng tiêu thụ của truy vấn. Nếu Key Condition Expression khớp 1,000,000 mục và Biểu thức Lọc giảm FilterExpression) tập kết quả xuống còn 100 mục, bạn vẫn sẽ bị tính phí để đọc tất cả 1,000,000 mục. Tuy nhiên, Biểu thức Lọc giảm lượng dữ liệu được trả về từ kết nối mạng, nên vẫn có lợi cho ứng dụng của chúng ta khi sử dụng Biểu thức Lọc ngay cả khi nó không ảnh hưởng đến giá của truy vấn.

Bảng **ProductCatalog** mà chúng tôi đã sử dụng trong các ví dụ trước chỉ có một Partition Key, vì vậy hãy xem dữ liệu trong bảng **Reply** có cả Partition Key và Sort Key:

```bash
aws dynamodb scan --table-name Reply
```

Dữ liệu trong bảng này có thuộc tính **Id** tham chiếu đến các mục trong bảng **Thread**. Dữ liệu của chúng ta có hai chủ đề, và mỗi chủ đề có 2 phản hồi. Hãy sử dụng lệnh query CLI để chỉ đọc các mục từ chủ đề 1:

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'Id = :Id' \
    --expression-attribute-values '{
        ":Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 1"}
    }' \
    --return-consumed-capacity TOTAL
```

Vì Sort Key trong bảng này là một dấu thời gian, chúng ta có thể chỉ định một Key Condition Expression để trả về chỉ những phản hồi trong một chủ đề đã được đăng sau một thời gian nhất định bằng cách thêm một điều kiện khóa sắp xếp:

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

Hãy nhớ rằng chúng ta có thể sử dụng Filter Expressions nếu muốn hạn chế kết quả của mình dựa trên các thuộc tính không phải Khóa. Ví dụ, chúng ta có thể tìm tất cả các phản hồi trong Chủ đề 1 được đăng bởi **User B**:

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

Chú ý rằng trong phản hồi, chúng ta thấy các dòng này:

```json
"Count": 1,
"ScannedCount": 2,
```

Điều này cho biết rằng Key Condition Expression đã khớp với 2 mục (ScannedCount) và đó là số mà chúng ta đã bị tính phí để đọc, nhưng Filter Expression đã giảm kích thước tập kết quả xuống còn 1 mục (Count).

### **Bài tập**

Đọc tài liệu về [--max-items](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html#synopsis) và viết hai truy vấn:

- Trả về chỉ phản hồi đầu tiên cho một chủ đề.
- Trả về chỉ phản hồi gần đây nhất cho một chủ đề.

Gợi ý: hãy xem xét các tùy chọn `max-items`, `scan-index-forward`, và `no-scan-index-forward`. Giải pháp có thể mở rộng dưới đây nhưng hãy cố gắng tìm ra nó trước khi tiến lên.

Nếu chúng ta muốn sắp xếp các mục theo thứ tự tăng dần của khóa sắp xếp, thì chúng ta sẽ hướng dẫn DynamoDB quét chỉ mục theo hướng tiến lên bằng tùy chọn `--scan-index-forward`. Nếu chúng ta muốn giới hạn số lượng mục trả về, thì chúng ta sử dụng tùy chọn `--max-items`. Điều này tương đương trong SQL với câu lệnh "ORDER BY ReplyDateTime ASC LIMIT 1".

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'Id = :Id' \
    --expression-attribute-values '{
        ":Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 1"}
    }' \
    --max-items 1 \
    --scan-index-forward  \
    --return-consumed-capacity TOTAL
```

- **`--scan-index-forward`**: Tham số này yêu cầu DynamoDB sắp xếp các mục theo thứ tự tăng dần dựa trên khóa sắp xếp (sort key). Điều này có nghĩa là các kết quả sẽ được trả về từ thấp đến cao theo `ReplyDateTime`.
- **`--max-items 1`**: Điều này giới hạn số lượng mục trong kết quả truy vấn. Chỉ một mục duy nhất sẽ được trả về.

Nếu chúng ta muốn DynamoDB sắp xếp các mục theo thứ tự giảm dần của khóa sắp xếp, chúng ta sẽ hướng dẫn DynamoDB quét chỉ mục theo hướng ngược lại bằng tùy chọn `--no-scan-index-forward`. Nếu chúng ta muốn thực hiện câu lệnh SQL tương đương với "ORDER BY ReplyDateTime DESC LIMIT 1", chúng ta sẽ chạy:

```bash
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression 'Id = :Id' \
    --expression-attribute-values '{
        ":Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 1"}
    }' \
    --max-items 1 \
    --no-scan-index-forward  \
    --return-consumed-capacity TOTAL
```
