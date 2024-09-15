---
title : "Access DynamoDB Table"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 1.5.7. </b> "
---

Amazon DynamoDB hỗ trợ [PartiQL](https://partiql.org/), một ngôn ngữ truy vấn tương thích với SQL, để chọn, chèn, cập nhật và xóa dữ liệu trong Amazon DynamoDB. Sử dụng PartiQL, bạn có thể dễ dàng tương tác với các bảng DynamoDB và chạy các truy vấn ad hoc bằng AWS Management Console. Trong bài tập này, chúng ta sẽ thực hành một số mẫu truy cập sử dụng các câu lệnh PartiQL.

1. Đăng nhập vào [DynamoDB console](https://console.aws.amazon.com/dynamodbv2/home) và chọn PartiQL editor từ thanh điều hướng bên trái.
2. Chọn bảng movies đã được tạo và tải bằng công việc của Database Migration Service. Chọn dấu ba chấm bên cạnh tên bảng và nhấp vào "scan table".  
   ![Final Deployment Architecture](/images/1/1.5/28.jpg)  
   Chúng ta sẽ sử dụng các tập lệnh PartiQL để minh họa tất cả 6 mẫu truy cập đã được thảo luận ở chương trước. Trong ví dụ của chúng ta, chúng tôi sẽ cung cấp cho bạn các giá trị khóa phân vùng, nhưng trong thực tế bạn sẽ cần tạo một chỉ mục của các khóa có thể bằng cách sử dụng GSI. Lấy thông tin chi tiết của bộ phim: Mỗi bộ phim IMDB có một tconst duy nhất. Bảng đã không chuẩn hóa được tạo ra với mỗi hàng đại diện cho một sự kết hợp duy nhất của bộ phim và đoàn làm phim, tức là tconst và nconst. Vì tconst là một phần của khóa phân vùng cho bảng cơ sở, nó có thể được sử dụng trong điều kiện WHERE để chọn chi tiết. Sao chép lệnh dưới đây để chạy bên trong PartiQL query editor.  
   ![Final Deployment Architecture](/images/1/1.5/29.png)

- Tìm tất cả các diễn viên và đoàn làm phim đã làm việc trong một bộ phim. Truy vấn dưới đây sẽ bao gồm diễn viên, diễn viên nữ, nhà sản xuất, nhà quay phim, v.v. đã làm việc trong một bộ phim cho trước.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'DETL|')
```

- Tìm chỉ các diễn viên đã làm việc trong một bộ phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'DETL|actor')
```

- Tìm chỉ thông tin chi tiết của một bộ phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'DETL|') and "ordering" = '1'
```

- Tìm tất cả các vùng, ngôn ngữ và tiêu đề của một bộ phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'REGN|')
```

- Tìm tiêu đề phim cho một vùng cụ thể của một bộ phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'REGN|NZ')
```

- Tìm tiêu đề gốc của một bộ phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'REGN|') and "types" = 'original'
```

Để truy cập thông tin ở cấp độ thành viên đoàn làm phim (#6 trong mẫu truy cập), chúng ta cần tạo một Chỉ mục Thứ cấp Toàn cầu bổ sung (GSI) với khóa phân vùng mới `nconst` (duy nhất cho thành viên đoàn làm phim). Điều này sẽ cho phép truy vấn trên khóa phân vùng mới cho GSI thay vì quét trên bảng cơ sở.

3. Chọn "Tables" từ thanh điều hướng bên trái, chọn bảng movies và nhấp vào tab "Index".
4. Nhấp vào "Create Index" và thêm các chi tiết sau.

|Tham số|Giá trị|
|---|---|
|Partition key|nconst|
|Data type|String|
|Sort key - optional|startYear|
|Data type|String|
|Attribute projections|All|

![Final Deployment Architecture](/images/1/1.5/30.jpg) ![Final Deployment Architecture](/images/1/1.5/31.jpg)

5. Cuối cùng, nhấp vào "Create Index". Quá trình này có thể mất một giờ tùy thuộc vào số lượng bản ghi trong bảng cơ sở.
6. Khi cột trạng thái GSI chuyển từ "Pending" sang "Available", quay lại PartiQL editor để thực thi truy vấn trên GSI.

- Tìm tất cả các bộ phim của một đoàn làm phim (như diễn viên, đạo diễn, v.v.)

```bash
  SELECT * FROM "movies"."nconst-startYear-index"
  WHERE "nconst" = 'nm0000142'
```

- Tìm tất cả các bộ phim của một đoàn làm phim với vai trò diễn viên từ năm 2002 và sắp xếp theo năm tăng dần.

```bash
  SELECT * FROM "movies"."nconst-startYear-index"
  WHERE "nconst" = 'nm0000142' and "startYear" >= '2002'
  ORDER BY "startYear"
```

Chúc mừng! Bạn đã hoàn thành bài tập di chuyển RDBMS.