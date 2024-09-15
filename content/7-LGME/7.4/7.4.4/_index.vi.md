---
title : "Quét sparse GSI"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 7.4.4. </b> "
---


Trong bước trước, bạn đã thấy cách tìm game cho một bản đồ cụ thể. Một số người chơi có thể thích chơi trên một bản đồ cụ thể, vì vậy điều này rất hữu ích. Những người chơi khác có thể sẵn sàng tham gia bất kỳ game nào trên bất kỳ bản đồ nào. Trong phần này, bạn sẽ học cách tìm bất kỳ game mở nào trong ứng dụng, bất kể loại bản đồ. Để làm điều này, bạn sử dụng API [Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html).

Thông thường, bạn không nên thiết kế bảng của mình để sử dụng thao tác `Scan` của DynamoDB vì DynamoDB được xây dựng cho các truy vấn chính xác để lấy những thực thể cần thiết. Thao tác `Scan` lấy ngẫu nhiên một tập hợp các thực thể trong bảng của bạn, vì vậy việc tìm kiếm các thực thể cần thiết có thể yêu cầu nhiều lần truy xuất đến cơ sở dữ liệu.

Tuy nhiên, đôi khi `Scan` có thể hữu ích. Ví dụ, khi bạn có một chỉ mục thứ cấp thưa (sparse secondary index), nghĩa là chỉ mục đó không có quá nhiều thực thể. Ngoài ra, chỉ mục chỉ bao gồm các game vẫn đang mở, và đó chính là những gì bạn cần.

Đối với trường hợp sử dụng này, `Scan` hoạt động rất tốt. Hãy xem cách nó hoạt động. Trong mã mà bạn đã tải xuống, tệp **find_open_games.py** nằm trong thư mục **scripts/**.

```python
import boto3
from entities import Game

dynamodb = boto3.client('dynamodb')

def find_open_games():
    resp = dynamodb.scan(
        TableName='battle-royale',
        IndexName="OpenGamesIndex",
    )

    games = [Game(item) for item in resp['Items']]

    return games

games = find_open_games()
print("Open games:")
for game in games:
    print(game)
```

Mã này tương tự như mã trong bước trước. Tuy nhiên, thay vì sử dụng phương thức `query()` trên client DynamoDB, bạn sử dụng phương thức `scan()`. Vì bạn đang sử dụng `scan()`, bạn không cần phải chỉ định bất kỳ điều gì về điều kiện khóa như đã làm với `query()`. DynamoDB sẽ trả về một nhóm các mục mà không có thứ tự cụ thể nào.

Chạy script với lệnh sau trong terminal:

```sh
python scripts/find_open_games.py
```

Terminal của bạn sẽ hiển thị danh sách chín game đang mở trên nhiều bản đồ khác nhau.

```text
Open games:
Game: c6f38a6a-d1c5-4bdf-8468-24692ccc4646   Map: Urban Underground
Game: d06af94a-2363-441d-a69b-49e3f85e748a   Map: Dirty Desert
Game: 873aaf13-0847-4661-ba26-21e0c66ebe64   Map: Dirty Desert
Game: fe89e561-8a93-4e08-84d8-efa88bef383d   Map: Dirty Desert
Game: 248dd9ef-6b17-42f0-9567-2cbd3dd63174   Map: Juicy Jungle
Game: 14c7f97e-8354-4ddf-985f-074970818215   Map: Green Grasslands
Game: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Map: Green Grasslands
Game: 683680f0-02b0-4e5e-a36a-be4e00fc93f3   Map: Green Grasslands
Game: 0ab37cf1-fc60-4d93-b72b-89335f759581   Map: Green Grasslands
```

Ngoài ra, bằng cách sử dụng [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), bạn có thể chạy một truy vấn `Scan index` để nhận được kết quả tương tự.  
Nhấp vào dấu ba chấm (...) bên cạnh `OpenGamesIndex` và chọn **Scan index**.

Trong bước này, bạn đã thấy rằng việc sử dụng thao tác `Scan` có thể là lựa chọn đúng đắn trong những tình huống cụ thể. Bạn đã sử dụng `Scan` để lấy một tập hợp các thực thể từ chỉ mục thứ cấp thưa (sparse global secondary index - GSI) để hiển thị các game mở cho người chơi.

### Tóm tắt

Trong module này, bạn đã thêm một chỉ mục thứ cấp toàn cục (GSI) vào bảng. Điều này đáp ứng hai mẫu truy cập bổ sung:

- Tìm game mở theo bản đồ (Đọc)
- Tìm game mở (Đọc)

Để thực hiện điều này, bạn đã sử dụng một chỉ mục thưa chỉ bao gồm các game vẫn đang mở cho người chơi bổ sung. Sau đó, bạn đã sử dụng cả hai API `Query` và `Scan` trên chỉ mục để tìm các game mở.

Trong module tiếp theo, bạn sẽ sử dụng các giao dịch DynamoDB khi thêm người chơi mới vào một game và đóng game khi đã đủ người chơi.
