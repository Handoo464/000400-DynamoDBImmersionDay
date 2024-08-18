---
title : "Access DynamoDB Table"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 1.5.7. </b> "
---

Hỗ trợ Amazon DynamoDB [PartiQL](https://partiql.org/) , một ngôn ngữ truy vấn tương thích với SQL, để chọn, chèn, cập nhật và xóa dữ liệu trong Amazon DynamoDB. Sử dụng PartiQL, bạn có thể dễ dàng tương tác với các bảng DynamoDB và chạy các truy vấn đặc biệt bằng Bảng điều khiển quản lý AWS. Trong bài tập này, chúng ta sẽ thực hành một vài mẫu hình truy cập bằng cách sử dụng các câu lệnh PartiQL.

1. Đăng nhập vào [Bảng điều khiển DynamoDB](https://console.aws.amazon.com/dynamodbv2/home)  và chọn trình chỉnh sửa PartiQL từ điều hướng bên trái.
2. Chọn bảng phim đã được tạo và tải bởi tác vụ Dịch vụ Di chuyển Cơ sở dữ liệu. Chọn dấu chấm lửng bên cạnh tên bảng và nhấp vào bảng quét. ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration28.jpg) Chúng tôi sẽ sử dụng các tập lệnh PartiQL để chứng minh tất cả 6 mẫu hình truy cập được thảo luận ở chương trước. Đối với ví dụ của chúng tôi, chúng tôi sẽ cung cấp cho bạn các giá trị khóa phân vùng, nhưng trong cuộc sống thực, bạn sẽ cần tạo chỉ mục các khóa có thể sử dụng GSI. Nhận thông tin chi tiết theo phim: Mỗi bộ phim IMDB có một tconst duy nhất. Bảng không chuẩn hóa được tạo với mỗi hàng đại diện cho sự kết hợp duy nhất giữa phim và đoàn làm phim, tức là tconst và nconst. Vì tconst là một phần của khóa phân vùng cho bảng cơ sở, nó có thể sử dụng trong điều kiện WHERE để chọn chi tiết. Sao chép lệnh bên dưới để chạy bên trong trình soạn thảo truy vấn PartiQL. ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration35.png)

- Tìm tất cả các diễn viên và đoàn làm phim đã làm việc trong một bộ phim. Truy vấn dưới đây sẽ bao gồm diễn viên, nữ diễn viên, nhà sản xuất, nhà quay phim, v.v. đã làm việc trong một bộ phim nhất định.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'DETL|')
```

- Chỉ tìm diễn viên làm việc trong một bộ phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'DETL|actor')
```

- Chỉ tìm thông tin chi tiết của một bộ phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'DETL|') and "ordering" = '1'
```

- Tìm tất cả các vùng, ngôn ngữ và tiêu đề cho phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'REGN|')
```

- Tìm tiêu đề phim cho một khu vực cụ thể của phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'REGN|NZ')
```

- Tìm tiêu đề gốc của phim.

```bash
  SELECT * FROM "movies"
  WHERE "mpkey" = 'tt0309377' and begins_with("mskey",'REGN|') and "types" = 'original'
```

Để truy cập thông tin ở cấp độ thành viên phi hành đoàn (#6 trong mẫu hình truy cập), chúng ta cần tạo thêm Chỉ mục phụ toàn cầu (GSI) với khóa phân vùng mới nconst (duy nhất cho thành viên phi hành đoàn). Điều này sẽ cho phép truy vấn trên khóa phân vùng mới cho GSI so với quét trên bảng cơ sở.

3. Chọn Bảng từ điều hướng bên trái, chọn bảng phim và nhấp vào tab Chỉ mục.
4. Nhấp vào Create Index và thêm các chi tiết sau.

|Thông số|Giá trị|
|---|---|
|Khóa phân vùng|NCONST|
|Loại dữ liệu|Xâu|
|Phím sắp xếp - tùy chọn|startNăm|
|Loại dữ liệu|Xâu|
|Phép chiếu thuộc tính|Tất cả|

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration29.jpg) ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration30.jpg)

5. Cuối cùng, nhấp vào Create Index. Quá trình này có thể mất một giờ tùy thuộc vào số lượng bản ghi trong bảng cơ sở.
6. Khi các cột trạng thái GSI thay đổi từ Đang chờ xử lý sang Có sẵn, hãy quay lại trình chỉnh sửa PartiQL để thực hiện truy vấn trên GSI.

- Tìm tất cả các bộ phim của một đoàn làm phim (như diễn viên, đạo diễn, v.v.)

```bash
  SELECT * FROM "movies"."nconst-startYear-index"
  WHERE "nconst" = 'nm0000142'
```

- Tìm tất cả các bộ phim của một đoàn làm phim với tư cách là diễn viên kể từ năm 2002 và sắp xếp theo năm tăng dần

```bash
  SELECT * FROM "movies"."nconst-startYear-index"
  WHERE "nconst" = 'nm0000142' and "startYear" >= '2002'
  ORDER BY "startYear"
```

Chúc mừng! bạn đã hoàn thành bài tập di chuyển RDBMS.