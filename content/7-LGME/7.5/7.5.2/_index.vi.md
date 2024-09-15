---
title : "Bắt đầu trò chơi"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 7.5.2. </b> "
---

Ngay khi một game có 50 người chơi, người tạo game có thể bắt đầu game để khởi động quá trình chơi. Trong bước này, bạn sẽ học cách xử lý mẫu truy cập này.

Khi hệ thống backend của ứng dụng nhận được yêu cầu bắt đầu game, bạn cần kiểm tra ba điều:

- Game đã có 50 người đăng ký.
- Người dùng yêu cầu bắt đầu game là người tạo ra game.
- Game chưa được bắt đầu.

Bạn có thể xử lý mỗi kiểm tra này bằng một [điều kiện biểu thức](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html) trong yêu cầu cập nhật game. Nếu tất cả các điều kiện này được thỏa mãn, bạn cần cập nhật thực thể theo các cách sau:

- Xóa thuộc tính `open_timestamp` để nó không còn xuất hiện dưới dạng game mở trong GSI thưa (sparse GSI) mà bạn đã tạo trước đó.
- Thêm thuộc tính `start_time` để chỉ ra thời điểm game bắt đầu.

Trong mã bạn đã tải xuống, tệp script **start_game.py** nằm trong thư mục **scripts/**.

```python
import datetime
import boto3
from entities import Game

dynamodb = boto3.client('dynamodb')

GAME_ID = "c6f38a6a-d1c5-4bdf-8468-24692ccc4646"
CREATOR = "gstanley"

def start_game(game_id, requesting_user, start_time):
    try:
        resp = dynamodb.update_item(
            TableName='battle-royale',
            Key={
                "PK": { "S": f"GAME#{game_id}" },
                "SK": { "S": f"#METADATA#{game_id}" }
            },
            UpdateExpression="REMOVE open_timestamp SET start_time = :time",
            ConditionExpression="people = :limit AND creator = :requesting_user AND attribute_not_exists(start_time)",
            ExpressionAttributeValues={
                ":time": { "S": start_time.isoformat() },
                ":limit": { "N": "50" },
                ":requesting_user": { "S": requesting_user }
            },
            ReturnValues="ALL_NEW"
        )
        return Game(resp['Attributes'])
    except Exception as e:
        print('Không thể bắt đầu game')
        return False

game = start_game(GAME_ID, CREATOR, datetime.datetime(2019, 4, 16, 10, 15, 35))

if game:
    print(f"Đã bắt đầu game: {game}")
```

Trong script này, hàm `start_game` tương tự như một hàm bạn sẽ có trong ứng dụng của mình. Nó nhận `game_id`, `requesting_user`, và `start_time`, và thực hiện một yêu cầu để cập nhật thực thể `Game` để bắt đầu game.

Tham số `ConditionExpression` trong cuộc gọi `update_item()` chỉ định các kiểm tra như đã liệt kê trước đó trong bước này — game phải có 50 người, người yêu cầu bắt đầu game phải là người tạo ra game, và game không thể có thuộc tính `start_time`, tức là game chưa bắt đầu.

Trong tham số `UpdateExpression`, bạn có thể thấy các thay đổi mà bạn muốn thực hiện với thực thể. Trước tiên bạn xóa thuộc tính `open_timestamp` khỏi thực thể, và sau đó bạn đặt thuộc tính `start_time` thành thời gian bắt đầu của game.

{{%notice tip%}}
Bạn có thể chọn chạy script Python `start_game.py` hoặc lệnh AWS CLI dưới đây. Cả hai đều được cung cấp để hiển thị các phương pháp khác nhau để tương tác với DynamoDB.
{{%/notice%}}

Chạy script này trong terminal của bạn bằng lệnh sau:

```shell
python scripts/start_game.py
```

Bạn sẽ thấy đầu ra trong terminal của mình cho biết rằng game đã bắt đầu thành công.

```text
Đã bắt đầu game: Game: c6f38a6a-d1c5-4bdf-8468-24692ccc4646   Map: Urban Underground
```

Thử chạy script một lần nữa trong terminal của bạn. Lần này, bạn sẽ thấy thông báo lỗi chỉ ra rằng bạn không thể bắt đầu game. Điều này là do bạn đã bắt đầu game rồi, vì vậy thuộc tính `start_time` đã tồn tại. Kết quả là, yêu cầu đã không vượt qua kiểm tra điều kiện trên thực thể.

Ngoài ra, bạn có thể chạy lệnh AWS CLI sau để bắt đầu game:

```sh
aws dynamodb update-item \
--table-name battle-royale \
--key \
"{
  \"PK\": { \"S\": \"GAME#c6f38a6a-d1c5-4bdf-8468-24692ccc4646\" },
  \"SK\": { \"S\": \"#METADATA#c6f38a6a-d1c5-4bdf-8468-24692ccc4646\" }
}" \
--update-expression "REMOVE open_timestamp SET start_time = :time" \
--condition-expression \
"people = :limit 
  AND creator = :requesting_user 
  AND attribute_not_exists(start_time)" \
--expression-attribute-values \
"{
  \":time\": { \"S\": \"2019-04-16T10:15:35\" },
  \":limit\": { \"N\": \"50\" },
  \":requesting_user\": { \"S\": \"gstanley\" }
}" \
--return-values "ALL_NEW"
```

Nếu bạn chạy lệnh AWS CLI, bạn sẽ thấy các giá trị mới của mục mà bạn đã cập nhật và bạn sẽ nhận thấy rằng không còn thuộc tính `open_timestamp`, nhưng có một thuộc tính tên là `start_time`.

```json
{
  "Attributes": {
    "creator": {
      "S": "gstanley"
    },
    "people": {
      "N": "50"
    },
    "SK": {
      "S": "#METADATA#c6f38a6a-d1c5-4bdf-8468-24692ccc4646"
    },
    "create_time": {
      "S": "2019-04-16T10:12:54"
    },
    "map": {
      "S": "Urban Underground"
    },
    "start_time": {
      "S": "2019-04-16T10:15:35"
    },
    "PK": {
      "S": "GAME#c6f38a6a-d1c5-4bdf-8468-24692ccc4646"
    },
    "game_id": {
      "S": "c6f38a6a-d1c5-4bdf-8468-24692ccc4646"
    }
  }
}
```

### Tóm tắt

Trong module này, bạn đã thấy cách thỏa mãn hai thao tác ghi nâng cao trong ứng dụng. Đầu tiên, bạn sử dụng giao dịch DynamoDB khi một người dùng tham gia game. Với giao dịch, bạn đã xử lý một thao tác ghi điều kiện phức tạp trên nhiều thực thể trong một yêu cầu duy nhất.

Thứ hai, bạn đã triển khai chức năng cho người tạo game để bắt đầu game khi nó đã sẵn sàng. Trong mẫu truy cập này, bạn đã thực hiện thao tác cập nhật yêu cầu kiểm tra giá trị của ba thuộc tính và cập nhật hai thuộc tính. Bạn có thể diễn đạt logic phức tạp này trong một yêu cầu duy nhất thông qua sức mạnh của các điều kiện và biểu thức cập nhật.

Trong module tiếp theo, bạn sẽ xem xét mẫu truy cập cuối cùng, liên quan đến việc xem các game đã diễn ra trong ứng dụng.

