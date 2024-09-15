---
title : "Bước 4 - Kiểm tra nội dung thư mục workshop"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 3.1.5. </b> "
---
Trên phiên bản EC2, vào thư mục workshop và chạy lệnh ls:

```bash
cd /home/ubuntu/workshop
ls -l .
```

Danh sách sau đây cho biết cấu trúc thư mục và các tệp sẽ được sử dụng trong hội thảo:

```bash
.
├── data
│   ├── employees.csv
│   ├── invoice-data2.csv
│   ├── invoice-data.csv
│   ├── logfile_medium1.csv
│   ├── logfile_medium2.csv
│   ├── logfile_small1.csv
│   └── logfile_stream.csv
├── ddbreplica_lambda.py
├── ddb-replication-role-arn.txt
├── gsi_city_dept.json
├── gsi_manager.json
├── iam-role-policy.json
├── iam-trust-relationship.json
├── lab_config.py
├── load_employees.py
├── load_invoice.py
├── load_logfile_parallel.py
├── load_logfile.py
├── query_city_dept.py
├── query_employees.py
├── query_index_invoiceandbilling.py
├── query_invoiceandbilling.py
├── query_responsecode.py
├── requirements.txt
├── scan_for_managers_gsi.py
├── scan_for_managers.py
├── scan_logfile_parallel.py
└── scan_logfile_simple.py
```

Mã Python:

- ddbreplica_lambda.py
- load_employees.py
- load_invoice.py
- load_logfile_parallel.py
- load_logfile.py
- lab_config.py
- query_city_dept.py
- query_employees.py
- query_index_invoiceandbilling.py
- query_invoiceandbilling.py
- query_responsecode.py
- scan_for_managers_gsi.py
- scan_for_managers.py
- scan_logfile_parallel.py
- scan_logfile_simple.py

JSON:

- gsi_city_dept.json
- gsi_manager.json
- iam-role-policy.json
- iam-trust-relationship.json

Văn bản (được sử dụng sau này trong phòng thí nghiệm):

- ddb-replication-role-arn.txt

Chạy lệnh ls để hiển thị các tệp dữ liệu mẫu:

```bash
ls -l ./data
```

./Nội dung dữ liệu:

- employees.csv
- invoice-data2.csv
- invoice-data.csv
- logfile_medium1.csv
- logfile_medium2.csv
- logfile_small1.csv
- logfile_stream.csv

{{%notice warning%}}
_Lưu ý: Mã được cung cấp chỉ dành cho mục đích sử dụng theo hướng dẫn. Nó không nên được sử dụng bên ngoài phòng thí nghiệm này, và nó không phù hợp để sử dụng sản xuất.
{{%/notice%}}