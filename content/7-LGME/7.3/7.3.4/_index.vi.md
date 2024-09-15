---
title : "Truy xuất Item collentions"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 7.3.4. </b> "
---

Một **Item collection (Bộ sưu tập mục)** là một tập hợp các mục có cùng giá trị **partition key (khóa phân vùng)** nhưng có các giá trị **sort key (khóa sắp xếp)** khác nhau, nơi mà các mục của các loại thực thể khác nhau có mối quan hệ với khóa phân vùng.

Như bạn đã đọc trong mô-đun trước, bạn nên tối ưu hóa bảng DynamoDB để giảm số lượng yêu cầu mà nó nhận được. Cũng đã đề cập rằng DynamoDB không có phép nối (joins) như cơ sở dữ liệu quan hệ. Thay vào đó, bạn thiết kế bảng của mình để cho phép hành vi tương tự như phép nối trong các yêu cầu của bạn.

Trong bước này, bạn sẽ truy xuất nhiều loại thực thể trong một yêu cầu duy nhất. Trong ứng dụng trò chơi, bạn có thể muốn lấy chi tiết về một trò chơi. Các chi tiết này bao gồm thông tin về trò chơi như thời gian bắt đầu, thời gian kết thúc, và chi tiết về người dùng đã tham gia trò chơi.

Yêu cầu này bao gồm hai loại thực thể: thực thể `Game` và thực thể `UserGameMapping`. Tuy nhiên, điều này không có nghĩa là bạn cần thực hiện nhiều yêu cầu.

Trong mã mà bạn đã tải xuống, có một script **fetch_game_and_players.py** trong thư mục **scripts/**. Script này cho thấy cách bạn có thể cấu trúc mã của mình để truy xuất cả thực thể `Game` và thực thể `UserGameMapping` cho trò chơi trong một yêu cầu duy nhất.

Dưới đây là mã tạo thành script **fetch_game_and_players.py**:

```python
import boto3
from entities import Game, UserGameMapping

dynamodb = boto3.client('dynamodb')

GAME_ID = "3d4285f0-e52b-401a-a59b-112b38c4a26b"


def fetch_game_and_users(game_id):
    resp = dynamodb.query(
        TableName='battle-royale',
        KeyConditionExpression="PK = :pk AND SK BETWEEN :metadata AND :users",
        ExpressionAttributeValues={
            ":pk": { "S": "GAME#{}".format(game_id) },
            ":metadata": { "S": "#METADATA#{}".format(game_id) },
            ":users": { "S": "USER$" },
        },
        ScanIndexForward=True
    )

    game = Game(resp['Items'][0])
    game.users = [UserGameMapping(item) for item in resp['Items'][1:]]

    return game


game = fetch_game_and_users(GAME_ID)

print(game)
for user in game.users:
    print(user)
```

Ở phần đầu của script này, bạn import thư viện Boto 3 và một số lớp đơn giản để đại diện cho các đối tượng trong mã ứng dụng. Bạn có thể xem định nghĩa cho các thực thể đó trong tệp **scripts/entities.py**.

Phần quan trọng của script diễn ra trong hàm `fetch_game_and_users` được định nghĩa trong mô-đun. Đây tương tự như một hàm mà bạn sẽ định nghĩa trong ứng dụng của mình để được sử dụng bởi bất kỳ endpoint nào cần dữ liệu này.

Hàm `fetch_game_and_users` thực hiện một số việc. Đầu tiên, nó thực hiện một yêu cầu `Query` tới DynamoDB. Yêu cầu `Query` này sử dụng `PK` của `GAME#<GameId>`. Sau đó, nó yêu cầu bất kỳ thực thể nào mà **sort key** nằm giữa `#METADATA#<GameId>` và `USER$`. Điều này lấy thực thể `Game`, với **sort key** là `#METADATA#<GameId>`, và tất cả các thực thể `UserGameMapping`, với các khóa bắt đầu bằng `USER#`. Các **sort key** của loại chuỗi được sắp xếp theo mã ký tự ASCII. Dấu đô la ($) đến ngay sau dấu thăng (#) trong [ASCII](http://support.ecisolutions.com/doc-ddms/help/reportsmenu/ascii_sort_order_chart.htm), vì vậy điều này đảm bảo rằng bạn sẽ lấy tất cả các bản đồ trong thực thể `UserGameMapping`.

Khi bạn nhận được phản hồi, bạn lắp ráp các thực thể dữ liệu thành các đối tượng được biết đến bởi ứng dụng. Bạn biết rằng thực thể đầu tiên được trả về là thực thể `Game`, vì vậy bạn tạo một đối tượng `Game` từ thực thể này. Đối với các thực thể còn lại, bạn tạo một đối tượng `UserGameMapping` cho mỗi thực thể và sau đó đính kèm mảng người dùng vào đối tượng `Game`.

Phần cuối của script cho thấy cách sử dụng hàm và in ra các đối tượng kết quả.

Bạn có thể chạy script trong Terminal của Cloud9 với lệnh sau:

```sh
python scripts/fetch_game_and_players.py
```

Script sẽ in đối tượng `Game` và tất cả các đối tượng `UserGameMapping` ra console.

```text
Game: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Map: Green Grasslands
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: branchmichael
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: deanmcclure
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: emccoy
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: emma83
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: iherrera
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: jeremyjohnson
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: lisabaker
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: maryharris
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: mayrebecca
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: meghanhernandez
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: nruiz
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: pboyd
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: richardbowman
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: roberthill
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: robertwood
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: victoriapatrick
UserGameMapping: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Username: waltervargas
```

Script này cho thấy cách bạn có thể mô hình hóa bảng của mình và viết các truy vấn để truy xuất nhiều loại thực thể trong một yêu cầu DynamoDB duy nhất. Trong một cơ sở dữ liệu quan hệ, bạn sử dụng các phép nối để truy xuất nhiều loại thực thể từ các bảng khác nhau trong một yêu cầu. Với DynamoDB, bạn mô hình hóa dữ liệu của mình một cách cụ thể, sao cho các thực thể mà bạn nên truy cập cùng nhau được đặt cạnh nhau trong một bảng duy nhất. Cách tiếp cận này thay thế nhu cầu sử dụng phép nối trong cơ sở dữ liệu quan hệ điển hình và giữ cho ứng dụng của bạn có hiệu suất cao khi mở rộng.

### Đánh giá

Trong mô-đun này, bạn đã thiết kế một khóa chính và tạo một bảng. Sau đó, bạn đã tải dữ liệu vào bảng và thấy cách truy vấn cho nhiều loại thực thể trong một yêu cầu duy nhất.

Với thiết kế khóa chính hiện tại, bạn có thể đáp ứng các mẫu truy cập sau:

- Tạo hồ sơ người dùng (Ghi)
  
- Cập nhật hồ sơ người dùng (Ghi)
  
- Lấy hồ sơ người dùng (Đọc)
  
- Tạo trò chơi (Ghi)
  
- Xem trò chơi (Đọc)
  
- Tham gia trò chơi cho một người dùng (Ghi)
  
- Bắt đầu trò chơi (Ghi)
  
- Cập nhật trò chơi cho một người dùng (Ghi)
  
- Cập nhật trò chơi (Ghi)