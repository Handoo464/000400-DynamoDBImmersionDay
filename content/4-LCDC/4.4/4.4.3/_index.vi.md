---
title : "Tạo hàm Lambda"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 4.4.3. </b> "
---

Tạo một hàm Lambda để sao chép các bản ghi đã thay đổi từ DynamoDB streams của bảng Orders sang bảng OrdersHistory.

1. Mở **AWS Management Console** và truy cập vào trang tổng quan dịch vụ **Lambda**.
2. Trong mục **Functions**, nhấp vào **Create function**.
3. Chọn **Author from scratch**.
4. Đặt **create-order-history-kds** làm tên hàm.
5. Chọn **Python 3.11** làm runtime.
6. Mở rộng phần **Change default execution role**.
7. Chọn **Create a new role from AWS policy templates**.
8. Đặt **create-order-history-kds-execution-role** làm tên vai trò.
9. Chọn **Simple microservice permissions** từ menu tùy chọn **Policy templates**.
10. Nhấp vào **Create function** và chờ để được chuyển hướng đến bảng điều khiển AWS Lambda cho hàm vừa được tạo.
11. Thay thế nội dung của **lambda_function.py** bằng mã hàm dưới đây.

```python
import os
import boto3
import datetime
from aws_lambda_powertools import Logger
from aws_lambda_powertools.utilities.data_classes import event_source, KinesisStreamEvent
from botocore.exceptions import ClientError

table_name = os.getenv("ORDERS_HISTORY_DB")
logger = Logger()

client = boto3.client('dynamodb')


def store_history_record(old_order_image):
    logger.debug({"Saving order history"})

    # Đặt giá trị cho khóa phân vùng - pk, và khóa sắp xếp - sk; cho bảng OrderHistory
    # trước khi ghi bản ghi vào bảng OrderHistory.
    pk = old_order_image["id"]
    sk = str(datetime.datetime.now())
    old_order_image["pk"] = pk
    old_order_image["sk"] = {
        "S": sk
    }
    logger.debug({"old_image": old_order_image})

    try:
        response = client.put_item(
            TableName=table_name,
            Item=old_order_image
        )
        logger.debug({"operation": "table.put_item", "response": response})
    except ClientError as err:
        logger.error({"operation": "store_history_record", "details": err})
        raise Exception(err)


@event_source(data_class=KinesisStreamEvent)
def lambda_handler(event: KinesisStreamEvent, context):
    logger.debug({"operation": "lambda_handler", "event": event, "context": context})

    kinesis_record = next(event.records).kinesis
    data = kinesis_record.data_as_json()
    if data["eventName"] == "MODIFY" or data["eventName"] == "REMOVE":
        logger.debug({"data": data})
        if "dynamodb" in data:
            if "Keys" in data["dynamodb"]:
                if "id" not in data["dynamodb"]["Keys"]:
                    raise ValueError("Expected partition key attribute - 'id' not found.")
            if "OldImage" in data["dynamodb"]:
                store_history_record(data["dynamodb"]["OldImage"])
```

Phần này giải thích mã hàm:

| Số dòng | Mô tả                                                                                                                                                                                                                                   |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 8 - 11      | Lấy tên bảng Orders History DynamoDB từ một biến môi trường, khởi tạo logger cho hàm Lambda, sau đó tạo một client boto3 để tương tác với các bảng DynamoDB.                                                        |
| 19 - 24     | Tạo một mục mới cho bảng Orders History sử dụng ảnh cũ của một mục được cập nhật trên bảng Orders. Đặt khóa phân vùng cho mục mới là `id` của đơn hàng được cập nhật, sau đó đặt khóa sắp xếp cho mục mới là thời gian hiện tại. |
| 27 - 35     | Ghi mục mới vào bảng Orders History. Ném ra một ngoại lệ nếu mục không được ghi thành công vào bảng.                                                                                                                      |
| 42 - 48     | Xử lý các ảnh cũ của sự kiện cập nhật mục và sự kiện xóa mục nhận được từ luồng dữ liệu Orders Kinesis.                                                                                                                                   |

Hàm Lambda này nhận các sự kiện từ luồng dữ liệu Orders Kinesis và ghi chúng vào bảng OrdersHistory của DynamoDB.

Vì chúng ta chỉ cần ghi lại các thay đổi đối với các mục trên bảng Orders, hàm Lambda được cài đặt để chỉ xử lý các sự kiện luồng Kinesis cho các mục đã được sửa đổi và xóa từ bảng Orders.

12. Triển khai các thay đổi mã cho hàm của bạn bằng cách chọn **Deploy**.

![Trình tạo hàm AWS Lambda](/images/4/4.4/2.png)