---
title : "Đọc dữ liệu mẫu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1.2.1. </b> "
---


Trước khi chúng ta làm bất cứ điều gì, chúng ta cần phải hiểu dữ liệu của mình trông như thế nào.

DynamoDB cung cấp [Scan API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html), có thể được gọi bằng lệnh [scan CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html). Scan sẽ thực hiện quét toàn bộ bảng và trả về các mục theo từng khối 1MB. Quét là cách chậm nhất và tốn kém nhất để lấy dữ liệu từ DynamoDB; việc quét trên một bảng lớn từ CLI có thể không thuận tiện, nhưng chúng ta biết rằng chỉ có một vài mục trong dữ liệu mẫu của chúng ta, vì vậy việc làm này ở đây là chấp nhận được. Hãy thử chạy lệnh quét trên bảng ProductCatalog:

```bash
aws dynamodb scan --table-name ProductCatalog
```

Sử dụng các phím mũi tên để di chuyển lên và xuống qua phản hồi Scan. Hãy gõ `:q` và nhấn Enter để thoát khỏi trình xem khi bạn đã xem xong phản hồi.

Đầu vào và đầu ra dữ liệu trong CLI sử dụng định dạng JSON của DynamoDB, được mô tả trong phần [DynamoDB Low-Level API](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.LowLevelAPI.html) trong Developer Guide.

Chúng ta có thể thấy từ dữ liệu rằng bảng ProductCatalog này có hai loại sản phẩm: Sách và Xe đạp.

Nếu chúng ta chỉ muốn đọc một mục cụ thể, chúng ta sẽ sử dụng  [GetItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_GetItem.html), có thể được gọi bằng [get-item CLI command](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/get-item.html). GetItem là cách nhanh nhất và rẻ nhất để lấy dữ liệu từ DynamoDB vì bạn phải chỉ định toàn bộ Khóa Chính, vì vậy lệnh này đảm bảo sẽ khớp với tối đa một mục trong bảng.

```bash
aws dynamodb get-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"101"}}'
```

Theo mặc định, một lần đọc từ DynamoDB sẽ sử dụng tính nhất quán cuối cùng (_eventual consistency_), vì các lần đọc nhất quán cuối cùng trong DynamoDB có giá chỉ bằng một nửa so với một lần đọc nhất quán mạnh(*strongly consistent*). Xem phần  [Read Consistency](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadConsistency.html) trong DynamoDB Developer Guide để biết thêm thông tin.

Có nhiều tùy chọn hữu ích cho lệnh `get-item`, nhưng một số tùy chọn thường được sử dụng là:

- `--consistent-read`: Chỉ định rằng bạn muốn một lần đọc nhất quán mạnh
- `--projection-expression`: Chỉ định rằng bạn chỉ muốn một số thuộc tính nhất định được trả về trong yêu cầu
- `--return-consumed-capacity`: Cho biết dung lượng đã được tiêu thụ bởi yêu cầu

Hãy chạy lệnh trước đó và thêm một số tùy chọn này vào dòng lệnh:

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

Việc thực hiện yêu cầu này tiêu tốn 1.0 RCU, bởi vì mục này có kích thước nhỏ hơn 4KB. Nếu chúng ta chạy lại lệnh nhưng bỏ tùy chọn `--consistent-read`, chúng ta sẽ thấy rằng các lần đọc nhất quán cuối cùng sẽ tiêu tốn một nửa dung lượng:

```bash
aws dynamodb get-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"101"}}' \
    --projection-expression "ProductCategory, Price, Title" \
    --return-consumed-capacity TOTAL
```

Chúng ta sẽ thấy đầu ra này:

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
