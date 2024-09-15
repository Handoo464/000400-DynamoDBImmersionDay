---
title : "Bước 2 - Kiểm tra cài đặt Python và AWS CLI"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3.1.3 </b> "
---

Chạy lệnh sau để kiểm tra Python trên phiên bản EC2 của bạn:

```bash
#Check the python version:
python --version
```

Ra:

```plain
Python 3.10.12
```

**Lưu ý: Phiên bản chính và phụ của Python có thể khác với những gì bạn thấy ở trên**

Chạy lệnh sau để kiểm tra AWS CLI trên phiên bản EC2 của bạn:

```bash
#Check the AWS CLI version.
aws --version
```

Đầu ra mẫu:

```bash
#Note that your linux kernel version may differ from the example.
aws-cli/2.13.26 Python/3.11.6 Linux/6.2.0-1013-aws exe/x86_64.ubuntu.22 prompt/off
```

{{%notice info%}}
_Đảm bảo bạn có AWS CLI phiên bản 2.x trở lên và python 3.10 trở lên trước khi tiếp tục. Nếu bạn không có các phiên bản này, bạn có thể gặp khó khăn trong việc hoàn thành thành công phòng thí nghiệm._
{{%/notice%}}