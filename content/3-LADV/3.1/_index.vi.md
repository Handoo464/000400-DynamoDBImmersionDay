---
title : "Mở AWS Systems Manager Console"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.1. </b> "
---
1. Sau khi bạn đã có quyền truy cập vào Bảng điều khiển quản lý AWS dành cho phòng thực hành, hãy kiểm tra kỹ xem khu vực có chính xác không và tên vai trò **WSParticipantRole** xuất hiện ở trên cùng bên phải của bảng điều khiển.
2. Trong thanh tìm kiếm dịch vụ, tìm kiếm **Systems Manager** và nhấp vào đó để mở phần AWS Systems Manager của Bảng điều khiển quản lý AWS.
3. Trong bảng điều khiển AWS Systems Manager, tìm menu ở bên trái, xác định phần **Quản lý nút** và chọn **Trình quản lý phiên** từ danh sách.
4. Chọn **Bắt đầu phiên** để khởi chạy phiên shell.
5. Nhấp vào nút radio để chọn phiên bản EC2 cho phòng thực hành. Nếu bạn thấy không có phiên bản nào, hãy đợi vài phút rồi nhấp vào làm mới. Đợi cho đến khi phiên bản ec2 có tên of sẵn dùng trước khi tiếp tục. Chọn phiên bản.`DynamoDBC9`
6. Nhấp vào nút **Bắt đầu phiên** (Hành động này sẽ mở một tab mới trong trình duyệt của bạn với vỏ màu đen mới).
7. Trong vỏ đen mới, chuyển sang tài khoản ubuntu bằng cách chạy `sudo su - ubuntu`
    
    ```bash
    sudo su - ubuntu
    ```
    
8. Chạy và chắc chắn rằng nó nói và sau đó thay đổi vào thư mục hội thảo.`shopt login_shell``login_shell on`
    
    ```bash
    #Verify login_shell is 'on'
    shopt login_shell
    #Change into the workshop directory
    cd ~/workshop/
    ```
    

Đầu ra của các lệnh của bạn trong phiên Trình quản lý phiên sẽ giống như sau:

```bash
$ sudo su - ubuntu
:~ $ #Verify login_shell is 'on'
shopt login_shell
#Change into the workshop directory
cd ~/workshop/
login_shell     on
:~/workshop $
```