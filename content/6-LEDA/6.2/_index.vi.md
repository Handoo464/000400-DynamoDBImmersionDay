---
title : "Tổng quan"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 6.2.1. </b> "
---

## Kiến trúc

Sau khi thiết lập, hầu hết các thành phần của pipeline tổng hợp dữ liệu đã được cài đặt sẵn cho bạn, như minh họa trong sơ đồ dưới đây. Tuy nhiên, một số liên kết giữa các thành phần liền kề bị thiếu hoặc cấu hình sai! Những kết nối này là trọng tâm của vấn đề mà bạn cần giải quyết.

Workshop này bao gồm hai bài thực hành. Mục tiêu của bài thực hành đầu tiên là thiết lập các kết nối giữa các thành phần này và đạt được xử lý dữ liệu đầu cuối, từ `IncomingDataStream` (Luồng dữ liệu đầu vào) ở bên trái đến `AggregateTable` (Bảng tổng hợp) ở cuối pipeline.

Tuy nhiên, pipeline mà bạn xây dựng trong bài thực hành đầu tiên không đảm bảo xử lý tin nhắn chính xác từng lần. Do đó, việc đảm bảo xử lý chính xác từng lần là mục tiêu của _Bài thực hành 2_.

![Architecture-1](/images/6/6.2/6.png)

## Bài thực hành 1

Sơ đồ dưới đây phác thảo một tập hợp các bước mà bạn cần thực hiện để kết nối tất cả các tài nguyên AWS. Phần _Bài thực hành 1_ sẽ cung cấp cho bạn thêm thông tin và giải thích cách thực hiện từng bước.

![Architecture-2](/images/6/6.2/7.png)

## Bài thực hành 2

Trong _Bài thực hành 2_, chúng ta sẽ nâng cao pipeline để đảm bảo xử lý chính xác từng lần cho bất kỳ tin nhắn nào được nạp vào. Để đảm bảo rằng pipeline của chúng ta có thể chịu đựng các chế độ lỗi khác nhau và đạt được xử lý tin nhắn chính xác từng lần, chúng ta sẽ sửa đổi hai hàm Lambda.

Phần _Bài thực hành 2_ sẽ cung cấp cho bạn thêm thông tin và giải thích cách thực hiện từng bước. ![Architecture-3](/images/6/6.2/1.png)

## Bước tiếp theo và cạnh tranh

Phần _Đi sâu vào Pipeline với ví dụ_ chứa một giải thích chi tiết về cách dữ liệu được xử lý trong pipeline này. Thông tin này được khuyến khích xem qua nhưng không bắt buộc để hoàn thành workshop.

Để làm cho workshop này thêm phần thú vị, khi được thực hiện tại một sự kiện AWS, tất cả các người tham gia sẽ được xếp hạng dựa trên số lượng tin nhắn họ có thể tổng hợp chính xác bằng bảng điểm. Phần _Luật chơi_ phác thảo các quy tắc của trò chơi và cách hàm `GeneratorLambda` nạp dữ liệu vào đầu pipeline!

Tiếp tục đến: [Luật chơi](https://catalog.workshops.aws/dynamodb-labs/en-US/event-driven-architecture/ex1overview/step1).