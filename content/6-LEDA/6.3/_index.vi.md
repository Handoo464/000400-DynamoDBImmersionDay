---
title : "Lab 1: Kết nối pipeline"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1.1. </b> "
---

### Hãy tìm hiểu sâu về các nguồn dữ liệu phát trực tuyến với AWS Lambda!

Trong bài thực hành đầu tiên này, bạn sẽ xử lý dữ liệu phát trực tuyến để tạo một pipeline xử lý dữ liệu từ đầu đến cuối. Bạn sẽ sử dụng Amazon Kinesis, AWS Lambda, Amazon DynamoDB và một trong những tính năng mạnh mẽ của nó là DynamoDB Streams để thực hiện điều này. Chúng ta sẽ dành thời gian để giải thích một số tính năng chính của các dịch vụ này.

### Amazon DynamoDB

Amazon DynamoDB là một cơ sở dữ liệu NoSQL có khả năng mở rộng theo chiều ngang lớn, được thiết kế để có độ trễ chỉ vài mili giây với hầu như không có giới hạn về quy mô, cả về thông lượng lẫn lưu trữ. DynamoDB cung cấp bảo mật tích hợp, sao lưu liên tục, sao chép tự động đa vùng, bộ nhớ đệm trong bộ nhớ, và các công cụ xuất dữ liệu.

Sơ đồ này cho thấy các tính năng và tích hợp của DynamoDB:

![DynamoDB ecosystem](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/event-driven-architecture/lab1/dynamodb-ecosystem.png) Nếu bạn muốn tìm hiểu thêm [bạn có thể xem lại trang tính năng của DynamoDB trên aws.amazon.com](https://aws.amazon.com/dynamodb/features/)

### DynamoDB Streams

DynamoDB Streams cung cấp một tập hợp các thay đổi có thứ tự từ bảng DynamoDB của bạn trong vòng 24 giờ sau khi các mục được ghi. Đây là một dịch vụ thu thập dữ liệu thay đổi (CDC), và khi bạn bật luồng trên bảng, các thay đổi sẽ bắt đầu chảy vào luồng. Các thay đổi mục của DynamoDB xuất hiện chính xác một lần trong luồng, và thay đổi này thường bao gồm bản sao cũ và mới của mục, nhưng bạn có thể chọn có một trong hai, hoặc chỉ có khóa mục. Dịch vụ này bền bỉ và có khả năng sẵn sàng cao. Mặc dù tên gọi tương tự như Kinesis Data Streams, DynamoDB Streams có một lộ trình hoàn toàn khác, đội ngũ kỹ sư khác, và hạm đội máy chủ khác.

Một `stream` DynamoDB được tạo thành từ nhiều shard. Một `shard` chứa danh sách các thay đổi theo thứ tự thời gian từ một `DynamoDB partition`. Các thay đổi mục được đưa vào shard dưới dạng `stream records`. Hãy cùng giải thích các thuật ngữ này:

- `Stream`: Một luồng DynamoDB Streams đơn, chứa dữ liệu CDC từ một bảng DynamoDB duy nhất. Luồng có thể được bật hoặc tắt, và tại thời điểm bật, bạn có thể chọn liệu bản sao cũ và mới của mục có được giữ lại, chỉ một trong hai hoặc chỉ có khóa. Mỗi luồng có một ID duy nhất gọi là _stream label_.
- `DynamoDB partition`: Một phân vùng là một phần lưu trữ cho một bảng, được hỗ trợ bởi ổ đĩa thể rắn (SSD) và tự động sao chép qua nhiều Vùng Sẵn Sàng (AZ) trong một Vùng AWS. Mỗi mục DynamoDB sẽ nằm trên chính xác một phân vùng, nhưng phân vùng được sao chép ba chiều để đảm bảo tính bền bỉ và khả năng sẵn sàng. Các thay đổi từ một phân vùng DynamoDB được sao chép vào một shard luồng trong chưa đến một giây.
- `Shard`: Một shard luồng giữ các bản ghi luồng. Một luồng bao gồm nhiều shard luồng. Trong DynamoDB, một phân vùng sẽ ghi vào chính xác một shard luồng, và hai cái này không nên bị nhầm lẫn với nhau. Một shard luồng có thể mở (có thể thêm các bản ghi luồng mới) hoặc đóng (không thể thêm bản ghi luồng mới). Một shard luồng đóng được định nghĩa là một shard có `EndingSequenceNumber`, trong khi một shard luồng mở không có `EndingSequenceNumber`. Để tìm hiểu thêm về các shard và nguồn gốc shard, hãy xem tài liệu của chúng tôi về [Đọc và Xử lý một Luồng](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html#Streams.Processing).
- `Stream record`: Một thay đổi đơn lẻ trên bảng DynamoDB. Bất kỳ thao tác tạo, cập nhật, hoặc xóa nào trên bảng đều có thể tạo ra một bản ghi luồng miễn là mục thực sự bị thay đổi. Nếu bạn xóa một mục không tồn tại hoặc "cập nhật" một mục nhưng không có thay đổi nào đối với thuộc tính của nó, thì không tạo ra bản ghi luồng. Một bản ghi có dấu thời gian ApproximateCreationDateTime chính xác đến mili giây gần nhất để sắp xếp trên tất cả các shard cùng với số thứ tự chỉ có thể được sử dụng để sắp xếp bên trong shard. Các bản ghi luồng và các loại nội dung (NEW_IMAGE | OLD_IMAGE | NEW_AND_OLD_IMAGES | KEYS_ONLY) [được giải thích trong tài liệu StreamRecord của chúng tôi](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_StreamRecord.html).

#### Dòng dõi shard của DynamoDB Streams

Khi đã trôi qua 4 giờ, khi một shard đạt đến kích thước xác định trước tính bằng byte, hoặc khi một phân vùng DynamoDB chia tách, một shard sẽ đóng lại và shard mới được tạo ra. Ngoại trừ các shard đầu tiên được tạo ra khi bật luồng, tất cả các shard đều có một shard cha. Để một khách hàng như Lambda có thể truy xuất các bản ghi luồng, họ phải xác định dòng dõi shard bằng cách truy xuất tất cả thông tin về các shard bằng cách sử dụng ID shard cha để tìm xem các shard nào là cũ nhất và shard nào là mới nhất.

Hãy xem xét một bảng DynamoDB với 4 phân vùng. Các shard được thay đổi sau mỗi 4 giờ, cho mỗi phân vùng. Như bạn có thể biết, có một ánh xạ 1:1 giữa một shard mở và một phân vùng DynamoDB. Khi luồng đã được bật trong 24 giờ (thời gian lưu giữ dài nhất cho dữ liệu luồng), sẽ có [(4 phân vùng) * (24 giờ / 4 giờ mỗi shard)] = 24 shard luồng. Tại bất kỳ thời điểm nào, chỉ có bốn shard mở và phần còn lại sẽ đóng. Khi bạn kết nối một hàm Lambda với luồng này, sẽ có bốn phiên bản Lambda.

Hãy dành thời gian để giải thích kết nối này và định nghĩa một phiên bản Lambda là gì.

### Triggers của AWS Lambda

AWS Lambda là một dịch vụ tính toán không máy chủ, dựa trên sự kiện cho phép bạn chạy mã cho hầu như bất kỳ loại ứng dụng hoặc dịch vụ backend nào mà không cần cung cấp hoặc quản lý máy chủ. Lambda hỗ trợ nhiều trình kích hoạt bao gồm Kinesis Data Streams và DynamoDB. Trong bài thực hành này, bạn sẽ kết nối một số hàm với các nguồn phát trực tuyến, thiết lập kích thước batch và mức đồng thời. Vì lý do này, chúng tôi sẽ dành thời gian để giải thích cách chúng hoạt động.

Dịch vụ Lambda hỗ trợ trình kích hoạt `Lambda function` thông qua cái gọi là `event source mapping`. Khi một hàm Lambda được kết nối với một nguồn phát trực tuyến, bạn chọn bắt đầu với các bản ghi cũ nhất trong luồng hay mới nhất bằng cách đặt `StartingPosition` trong event source mapping. Sau đó, dịch vụ Lambda bắt đầu truy vấn luồng. Dịch vụ truy vấn Lambda sau đó chuyển các batch bản ghi (kích thước được xác định bởi `BatchSize` trong event source mapping) cho một `Lambda instance`. Bạn có thể giới hạn số lượng phiên bản Lambda đồng thời bằng cách sử dụng `reserved concurrency`.

- `Lambda function`: Từ tài liệu: _Một hàm là một tài nguyên mà bạn có thể gọi để chạy mã của mình trong Lambda. Một hàm có mã để xử lý các sự kiện mà bạn truyền vào hàm hoặc các dịch vụ AWS khác gửi đến hàm._ Trong bài thực hành này, chúng tôi có ba hàm Lambda dưới sự kiểm soát của bạn.
- `Reserved concurrency`: Đồng thời được dự trữ đảm bảo số lượng phiên bản đồng thời tối đa cho hàm. Bạn có thể dự trữ đồng thời để ngăn hàm của bạn sử dụng tất cả các đồng thời có sẵn trong tài khoản hoặc làm quá tải các tài nguyên hạ lưu. Trong bài thực hành này, chúng tôi thay đổi mức đồng thời của một hàm thành 1.
- `Lambda instance`: Một phiên bản duy nhất (một container đang chạy) của một hàm Lambda. Thông qua đồng thời được dự trữ với các nguồn phát trực tuyến, số lượng phiên bản Lambda đang chạy đồng thời có thể được kiểm soát, tuy nhiên bạn không có quyền kiểm soát số lượng tổng thể các phiên bản. Với các nguồn phát trực tuyến như DynamoDB, Lambda mặc định tạo một phiên bản cho mỗi shard luồng quan trọng.
- `Event source mapping`: Bản đồ nguồn sự kiện là kết nối logic giữa một nguồn dữ liệu và một hàm Lambda. Khi bạn thêm một trigger vào hàm Lambda trong bài thực hành, bảng điều khiển gọi [_CreateEventSourceMapping_](https://docs.aws.amazon.com/lambda/latest/dg/API_CreateEventSourceMapping.html) để liên kết nguồn phát trực tuyến với hà

m Lambda. Tài liệu API cung cấp nhiều tùy chọn, nhưng chúng tôi sẽ chỉ ra ba tùy chọn thú vị:
  - `BatchSize`: Số lượng bản ghi luồng tối đa mà Lambda cung cấp cho phiên bản hàm Lambda khi nó được gọi. Hàm Lambda sẽ cần xử lý các bản ghi đó và trả về chúng một cách kịp thời. Kích thước batch là 1 sẽ rất không hiệu quả do chi phí quản lý việc gọi hàm. Giá trị tối đa là 1.000 cho DynamoDB Streams.
  - `StartingPosition`: Vị trí trong một luồng để bắt đầu đọc. Với _TRIM_HORIZON_, Lambda sẽ bắt đầu đọc từ dữ liệu cũ nhất trong luồng. Với _LATEST_, Lambda sẽ bắt đầu đọc từ các shard mới nhất (các shard luồng mở, như đã định nghĩa ở trên). Như đã đề cập trước đó, có một phiên bản Lambda cho mỗi shard quan trọng. Theo thời gian, có thể có nhiều shard được thêm vào một luồng, trong trường hợp đó, số lượng phiên bản đang chạy sẽ tăng lên để phù hợp với số lượng shard quan trọng. Lambda sẽ khám phá dòng dõi shard để nó có thể quyết định shard nào là quan trọng để bắt đầu (shard cũ nhất, hoặc shard mới nhất tùy thuộc vào cài đặt tham số này). Trong bài thực hành này, chúng ta sẽ sử dụng cài đặt _LATEST_.
  - _ParallelizationFactor_: Số lượng phiên bản Lambda để tạo ra trên mỗi shard luồng. Mặc định là 1, nhưng bạn có thể tăng lên 10 phiên bản trên mỗi shard. Với giá trị lớn hơn 1, dịch vụ Lambda sử dụng một sơ đồ băm nhất quán để đảm bảo các khóa mục DynamoDB giống nhau được truyền đến cùng một phiên bản, duy trì thứ tự của shard luồng. ParallelizationFactor thực chất là "chia shard" một shard luồng để tăng tốc độ xử lý khi mã hàm Lambda của bạn không hiệu quả hoặc nhiệm vụ của mã phức tạp hoặc chậm để hoàn thành. Chúng tôi không đặt giá trị này trong bài thực hành.


