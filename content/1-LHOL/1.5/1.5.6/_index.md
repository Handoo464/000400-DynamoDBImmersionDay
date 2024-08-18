---
title : "Load DynamoDB Table"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 1.5.6. </b> "
---

Trong bài tập này, chúng ta sẽ thiết lập các tác vụ Database Migration Service (DMS) để di chuyển dữ liệu từ cơ sở dữ liệu MySQL nguồn (quan hệ view, bảng) sang Amazon DynamoDB.

## Xác minh việc tạo DMS

1. Đi tới [Bảng điều khiển DMS](https://console.aws.amazon.com/dms/v2/home?region=us-east-1#dashboard)  và nhấp vào Phiên bản sao chép. Bạn có thể xem phiên bản sao chép với Lớp dms.c5.2xlarge ở Trạng thái khả dụng. ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration20.jpg)
{{%notice tip%}}
_Đảm bảo phiên bản DMS có sẵn trước khi bạn tiếp tục. Nếu nó không khả dụng, hãy quay lại bảng điều khiển CloudFormation để xem lại và khắc phục sự cố ngăn xếp CloudFormation._
{{%/notice%}}

## Tạo điểm cuối nguồn và đích


1. Nhấp vào nút Điểm cuối và Tạo điểm cuối ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration21.jpg)
    
2. Tạo điểm cuối nguồn. Sử dụng các tham số sau để đặt cấu hình điểm cuối:
    
    |Thông số|Giá trị|
    |---|---|
    |Loại điểm cuối|Điểm cuối nguồn|
    |Mã định danh điểm cuối|điểm cuối mysql|
    |Công cụ nguồn|MySQL|
    |Truy cập vào cơ sở dữ liệu điểm cuối|Chọn nút radio "Cung cấp thông tin truy cập theo cách thủ công"|
    |Tên máy chủ|Từ [Bảng thông tin EC2](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:instanceState=running) , chọn MySQL-Instance và sao chép DNS IPv4 công cộng|
    |Cảng|3306|
    |Chế độ SSL|không ai|
    |Tên người dùng|Giá trị của DbMasterUsername được thêm vào dưới dạng tham số trong khi Định cấu hình môi trường MySQL|
    |Mật khẩu|Giá trị của DbMasterPassword được thêm vào dưới dạng tham số trong quá trình Định cấu hình môi trường MySQL|
    
    ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration22.jpg) Mở mục Test endpoint connection (optional), sau đó trong menu thả xuống VPC, chọn DMS-VPC và nhấp vào nút Run test để xác minh rằng cấu hình điểm cuối của bạn là hợp lệ. Thử nghiệm sẽ chạy trong một phút và bạn sẽ thấy thông báo thành công trong cột Trạng thái. Nhấp vào nút Tạo điểm cuối để tạo điểm cuối. Nếu bạn thấy lỗi kết nối, hãy nhập lại tên người dùng và mật khẩu để đảm bảo không có lỗi nào xảy ra. Hơn nữa, hãy đảm bảo bạn đã cung cấp tên DNS IPv4 kết thúc bằng amazonaws.com trong trường **Tên máy chủ**. ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration23.jpg)
    
3. Tạo điểm cuối đích. Lặp lại tất cả các bước để tạo điểm cuối đích với các giá trị tham số sau:
    
    |Thông số|Giá trị|
    |---|---|
    |Loại điểm cuối|Điểm cuối mục tiêu|
    |Mã định danh điểm cuối|điểm cuối dynamoDB|
    |Động cơ mục tiêu|Amazon DynamoDB|
    |Vai trò truy cập dịch vụ ARN|Mẫu CloudFormation đã tạo vai trò mới với toàn quyền truy cập vào Amazon DynamoDB. Sao chép ARN vai trò từ [Truy cập DynamoDB](https://us-east-1.console.aws.amazon.com/iamv2/home#/roles/details/dynamodb-access?section=permissions)  vai trò|
    
    ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration24.jpg) Mở mục Test endpoint connection (optional), sau đó trong menu thả xuống VPC, chọn DMS-VPC và nhấp vào nút Run test để xác minh rằng cấu hình điểm cuối của bạn là hợp lệ. Thử nghiệm sẽ chạy trong một phút và bạn sẽ thấy thông báo thành công trong cột Trạng thái. Nhấp vào nút Tạo điểm cuối để tạo điểm cuối.
    

## Cấu hình và chạy tác vụ sao chép

Vẫn trong bảng điều khiển AWS DMS, hãy chuyển đến Tác vụ di chuyển cơ sở dữ liệu và nhấp vào nút Tạo tác vụ. Chúng tôi sẽ tạo 3 tác vụ sao chép để di chuyển thông tin chế độ xem, xếp hạng (title_ratings) và khu vực/ngôn ngữ (title_akas) không chuẩn hóa.

1. Nhiệm vụ 1: Nhập các giá trị tham số sau vào Tạo tác vụ di chuyển cơ sở dữ liệu màn:
    
    |Thông số|Giá trị|
    |---|---|
    |Nhiệm vụ đã xác định|lịch sử-di cư01|
    |Phiên bản sao chép|mysqltodynamodb-instance-*|
    |Điểm cuối cơ sở dữ liệu nguồn|điểm cuối mysql|
    |Điểm cuối cơ sở dữ liệu đích|điểm cuối dynamoDB|
    |Loại di chuyển|Di chuyển dữ liệu hiện có|
    |Cài đặt tác vụ: Chế độ chỉnh sửa|Thuật sĩ|
    |Cài đặt tác vụ: Chế độ chuẩn bị bảng mục tiêu|Không làm gì cả|
    |Cài đặt tác vụ: Bật nhật ký CloudWatch|Kiểm tra|
    |Ánh xạ bảng: Chế độ chỉnh sửa|Chọn tùy chọn trình chỉnh sửa JSON và làm theo hướng dẫn sau ảnh chụp màn hình bên dưới|
    

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration25.jpg) ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration26.jpg)

Bắt đầu với phần trình soạn thảo JSON mở trong trình duyệt của bạn. Trong phần này, chúng ta sẽ tạo tài liệu Table mappings JSON để thay thế những gì bạn thấy trong trình soạn thảo JSON. Tài liệu này bao gồm ánh xạ từ nguồn đến đích, bao gồm bất kỳ chuyển đổi nào trên các bản ghi sẽ được thực hiện trong quá trình di chuyển. Để giảm thời gian tải trong Immersion Day, chúng tôi đã thu hẹp danh sách di chuyển thành phim chọn lọc. Dưới đây là tài liệu JSON có danh sách 28 bộ phim được thực hiện bởi Clint Eastwood. Bài tập còn lại sẽ chỉ tập trung vào những bộ phim này. Tuy nhiên, hãy thoải mái tải dữ liệu còn lại trong trường hợp bạn muốn khám phá thêm. Một số thống kê xung quanh tập dữ liệu đầy đủ được đưa ra ở cuối chương này.

Sao chép danh sách các bộ phim chọn lọc của Clint Eastwood.

```
    {
      "filter-operator": "eq",
      "value": "tt0309377"
    },
    {
      "filter-operator": "eq",
      "value": "tt12260846"
    },
    {
      "filter-operator": "eq",
      "value": "tt1212419"
    },
    {
      "filter-operator": "eq",
      "value": "tt1205489"
    },
    {
      "filter-operator": "eq",
      "value": "tt1057500"
    },
    {
      "filter-operator": "eq",
      "value": "tt0949815"
    },
    {
      "filter-operator": "eq",
      "value": "tt0824747"
    },
    {
      "filter-operator": "eq",
      "value": "tt0772168"
    },
    {
      "filter-operator": "eq",
      "value": "tt0498380"
    },
    {
      "filter-operator": "eq",
      "value": "tt0418689"
    },
    {
      "filter-operator": "eq",
      "value": "tt0405159"
    },
    {
      "filter-operator": "eq",
      "value": "tt0327056"
    },
    {
      "filter-operator": "eq",
      "value": "tt2310814"
    },
    {
      "filter-operator": "eq",
      "value": "tt2179136"
    },
    {
      "filter-operator": "eq",
      "value": "tt2083383"
    },
    {
      "filter-operator": "eq",
      "value": "tt1924245"
    },
    {
      "filter-operator": "eq",
      "value": "tt1912421"
    },
    {
      "filter-operator": "eq",
      "value": "tt1742044"
    },
    {
      "filter-operator": "eq",
      "value": "tt1616195"
    },
    {
      "filter-operator": "eq",
      "value": "tt6997426"
    },
    {
      "filter-operator": "eq",
      "value": "tt6802308"
    },
    {
      "filter-operator": "eq",
      "value": "tt3513548"
    },
    {
      "filter-operator": "eq",
      "value": "tt3263904"
    },
    {
      "filter-operator": "eq",
      "value": "tt3031654"
    },
    {
      "filter-operator": "eq",
      "value": "tt8884452"
    }
```

Bên dưới tài liệu JSON sẽ di chuyển chế độ xem không chuẩn hóa từ cơ sở dữ liệu mySQL imdb (Tác vụ được xác định: lịch sử-migration01). Thay thế chuỗi "THAY THẾ CHUỖI NÀY BẰNG DANH SÁCH PHIM" bằng danh sách phim được sao chép trước đó (Kiểm tra sau ảnh chụp màn hình cho bất kỳ sự nhầm lẫn nào). Sau đó dán mã JSON kết quả vào trình soạn thảo JSON, thay thế mã hiện có.

```json
{
  "rules": [
    {
      "rule-type": "selection",
      "rule-id": "1",
      "rule-name": "1",
      "object-locator": {
        "schema-name": "imdb",
        "table-name": "movies",
        "table-type": "view"
      },
      "rule-action": "include",
      "filters": [
        {
          "filter-type": "source",
          "column-name": "tconst",
          "filter-conditions": ["REPLACE THIS STRING BY MOVIES LIST"]
        }
      ]
    },
    {
      "rule-type": "object-mapping",
      "rule-id": "2",
      "rule-name": "2",
      "rule-action": "map-record-to-record",
      "object-locator": {
        "schema-name": "imdb",
        "table-name": "movies",
        "table-type": "view"
      },
      "target-table-name": "movies",
      "mapping-parameters": {
        "partition-key-name": "mpkey",
        "sort-key-name": "mskey",
        "exclude-columns": [],
        "attribute-mappings": [
          {
            "target-attribute-name": "mpkey",
            "attribute-type": "scalar",
            "attribute-sub-type": "string",
            "value": "${tconst}"
          },
          {
            "target-attribute-name": "mskey",
            "attribute-type": "scalar",
            "attribute-sub-type": "string",
            "value": "DETL|${category}|${ordering}"
          }
        ]
      }
    }
  ]
}
```

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration36.png) Chuyển xuống dưới cùng và nhấp vào Tạo tác vụ. Tại thời điểm này, tác vụ sẽ được tạo và sẽ tự động bắt đầu tải các phim đã chọn từ nguồn vào bảng DynamoDB đích. Bạn có thể tiếp tục và tạo thêm hai tác vụ với các bước tương tự (history-migration02 và history-migration03). Sử dụng các cài đặt tương tự như trên ngoại trừ tài liệu JSON ánh xạ bảng. Đối với các tác vụ di cư lịch sử02 và di cư lịch sử03, hãy sử dụng tài liệu JSON được đề cập bên dưới.

Dưới đây tài liệu JSON sẽ di chuyển bảng title_akas từ cơ sở dữ liệu mySQL imdb (Tác vụ được xác định: lịch sử-migration02) Thay thế chuỗi "REPLACE THIS STRING BY MOVIES LIST" bằng danh sách phim đã sao chép trước đó.

```json
{
  "rules": [
    {
      "rule-type": "selection",
      "rule-id": "1",
      "rule-name": "1",
      "object-locator": {
        "schema-name": "imdb",
        "table-name": "title_akas",
        "table-type": "table"
      },
      "rule-action": "include",
      "filters": [
        {
          "filter-type": "source",
          "column-name": "titleId",
          "filter-conditions": ["REPLACE THIS STRING BY MOVIES LIST"]
        }
      ]
    },
    {
      "rule-type": "object-mapping",
      "rule-id": "2",
      "rule-name": "2",
      "rule-action": "map-record-to-record",
      "object-locator": {
        "schema-name": "imdb",
        "table-name": "title_akas",
        "table-type": "table"
      },
      "target-table-name": "movies",
      "mapping-parameters": {
        "partition-key-name": "mpkey",
        "sort-key-name": "mskey",
        "exclude-columns": [],
        "attribute-mappings": [
          {
            "target-attribute-name": "mpkey",
            "attribute-type": "scalar",
            "attribute-sub-type": "string",
            "value": "${titleId}"
          },
          {
            "target-attribute-name": "mskey",
            "attribute-type": "scalar",
            "attribute-sub-type": "string",
            "value": "REGN|${region}"
          }
        ]
      }
    }
  ]
}
```

Dưới đây tài liệu JSON sẽ di chuyển bảng title_ratings từ cơ sở dữ liệu mySQL imdb (Nhiệm vụ được xác định: lịch sử-di chuyển03) Thay thế chuỗi "REPLACE THIS STRING BY MOVIES LIST" bằng danh sách phim đã sao chép trước đó.

```json
{
  "rules": [
    {
      "rule-type": "selection",
      "rule-id": "1",
      "rule-name": "1",
      "object-locator": {
        "schema-name": "imdb",
        "table-name": "title_ratings",
        "table-type": "table"
      },
      "rule-action": "include",
      "filters": [
        {
          "filter-type": "source",
          "column-name": "tconst",
          "filter-conditions": ["REPLACE THIS STRING BY MOVIES LIST"]
        }
      ]
    },
    {
      "rule-type": "object-mapping",
      "rule-id": "2",
      "rule-name": "2",
      "rule-action": "map-record-to-record",
      "object-locator": {
        "schema-name": "imdb",
        "table-name": "title_ratings",
        "table-type": "table"
      },
      "target-table-name": "movies",
      "mapping-parameters": {
        "partition-key-name": "mpkey",
        "sort-key-name": "mskey",
        "exclude-columns": [],
        "attribute-mappings": [
          {
            "target-attribute-name": "mpkey",
            "attribute-type": "scalar",
            "attribute-sub-type": "string",
            "value": "${tconst}"
          },
          {
            "target-attribute-name": "mskey",
            "attribute-type": "scalar",
            "attribute-sub-type": "string",
            "value": "RTNG"
          }
        ]
      }
    }
  ]
}
```

#### Giải pháp


Nếu bạn gặp sự cố khi tạo tài liệu JSON cho các tác vụ, hãy mở rộng phần này để có giải pháp!
- [Nhiệm vụ đầu tiên - lịch sử-di cư01](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/files/hands-on-labs/Task_1.json) 
- [Nhiệm vụ thứ hai - lịch sử-di cư02](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/files/hands-on-labs/Task_2.json) 
- [Nhiệm vụ thứ ba - lịch sử-di cư03](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/files/hands-on-labs/Task_3.json)

### Giám sát và khởi động lại / tiếp tục các tác vụ


Tác vụ sao chép cho di chuyển lịch sử sẽ bắt đầu di chuyển dữ liệu từ MySQL imdb.movies view, title_akas và title_ratings sang bảng DynamoDB sẽ bắt đầu sau vài phút. Nếu bạn đang tải các bản ghi chọn lọc dựa trên danh sách trên, có thể mất 5-10 phút để hoàn thành cả ba tác vụ.

Nếu bạn chạy lại bài tập này nhưng thực hiện tải đầy đủ, thời gian tải sẽ như sau:

- tác vụ history-migration01 sẽ di chuyển 800K + bản ghi và thường mất 2-3 giờ.
- tác vụ history-migration02 sẽ di chuyển các bản ghi 747K + và thường mất 2-3 giờ.
- tác vụ history-migration03 sẽ di chuyển 79K + bản ghi và thường mất 10-15 phút.

Bạn có thể theo dõi trạng thái tải dữ liệu trong Bảng thống kê của tác vụ di chuyển. Sau khi tải đang diễn ra, vui lòng chuyển sang phần tiếp theo của bài tập. ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration27.jpg)

{{%notice tip%}}
_Đảm bảo rằng tất cả các tác vụ đang chạy hoặc hoàn thành trước khi bạn tiếp tục. Nếu một tác vụ cho biết **Sẵn sàng**, hãy chọn hộp của nó và chọn "Khởi động lại / Tiếp tục" dưới nút Hành động để bắt đầu tác vụ._
{{%/notice%}}