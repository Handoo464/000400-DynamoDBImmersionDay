---
title : "Bước 3 - Truy vấn bảng nhân viên bằng cách sử dụng global secondary index với các thuộc tính quá tải"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.5.3. </b> "
---

Bạn có thể truy vấn tất cả nhân viên làm việc tại bang Washington (WA) ở Hoa Kỳ bằng cách chạy script `query_employees.py` sau đây, bao gồm mã để truy vấn một bảng bằng cách sử dụng phương pháp nạp chồng chỉ mục phụ toàn cầu.

```py
if attribute == 'name':
    ke = Key('GSI_1_PK').eq('root') & Key('GSI_1_SK').eq(value)
else:
    ke = Key('GSI_1_PK').eq(attribute + "#" + value)

response = table.query(
    IndexName='GSI_1',
    KeyConditionExpression=ke
    )
```

Chạy script Python sau để truy xuất các nhân viên làm việc tại bang Washington (WA).

```bash
python query_employees.py employees state 'WA'
```

Script sẽ cho bạn kết quả như sau:

```txt
List of employees with WA in the attribute state:
    Employee name: Alice Beilby - hire date: 2014-12-03
    Employee name: Alla Absalom - hire date: 2015-06-25
    Employee name: Alvan Heliar - hire date: 2016-05-15
    Employee name: Anders Galtone - hire date: 2015-12-22
    Employee name: Ashil Hutchin - hire date: 2015-02-11
  ...
  Employee name: Sula Prattin - hire date: 2014-01-11
    Employee name: Vittoria Edelman - hire date: 2014-10-01
    Employee name: Willie McCuthais - hire date: 2015-05-27
Total of employees: 46. Execution time: 0.13477110862731934 seconds
```

Bạn có thể thử truy vấn một bang khác của Hoa Kỳ bằng cách thay đổi tham số cuối cùng của lệnh. Lệnh sau truy vấn tất cả nhân viên làm việc tại bang Texas.

```bash
python query_employees.py employees state 'TX'
```

Nếu bạn muốn truy vấn các bang khác, hãy nhấp vào đây để mở danh sách các bang của Hoa Kỳ có dữ liệu trong bảng.

Sử dụng cùng truy vấn này, bạn có thể truy vấn nhân viên theo chức danh công việc. Chạy lệnh sau làm ví dụ.

```bash
python query_employees.py employees current_title 'Software Engineer'
```

Lệnh trên sẽ cho bạn kết quả sau:

```txt
 List of employees with Software Engineer in the attribute current_title:
    Employee name: Alice Beilby - hire date: 2014-11-03
    Employee name: Anetta Byrne - hire date: 2017-03-15
    Employee name: Ardis Panting - hire date: 2015-08-06
    Employee name: Chris Randals - hire date: 2016-10-27
    Employee name: Constantine Barendtsen - hire date: 2016-06-10
    Employee name: Eudora Janton - hire date: 2015-01-05
    Employee name: Florella Allsep - hire date: 2015-03-31
    Employee name: Horatius Trangmar - hire date: 2013-10-21
    Employee name: Korey Daugherty - hire date: 2016-11-03
    Employee name: Lenka Luquet - hire date: 2014-10-01
    Employee name: Leonora Hyland - hire date: 2016-06-14
    Employee name: Lucretia Ruffell - hire date: 2015-07-04
    Employee name: Malcolm Adiscot - hire date: 2014-04-17
    Employee name: Melodie Sebire - hire date: 2013-08-27
    Employee name: Menard Ogborn - hire date: 2014-06-27
    Employee name: Merwyn Petters - hire date: 2014-06-19
    Employee name: Niels Buston - hire date: 2014-10-30
    Employee name: Noelani Studde - hire date: 2015-03-30
Total of employees: 18. Execution time: 0.11937260627746582 seconds
```

Bạn cũng có thể thử một chức danh khác, như trong lệnh Python sau.

```bash
python query_employees.py employees current_title 'IT Support Manager'
```

Nếu bạn muốn biết danh sách tất cả các chức danh có sẵn, nhấp vào đây!

Sử dụng cùng truy vấn với một thay đổi nhỏ, bạn có thể truy vấn nhân viên theo tên, như trong lệnh sau.

```bash
python query_employees.py employees name 'Dale Marlin'
```

Lệnh trên sẽ cho bạn kết quả sau:

```txt
 List of employees with Dale Marlin in the attribute name:
    Employee name: Dale Marlin - hire date: 2014-10-19
Total of employees: 1. Execution time: 0.1274700164794922 seconds
```

#### Tóm tắt

Chúc mừng, bạn đã hoàn thành bài tập này và minh họa cách thiết kế nạp chồng khóa chỉ mục phụ toàn cầu có thể hỗ trợ nhiều mẫu truy cập. Sử dụng mẫu này để phù hợp với các loại thực thể khác nhau trong cùng một bảng DynamoDB và giữ khả năng truy vấn dữ liệu trên các khóa phân vùng khác nhau với chỉ mục phụ toàn cầu. Trong bài tập tiếp theo, bạn sẽ tìm hiểu về Sparse Global Secondary Indexes!