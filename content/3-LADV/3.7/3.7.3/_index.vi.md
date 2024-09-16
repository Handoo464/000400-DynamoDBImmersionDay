---
title : "Bước 3 - Truy vấn tất cả nhân viên (employees) của một thành phố (city)"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.7.3. </b> "
---
Bạn có một global secondary index mới mà bạn có thể sử dụng để truy vấn nhân viên theo thành phố. Chạy lệnh Python sau để liệt kê tất cả nhân viên theo bộ phận ở Dallas, Texas.

```bash
python query_city_dept.py employees TX --citydept Dallas
```

Kết quả sẽ giống như sau.

```txt
List of employees . State: TX
    Name: Grayce Duligal. City: Dallas. Dept: Development
    Name: Jere Vaughn. City: Dallas. Dept: Development
    Name: Valeria Gilliatt. City: Dallas. Dept: Development
  ...
  Name: Brittani Hunn. City: Dallas. Dept: Support
    Name: Oby Peniello. City: Dallas. Dept: Support
Total of employees: 47. Execution time: 0.21702003479 seconds
```