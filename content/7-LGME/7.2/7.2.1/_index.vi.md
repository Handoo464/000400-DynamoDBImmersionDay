---
title : "Best Practices"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 7.2.1 </b> "
---

### Sử dụng các phương pháp tốt nhất sau đây khi mô hình hóa dữ liệu với DynamoDB

#### Tập trung vào các mẫu truy cập

Khi thực hiện bất kỳ loại mô hình hóa dữ liệu nào, bạn sẽ bắt đầu với một sơ đồ thực thể-mối quan hệ mô tả các đối tượng khác nhau (hoặc thực thể) trong ứng dụng của bạn và cách chúng được kết nối (hoặc các mối quan hệ giữa các thực thể của bạn).

Trong các cơ sở dữ liệu quan hệ, bạn sẽ đặt các thực thể của mình trực tiếp vào các bảng và chỉ định các mối quan hệ bằng cách sử dụng [khóa ngoại](https://vi.wikipedia.org/wiki/Kh%C3%B3a_ngo%E1%BA%A1i). Sau khi bạn đã xác định các bảng dữ liệu của mình, cơ sở dữ liệu quan hệ cung cấp một ngôn ngữ truy vấn linh hoạt để trả về dữ liệu dưới dạng bạn cần.

Trong DynamoDB, bạn cần xem xét các mẫu truy cập trước khi mô hình hóa bảng của mình. Các cơ sở dữ liệu NoSQL tập trung vào tốc độ, không phải tính linh hoạt. Bạn cần hỏi trước cách bạn sẽ truy cập dữ liệu của mình, sau đó mô hình hóa dữ liệu theo cách nó sẽ được truy cập.

Trước khi thiết kế bảng DynamoDB của bạn, hãy ghi lại mọi nhu cầu mà bạn cần để đọc và ghi dữ liệu trong ứng dụng của mình. Hãy kỹ lưỡng và suy nghĩ về tất cả các luồng trong ứng dụng của bạn bởi vì bạn sẽ tối ưu hóa bảng của mình cho các mẫu truy cập của bạn.

#### Tối ưu hóa số lượng yêu cầu đến DynamoDB

Sau khi bạn đã ghi lại các nhu cầu về mẫu truy cập của ứng dụng, bạn đã sẵn sàng để thiết kế bảng của mình. Bạn nên thiết kế bảng để giảm thiểu số lượng yêu cầu đến DynamoDB cho mỗi mẫu truy cập. Lý tưởng nhất là mỗi mẫu truy cập chỉ cần một yêu cầu duy nhất đến DynamoDB, do đó bạn muốn giảm số lần round-trip từ ứng dụng đến bảng.

##### Để tối ưu hóa số lượng yêu cầu đến DynamoDB, bạn cần hiểu một số khái niệm cốt lõi:

- [Khóa chính (Primary Keys)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html#HowItWorks.CoreComponents.PrimaryKey)
    
- [Chỉ mục phụ (Secondary Indexes)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html)
    
- [Giao dịch (Transactions)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html)
    
#### Đừng giả lập mô hình quan hệ

Những người mới sử dụng DynamoDB thường cố gắng triển khai mô hình quan hệ trên nền tảng DynamoDB không quan hệ. Nếu bạn cố gắng làm điều này, bạn sẽ mất hầu hết các lợi ích của DynamoDB.

##### Các anti-pattern (phản mẫu) phổ biến nhất mà mọi người thử với DynamoDB là:

**Chuẩn hóa (Normalization)**

Trong cơ sở dữ liệu quan hệ, bạn chuẩn hóa dữ liệu của mình để giảm dư thừa dữ liệu và không gian lưu trữ, sau đó sử dụng các phép nối (joins) để kết hợp nhiều bảng khác nhau. Tuy nhiên, các phép nối ở quy mô lớn rất chậm và tốn kém. DynamoDB không cho phép các phép nối vì chúng sẽ chậm lại khi bảng của bạn phát triển.

**Một loại thực thể cho mỗi bảng**

Bảng DynamoDB của bạn thường bao gồm các loại dữ liệu khác nhau trong một bảng duy nhất. Trong ví dụ này, bạn có các thực thể `User`, `Game`, và `UserGameMapping` trong một bảng duy nhất. Trong cơ sở dữ liệu quan hệ, điều này sẽ được mô hình hóa như ba bảng khác nhau.

**Quá nhiều chỉ mục phụ**

Mọi người thường cố gắng tạo một chỉ mục phụ cho mỗi mẫu truy cập bổ sung mà họ cần. DynamoDB không có schema, và điều này cũng áp dụng cho các chỉ mục của bạn. Hãy sử dụng tính linh hoạt trong các thuộc tính của bạn để tái sử dụng một chỉ mục phụ duy nhất cho nhiều loại dữ liệu trong bảng của bạn. Điều này được gọi là [index overloading (quá tải chỉ mục)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-gsi-overloading.html).

Trong bước tiếp theo, chúng ta sẽ xây dựng sơ đồ thực thể-mối quan hệ và vạch ra các mẫu truy cập ngay từ đầu. Đây luôn nên là các bước đầu tiên khi sử dụng DynamoDB. Sau đó, trong các mô-đun tiếp theo, bạn sẽ triển khai các mẫu truy cập này trong thiết kế bảng.