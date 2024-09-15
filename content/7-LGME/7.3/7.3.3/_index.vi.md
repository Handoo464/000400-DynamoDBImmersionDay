---
title : "Tải dữ liệu hàng loạt"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 7.3.3. </b> "
---

Trong bước này, bạn sẽ tải một lượng lớn dữ liệu vào DynamoDB mà bạn đã tạo trong bước trước. Điều này có nghĩa là trong các bước tiếp theo, bạn sẽ có dữ liệu mẫu để sử dụng.

Trong thư mục **scripts/**, bạn sẽ tìm thấy một tệp có tên là **items.json**. Tệp này chứa 835 mục ví dụ được tạo ngẫu nhiên cho lab này. Những mục này bao gồm các thực thể `User`, `Game`, và `UserGameMapping`. Hãy mở tệp này nếu bạn muốn xem một số mục ví dụ.

Thư mục **scripts/** cũng có một tệp gọi là **bulk_load_table.py** đọc các mục trong tệp **items.json** và ghi chúng hàng loạt vào bảng DynamoDB. Dưới đây là nội dung của tệp:

```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('battle-royale')

items = []

with open('scripts/items.json', 'r') as f:
    for row in f:
        items.append(json.loads(row))

with table.batch_writer() as batch:
    for item in items:
        batch.put_item(Item=item)
```

Trong script này, thay vì sử dụng client cấp thấp trong Boto 3, bạn sử dụng một [đối tượng Resource](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/migration.html#resource-objects) cấp cao hơn. Đối tượng Resource cung cấp một giao diện dễ dàng hơn để sử dụng các API của AWS. Đối tượng Resource hữu ích trong tình huống này vì nó chia lô các yêu cầu. Thao tác [BatchWriteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html) chấp nhận tối đa 25 mục trong một yêu cầu. Đối tượng Resource xử lý việc chia lô đó cho bạn thay vì buộc bạn phải chia dữ liệu thành các yêu cầu có 25 mục hoặc ít hơn.

Chạy script **bulk_load_table.py** và tải dữ liệu vào bảng của bạn bằng cách chạy lệnh sau trong terminal:

```sh
python scripts/bulk_load_table.py
```

Bạn có thể đảm bảo rằng tất cả dữ liệu của bạn đã được tải vào bảng bằng cách chạy thao tác `Scan` và lấy số lượng.

```sh
aws dynamodb scan --table-name battle-royale --select COUNT --return-consumed-capacity TOTAL
```

Điều này sẽ hiển thị kết quả sau:

```json
{
    "Count": 835, 
    "ScannedCount": 835, 
    "ConsumedCapacity": {
        "CapacityUnits": 14.5, 
        "TableName": "battle-royale", 
        "Table": {
            "CapacityUnits": 14.5
        }
    }
}
```

Bạn nên thấy `Count` là 835, cho biết rằng tất cả các mục của bạn đã được tải thành công.

Bạn cũng có thể duyệt bảng bằng cách điều hướng đến **Services** -> **Database** -> **DynamoDB** trong AWS console.

![Khám phá các mục trong bảng DynamoDB](/images/7/7.3/1.png)

Trong bước tiếp theo, bạn sẽ thấy cách truy xuất nhiều loại thực thể trong một yêu cầu duy nhất, điều này có thể giảm tổng số yêu cầu mạng mà bạn thực hiện trong ứng dụng và cải thiện hiệu suất ứng dụng.