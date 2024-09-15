---
title : "Truy vấn sparse GSI"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 7.4.3. </b> "
---

Bây giờ bạn đã cấu hình GSI, hãy sử dụng nó để thỏa mãn một số mẫu truy cập.

Để sử dụng chỉ mục thứ cấp, có hai API có sẵn: `Query` và `Scan`. Với `Query`, bạn phải chỉ định khóa phân vùng (partition key), và nó sẽ trả về kết quả mục tiêu. Với `Scan`, bạn không cần chỉ định khóa phân vùng và thao tác này sẽ quét toàn bộ bảng của bạn. Trong DynamoDB, quét dữ liệu (Scan) thường không được khuyến khích trừ trong những trường hợp cụ thể vì chúng truy cập **mọi** mục trong cơ sở dữ liệu. Nếu bạn có một lượng lớn dữ liệu trong bảng, việc quét có thể mất rất nhiều thời gian. Ở bước tiếp theo, bạn sẽ thấy tại sao quét lại là một công cụ mạnh mẽ khi sử dụng với các chỉ mục thưa (sparse indexes).

Bạn có thể sử dụng API `Query` trên chỉ mục thứ cấp toàn cục (GSI) mà bạn đã tạo ở bước trước để tìm tất cả các game mở theo tên bản đồ. GSI được phân vùng bởi tên `map`, cho phép bạn truy vấn có mục tiêu để tìm các game mở.

Trong mã bạn đã tải xuống, tệp **find_open_games_by_map.py** có trong thư mục **scripts/**.

```python
import sys
import boto3
from entities import Game

dynamodb = boto3.client('dynamodb')

MAP_NAME = sys.argv[1] if len(sys.argv) == 2 else "Green Grasslands"

def find_open_games_by_map(map_name):
    resp = dynamodb.query(
        TableName='battle-royale',
        IndexName="OpenGamesIndex",
        KeyConditionExpression="#map = :map",
        ExpressionAttributeNames={
            "#map": "map"
        },
        ExpressionAttributeValues={
            ":map": { "S": map_name },
        },
        ScanIndexForward=True
    )

    games = [Game(item) for item in resp['Items']]

    return games

games = find_open_games_by_map(MAP_NAME)
print(f"Open games for map: {MAP_NAME}:")
for game in games:
    print(game)
```

Trong script trên, hàm `find_open_games_by_map` tương tự như một hàm mà bạn sẽ có trong ứng dụng của mình. Hàm này nhận một tên bản đồ và thực hiện truy vấn trên `OpenGamesIndex` để tìm tất cả các game mở cho bản đồ đó. Sau đó, nó lắp ráp các thực thể trả về thành các đối tượng `Game` có thể sử dụng trong ứng dụng của bạn.

Thực thi script này bằng cách chạy lệnh sau trong terminal:

```sh
python scripts/find_open_games_by_map.py
```

Terminal sẽ hiển thị đầu ra với bốn game mở cho bản đồ Green Grasslands.

```text
Open games for map: Green Grasslands:
Game: 14c7f97e-8354-4ddf-985f-074970818215   Map: Green Grasslands
Game: 3d4285f0-e52b-401a-a59b-112b38c4a26b   Map: Green Grasslands
Game: 683680f0-02b0-4e5e-a36a-be4e00fc93f3   Map: Green Grasslands
Game: 0ab37cf1-fc60-4d93-b72b-89335f759581   Map: Green Grasslands
```

Bạn có thể chạy lại script Python và sử dụng một tên bản đồ cụ thể. Hãy thử chạy mã dưới đây cho bản đồ có tên là `Dirty Desert`.

```sh
python scripts/find_open_games_by_map.py "Dirty Desert"
```

Terminal sẽ hiển thị đầu ra với ba game mở cho bản đồ Dirty Desert.

```text
Open games for map: Dirty Desert:
Game: d06af94a-2363-441d-a69b-49e3f85e748a   Map: Dirty Desert
Game: 873aaf13-0847-4661-ba26-21e0c66ebe64   Map: Dirty Desert
Game: fe89e561-8a93-4e08-84d8-efa88bef383d   Map: Dirty Desert
```

Ngoài ra, bằng cách sử dụng [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), bạn có thể chạy ngôn ngữ truy vấn tương thích SQL để truy xuất các mục từ bảng và chỉ mục của DynamoDB.

Bạn có thể truy cập trình chỉnh sửa PartiQL từ menu bên trái sau khi điều hướng đến dịch vụ DynamoDB trong AWS console dưới mục **Services**, **Database**, **DynamoDB**, và chạy một truy vấn để nhận được kết quả tương tự.

Trong cửa sổ truy vấn, bạn có thể chạy câu lệnh SQL dưới đây. Bạn sẽ thấy bốn game mở cho bản đồ Green Grasslands giống như trên:

```sql
SELECT * FROM "battle-royale"."OpenGamesIndex"
WHERE map = 'Green Grasslands'
```

Bạn có thể thay đổi tên bản đồ trong câu lệnh `WHERE` để xem các game mở cho các bản đồ khác. Ví dụ, hãy kiểm tra xem có bao nhiêu game mở cho bản đồ có tên là **Juicy Jungle**.

```sql
SELECT * FROM "battle-royale"."OpenGamesIndex"
WHERE map = 'Juicy Jungle'
```