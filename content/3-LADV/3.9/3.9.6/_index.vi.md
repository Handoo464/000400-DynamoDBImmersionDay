---
title : "Bước 6 - Điền vào bảng logfile và xác minh sao chép để logfile_replica"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 3.9.6. </b> "
---

Chạy đoạn mã Python sau để tải thêm các mục vào bảng `logfile`. Các hàng sẽ được sao chép vào DynamoDB stream, được xử lý bởi hàm AWS Lambda, và sau đó được ghi vào bảng `logfile_replica` cuối cùng.

```bash
python load_logfile.py logfile ./data/logfile_stream.csv
```

Kết quả đầu ra sẽ trông như sau:

```txt
RowCount: 2000, Total seconds: 15.808809518814087
```

#### Xác minh việc sao chép

Bạn có thể quét bảng `logfile_replica` để xác minh rằng các bản ghi đã được sao chép. Quá trình này mất vài giây, vì vậy bạn có thể cần phải lặp lại lệnh AWS CLI sau đây cho đến khi bạn nhận được các bản ghi. Một lần nữa, sử dụng phím mũi tên lên để lặp lại lệnh trước đó.

```bash
aws dynamodb scan --table-name 'logfile_replica' --max-items 2 --output text
```

Bạn sẽ thấy hai mục đầu tiên của bảng sao chép như sau:

```txt
None    723     723
BYTESSENT       2969
DATE    2009-07-21
HOST    64.233.172.17
HOUROFDAY       8
METHOD  GET
REQUESTID       4666
RESPONSECODE    200
TIMEZONE        GMT-0700
URL     /gwidgets/alexa.xml
USERAGENT       Mozilla/5.0 (compatible) Feedfetcher-Google; (+http://www.google.com/feedfetcher.html)
BYTESSENT       1160
DATE    2009-07-21
HOST    64.233.172.17
HOUROFDAY       6
METHOD  GET
REQUESTID       4119
RESPONSECODE    200
TIMEZONE        GMT-0700
URL     /gadgets/adpowers/AlexaRank/ALL_ALL.xml
USERAGENT       Mozilla/5.0 (compatible) Feedfetcher-Google; (+http://www.google.com/feedfetcher.html)
NEXTTOKEN       eyJFeGNsdXNpdmVTdGFydEtleSI6IG51bGwsICJib3RvX3RydW5jYXRlX2Ftb3VudCI6IDJ9
```

**Lưu ý**: Nhật ký của bạn có thể khác nhau. Miễn là bạn có hai mục nhật ký, bạn đã xác minh việc sao chép thành công. Nếu bạn không thấy bất kỳ mục nào, hãy chạy lại lệnh `load_logfile.py` vì bạn có thể đã thực hiện các lệnh chèn quá sớm sau khi tạo hàm Lambda.

## Chúc mừng, bạn đã hoàn thành thành công tất cả các bài tập trong buổi workshop!

Nếu bạn tự thực hiện lab trên tài khoản AWS của mình, bạn nên xóa tất cả các bảng được tạo trong quá trình này. Nếu bạn đang ở một sự kiện AWS sử dụng nền tảng AWS Workshop (Workshop Studio), bạn không cần xóa bảng của mình.

{{%notice tip%}}
Trong quá trình thực hiện lab, bạn đã tạo các bảng DynamoDB có thể phát sinh chi phí lên tới hàng chục hoặc hàng trăm đô la mỗi ngày. Bạn phải xóa các bảng DynamoDB bằng cách sử dụng bảng điều khiển DynamoDB để dọn dẹp lab. Ngoài ra, nếu bạn không thuộc một sự kiện AWS hoặc bạn đang chạy lab này trong tài khoản của riêng mình, hãy đảm bảo xóa stack CloudFormation ngay khi hoàn thành lab. Nếu bạn đang sử dụng Workshop Studio Event Delivery, bạn không cần phải xóa stack CloudFormation.
{{%/notice%}}
