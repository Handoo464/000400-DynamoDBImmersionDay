---
title : "Bước 2: Kiểm tra MapLambda trigger"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 6.3.2. </b> "
---

{{%notice tip%}}
Hàm `MapLambda` đã được kết nối sẵn cho bạn, vì vậy hãy nhanh chóng kiểm tra xem nó hoạt động như mong đợi không!
{{%/notice%}}

![Architecture-1](/images/6/6.3/7.png)

Kiểm tra xem `MapLambda` đã được cấu hình trình kích hoạt chính xác để nhận thông điệp từ luồng `StateTable`:

1. Điều hướng đến dịch vụ AWS Lambda trong AWS Management Console.
2. Nhấp vào hàm `MapLambda` để xem cấu hình của nó.
3. Xác minh rằng hàm `MapLambda` có một trình kích hoạt DynamoDB và trình kích hoạt này trỏ đến `StateTable` (xem hình bên dưới).

![Architecture-1](/images/6/6.3/8.png)

## Làm thế nào để biết nó đang hoạt động?

Bất kỳ hàng nào được ghi vào `StateTable` sẽ kích hoạt hàm `MapLambda`. Do đó, bạn sẽ có thể thấy các bản ghi cho các lần gọi Lambda.

Ngoài ra, bạn có thể quan sát đầu ra của hàm `MapLambda` trong bảng DynamoDB `ReduceTable`. Để làm điều đó, điều hướng đến dịch vụ DynamoDB trong bảng điều khiển AWS, nhấp vào `Items` ở bên trái, và chọn `ReduceTable`. Tại giai đoạn này, bạn sẽ thấy nhiều hàng tương tự như hình dưới đây.

![Reduce table items](/images/6/6.3/9.png)

Tiếp tục đến: [Bước 3](https://catalog.workshops.aws/dynamodb-labs/en-US/event-driven-architecture/ex2pipeline/step3).