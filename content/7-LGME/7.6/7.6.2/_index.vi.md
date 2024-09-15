---
title : "Truy xuất trò chơi cho người dùng"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 7.6.2. </b> "
---

Bây giờ bạn đã tạo chỉ mục đảo ngược, hãy sử dụng nó để truy xuất các thực thể `Game` mà một `User` đã chơi. Để xử lý điều này, bạn cần truy vấn chỉ mục đảo ngược với `User` mà bạn muốn xem các thực thể `Game` của họ.

Trong mã bạn đã tải xuống, tệp script **find_games_for_user.py** nằm trong thư mục **scripts/**.

```python
import sys
import boto3

from entities import UserGameMapping

dynamodb = boto3.client('dynamodb')

USERNAME = sys.argv[1] if len(sys.argv) == 2 else "carrpatrick"


def find_games_for_user(username):
    try:
        resp = dynamodb.query(
            TableName='battle-royale',
            IndexName='InvertedIndex',
            KeyConditionExpression="SK = :sk",
            ExpressionAttributeValues={
                ":sk": { "S": f"USER#{username}" }
            },
            ScanIndexForward=True
        )
    except Exception as e:
        print('Chỉ mục vẫn đang trong quá trình backfilling. Vui lòng thử lại sau.')
        return None

    return [UserGameMapping(item) for item in resp['Items']]

games = find_games_for_user(USERNAME)

if games:
    print(f"Các game đã chơi bởi người dùng {USERNAME}:")
    for game in games:
        print(game)
```

Trong script này, bạn có một hàm gọi là `find_games_for_user()` tương tự như một hàm bạn sẽ có trong ứng dụng game của mình. Hàm này nhận tên người dùng và trả về tất cả các game mà người dùng đã chơi.

Chạy script trong terminal của bạn với lệnh sau:

```sh
python scripts/find_games_for_user.py
```

Script sẽ in ra tất cả các game đã được chơi bởi người dùng carrpatrick.

```text
Các game đã chơi bởi người dùng carrpatrick:
UserGameMapping: 25cec5bf-e498-483e-9a00-a5f93b9ea7c7   Username: carrpatrick   Place: SILVER
UserGameMapping: c6f38a6a-d1c5-4bdf-8468-24692ccc4646   Username: carrpatrick
UserGameMapping: c9c3917e-30f3-4ba4-82c4-2e9a0e4d1cfd   Username: carrpatrick
```

Bạn có thể chạy script cho những người dùng khác bằng cách thêm tên người dùng của họ làm tham số dòng lệnh.  
Thử chạy script một lần nữa và tìm các game cho người dùng vlopez:

```sh
python scripts/find_games_for_user.py vlopez
```

Đầu ra sẽ trông như thế này:

```text
Các game đã chơi bởi người dùng vlopez:
UserGameMapping: c6f38a6a-d1c5-4bdf-8468-24692ccc4646   Username: vlopez
```

### Tóm tắt
Trong module này, bạn đã thỏa mãn mẫu truy cập cuối cùng bằng cách truy xuất tất cả các thực thể `Game` mà một `User` đã chơi. Để xử lý mẫu truy cập này, bạn đã tạo một chỉ mục thứ cấp sử dụng mẫu chỉ mục đảo ngược để cho phép truy vấn mối quan hệ nhiều-nhiều giữa các thực thể `User` và `Game`.
