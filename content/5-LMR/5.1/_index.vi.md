---
title : "Getting Started"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---
{{%notice warning%}}
Phần đầu tiên của hướng dẫn thiết lập này giống hệt nhau cho LADV, LHOL, LMR, LBED, và LGME - tất cả đều sử dụng cùng một mẫu Cloud9. Chỉ hoàn thành phần này một lần và chỉ khi bạn đang chạy nó trên tài khoản của riêng mình. Nếu bạn đã khởi chạy Cloud9 stack trong một phòng thí nghiệm khác, hãy bỏ qua và chuyển đến phần **Launch the zETL CloudFormation stack**.
{{%/notice%}}

{{%notice tip%}}
Chỉ hoàn thành phần này nếu bạn tự chạy workshop. Nếu bạn đang tham gia một sự kiện do AWS tổ chức (như re:Invent, Immersion Day, v.v.), hãy chuyển đến [At an AWS hosted Event](https://catalog.workshops.aws/dynamodb-labs/en-US/dynamodb-opensearch-zetl/setup/aws-ws-event).
{{%/notice%}}

## Khởi chạy Cloud9 CloudFormation stack

{{%notice tip%}}
Trong suốt quá trình của phòng thí nghiệm, bạn sẽ tạo các tài nguyên có thể phát sinh chi phí lên đến hàng chục đô la mỗi ngày. Hãy đảm bảo bạn xóa CloudFormation stack ngay khi hoàn thành phòng thí nghiệm và xác minh rằng tất cả các tài nguyên đã được xóa.
{{%/notice%}}

1. Khởi chạy mẫu CloudFormation tại US West 2 để triển khai các tài nguyên vào tài khoản của bạn: [![CloudFormation](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=DynamoDBID&templateURL=https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml)  
    _Tùy chọn, tải xuống [mẫu YAML](https://s3.amazonaws.com/amazon-dynamodb-labs.com/assets/C9.yaml) và khởi chạy theo cách riêng của bạn._
    
2. Nhấp vào _Next_ trên hộp thoại đầu tiên.
    
3. Trong phần Parameters, lưu ý rằng _Timeout_ được đặt là 0. Điều này có nghĩa là phiên bản Cloud9 sẽ không ngủ; bạn có thể muốn thay đổi thủ công giá trị này thành một số như 60 để tránh các chi phí không mong đợi nếu bạn quên xóa stack khi kết thúc.  
    Để nguyên tham số _WorkshopZIP_ không thay đổi và nhấp vào _Next_.
    

![CloudFormation parameters](/images/2/2.1/2.png)

4. Cuộn xuống dưới cùng và nhấp vào _Next_, sau đó xem lại _Template_ và _Parameters_. Khi bạn đã sẵn sàng tạo stack, cuộn xuống dưới cùng, chọn hộp xác nhận việc tạo tài nguyên IAM và nhấp vào _Submit_.

![Acknowledge IAM role capabilities](/images/2/2.1/3.png) Stack sẽ tạo một phiên bản Cloud9 lab, một vai trò cho phiên bản này, và một vai trò cho hàm AWS Lambda sẽ được sử dụng sau này trong phòng thí nghiệm. Nó sẽ sử dụng Systems Manager để cấu hình phiên bản Cloud9.

5. Sau khi CloudFormation stack là `CREATE_COMPLETE`, tiếp tục với stack tiếp theo.

## Tải xuống và xem lại mã

Trong phòng thí nghiệm này, bạn sử dụng các script Python để tương tác với DynamoDB API. Chạy các lệnh sau trong terminal AWS Cloud9 của bạn để tải xuống và giải nén mã của phòng thí nghiệm này.

```bash
cd ~/environment
curl -sL https://amazon-dynamodb-labs.com/assets/battle-royale.zip -o battle-royal.zip && unzip -oq battle-royal.zip && rm battle-royal.zip
```

Bạn sẽ thấy hai thư mục trong trình duyệt tệp AWS Cloud9:

- **application**: Thư mục _application_ chứa mã ví dụ cho việc đọc và ghi dữ liệu trong bảng của bạn. Mã này tương tự như mã bạn sẽ có trong ứng dụng trò chơi thực tế của mình.
    
- **scripts**: Thư mục _scripts_ chứa các script ở cấp độ quản trị viên, như tạo bảng, thêm chỉ mục phụ, hoặc xóa bảng.
    

Bây giờ bạn đã sẵn sàng bắt đầu phòng thí nghiệm. Với DynamoDB, điều quan trọng là phải lập kế hoạch mô hình dữ liệu của bạn trước để bạn có hiệu suất nhanh, nhất quán trong ứng dụng của mình. Trong module tiếp theo, bạn sẽ học về cách lập kế hoạch mô hình dữ liệu của mình.