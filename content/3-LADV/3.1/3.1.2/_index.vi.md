---
title : "Bước 1 - Mở AWS Manager Console"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.1.2. </b> "
---

1. Sau khi bạn đã truy cập vào AWS Management Console cho bài lab, hãy kiểm tra lại xem vùng (region) có đúng không và tên vai trò **WSParticipantRole** xuất hiện ở góc trên bên phải của console.
2. Trong thanh tìm kiếm dịch vụ, tìm **Systems Manager** và nhấp vào đó để mở phần AWS Systems Manager của AWS Management Console.
3. Trong bảng điều khiển AWS Systems Manager, tìm menu ở bên trái, xác định phần **Node Management** và chọn **Session Manager** từ danh sách.
4. Chọn **Start session** để khởi chạy một phiên shell.
5. Nhấp vào nút radio để chọn phiên bản EC2 cho bài lab. Nếu bạn không thấy phiên bản nào, chờ vài phút và sau đó nhấn làm mới. Chờ cho đến khi có một phiên bản EC2 với tên `DynamoDBC9` khả dụng trước khi tiếp tục. Chọn phiên bản đó.
6. Nhấp vào nút **Start Session** (Thao tác này sẽ mở một tab mới trong trình duyệt của bạn với một shell đen mới).
7. Trong shell đen mới, chuyển sang tài khoản ubuntu bằng cách chạy `sudo su - ubuntu`

    ```bash
    sudo su - ubuntu
    ```
    
8. Chạy lệnh `shopt login_shell` và đảm bảo nó hiển thị `login_shell on`, sau đó chuyển vào thư mục workshop.

    ```bash
    # Xác minh login_shell là 'on'
    shopt login_shell
    # Chuyển vào thư mục workshop
    cd ~/workshop/
    ```
    
Đầu ra của các lệnh trong phiên Session Manager sẽ trông như sau:

```bash
$ sudo su - ubuntu
:~ $ # Xác minh login_shell là 'on'
shopt login_shell
# Chuyển vào thư mục workshop
cd ~/workshop/
login_shell     on
:~/workshop $
```