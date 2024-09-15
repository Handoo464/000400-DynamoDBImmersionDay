---
title : "Thêm người dùng vào trò chơi"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 7.5.1. </b> "
---

Mẫu truy cập đầu tiên mà bạn giải quyết trong module này là thêm người dùng mới vào một game.

Khi thêm một người dùng mới vào game, bạn cần:

- Xác nhận rằng hiện chưa có 50 người chơi trong game (mỗi game có tối đa 50 người chơi).
- Xác nhận rằng người dùng này chưa tham gia game.
- Tạo một thực thể `UserGameMapping` mới để thêm người dùng vào game.
- Tăng thuộc tính `people` trên thực thể `Game` để theo dõi số lượng người chơi trong game.

Lưu ý rằng để thực hiện tất cả những điều này, bạn cần các hành động ghi trên thực thể `Game` hiện có và thực thể `UserGameMapping` mới, cũng như logic điều kiện cho từng thực thể. Đây là loại thao tác hoàn hảo để sử dụng giao dịch DynamoDB vì bạn cần thao tác trên nhiều thực thể trong cùng một yêu cầu và bạn muốn toàn bộ yêu cầu thành công hoặc thất bại cùng nhau.

Trong mã bạn đã tải xuống, tệp script **join_game.py** nằm trong thư mục **scripts/**. Hàm trong tệp này sử dụng giao dịch DynamoDB để thêm người dùng vào game.

```python
import boto3

dynamodb = boto3.client('dynamodb')

GAME_ID = "c6f38a6a-d1c5-4bdf-8468-24692ccc4646"
USERNAME = 'vlopez'


def join_game_for_user(game_id, username):
    try:
        resp = dynamodb.transact_write_items(
            TransactItems=[
                {
                    "Put": {
                        "TableName": "battle-royale",
                        "Item": {
                            "PK": {"S": f"GAME#{game_id}" },
                            "SK": {"S": f"USER#{username}" },
                            "game_id": {"S": game_id },
                            "username": {"S": username }
                        },
                        "ConditionExpression": "attribute_not_exists(SK)",
                        "ReturnValuesOnConditionCheckFailure": "ALL_OLD"
                    },
                },
                {
                    "Update": {
                        "TableName": "battle-royale",
                        "Key": {
                            "PK": { "S": f"GAME#{game_id}" },
                            "SK": { "S": f"#METADATA#{game_id}" },
                        },
                        "UpdateExpression": "SET people = people + :p",
                        "ConditionExpression": "people < :limit",
                        "ExpressionAttributeValues": {
                            ":p": { "N": "1" },
                            ":limit": { "N": "50" }
                        },
                        "ReturnValuesOnConditionCheckFailure": "ALL_OLD"
                    }
                }
            ]
        )
        print(f"Đã thêm người dùng: {username} vào game: {game_id}")
        return True
    except Exception as e:
        print("Không thể thêm người dùng vào game")

join_game_for_user(GAME_ID, USERNAME)
```

Trong hàm `join_game_for_user` của script này, phương thức `transact_write_items()` thực hiện một giao dịch ghi. Giao dịch này có hai thao tác.

Trong thao tác đầu tiên của giao dịch, bạn sử dụng thao tác `Put` để chèn một thực thể `UserGameMapping` mới. Là một phần của thao tác đó, bạn chỉ định điều kiện rằng thuộc tính `SK` không được tồn tại cho thực thể này. Điều này đảm bảo rằng không có thực thể nào có `PK` và `SK` này đã tồn tại. Nếu đã có thực thể như vậy, nghĩa là người dùng này đã tham gia game.

Thao tác thứ hai là một thao tác `Update` trên thực thể `Game` để tăng thuộc tính `people` lên một. Là một phần của thao tác này, bạn thêm một kiểm tra điều kiện rằng giá trị hiện tại của `people` không lớn hơn 50. Ngay khi có 50 người tham gia game, game đã đầy và sẵn sàng bắt đầu.

Trước khi thêm `vlopez` vào game, bạn có thể xác minh số người dùng hiện có trong game bằng cách truy vấn GSI thưa mà chúng ta đã tạo. Trong bảng điều khiển AWS DynamoDB, chọn `Explore items` ở bên trái và lọc bảng có tên `Battle Royale`. Chọn **Query** và sau đó chọn GSI có tên **OpenGamesIndex** từ danh sách thả xuống. Chỉ định `Urban Underground` là giá trị cho `map (Partition Key)` và nhấp vào nút màu cam **Run**. Bạn sẽ thấy một mục được trả về với giá trị 49 cho thuộc tính `people`.

![Truy vấn GSI thưa từ bảng điều khiển DynamoDB](/images/7/7.5/1.png)

{{%notice tip%}}
Bạn có thể chọn chạy script Python `join_game.py` hoặc lệnh AWS CLI dưới đây. Cả hai đều được cung cấp để hiển thị các phương pháp khác nhau để tương tác với DynamoDB.
{{%/notice%}}

Chạy script này bằng lệnh sau trong terminal của bạn:

```sh
python scripts/join_game.py
```

Đầu ra trong terminal của bạn sẽ cho biết rằng người dùng đã được thêm vào game.

```text
Đã thêm người dùng: vlopez vào game: c6f38a6a-d1c5-4bdf-8468-24692ccc4646
```

Bạn có thể quay lại bảng điều khiển DynamoDB và nhấp vào `Run` một lần nữa để truy vấn GSI và bạn sẽ thấy rằng thuộc tính `people` bây giờ hiển thị **50**.

Lưu ý rằng nếu bạn cố gắng chạy lại script, hàm sẽ thất bại. Người dùng `vlopez` đã được thêm vào game, vì vậy việc cố gắng thêm người dùng này một lần nữa không thỏa mãn các điều kiện bạn đã chỉ định.

Ngoài ra, bạn cũng có thể gửi giao dịch qua AWS CLI.  
Chạy lệnh sau để thêm người dùng `ebarton` vào game sử dụng bản đồ `Juicy Jungle`:

```sh
aws dynamodb transact-write-items \
--transact-items \
"[
  {
    \"Put\": {
      \"TableName\": \"battle-royale\",
      \"Item\": {
        \"PK\": {\"S\": \"GAME#248dd9ef-6b17-42f0-9567-2cbd3dd63174\" },
        \"SK\": {\"S\": \"USER#ebarton\" },
        \"game_id\": {\"S\": \"248dd9ef-6b17-42f0-9567-2cbd3dd63174\" },
        \"username\": {\"S\": \"ebarton\" }
      },
      \"ConditionExpression\": \"attribute_not_exists(SK)\",
      \"ReturnValuesOnConditionCheckFailure\": \"ALL_OLD\"
    }
  },
  {
    \"Update\": {
      \"TableName\": \"battle-royale\",
      \"Key\": {
        \"PK\": { \"S\": \"GAME#248dd9ef-6b17-42f0-9567-2cbd3dd63174\" },
        \"SK\": { \"S\": \"#METADATA#248dd9ef-6b17-42f0-9567-2cbd3dd63174\" }
      },
      \"UpdateExpression\": \"SET people = people + :p\",
      \"ConditionExpression\": \"people < :limit\",
      \"ExpressionAttributeValues\": {
        \":p\": { \"N\": \"1\" },
        \":limit\": { \"N\": \"50\" }
      },
      \"ReturnValuesOnConditionCheckFailure\": \"ALL_OLD\"
    }
  }
]"
```

Việc thêm giao dịch DynamoDB giúp đơn giản hóa rất nhiều quy trình làm việc xung quanh các thao tác phức tạp như thế này. Nếu không có giao dịch, việc này sẽ yêu cầu nhiều lệnh API với các điều kiện phức tạp và phải hoàn tác thủ công trong trường hợp xảy ra xung đột. Bây giờ, bạn có thể triển khai các thao tác phức tạp như vậy với ít hơn 50 dòng mã.

