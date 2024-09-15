---
title : "Đọc dữ liệu mẫu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1.2.1. </b> "
---

Trước khi có thể làm bất cứ điều gì, chúng ta cần tìm hiểu về cấu trúc dữ liệu của mình.

DynamoDB cung cấp [API Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html) mà bạn có thể gọi bằng [lệnh CLI scan](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html). Lệnh Scan sẽ quét toàn bộ bảng và trả về các mục dữ liệu theo từng khối 1MB. Việc quét bảng là cách chậm nhất và tốn kém nhất để lấy dữ liệu ra khỏi DynamoDB; quét một bảng lớn từ CLI có thể sẽ rất phức tạp, nhưng chúng ta biết rằng trong dữ liệu mẫu của chúng ta chỉ có một vài mục, nên có thể thực hiện điều này ở đây. Hãy thử chạy lệnh quét trên bảng ProductCatalog:

```bash
aws dynamodb scan --table-name ProductCatalog
```

Sử dụng các phím mũi tên để di chuyển lên và xuống qua phản hồi của lệnh **_Scan_**. **Nhập `:q` và nhấn Enter để thoát khỏi chế độ xem** khi bạn đã hoàn thành việc xem phản hồi.

Việc nhập và xuất dữ liệu trong CLI sử dụng định dạng JSON của DynamoDB, được mô tả trong phần [API Cấp thấp của DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.LowLevelAPI.html) trong Hướng dẫn Dành cho Nhà Phát triển.

Chúng ta có thể thấy từ dữ liệu của mình rằng bảng ProductCatalog có hai loại sản phẩm: các mục Sách (Book) và Xe đạp (Bicycle).

Nếu chúng ta muốn đọc chỉ một mục, chúng ta sẽ sử dụng [API GetItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_GetItem.html) mà có thể gọi bằng [lệnh CLI get-item](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/get-item.html). GetItem là cách nhanh nhất và rẻ nhất để lấy dữ liệu ra khỏi DynamoDB vì bạn phải chỉ định đầy đủ Khóa Chính, do đó lệnh được đảm bảo chỉ khớp với tối đa một mục trong bảng.

```bash
aws dynamodb get-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"101"}}'
```

Mặc định, một lần đọc từ DynamoDB sẽ sử dụng **đọc nhất quán cuối cùng** (eventual consistency) vì các lần đọc nhất quán cuối cùng trong DynamoDB có giá chỉ bằng một nửa so với lần đọc **nhất quán mạnh** (strongly consistent). Xem thêm thông tin về [Tính nhất quán của lần đọc](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadConsistency.html) trong Hướng dẫn Dành cho Nhà Phát triển DynamoDB.

Có nhiều tùy chọn hữu ích cho lệnh get-item, nhưng một số tùy chọn được sử dụng thường xuyên là:

- **--consistent-read**: Xác định rằng bạn muốn đọc nhất quán mạnh.
- **--projection-expression**: Xác định rằng bạn chỉ muốn trả về một số thuộc tính nhất định trong yêu cầu.
- **--return-consumed-capacity**: Cho biết mức dung lượng đã được tiêu thụ bởi yêu cầu.

Hãy chạy lại lệnh trước đó và thêm một số tùy chọn này vào dòng lệnh:

```bash
aws dynamodb get-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"101"}}' \
    --consistent-read \
    --projection-expression "ProductCategory, Price, Title" \
    --return-consumed-capacity TOTAL
```

Chúng ta có thể thấy từ các giá trị trả về:

```json
{
    "Item": {
        "Price": {
            "N": "2"
        },
        "Title": {
            "S": "Book 101 Title"
        },
        "ProductCategory": {
            "S": "Book"
        }
    },
    "ConsumedCapacity": {
        "TableName": "ProductCatalog",
        "CapacityUnits": 1.0
    }
}
```

Rằng việc thực hiện yêu cầu này tiêu thụ 1.0 RCU, vì mục này nhỏ hơn 4KB. Nếu chúng ta chạy lại lệnh mà loại bỏ tùy chọn *--consistent-read*, chúng ta sẽ thấy rằng các lần đọc nhất quán cuối cùng tiêu thụ dung lượng chỉ bằng một nửa:

```bash
aws dynamodb get-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"101"}}' \
    --projection-expression "ProductCategory, Price, Title" \
    --return-consumed-capacity TOTAL
```

Chúng ta sẽ thấy kết quả đầu ra này:

```json
{
    "Item": {
        "Price": {
            "N": "2"
        },
        "Title": {
            "S": "Book 101 Title"
        },
        "ProductCategory": {
            "S": "Book"
        }
    },
    "ConsumedCapacity": {
        "TableName": "ProductCatalog",
        "CapacityUnits": 0.5
    }
}
```