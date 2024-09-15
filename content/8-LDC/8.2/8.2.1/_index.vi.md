---
title : "Tài liệu tham khảo về thanh toán ngân hàng"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> </b> "
---


## Cách giải quyết thách thức này

### **Các mẫu truy cập (access patterns)**

Các mẫu truy cập trong tình huống này bao gồm:

1. **Chèn thanh toán theo lịch**.
2. **Trả về các thanh toán theo lịch của người dùng** trong vòng 90 ngày tới.
3. **Trả về các thanh toán theo lịch của tất cả người dùng** cho một ngày cụ thể, với trạng thái (_SCHEDULED_ hoặc _PENDING_).

### **Xác định khóa phân vùng khả thi để đáp ứng mẫu truy cập chính:**

- **Thuộc tính nào của mục thanh toán (_AccountID_, _ScheduledTime_, _Status_, _DataBlob_) tỷ lệ với mẫu truy cập?**
   - **_AccountID_**: Có thể làm khóa phân vùng (partition key) chính cho các mẫu truy cập dựa trên người dùng, vì mỗi người dùng sẽ có các thanh toán theo lịch riêng.
   - **_ScheduledTime_**: Có thể được dùng làm khóa sắp xếp (sort key) để truy vấn các thanh toán theo thời gian.
  
- **Tổ chức tự nhiên cho các mục thanh toán liên quan** (để trả về các mục dữ liệu liên quan đến các mẫu truy cập)?
   - **_AccountID_** làm khóa phân vùng để liên kết các thanh toán với từng người dùng, và **_ScheduledTime_** làm khóa sắp xếp để có thể truy vấn theo thời gian cụ thể.

- **Xem xét các chiều của truy cập**: cả truy cập đọc và ghi:
   - **Ghi (write)**: Mỗi khi có thanh toán mới được lên lịch, hệ thống cần ghi lại các mục thanh toán mới cho người dùng cụ thể (_AccountID_).
   - **Đọc (read)**: Người dùng cần truy vấn các thanh toán đã lên lịch trong khoảng thời gian cụ thể, ví dụ, các thanh toán trong 90 ngày tới.

### **Tổ chức các mục thanh toán liên quan đến mẫu truy cập chính:**

- **Làm thế nào để sắp xếp các mục thanh toán để trả về theo khoảng thời gian đã định?** (sort by)
   - Dùng **_ScheduledTime_** làm khóa sắp xếp để có thể truy vấn các thanh toán theo khoảng thời gian 90 ngày tới.

- **Cấu trúc quan hệ từ tổng quát nhất đến cụ thể hơn:**
   - Sử dụng **_AccountID_** làm khóa phân vùng và **_ScheduledTime_** làm khóa sắp xếp. Điều này sẽ giúp truy vấn thanh toán của một người dùng trong một khoảng thời gian cụ thể.

### **Đáp ứng mẫu truy cập thứ ba (OLTP):**

- **Trả về thanh toán theo ngày và trạng thái cụ thể (_SCHEDULED_ hoặc _PENDING_) cho tất cả người dùng**. Để đáp ứng mẫu truy cập này, bạn có thể sử dụng **Global Secondary Index (GSI)** với _ScheduledTime_ làm khóa phân vùng và _Status_ làm khóa sắp xếp. Điều này cho phép bạn truy vấn các thanh toán theo trạng thái và thời gian cụ thể.

### **Tham khảo hữu ích**

- **[Best Practices for Using Sort Keys to Organize Data](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-sort-keys.html)**  
- **[Working with Queries](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html)**  
- **[Using Global Secondary Indexes in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html)**  
- **[Write Shard a GSI for Selective Queries in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-indexes-gsi-sharding.html)**  