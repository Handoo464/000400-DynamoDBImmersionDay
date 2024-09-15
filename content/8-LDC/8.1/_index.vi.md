---
title : "Giỏ hàng bán lẻ"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 8.1. </b> "
---

## Thử Thách Giỏ Hàng Bán Lẻ

Một cửa hàng bán lẻ trực tuyến đã yêu cầu bạn thiết kế lớp lưu trữ dữ liệu và bảng NoSQL cho họ. Trang web phục vụ các khách hàng và các sản phẩm mà họ xem, lưu và mua. Lượng truy cập trang web hiện tại đang thấp, nhưng họ muốn có khả năng phục vụ hàng triệu khách hàng đồng thời.

- Khách hàng tương tác với các sản phẩm có thể có trạng thái `ACTIVE`, `SAVED`, hoặc `PURCHASED`. Một khi sản phẩm được `PURCHASED` thì sẽ được gán một _OrderId_.
- Các sản phẩm có các thuộc tính sau: _AccountID_, _Status_ (`ACTIVE`, `SAVED`, hoặc `PURCHASED`), _CreateTimestamp_, và _ItemSKU_ (Tổng kích thước sản phẩm <= 1 KB).
- Khi khách hàng mở ứng dụng của cửa hàng bán lẻ, họ sẽ xem các sản phẩm `ACTIVE` trong giỏ hàng của họ, được sắp xếp theo thứ tự sản phẩm được thêm vào gần đây nhất.
- Người dùng có thể xem các sản phẩm mà họ đã lưu để dùng sau (`SAVED`), được sắp xếp theo thứ tự gần đây nhất.
- Người dùng có thể xem các sản phẩm mà họ đã mua (`PURCHASED`), được sắp xếp theo thứ tự gần đây nhất.
- Các đội sản phẩm có khả năng thường xuyên truy vấn trên tất cả khách hàng để xác định những người có một sản phẩm cụ thể trong tài khoản của họ với trạng thái là `ACTIVE`, `SAVED`, hoặc `PURCHASED`.
- Đội Business Intelligence (BI) cần chạy một số truy vấn phức tạp bất kỳ để tạo các báo cáo hàng tuần và hàng tháng.

Hãy xây dựng một mô hình dữ liệu NoSQL để đáp ứng phần OLTP (xử lý giao dịch trực tuyến) của công việc. Bạn sẽ đáp ứng yêu cầu của đội BI như thế nào?
