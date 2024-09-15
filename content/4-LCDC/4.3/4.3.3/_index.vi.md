---
title : "Tạo Lambda Function"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.3.3. </b> "
---

Tạo một hàm Lambda để sao chép các bản ghi đã thay đổi từ DynamoDB streams của bảng Orders sang bảng OrdersHistory.

1. Mở **AWS Management Console** và truy cập vào bảng điều khiển **Lambda Service**.
2. Trong phần **Functions**, nhấp vào **Create function**.
3. Chọn **Author from scratch**.
4. Đặt **create-order-history-ddbs** làm tên hàm.
5. Chọn một phiên bản **Python** làm runtime.

![Trình hướng dẫn tạo hàm AWS Lambda](/images/4/4.3/2.png)

6. Mở rộng phần **Change default execution role**.
7. Chọn **Create a new role from AWS policy templates**.
8. Đặt **create-order-history-ddbs-execution-role** làm tên vai trò (role).
9. Chọn **Simple microservice permissions** từ menu tùy chọn **Policy templates**.

![Trình hướng dẫn tạo hàm AWS Lambda](/images/4/4.3/3.png)

10. Nhấp vào **Create function** và chờ để được chuyển hướng đến bảng điều khiển AWS Lambda cho hàm vừa tạo.
    
11. Thay thế nội dung của **lambda_function.py** bằng mã hàm dưới đây.
    

```python
import os
import boto3
import datetime
from aws_lambda_powertools import Logger
from aws_lambda_powertools.utilities.data_classes.dynamo_db_stream_event import (
    DynamoDBStreamEvent,
    DynamoDBRecordEventName
)
from botocore.exceptions import ClientError

table_name = os.getenv("ORDERS_HISTORY_DB")
logger = Logger()

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table(table_name)


def store_history_record(old_item_image):
    logger.debug({"operation": "store_history_record", "old_image": old_item_image})

    # Set a value for the partition key - pk, and sort key - sk; for the OrdersHistory table
    # before writing the record to the OrdersHistory table.
    pk = old_item_image["id"]
    sk = str(datetime.datetime.now())
    old_item_image["pk"] = pk
    old_item_image["sk"] = sk

    try:
        response = table.put_item(
            Item=old_item_image
        )
        logger.debug({"operation": "table.put_item", "response": response})
    except ClientError as err:
        logger.error({"operation": "store_history_record", "details": err})
        raise Exception(err)


def lambda_handler(event, context):
    logger.debug({"operation": "lambda_handler", "event": event, "context": context})
    event: DynamoDBStreamEvent = DynamoDBStreamEvent(event)

    for record in event.records:
        if record.event_name == DynamoDBRecordEventName.MODIFY or record.event_name == DynamoDBRecordEventName.REMOVE:
            logger.debug({"record": record})
            if "id" in record.dynamodb.keys:
                store_history_record(record.dynamodb.old_image)
            else:
                raise ValueError("Expected partition key attribute - 'id' not found.")

```

{{%notice info%}}

| Số dòng | Mô tả                                                                                                                                                                                                                                    |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 11 - 15 | Lấy tên bảng Orders History DynamoDB từ một biến môi trường, tạo một logger cho hàm Lambda và sau đó tạo một resource boto3 để tương tác với bảng Orders History DynamoDB.                                                               |
| 23 - 26 | Tạo một mục mới cho bảng Orders History sử dụng hình ảnh cũ của một mục đã được cập nhật trên bảng Orders. Đặt khóa phân vùng cho mục mới thành `id` của đơn hàng đã cập nhật và đặt khóa sắp xếp cho mục mới thành thời gian hiện tại. |
| 28 - 35 | Ghi mục mới vào bảng Orders History. Ném ra ngoại lệ nếu mục không được ghi thành công vào bảng.                                                                                                                                        |
| 42 - 45 | Xử lý hình ảnh cũ của sự kiện cập nhật mục và sự kiện xóa mục nhận được từ bảng Orders DynamoDB thông qua DynamoDB streams.                                                                                                               |


{{%/notice%}}

Hàm Lambda này nhận các sự kiện từ DynamoDB streams và ghi các mục mới vào bảng DynamoDB, cụ thể là bảng OrdersHistory.

Vì chúng ta chỉ cần ghi lại các thay đổi đối với các mục trên bảng Orders, hàm Lambda được thiết lập để chỉ xử lý các sự kiện stream cho các mục đã được sửa đổi và xóa từ bảng Orders.

12. Triển khai các thay đổi mã cho hàm của bạn bằng cách chọn **Deploy**.

![Trình hướng dẫn tạo hàm AWS Lambda](/images/4/4.3/4.png)

Đừng thực thi hàm Lambda mà bạn vừa tạo. Cần có thêm cấu hình để thiết lập hoạt động đúng cách. Bạn sẽ cập nhật cấu hình hàm Lambda trong bước tiếp theo.