---
title : "Bước 5 - Kiểm tra định dạng và nội dung tệp"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 3.1.6. </b> "
---

Trong quá trình thực hiện lab này, bạn sẽ làm việc với các loại dữ liệu khác nhau:

1. Dữ liệu nhật ký máy chủ (Server Logs)
2. Dữ liệu nhân viên (Employees data)
3. Dữ liệu hóa đơn và biên lai (Invoices and Bills data)

Tệp nhật ký máy chủ (Server Logs) có cấu trúc như sau:

- requestid (số)
- host (chuỗi)
- date (chuỗi)
- hourofday (số)
- timezone (chuỗi)
- method (chuỗi)
- url (chuỗi)
- responsecode (số)
- bytessent (số)
- useragent (chuỗi)

Để xem một bản ghi mẫu trong tệp, thực hiện lệnh:

```bash
head -n1 ./data/logfile_small1.csv
```

Bản ghi nhật ký mẫu:

```csv
1,66.249.67.3,2017-07-20,20,GMT-0700,GET,"/gallery/main.php?g2_controller=exif.SwitchDetailMode&g2_mode=detailed&g2_return=%2Fgallery%2Fmain.php%3Fg2_itemId%3D15741&g2_returnName=photo",302,5,"Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"
```

Tệp dữ liệu nhân viên (Employees data) có cấu trúc như sau:

- employeeid (số)
- name (chuỗi)
- title (chuỗi)
- dept (chuỗi)
- city (chuỗi)
- state (chuỗi)
- dob (chuỗi)
- hire-date (chuỗi)
- previous title (chuỗi)
- previous title end date (chuỗi)
- is a manager (chuỗi), 1 nếu là quản lý, không có giá trị nếu không phải là quản lý

Để xem một bản ghi mẫu trong tệp, thực hiện lệnh:

```bash
head -n1 ./data/employees.csv
```

Bản ghi nhân viên mẫu:

```csv
1,Onfroi Greeno,Systems Administrator,Operation,Portland,OR,1992-03-31,2014-10-24,Application Support Analyst,2014-04-12
```