---
title : "Tùy chọn - Pipeline Deep Dive"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> </b> "
---

{{%notice tip%}}
Hiểu cách pipeline hoạt động bên trong được khuyến nghị, tuy nhiên việc đọc trang này không bắt buộc để hoàn thành workshop.
{{%/notice%}}

Phần này giải thích chi tiết cách pipeline hoạt động từ đầu đến cuối. Để có một giải thích đơn giản hơn, tham khảo hình minh họa dưới đây, trong đó chúng tôi chia pipeline thành ba giai đoạn. Đối với mỗi giai đoạn, chúng tôi mô tả đầu vào và đầu ra của giai đoạn.

![DD Stages](/images/6/6.2/2.png)

## Giai đoạn 1: 'State' (Trạng thái)

Vấn đề kinh doanh của việc tổng hợp dữ liệu gần như thời gian thực đang được khách hàng đối mặt trong nhiều ngành công nghiệp khác nhau như sản xuất, bán lẻ, trò chơi, tiện ích, và dịch vụ tài chính. Trong workshop này, chúng tôi tập trung vào ngành ngân hàng, và cụ thể là vấn đề tổng hợp rủi ro giao dịch gần như thời gian thực. Thông thường, các tổ chức tài chính liên kết mỗi giao dịch được thực hiện trên sàn giao dịch với một giá trị rủi ro, và bộ phận quản lý rủi ro của ngân hàng cần có cái nhìn nhất quán về tổng giá trị rủi ro được tổng hợp từ tất cả các giao dịch. Trong workshop này, chúng tôi sử dụng cấu trúc sau cho các thông điệp rủi ro:

```json
{
"RiskMessage": {
   "TradeID"   : "0d957268-2913-4dbb-b359-5ec5ff732cac",
   "Value"     : 34624.51,
   "Version"   : 3,
   "Timestamp" : 1616413258.8997078,
   "Hierarchy" : {"RiskType": "Delta", "Region": "AMER", "TradeDesk": "FXSpot"}
 }
}
```

- `TradeID`: Mã định danh duy nhất cho mỗi thông điệp giao dịch.
- `Value`: Giá trị rủi ro (tiền tệ) liên quan đến giao dịch này.
- `Version`: Giá trị rủi ro của một giao dịch đôi khi cần phải được sửa đổi ở giai đoạn sau. Thuộc tính này theo dõi phiên bản mới nhất được biết của một giao dịch cụ thể.
- `Timestamp`: Thời gian khi giao dịch xảy ra.
- `Hierarchy`: Danh sách các thuộc tính liên quan đến giao dịch này. Để mở rộng, điều này bao gồm loại rủi ro, khu vực nơi giao dịch diễn ra, và loại bàn giao dịch thực hiện giao dịch. Những thuộc tính này sẽ được sử dụng để nhóm các giao dịch và tổng hợp dữ liệu.

Hãy xem xét một ví dụ khi năm thông điệp rủi ro được nạp vào pipeline, như minh họa trong hình dưới đây. Để dễ hiểu, chúng tôi đã gắn nhãn mỗi thông điệp với một mã định danh từ M1 đến M5. Mỗi thông điệp có một `TradeID` duy nhất, một giá trị rủi ro `Value`, và một nhóm thuộc tính phân cấp (như đã giải thích ở trên). Để đơn giản hóa, tất cả các thông điệp có `RiskType` là `"Delta"` và thuộc tính `Version` luôn được đặt là `1`.

![DD Part1](/images/6/6.2/3.png)

Pipeline được điều khiển bởi một nguồn dữ liệu đầu vào ghi các bản ghi vào Kinesis Data Stream, và hàm `StateLambda` được kích hoạt để xử lý các thông điệp này.

Do tỷ lệ xuất hiện của thông điệp có thể rất cao, nhiều hàm `StateLambda` sẽ được gọi đồng thời. Điều này có nghĩa là nhiều phiên bản của hàm `StateLambda` sẽ chạy cùng lúc, mỗi phiên bản xử lý một tập hợp con của các bản ghi. Để tìm hiểu thêm về việc mở rộng hàm Lambda, hãy xem trang [tài liệu về mở rộng hàm AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html), giải thích khái niệm về một phiên bản Lambda. Ví dụ trên cho thấy việc gọi hai phiên bản hàm `StateLambda` được gắn nhãn là #1 và #2. Phiên bản #1 xử lý thông điệp M1 và M2, trong khi phiên bản #2 xử lý thông điệp M3, M4, và M5.

Trách nhiệm của hàm `StateLambda` là bảo tồn tất cả các thông điệp đầu vào bằng cách ghi chúng vào DynamoDB `StateTable`, và hơn nữa để đảm bảo xử lý chính xác từng lần bằng cách theo dõi mã ID duy nhất của mỗi thông điệp đã được xử lý ở giai đoạn này. Trong ví dụ này, cả hai hàm `StateLambda` đều ghi các thông điệp tương ứng của chúng vào DynamoDB `StateTable`. `StateTable` ở phía dưới của hình lưu trữ các thông điệp rủi ro đầu vào mà không có sửa đổi đáng kể.

## Giai đoạn 2: 'Reduce' (Giảm)

Trong Giai đoạn 2, tất cả các hàng được ghi vào `StateTable` được gửi đến `MapLambda` để xử lý thêm thông qua một ánh xạ nguồn sự kiện kết nối hàm với luồng DynamoDB của `StateTable`. DynamoDB Streams đảm bảo rằng mỗi thay đổi mục xuất hiện chính xác một lần, và tất cả các thay đổi đối với một mục cho trước xuất hiện trong các shard luồng theo thứ tự chúng được ghi. Tuy nhiên, nhiều hàm `StateLambda` ghi vào các khóa khác nhau trong `StateTable`, và do đó các bản ghi luồng trong luồng bảng có thể không theo thứ tự như thứ tự mà chúng được nạp vào ở Giai đoạn 1.

Để xử lý lượng lớn thông điệp đầu vào, nhiều phiên bản `MapLambda` được gọi để xử lý các thông điệp từ luồng DynamoDB `StateTable`, tương tự như Giai đoạn 1. Trong ví dụ này, `MapLambda` #1 lấy các thông điệp M4, M3, và M1 trong khi `MapLambda` #2 xử lý các thông điệp M5 và M2.

Trách nhiệm của `MapLambda` là thực hiện sự tổng hợp ban đầu của các thông điệp, hay cụ thể hơn là thực hiện phép cộng số học dựa trên các thuộc tính thông điệp. Kết quả đầu ra đã được tổng hợp của mỗi hàm `MapLambda` được ghi vào `ReduceTable` dưới dạng một hàng duy nhất, như đã thấy trong "Trạng thái đầu ra của `ReduceTable`" trong hình trên. Để đơn giản hóa, chúng tôi gọi các hàng này là AM1 và AM2.

![DD Part2](/images/6/6.2/4.png)

### Tổng hợp ban đầu hoạt động như thế nào?

Hãy xem xét hàng thứ hai, AM2, kết hợp các thông điệp M2 và M5. Dựa trên các thuộc tính thông điệp, thông điệp M2 thuộc về `Delta::EMEA::FXSpot`, trong khi thông điệp M5 thuộc về `Delta::AMER::FXSpot`. Điểm chung cho các giao dịch này là cả hai đều thuộc loại rủi ro `Delta`. Do đó, thuộc tính `Delta` trong `ReduceTable` là tổng của cả hai thông điệp, được biểu thị là `100 + 20 = 120`. Dưới đây là các thông điệp M2 và M5 ở định dạng JSON để tham khảo.

```json

{"TradeID"   : "8ec2fdcd", "Value": 100, "Version": 1,
    "Hierarchy" : {"RiskType": "Delta", "Region": "EMEA", "TradeDesk": "FXSpot"} }

{"TradeID"   : "395974a4", "Value": 20, "Version": 1,
    "Hierarchy" : {"RiskType": "Delta", "Region": "AMER", "TradeDesk": "FXSpot"} }
```

## Giai đoạn 3: 'Aggregate' (Tổng hợp)

Trong Giai đoạn 3, tất cả các hàng được ghi vào `ReduceTable` được gửi đến `ReduceLambda` để xử lý thêm thông qua DynamoDB Streams. Trách nhiệm của `ReduceLambda` là kết hợp thêm các thông điệp đã tổng hợp ban đầu và cập nhật giá trị cuối cùng trong `AggregateTable`.

Điều này được thực hiện trong ba bước:

1. Hàm `ReduceLambda` được gọi với một lô thông điệp và đọc trạng thái hiện tại từ `AggregateTable`
2. Hàm tính toán lại tổng hợp dựa trên lô mục đã tổng hợp trước đó
3. Hàm giảm viết các giá trị đã cập nhật vào `AggregateTable`

Lưu ý: chỉ có một phiên bản của hàm `ReduceLambda`, được đạt được bằng cách đặt đồng thời dự trữ thành 1. Điều này là mong muốn để tránh xung đột ghi tiềm ẩn khi cập nhật `AggregateTable`. Từ góc độ hiệu suất, một phiên bản hàm Lambda duy nhất có thể xử lý việc tổng hợp toàn bộ pipeline vì các thông điệp đầu vào đã được tổng hợp trước bởi các hàm `MapLambda`.

Kết quả cuối cùng trong `AggregateTable` giống như các yếu tố thuộc tính `Hierarchy`, và có thể dễ dàng đọc và hiển thị bởi một giao diện người dùng!

![DD Part3](/images/6/6.2/4.png)
