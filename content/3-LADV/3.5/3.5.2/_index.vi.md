---
title : "Bước 2 - Tải dữ liệu vào bảng mới"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.5.2. </b> "
---

Bây giờ, hãy thực thi một script để nạp dữ liệu từ tệp có tên `./employees.csv` vào bảng có tên `employees`.

```bash
python load_employees.py employees ./data/employees.csv
```

Bản ghi mẫu trong tệp `employees.csv` trông như sau:

```csv
1000,Nanine Denacamp,Programmer Analyst,Development,San Francisco,CA,1981-09-30,2014-06-01,Senior Database Administrator,2014-01-25
```

Khi bạn nạp dữ liệu này vào bảng, bạn sẽ nối một số thuộc tính, chẳng hạn như `city_dept` (ví dụ: San Francisco:Development) vì bạn có một mẫu truy cập trong truy vấn tận dụng thuộc tính đã nối này. Thuộc tính `SK` cũng là một thuộc tính được tạo ra. Việc nối các thuộc tính này được xử lý trong script Python, script này sẽ lắp ráp bản ghi và sau đó thực thi một lệnh `put_item()` để ghi bản ghi vào bảng.

Kết quả đầu ra:

```txt
$ python load_employees.py employees ./data/employees.csv
employee count: 100 in 3.7393667697906494
employee count: 200 in 3.7162938117980957
...
employee count: 900 in 3.6725080013275146
employee count: 1000 in 3.6174678802490234
RowCount: 1000, Total seconds: 36.70457601547241
```

Kết quả đầu ra xác nhận rằng 1000 mục đã được chèn vào bảng.

Xem lại bảng `employees` trong bảng điều khiển DynamoDB (như hiển thị trong ảnh chụp màn hình sau) bằng cách chọn bảng **employees** và sau đó chọn mục menu **Items**.

![Bảng Employees](/images/3/3.5/2.png)

Trên cùng trang đó, ở ngăn bên phải, chọn **[Index]** từ menu thả xuống và sau đó nhấp vào **Run**.

![Tìm kiếm GSI](/images/3/3.5/3.png)

Bây giờ bạn có thể thấy kết quả của thao tác "Scan" trên một chỉ mục phụ toàn cầu được nạp chồng. Có nhiều loại thực thể khác nhau trong tập kết quả: một mục gốc, một chức danh trước đây, và một chức danh hiện tại. ![Chỉ mục phụ toàn cầu](/images/3/3.5/4.png)