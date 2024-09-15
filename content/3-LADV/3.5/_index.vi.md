---
title : "Bài tập 4: Quá tải Global Secondary Index"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 3.5. </b> "
---

Tại thời điểm trang này được viết, bạn có thể tạo tối đa 20 chỉ mục phụ toàn cầu (global secondary indexes) cho một bảng DynamoDB. Tuy nhiên, đôi khi ứng dụng của bạn có thể cần hỗ trợ nhiều mẫu truy cập và vượt quá giới hạn hiện tại về số lượng chỉ mục phụ toàn cầu trên mỗi bảng. Mẫu thiết kế "nạp chồng khóa chỉ mục phụ toàn cầu" được kích hoạt bằng cách chỉ định và tái sử dụng tên thuộc tính (tiêu đề cột) trên các loại mục khác nhau và lưu trữ giá trị trong thuộc tính đó tùy thuộc vào ngữ cảnh của loại mục. Khi bạn tạo một chỉ mục phụ toàn cầu trên thuộc tính đó, bạn đang lập chỉ mục cho nhiều mẫu truy cập, mỗi mẫu cho một loại mục khác nhau—và chỉ sử dụng một chỉ mục phụ toàn cầu duy nhất. Ví dụ, một bảng `employees`. Một nhân viên có thể chứa các mục kiểu `metadata` (cho chi tiết nhân viên), `employee-title` (tất cả các chức danh công việc mà nhân viên đã giữ), hoặc `employee-location` (tất cả các tòa nhà văn phòng và vị trí mà nhân viên đã làm việc).

Các mẫu truy cập cần thiết cho kịch bản này bao gồm:

- Truy vấn tất cả nhân viên của một tiểu bang
- Truy vấn tất cả nhân viên với một chức danh hiện tại cụ thể
- Truy vấn tất cả nhân viên từng có một chức danh cụ thể
- Truy vấn nhân viên theo tên

Ảnh chụp màn hình sau đây hiển thị thiết kế của bảng `employees`. Thuộc tính có tên `PK` chứa ID của nhân viên, được thêm vào phía trước bởi chữ cái `e`. Dấu thăng (#) được sử dụng làm ký tự phân tách giữa định danh loại thực thể (`e`) và ID nhân viên thực tế. Thuộc tính `SK` là một thuộc tính được nạp chồng và có thể là chức danh hiện tại, chức danh trước đây, hoặc từ khóa `root`, biểu thị mục chính cho nhân viên chứa hầu hết các thuộc tính quan trọng của họ. Thuộc tính `GSI_1_PK` bao gồm chức danh hoặc tên của nhân viên. Việc tái sử dụng một chỉ mục phụ toàn cầu nhất định cho nhiều loại thực thể như nhân viên, vị trí làm việc của nhân viên, và chức danh nhân viên giúp chúng ta đơn giản hóa việc quản lý bảng DynamoDB vì chúng ta chỉ cần giám sát và trả tiền cho một chỉ mục phụ toàn cầu thay vì ba chỉ mục riêng biệt.

![Thiết kế mẫu cho bảng Employees sử dụng mẫu nạp chồng GSI](/images/3/3.5/1.png)