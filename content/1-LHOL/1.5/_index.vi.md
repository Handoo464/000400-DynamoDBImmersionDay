---
title : "LMIG: Relational Modeling & Migration"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 1.5. </b> "
---

Trong module này, còn được phân loại là LMIG, bạn sẽ học cách thiết kế một mô hình dữ liệu mục tiêu trong DynamoDB cho dữ liệu quan hệ được chuẩn hóa cao trong cơ sở dữ liệu quan hệ. Bài tập này cũng hướng dẫn từng bước quá trình di chuyển một bộ dữ liệu IMDb từ một phiên bản cơ sở dữ liệu MySQL tự quản lý trên EC2 sang cơ sở dữ liệu cặp khóa-giá trị được quản lý hoàn toàn, Amazon DynamoDB. Khi kết thúc bài học này, bạn sẽ tự tin vào khả năng thiết kế và di chuyển một cơ sở dữ liệu quan hệ hiện có sang Amazon DynamoDB.

Đôi khi, dữ liệu xuất hiện dưới dạng định dạng quan hệ tại một thời điểm nhất định, nhưng yêu cầu kinh doanh thay đổi có thể gây ra các thay đổi trong schema trong suốt vòng đời dự án. Mỗi thay đổi schema đều tốn công sức, chi phí và đôi khi khiến doanh nghiệp phải ưu tiên lại nhu cầu của họ do các tác động phức tạp của việc thay đổi này. Amazon DynamoDB giúp bộ phận CNTT suy nghĩ lại về mô hình dữ liệu trong định dạng cặp khóa-giá trị. Định dạng này có tiềm năng hấp thụ những gián đoạn do schema thay đổi. Amazon DynamoDB cung cấp một kho dữ liệu không cần quản lý, không máy chủ cho thông tin được lưu trữ dưới dạng cặp khóa-giá trị. Sự linh hoạt về schema cho phép DynamoDB lưu trữ dữ liệu phân cấp phức tạp trong một mục và cung cấp độ trễ chỉ vài mili giây ngay cả khi mở rộng quy mô.

Module này sẽ thảo luận ngắn gọn về các kỹ thuật để thiết kế mô hình dữ liệu mục tiêu và di chuyển các bộ dữ liệu quan hệ từ MySQL sang Amazon DynamoDB. Dữ liệu IMDb trong cơ sở dữ liệu MySQL bắt đầu dưới dạng chuẩn hóa trên nhiều bảng. Chúng ta sẽ sử dụng các kỹ thuật mô hình hóa dữ liệu phi chuẩn hóa hoặc mô hình hóa bộ sưu tập mục để tạo ra một mô hình dữ liệu toàn diện cho các mẫu truy cập đã xác định. Có nhiều yếu tố sẽ ảnh hưởng đến quyết định của chúng ta trong việc xây dựng mô hình dữ liệu mục tiêu:

- Mẫu truy cập (Access patterns)
- Độ đông đúc (Cardinality)
- Tổng thể I/O

Chúng ta sẽ thảo luận ngắn gọn về các khía cạnh chính của việc tạo ra một mô hình có thể phục vụ nhiều mẫu truy cập với độ trễ cực thấp, I/O thấp và chi phí thấp.

![Final Deployment Architecture](/images/1/1.5/denormalization.png)