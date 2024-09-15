---
title : "Tài liệu tham khảo Retail Cart"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> </b> "
---

## Cách giải quyết thách thức này

### **Các mẫu truy cập (access patterns)**
Các mẫu truy cập trong tình huống này bao gồm:

1. **Chèn và cập nhật các mặt hàng** được người dùng đặt trong giỏ hàng.
2. **Trả về các mặt hàng liên quan đến người dùng** (_AccountID_), được sắp xếp theo _CreateTimestamp_ và trong phạm vi một _Status_ cụ thể.
3. **Trả về các mặt hàng trên nhiều người dùng** theo _ItemSKU_, được sắp xếp theo _CreateTimestamp_ và trong phạm vi một _Status_ cụ thể.
4. **Truy vấn ngoại tuyến không theo thời gian thực** cho đội ngũ Business Intelligence.

### **Xác định khóa phân vùng khả thi để đáp ứng mẫu truy cập chính:**

- **Thuộc tính nào của mặt hàng tăng lên cùng với khối lượng truy cập cao hơn?**
   - Thuộc tính _AccountID_ sẽ tỷ lệ thuận với khối lượng truy cập, vì nhiều người dùng khác nhau sẽ có các hành vi đặt hàng riêng biệt.
  
- **Tổ chức tự nhiên cho các mục dữ liệu liên quan** (để trả về các mục dữ liệu liên quan đến các mẫu truy cập)?
   - **_AccountID_** có thể là khóa phân vùng tốt cho các mẫu truy cập liên quan đến người dùng. Mỗi tài khoản sẽ chứa các mặt hàng mà người dùng đã đặt trong giỏ hàng.
  
- **Xem xét các chiều của truy cập**: cả truy cập đọc và ghi:
   - Ghi (write): Mỗi khi người dùng thêm hoặc cập nhật mặt hàng trong giỏ hàng, dữ liệu sẽ cần được ghi lại cho mỗi _AccountID_.
   - Đọc (read): Người dùng có thể yêu cầu xem các mặt hàng đã thêm vào giỏ hàng theo _AccountID_ và có thể cần truy vấn dựa trên _CreateTimestamp_ và _Status_.

### **Tổ chức các mục dữ liệu liên quan đến mẫu truy cập chính:**

- **Làm thế nào để sắp xếp các mục dữ liệu để trả về theo thứ tự đã sắp xếp?** (sort by)
   - Sử dụng _CreateTimestamp_ làm khóa sắp xếp (sort key). Điều này giúp việc truy vấn các mục theo thứ tự thời gian trở nên hiệu quả hơn.
  
- **Cấu trúc quan hệ từ tổng quát nhất đến cụ thể hơn:**
   - **_AccountID_** là khóa phân vùng chính, và **_CreateTimestamp_** là khóa sắp xếp chính. Điều này giúp truy vấn dữ liệu theo người dùng và các mục trong giỏ hàng theo thời gian.

### **Đáp ứng các mẫu truy cập thứ hai, ba và bốn:**

- **Mẫu truy cập thứ hai (OLTP - Online Transaction Processing):**
   - Trả về các mặt hàng liên quan đến một _AccountID_ cụ thể và được sắp xếp theo _CreateTimestamp_ và _Status_. Điều này có thể được thực hiện dễ dàng với cấu trúc khóa phân vùng là _AccountID_ và khóa sắp xếp là _CreateTimestamp_.
  
- **Mẫu truy cập thứ ba (OLTP):**
   - Trả về các mặt hàng theo _ItemSKU_, sắp xếp theo _CreateTimestamp_ và trong phạm vi _Status_. Để đáp ứng mẫu truy cập này, bạn có thể tạo **Global Secondary Index (GSI)** với _ItemSKU_ làm khóa phân vùng và _CreateTimestamp_ làm khóa sắp xếp.
  
- **Mẫu truy cập thứ tư (OLAP - Online Analytical Processing):**
   - Truy vấn ngoại tuyến cho Business Intelligence có thể không cần thực hiện trực tiếp trên DynamoDB. Bạn có thể xuất dữ liệu ra Amazon S3 và sử dụng các công cụ như **Amazon Athena** hoặc **AWS Glue** để thực hiện các phân tích chuyên sâu và tạo báo cáo.

### **Tham khảo hữu ích**

- **[Best Practices for Using Sort Keys to Organize Data](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-sort-keys.html)**  
- **[Working with Queries in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html)**  
- **[Using Global Secondary Indexes in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html)**  
- **[How to perform advanced analytics and build visualizations of your Amazon DynamoDB data by using Amazon Athena](https://aws.amazon.com/blogs/database/how-to-perform-advanced-analytics-and-build-visualizations-of-your-amazon-dynamodb-data-by-using-amazon-athena/)**

