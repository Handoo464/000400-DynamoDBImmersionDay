---
title : "Làm việc với Table Scans"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.2.3. </b> "
---

Như đã đề cập trước đó, [API Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html) có thể được gọi bằng [lệnh CLI scan](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html). Lệnh Scan sẽ quét toàn bộ bảng và trả về các mục theo từng khối 1MB.

API Scan tương tự như API Query ngoại trừ việc vì chúng ta muốn quét toàn bộ bảng chứ không chỉ một Item Collection duy nhất, do đó không có Biểu thức Điều kiện Khóa (Key Condition Expression) cho Scan. Tuy nhiên, bạn có thể chỉ định một [Biểu thức Lọc (Filter Expression)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.FilterExpression) để giảm kích thước bộ kết quả (mặc dù nó sẽ không giảm lượng dung lượng đã tiêu thụ).

Ví dụ, chúng ta có thể tìm tất cả các phản hồi trong bảng Reply được đăng bởi Người dùng A:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --return-consumed-capacity TOTAL
```

Lưu ý rằng trong phản hồi, chúng ta thấy các dòng này:

```text
"Count": 3,
"ScannedCount": 4,
```

Điều này cho chúng ta biết rằng lệnh Scan đã quét tất cả 4 mục (ScannedCount) trong bảng và đó là số mục chúng ta phải trả phí để đọc, nhưng Biểu thức Lọc đã giảm kích thước bộ kết quả xuống còn 3 mục (Count).

Đôi khi, khi quét dữ liệu, sẽ có nhiều dữ liệu hơn mức có thể phù hợp trong phản hồi nếu lệnh scan đạt giới hạn 1MB ở phía máy chủ, hoặc có thể còn nhiều mục hơn so với tham số **_--max-items_** đã chỉ định. Trong trường hợp đó, phản hồi của lệnh scan sẽ bao gồm một **_NextToken_** mà chúng ta có thể sử dụng trong lệnh scan tiếp theo để tiếp tục từ vị trí đã dừng. Ví dụ, trong lệnh scan trước đó, chúng ta biết rằng có 3 mục trong bộ kết quả. Hãy chạy lại lệnh này nhưng giới hạn số mục tối đa là 2:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --max-items 2 \
    --return-consumed-capacity TOTAL
```

Chúng ta có thể thấy trong phản hồi có một dòng:

```text
"NextToken": "eyJFeGNsdXNpdmVTdGFydEtleSI6IG51bGwsICJib3RvX3RydW5jYXRlX2Ftb3VudCI6IDJ9"
```

Vì vậy, chúng ta có thể gọi lại lệnh scan, lần này truyền giá trị **NextToken** vào tham số **_--starting-token_**:

```bash
aws dynamodb scan \
    --table-name Reply \
    --filter-expression 'PostedBy = :user' \
    --expression-attribute-values '{
        ":user" : {"S": "User A"}
    }' \
    --max-items 2 \
    --starting-token eyJFeGNsdXNpdmVTdGFydEtleSI6IG51bGwsICJib3RvX3RydW5jYXRlX2Ftb3VudCI6IDJ9 \
    --return-consumed-capacity TOTAL
```