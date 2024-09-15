---
title : "Thảo luận chủ đề Global Tables "
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b>  </b> "
---

Dưới đây là một số chủ đề thảo luận dành cho những người đã hoàn thành công việc phát triển hoặc muốn thảo luận về các khía cạnh thú vị của Global Tables với chuyên gia DynamoDB.

## Global Tables là gì?

- Global Tables (Bảng toàn cầu) xây dựng trên cơ sở hạ tầng toàn cầu của Amazon DynamoDB để cung cấp cho bạn một cơ sở dữ liệu đa vùng (multi-Region) và đa hoạt động (multi-active) được quản lý hoàn toàn, mang lại hiệu suất đọc và ghi nhanh chóng, cục bộ cho các ứng dụng quy mô lớn toàn cầu. Global Tables tự động sao chép các bảng DynamoDB của bạn trên các Vùng AWS mà bạn chọn.
- Global Tables loại bỏ công việc phức tạp của việc sao chép dữ liệu giữa các vùng và giải quyết các xung đột cập nhật, cho phép bạn tập trung vào logic kinh doanh của ứng dụng. Ngoài ra, Global Tables còn giúp ứng dụng của bạn luôn khả dụng cao ngay cả trong trường hợp hiếm có khi toàn bộ một vùng bị cô lập hoặc suy giảm.
- Bạn có thể thiết lập Global Tables trong Bảng điều khiển quản lý AWS hoặc AWS CLI. Không cần thay đổi ứng dụng vì Global Tables sử dụng các API DynamoDB hiện có. Không có chi phí ban đầu hay cam kết sử dụng Global Tables, và bạn chỉ trả tiền cho các tài nguyên đã cấp phát.

## Chi phí sử dụng Global Tables như thế nào?

- Một lần ghi vào bảng DynamoDB truyền thống được tính bằng Write Units (Đơn vị ghi), trong đó nếu bạn ghi một mục 5 KB, nó sẽ phải chịu phí 5 Write Units. Ghi vào Global Table được tính bằng Replicated Write Capacity Units (rWCUs, đối với bảng cấp phát sẵn) hoặc Replicated Write Request Units (rWRUs, đối với bảng theo yêu cầu).
- Các đơn vị ghi sao chép bao gồm chi phí của cơ sở hạ tầng truyền phát cần thiết để quản lý sao chép. Đối với bảng theo yêu cầu ở us-east-1, giá là $1.875 cho mỗi triệu đơn vị ghi sao chép thay vì $1.25 mỗi triệu. Đối với bảng cấp phát sẵn, giá là $0.000975 mỗi giờ rWCU thay vì $0.00065 mỗi giờ WCU. Các phí chuyển dữ liệu giữa các vùng cũng áp dụng.
- Phí đơn vị ghi sao chép được tính ở mỗi vùng mà mục được ghi trực tiếp hoặc ghi sao chép.
- Ghi vào Global Secondary Index (GSI) được coi là ghi cục bộ và sử dụng Write Units thông thường.
- Hiện tại không có dung lượng dự trữ cho rWCUs. Mua Dung lượng Dự trữ có thể vẫn có lợi cho các bảng với GSIs tiêu thụ đơn vị ghi.

## Sự khác biệt chính giữa GTv1 (2017) và GTv2 (2019) là gì?
- DynamoDB có hai phiên bản Global Tables. Cả hai đều vẫn được hỗ trợ, nhưng chúng tôi khuyên bạn sử dụng GTv2 (2019) hoặc nâng cấp khi có thể. Mọi thảo luận ngoài câu hỏi này đều đề cập đến các hành vi của GTv2.
- Với GTv2, bảng nguồn và đích được duy trì cùng nhau và tự động được căn chỉnh (đối với thông lượng, cài đặt TTL, cài đặt tự động mở rộng, v.v.).
- Với GTv2, các thuộc tính siêu dữ liệu cần thiết để kiểm soát sao chép giờ đây được ẩn, ngăn chặn việc ghi nhầm (hoặc cố ý) vào chúng gây ra vấn đề với việc sao chép.
- Mã hóa Customer Managed Key (CMK) chỉ có sẵn trên GTv2.
- Nhiều vùng được hỗ trợ với GTv2 hơn.
- GTv2 cho phép bạn thêm/xóa vùng vào một bảng hiện có.
- GTv2 thường có chi phí hiệu quả hơn.

## Làm thế nào để nâng cấp từ Global Tables v1 lên v2?

- Việc nâng cấp là một nút nhấn trong Console. Đây là một nâng cấp trực tiếp sẽ hoàn tất trong vòng chưa đầy một giờ.

## Cách quản lý thông lượng đọc và ghi cho Global Tables như thế nào?

- Công suất ghi phải giống nhau trên tất cả các bảng trong các vùng. Với GTv2, công suất ghi tự động được đồng bộ hóa bởi cơ sở hạ tầng GT, vì vậy thay đổi công suất ghi trên một bảng sẽ được sao chép sang các bảng khác. Bảng phải hỗ trợ tự động mở rộng hoặc ở chế độ theo yêu cầu.
- Công suất đọc được phép khác nhau vì số lượng đọc có thể không đồng đều giữa các vùng. Khi thêm một bản sao toàn cầu vào bảng, công suất của vùng nguồn sẽ được truyền đi. Sau khi tạo, bạn có thể điều chỉnh công suất đọc, điều này không được chuyển đến phía bên kia. Công suất đọc cũng có thể được điều chỉnh cho từng chỉ số thứ cấp toàn cầu của từng vùng thông qua [điều chỉnh thông lượng cung cấp](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ReplicaGlobalSecondaryIndex.html#DDB-Type-ReplicaGlobalSecondaryIndex-ProvisionedThroughputOverride).

## Global Tables hỗ trợ những vùng nào?

- Tính đến hôm nay, GTv2 hỗ trợ hơn 32 vùng. Danh sách mới nhất có thể được xem trong menu thả xuống trên Console khi chọn một vùng để thêm bản sao.

## GSIs được xử lý như thế nào với Global Tables?

- Với GTv2, bạn tạo một GSI trong một vùng, và nó sẽ tự động được sao chép sang vùng khác cũng như được tự động điền lại.
- Công suất ghi phải giống nhau trên mỗi bản sao chỉ số, nhưng bạn có thể ghi đè công suất đọc cho từng vùng.

## Làm thế nào để xóa một bảng toàn cầu?

- Bạn có thể xóa một bảng bản sao giống như bất kỳ bảng nào khác, điều này sẽ dừng việc sao chép đến vùng đó và xóa bản sao bảng được giữ trong vùng đó. Tuy nhiên, bạn không thể yêu cầu ngắt kết nối việc sao chép và giữ bản sao bảng như các thực thể độc lập.
- Cũng có một quy tắc bạn không thể xóa bảng nguồn nhanh chóng sau khi nó được sử dụng để khởi tạo một vùng mới. Nếu bạn thử, bạn sẽ nhận được lỗi: "Bản sao không thể bị xóa vì nó đã hoạt động như một vùng nguồn cho các bản sao mới được thêm vào bảng trong 24 giờ qua.."

## Xử lý xung đột ghi như thế nào với Global Tables?

- Xung đột có thể xảy ra nếu các ứng dụng cập nhật cùng một mục trong các vùng khác nhau gần như cùng lúc. Để đảm bảo tính nhất quán cuối cùng, bảng toàn cầu DynamoDB sử dụng cơ chế hòa giải "last writer wins" giữa các cập nhật đồng thời, trong đó DynamoDB sẽ cố gắng hết sức để xác định người ghi cuối cùng. Với cơ chế giải quyết xung đột này, tất cả các bản sao sẽ đồng ý về bản cập nhật mới nhất và hướng tới trạng thái trong đó tất cả đều có dữ liệu giống hệt nhau. Có một số cách để tránh xung đột, chẳng hạn như sử dụng chính sách IAM để chỉ cho phép ghi vào bảng ở một vùng, định tuyến người dùng đến một vùng duy nhất và giữ vùng khác ở chế độ chờ không hoạt động, định tuyến người dùng lẻ đến một vùng và người dùng chẵn đến vùng khác, tránh sử dụng các cập nhật không xác định như Bookmark = Bookmark + 1 thay vì các cập nhật tĩnh như Bookmark = 25.
- Để biết thêm thông tin, hãy xem hướng dẫn thực hành tốt nhất của chúng tôi [về định tuyến yêu cầu với bảng toàn cầu](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-global-table-design.prescriptive-guidance.request-routing.html).

## Những thực hành tốt nhất khi triển khai Global Tables là gì? Làm thế nào để tự động hóa việc triển khai?

- Trong AWS CloudFormation, mỗi bảng toàn cầu được kiểm soát bởi một ngăn xếp duy nhất, trong một vùng duy nhất, bất kể số lượng bản sao. Khi bạn triển khai mẫu của mình, CloudFormation sẽ tạo/cập nhật tất cả các bản sao như một phần của một hoạt động ngăn xếp duy nhất. Bạn không nên triển khai tài nguyên `AWS::DynamoDB::GlobalTable` cùng một lúc trong nhiều vùng. Làm như vậy sẽ dẫn đến lỗi và không được hỗ trợ. Nếu bạn triển khai mẫu ứng dụng của mình trong nhiều vùng, bạn có thể sử dụng điều kiện để chỉ tạo tài nguyên trong một vùng duy nhất. Ngoài ra, bạn có thể chọn định nghĩa các tài nguyên `AWS::DynamoDB::GlobalTable` trong một ngăn xếp tách biệt với ngăn xếp ứng dụng của bạn, và đảm bảo rằng nó chỉ được triển khai vào một vùng duy nhất.
- [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dynamodb-globaltable.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dynamodb-globaltable.html)
- Một bảng DynamoDB là `AWS::DynamoDB::Table` và một bảng toàn cầu là `AWS::DynamoDB::GlobalTable`, điều này khiến chúng về cơ bản trở thành hai tài nguyên khác nhau liên quan đến CFN. Một cách tiếp cận là tạo tất cả các bảng có thể từng là bảng toàn cầu bằng cách sử dụng cấu trúc Global