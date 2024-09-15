---
title : "Lab 2: Đảm bảo khả năng chịu lỗi và xử lý chính xác một lần"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 6.4. </b> "
---

{{%notice tip%}}
Điểm và bảng xếp hạng chỉ áp dụng khi phòng lab này được thực hiện trong một sự kiện AWS.
{{%/notice%}}

# Bạn đã sẵn sàng bắt đầu Lab 2 chưa?

Trước khi tiến hành _Lab 2_, hãy xác minh rằng _Lab 1_ đã được hoàn thành thành công. Có hai giai đoạn cần hoàn thành trước khi tiếp tục:

- Thứ nhất, bạn đã bắt đầu tích lũy điểm trên bảng xếp hạng. Nếu bạn có số điểm khác không, thì giai đoạn này đã hoàn thành. Mở bảng xếp hạng và tìm đội của bạn. Tìm ID tài khoản của bạn trong AWS Management Console ở góc trên bên phải của bảng điều khiển.
- Thứ hai, bạn cần tích lũy 300 điểm để tiếp tục. Workshop sẽ tự động chuyển sang _Lab 2_ khi bạn đạt được mốc này, và giai đoạn này hoàn thành.
- Khi tích lũy đủ 300 điểm, các chế độ lỗi mới sẽ được giới thiệu và cả ba hàm Lambda (`StateLambda`, `MapLambda`, và `ReduceLambda`) sẽ bắt đầu thất bại ngẫu nhiên. Đây là sự phát triển được lập trình sẵn của workshop. Trong Lambda console, nhấp vào bất kỳ hàm Lambda nào trong ba hàm, điều hướng đến tab `Monitor` và sau đó đến sub-tab `Metrics`. Bạn nên thấy tỷ lệ lỗi khác không trên một số đồ thị!

{{%notice tip%}}
Nếu bảng điều khiển đã có 300 điểm, thì xin chúc mừng: bạn có thể bắt đầu Lab 2!
{{%/notice%}}

![Kiến trúc-1](/images/6/6.4/1.png)

# Hãy sử dụng các tính năng khác nhau của DynamoDB để đảm bảo tính toàn vẹn dữ liệu và khả năng chịu lỗi

Trong _Lab 2_, chúng ta sẽ đạt được quá trình xử lý thông điệp đúng một lần duy nhất. Để đảm bảo rằng pipeline của chúng ta có thể chịu được các chế độ lỗi khác nhau và đạt được quá trình xử lý thông điệp đúng một lần.

![Kiến trúc-3](/images/6/6.4/2.png)
