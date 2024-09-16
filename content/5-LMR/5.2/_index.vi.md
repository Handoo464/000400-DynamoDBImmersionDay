---
title : "Module 1: Triển khai các tài nguyên phụ trợ"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---
Bạn có thể truy cập EC2 Instance hoặc Cloud9 để thực hiện các bước thiết lập.

## Các bước thiết lập

Phòng thí nghiệm này yêu cầu một terminal shell với Python3 và AWS Command Line Interface (CLI) đã được cài đặt và cấu hình với thông tin đăng nhập quản trị.

### Để thiết lập môi trường cho một EC2 Instance:
1. Truy cập EC2
2. Chọn Instance và kết nối sử dụng session manager
![](/images/5/5.2/1.png)
![](/images/5/5.2/3.png)

### Để thiết lập môi trường phát triển AWS Cloud9 của bạn:

1. Chọn **Services** ở đầu trang, sau đó chọn **Cloud9** trong **Developer Tools**.
    
2. Sẽ có một môi trường sẵn sàng sử dụng dưới **My environments**.
    
3. Nhấp vào **Open** dưới **Cloud9 IDE**, và IDE của bạn sẽ mở với một ghi chú chào mừng.
    

{{%notice tip%}}
Bây giờ bạn sẽ thấy môi trường AWS Cloud9 của mình. Bạn cần làm quen với ba khu vực của bảng điều khiển AWS Cloud9 được hiển thị trong ảnh chụp màn hình sau:
{{%/notice%}}

![Cloud9 Environment](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/global-serverless-application/module_1/cloud9-environment.png)

- **File explorer**: Ở phía bên trái của IDE, file explorer hiển thị danh sách các tệp trong thư mục của bạn.
    
- **File editor**: Ở phía trên bên phải của IDE, file editor là nơi bạn xem và chỉnh sửa các tệp mà bạn đã chọn trong file explorer.
    
- **Terminal**: Ở phía dưới bên phải của IDE, đây là nơi bạn chạy các lệnh để thực thi các mẫu mã.
    
### Xác minh môi trường

1. Chạy `aws sts get-caller-identity` để xác minh AWS CLI đang hoạt động.
2. Chạy `python3 --version` để xác minh rằng python3 đã được cài đặt.
3. Môi trường Cloud9 của bạn đã được cấu hình sẵn với boto3, nhưng cho phòng thí nghiệm này, chúng ta cũng sẽ cần AWS Chalice.  
    Chạy `sudo python3 -m pip install chalice` để cài đặt [AWS Chalice](https://github.com/aws/chalice).

Bạn có thể thấy một vài dòng WARNING ở gần cuối đầu ra của lệnh, nhưng bạn có thể bỏ qua chúng một cách an toàn.

4. Chạy `curl -O https://amazon-dynamodb-labs.com/assets/global-serverless.zip`
5. Chạy `unzip global-serverless.zip && cd global-serverless`
6. Để xem các tài nguyên ứng dụng mà chúng ta sẽ triển khai, bạn có thể mở tệp **app.py** bằng cách điều hướng đến "global-serverless/app.py" trong file explorer. Mã này định nghĩa các hàm Lambda và các route của API Gateway.

### Triển khai bảng DynamoDB mới

1. Trong terminal của bạn, chạy:

```bash
aws dynamodb create-table \
  --region us-west-2 \
  --table-name global-serverless \
  --attribute-definitions \
    AttributeName=PK,AttributeType=S \
    AttributeName=SK,AttributeType=S \
  --key-schema \
    AttributeName=PK,KeyType=HASH \
    AttributeName=SK,KeyType=RANGE \
  --billing-mode PAY_PER_REQUEST \
  --query '{"New Table ":TableDescription.TableArn, 
            "Status    ":TableDescription.TableStatus }'
```

2. Chờ một lát để bảng được tạo.

Kiểm tra xem trạng thái bảng thay đổi từ `CREATING` sang `ACTIVE` bằng cách chạy lệnh này:

```bash
aws dynamodb describe-table \
  --table-name global-serverless \
  --region us-west-2 \
  --query '{TableStatus: Table.TableStatus}'
```

3. Bảng của chúng ta ở vùng us-west-2 (Oregon). Hãy làm cho nó trở thành một **Global Table** bằng cách yêu cầu một bản sao ở eu-west-1 (Europe/Dublin).

Chạy lệnh này để tạo một bản sao mới ở vùng eu-west-1 (Europe/Dublin):

```bash
aws dynamodb update-table --table-name global-serverless --region us-west-2 --cli-input-json  \
'{"ReplicaUpdates": [
    {
        "Create": {"RegionName": "eu-west-1" }
        }
    ]}'
```

Kiểm tra xem trạng thái bản sao bảng đã chuyển sang `ACTIVE` bằng cách chạy lệnh này:

```bash
aws dynamodb describe-table \
  --table-name global-serverless \
  --region us-west-2 \
  --query '{TableStatus: Table.TableStatus,
               Replicas: Table.Replicas}'
```

4. Tiếp theo, thêm một số dữ liệu vào bảng: Việc ghi vào một Global Table được thực hiện bằng cách ghi vào bất kỳ bảng bản sao vùng nào.

Chạy lệnh này để tải các mục thư viện video vào bảng bằng batch-write-item:

```bash
aws dynamodb batch-write-item \
  --region us-west-2 \
  --request-items file://sample-data.json
```

Những mục này là cách giao diện người dùng sẽ hiển thị video nào có sẵn để phát trực tuyến.

5. Xác minh dữ liệu đã được ghi:

```bash
aws dynamodb get-item \
  --table-name global-serverless \
  --region us-west-2 \
  --key '{"PK": {"S": "library"}, "SK": {"S": "01"}}'
```

### Triển khai dịch vụ API backend tới vùng đầu tiên

1. Chạy `export AWS_DEFAULT_REGION=us-west-2` để hướng dẫn Chalice triển khai vào us-west-2 cho vùng đầu tiên của chúng ta.
2. Chạy `chalice deploy` và chờ cơ sở hạ tầng được tạo. Chalice là một framework serverless dựa trên Python.
3. Khi script hoàn thành, nó sẽ báo cáo danh sách các tài nguyên đã triển khai. **Sao chép và dán URL của Rest API vào một ghi chú vì bạn sẽ cần nó sau này.**
4. Sao chép URL REST API đó và dán vào một tab trình duyệt mới để kiểm tra. Bạn sẽ thấy một phản hồi JSON dạng {ping: "ok"}.
5. Bạn có thể nhập vào các đường dẫn nhất định vào cuối URL. Thêm từ scan để URL kết thúc với `/api/scan`. Bạn sẽ thấy một phản hồi JSON đại diện cho kết quả của một lần quét bảng.

### Ứng dụng web

Một ứng dụng web đơn trang tĩnh đã được cung cấp cho bạn.

- [https://amazon-dynamodb-labs.com/static/global-serverless-application/web/index.html](https://amazon-dynamodb-labs.com/static/global-serverless-application/web/index.html) 
- Ứng dụng này cho phép bạn nhập một hoặc nhiều điểm cuối API, và lưu trữ mỗi điểm dưới dạng cookie trình duyệt.
- Các điểm cuối API được lưu trữ sẽ vẫn ở trong trình duyệt ngay cả khi backend gặp sự cố.
- Bằng cách này, ứng dụng web có thể tự quyết định về việc chuyển hướng đến một API thay thế nếu có lỗi hoặc không có phản hồi từ API đang sử dụng.
- Ứng dụng web không chứa mã AWS hoặc thông tin xác thực nào, nó chỉ thực hiện các cuộc gọi HTTP GET đến API cho bạn.
- Nội dung web của ứng dụng có thể được lưu trữ từ một bucket S3, làm cho sẵn có toàn cầu qua Cloudfront, lưu trữ cục bộ trong Chrome, hoặc chuyển đổi thành một ứng dụng di động. Trong buổi workshop này, chúng ta giả định rằng người dùng luôn có quyền truy cập vào ứng dụng web ngay cả khi các dịch vụ backend trở nên không khả dụng.

Các bước:

1. Trong ứng dụng web, nhấn nút **Add API**.
2. Dán vào URL API mà bạn đã tạo trước đó và nhấp OK.
3. Xem lại các nút xuất hiện. Nhấp vào Ping để tạo một yêu cầu đến URL mức cơ bản. Độ trễ của vòng lặp sẽ được hiển thị. Điều này có thể chậm hơn mong đợi do Lambda cold start. Nhấp lại vào Ping và kiểm tra độ trễ.
4. Nhấp vào nút **get-item**. Điều này sẽ trả về bookmark cho user100 đang xem một chương trình tên là AshlandValley.
5. Nhấp vào các nút forward và back. Chúng sẽ tạo ra các yêu cầu để tăng hoặc giảm bookmark theo 1 giây.

Bây giờ bạn có một môi trường thử nghiệm nơi bạn có thể thực hiện đọc và ghi vào một bản ghi DynamoDB thông qua API tùy chỉnh.

### Triển khai stack dịch vụ đến vùng thứ hai, Ireland

1. Chạy `export AWS_DEFAULT_REGION=eu-west-1` để hướng dẫn Chalice triển khai vào eu-west-1 cho vùng thứ hai của chúng ta.
2. Chạy `chalice deploy` và chờ cơ sở hạ tầng được tạo trong eu-west-1.
3. Khi script hoàn thành, nó sẽ báo cáo danh sách các tài nguyên đã triển khai. Lần nữa, ghi lại URL REST API mới vào một ghi chú để sử dụng sau này.
4. Quay lại ứng dụng web.
5. Nhấp vào **Add API** một