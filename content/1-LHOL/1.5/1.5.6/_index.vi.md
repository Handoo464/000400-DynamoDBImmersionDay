---
title : "Load DynamoDB Table"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 1.5.6. </b> "
---

Trong bài tập này, chúng ta sẽ thiết lập các công việc của Dịch vụ Di chuyển Cơ sở dữ liệu (Database Migration Service - DMS) để di chuyển dữ liệu từ cơ sở dữ liệu MySQL nguồn (dạng xem quan hệ, bảng) sang Amazon DynamoDB.

## Xác minh việc tạo DMS

1. Truy cập [DMS Console](https://console.aws.amazon.com/dms/v2/home?region=us-east-1#dashboard) và nhấp vào "Replication Instances". Bạn sẽ thấy một bản sao nhân bản với Class dms.c5.2xlarge ở trạng thái "Available".  
   ![Final Deployment Architecture](/images/1/1.5/19.jpg)

{{%notice tip%}}
_Hãy đảm bảo rằng DMS instance ở trạng thái "Available" trước khi bạn tiếp tục. Nếu không phải là "Available", quay lại bảng điều khiển CloudFormation để xem xét và khắc phục sự cố với CloudFormation stack._
{{%/notice%}}

## Tạo endpoints nguồn và đích

1. Nhấp vào "Endpoints" và nút "Create endpoint".  
   ![Final Deployment Architecture](/images/1/1.5/20.jpg)
    
2. Tạo endpoint nguồn. Sử dụng các tham số sau để cấu hình endpoint:

    |Tham số|Giá trị|
    |---|---|
    |Endpoint type|Source endpoint|
    |Endpoint identifier|mysql-endpoint|
    |Source engine|MySQL|
    |Access to endpoint database|Chọn radio button "Provide access information manually"|
    |Server name|Từ [EC2 dashboard](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:instanceState=running), chọn MySQL-Instance và sao chép Public IPv4 DNS|
    |Port|3306|
    |SSL mode|none|
    |User name|Giá trị của DbMasterUsername đã thêm làm tham số khi cấu hình môi trường MySQL|
    |Password|Giá trị của DbMasterPassword đã thêm làm tham số khi cấu hình môi trường MySQL|
    
   ![Final Deployment Architecture](/images/1/1.5/21.jpg)  
   Mở phần "Test endpoint connection (optional)", sau đó trong danh sách thả xuống VPC chọn DMS-VPC và nhấp vào nút "Run test" để xác minh cấu hình endpoint của bạn là hợp lệ. Bài kiểm tra sẽ chạy trong một phút và bạn sẽ thấy một thông báo thành công trong cột Status. Nhấp vào nút "Create endpoint" để tạo endpoint. Nếu bạn thấy lỗi kết nối, hãy nhập lại tên người dùng và mật khẩu để đảm bảo không có sai sót. Ngoài ra, hãy đảm bảo bạn đã cung cấp tên DNS IPv4 kết thúc bằng amazonaws.com trong trường **Server name**.  
   ![Final Deployment Architecture](/images/1/1.5/22.jpg)
    
3. Tạo endpoint đích. Lặp lại tất cả các bước để tạo endpoint đích với các giá trị tham số sau:

    |Tham số|Giá trị|
    |---|---|
    |Endpoint type|Target endpoint|
    |Endpoint identifier|dynamodb-endpoint|
    |Target engine|Amazon DynamoDB|
    |Service access role ARN|CloudFormation template đã tạo vai trò mới với toàn quyền truy cập Amazon DynamoDB. Sao chép Role ARN từ vai trò [dynamodb-access](https://us-east-1.console.aws.amazon.com/iamv2/home#/roles/details/dynamodb-access?section=permissions)|
    
   ![Final Deployment Architecture](/images/1/1.5/23.jpg)  
   Mở phần "Test endpoint connection (optional)", sau đó trong danh sách thả xuống VPC chọn DMS-VPC và nhấp vào nút "Run test" để xác minh cấu hình endpoint của bạn là hợp lệ. Bài kiểm tra sẽ chạy trong một phút và bạn sẽ thấy một thông báo thành công trong cột Status. Nhấp vào nút "Create endpoint" để tạo endpoint.
    

## Cấu hình và chạy tác vụ sao chép

Vẫn ở trong AWS DMS console, vào "Database migration tasks" và nhấp vào nút "Create Task". Chúng ta sẽ tạo 3 công việc sao chép để di chuyển dữ liệu đã được không chuẩn hóa, xếp hạng (title_ratings), và thông tin vùng/ngôn ngữ (title_akas).

1. Tác vụ 1: Nhập các giá trị tham số sau trong màn hình "Create database migration task":

    |Tham số|Giá trị|
    |---|---|
    |Task identified|historical-migration01|
    |Replication instance|mysqltodynamodb-instance-*|
    |Source database endpoint|mysql-endpoint|
    |Target database endpoint|dynamodb-endpoint|
    |Migration type|Migrate existing data|
    |Task settings: Editing mode|Wizard|
    |Task settings: Target table preparation mode|Do nothing|
    |Task settings: Turn on CloudWatch logs|Checked|
    |Table mappings: Editing mode|Chọn tùy chọn "JSON editor" và làm theo hướng dẫn sau các ảnh chụp màn hình bên dưới|

   ![Final Deployment Architecture](/images/1/1.5/24.jpg) ![Final Deployment Architecture](/images/1/1.5/25.jpg)

Mở phần "JSON editor" trong trình duyệt của bạn. Trong phần này, chúng ta sẽ tạo tài liệu JSON cho "Table mappings" để thay thế những gì bạn thấy trong "JSON editor". Tài liệu này bao gồm việc ánh xạ từ nguồn sang đích bao gồm bất kỳ chuyển đổi nào trên các bản ghi sẽ được thực hiện trong quá trình di chuyển. Để giảm thời gian tải trong Immersion Day, chúng tôi đã giới hạn danh sách di chuyển đến các bộ phim đã chọn lọc. Dưới đây là tài liệu JSON với danh sách 28 bộ phim do Clint Eastwood tham gia. Các bài tập còn lại sẽ chỉ tập trung vào những bộ phim này. Tuy nhiên, bạn có thể tải dữ liệu còn lại nếu muốn khám phá thêm. Một số thống kê xung quanh bộ dữ liệu đầy đủ được đưa ra ở cuối chương này.

Sao chép danh sách các bộ phim đã chọn lọc của Clint Eastwood.

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

Tài liệu JSON dưới đây sẽ di chuyển dữ liệu đã được không chuẩn hóa từ cơ sở dữ liệu MySQL imdb (Task identified: historical-migration01). Thay thế chuỗi “REPLACE THIS STRING BY MOVIES LIST” bằng danh sách phim đã sao chép trước đó (Xem ảnh chụp màn hình dưới đây nếu có bất kỳ nhầm lẫn nào). Sau đó dán mã JSON kết quả vào "JSON editor", thay thế mã hiện có.

```json
{
  "rules": [
    {
      "rule-type": "selection",
```json
    {
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

![Final Deployment Architecture](/images/1/1.5/26.png) Đi tới cuối trang và nhấp vào "Create task". Lúc này, tác vụ sẽ được tạo và tự động bắt đầu tải các bộ phim đã chọn từ nguồn sang bảng DynamoDB đích. Bạn có thể tiến hành và tạo thêm hai tác vụ với các bước tương tự (historical-migration02 và historical-migration03). Sử dụng cùng cài đặt như trên ngoại trừ tài liệu JSON "Table Mappings". Đối với các tác vụ historical-migration02 và historical-migration03, hãy sử dụng tài liệu JSON được đề cập bên dưới.

Dưới đây là tài liệu JSON sẽ di chuyển bảng `title_akas` từ cơ sở dữ liệu MySQL IMDb (Task identified: historical-migration02). Thay thế chuỗi "REPLACE THIS STRING BY MOVIES LIST" bằng danh sách các bộ phim đã sao chép trước đó.

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

Dưới đây là tài liệu JSON sẽ di chuyển bảng `title_ratings` từ cơ sở dữ liệu MySQL IMDb (Task identified: historical-migration03). Thay thế chuỗi "REPLACE THIS STRING BY MOVIES LIST" bằng danh sách các bộ phim đã sao chép trước đó.

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

Nếu bạn gặp khó khăn khi tạo tài liệu JSON cho các tác vụ, mở rộng phần này để xem giải pháp!
- [Task đầu tiên - historical-migration01](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/files/hands-on-labs/Task_1.json) 
- [Task thứ hai - historical-migration02](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/files/hands-on-labs/Task_2.json) 
- [Task thứ ba - historical-migration03](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/files/hands-on-labs/Task_3.json)

### Giám sát và khởi động lại/tái khởi động các tác vụ

Các tác vụ sao chép để di chuyển dữ liệu lịch sử từ MySQL `imdb.movies` view, `title_akas`, và `title_ratings` sang bảng DynamoDB sẽ bắt đầu trong vài phút. Nếu bạn đang tải các bản ghi đã chọn dựa trên danh sách trên, có thể mất 5-10 phút để hoàn tất cả ba tác vụ.

Nếu bạn chạy bài tập này lần nữa và thực hiện tải đầy đủ, thời gian tải sẽ như sau:

- Tác vụ `historical-migration01` sẽ di chuyển hơn 800K bản ghi và thường mất 2-3 giờ.
- Tác vụ `historical-migration02` sẽ di chuyển hơn 747K bản ghi và thường mất 2-3 giờ.
- Tác vụ `historical-migration03` sẽ di chuyển hơn 79K bản ghi và thường mất 10-15 phút.

Bạn có thể theo dõi trạng thái tải dữ liệu dưới phần "Table statistics" của tác vụ di chuyển. Khi quá trình tải đang tiến hành, hãy thoải mái chuyển sang phần tiếp theo của bài tập.  
![Final Deployment Architecture](/images/1/1.5/27.jpg)

{{%notice tip%}}
_Hãy đảm bảo tất cả các tác vụ đang chạy hoặc đã hoàn tất trước khi bạn tiếp tục. Nếu một tác vụ ở trạng thái "Ready", hãy chọn hộp kiểm của nó và chọn "Restart/Resume" dưới nút "Actions" để bắt đầu tác vụ._
{{%/notice%}}