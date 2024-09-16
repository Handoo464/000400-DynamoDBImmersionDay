---
title : "Bước 2 - Truy vấn tất cả nhân viên (employees) từ tiểu bang (state)"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.7.2. </b> "
---

Bạn có thể sử dụng global secondary index mới để truy vấn bảng. Nếu bạn chỉ sử dụng tiểu bang, truy vấn sẽ không sử dụng thuộc tính khóa sắp xếp. Tuy nhiên, nếu truy vấn có giá trị cho tham số thứ hai, mã sẽ sử dụng thuộc tính `GSI_3_SK` của global secondary index, chứa cùng giá trị với thuộc tính `city_dept`, để truy vấn tất cả các giá trị bắt đầu với giá trị tham số.

Ảnh chụp màn hình sau đây hiển thị việc sử dụng các thuộc tính khóa tổng hợp để truy vấn theo thành phố và phòng ban.  
![Using Composite key attributes to query by city and department](/images/3/3.7/2.png)

Chúng ta có thể thực hiện truy vấn này trong một script Python. Đoạn mã này cho thấy cách một script có thể nhận hai tham số đầu vào (được hiển thị dưới dạng value1 và value2) và tạo một truy vấn đối với global secondary index GSI_3.

```py
if value2 == "-":
  ke = Key('GSI_3_PK').eq("state#{}".format(value1))
else:
  ke = Key('GSI_3_PK').eq("state#{}".format(value1)) & Key('GSI_3_SK').begins_with(value2)

response = table.query(
  IndexName='GSI_3',
  KeyConditionExpression=ke
  )
```

Chạy script Python sau để truy vấn global secondary index `GSI_3` cho tất cả nhân viên ở bang Texas.

```bash
python query_city_dept.py employees TX
```

Kết quả sẽ trông tương tự như sau:

```txt
List of employees . State: TX
    Name: Bree Gershom. City: Austin. Dept: Development
    Name: Lida Flescher. City: Austin. Dept: Development
    Name: Tristam Mole. City: Austin. Dept: Development
    Name: Malinde Spellman. City: Austin. Dept: Development
    Name: Giovanni Goutcher. City: Austin. Dept: Development
  ...
  Name: Cullie Sheehy. City: San Antonio. Dept: Support
  Name: Ari Wilstead. City: San Antonio. Dept: Support
  Name: Odella Kringe. City: San Antonio. Dept: Support
Total of employees: 197. Execution time: 0.238062143326 seconds
```