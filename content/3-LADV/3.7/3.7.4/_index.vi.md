---
title : "Kết nối đến máy chủ Public"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.7.4. </b> "
---
Bạn cũng có thể sử dụng global secondary index để truy vấn nhân viên theo tiểu bang. Chạy tập lệnh Python sau để liệt kê tất cả nhân viên trong bộ phận Vận hành ở Dallas, Texas.

```bash
python query_city_dept.py employees TX --citydept 'Dallas#Op'
```

Ra:

```txt
List of employees . State: TX
    Name: Brady Marvel. City: Dallas. Dept: Operation
    Name: Emmye Fletcher. City: Dallas. Dept: Operation
    Name: Audra Leahey. City: Dallas. Dept: Operation
    Name: Waneta Parminter. City: Dallas. Dept: Operation
    Name: Lizbeth Proudler. City: Dallas. Dept: Operation
    Name: Arlan Cummings. City: Dallas. Dept: Operation
    Name: Bone Ruggs. City: Dallas. Dept: Operation
    Name: Karlis Prisk. City: Dallas. Dept: Operation
    Name: Marve Bignold. City: Dallas. Dept: Operation
Total of employees: 9. Execution time: 0.174154996872 seconds
```

Trong bài tập này, chúng ta đã tạo một chỉ mục phụ toàn cục để truy vấn các thuộc tính bổ sung. Dữ liệu hiện có thể được truy xuất bằng cách sử dụng các trường City và Department.