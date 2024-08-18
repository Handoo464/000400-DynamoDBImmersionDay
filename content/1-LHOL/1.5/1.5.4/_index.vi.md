---
title : "Explore Source Model"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.5.4. </b> "
---


IMDb [(Cơ sở dữ liệu phim Internet)](https://www.imdb.com/interfaces/)  là một trong những cái tên được công nhận nhất với bộ sưu tập cơ sở dữ liệu trực tuyến toàn diện về phim, phim, phim truyền hình, v.v. Bài tập sẽ sử dụng tập hợp con của tập dữ liệu IMDb (có sẵn ở định dạng TSV). Hội thảo này sẽ sử dụng 6 bộ dữ liệu IMDb có liên quan đến các bộ phim dựa trên Hoa Kỳ kể từ năm 2000. Bộ dữ liệu có khoảng 106K + phim, xếp hạng, phiếu bầu và thông tin diễn viên / đoàn làm phim.

Mẫu CloudFormation đã khởi chạy phiên bản EC2 Amazon Linux 2 với MySQL được cài đặt và chạy. Nó đã tạo cơ sở dữ liệu imdb, 6 bảng mới (một cho mỗi tập dữ liệu IMDb), tải xuống các tệp IMDb TSV vào thư mục cục bộ của máy chủ MySQL và tải các tệp lên 6 bảng mới. Để khám phá tập dữ liệu, hãy làm theo hướng dẫn bên dưới để đăng nhập máy chủ EC2. Nó cũng đã cấu hình người dùng MySQL từ xa dựa trên tham số đầu vào CloudFormation.

1. Đi tới [Bảng điều khiển EC2](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:instanceState=running) 
2. Chọn MySQL-Instance và nhấp vào Connect ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration9.jpg)
3. Đảm bảo ec2-user được điền vào trường Tên người dùng. Nhấp vào Kết nối ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration10.jpg)
4. Nâng cao đặc quyền của bạn bằng lệnh sudo
    
    ```bash
      sudo su
    ```
    
    ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration11.jpg)
5. Chuyển đến thư mục tệp
    
    ```bash
      cd /var/lib/mysql-files/
      ls -lrt
    ```
    
6. Bạn có thể xem tất cả 6 tệp được sao chép từ tập dữ liệu IMDB vào thư mục EC2 cục bộ ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration12.jpg)
7. Hãy thoải mái khám phá các tệp.
8. Truy cập AWS CloudFormation [Ngăn xếp](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringStatus=active&filteringText=&viewNested=true&hideStacks=false)  và nhấp vào ngăn xếp bạn đã tạo trước đó. Chuyển đến tab Tham số và sao chép tên người dùng và mật khẩu được đề cập bên cạnh DbMasterUsername &; DbMasterPassword; ![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration13.jpg)
9. Quay lại bảng điều khiển EC2 Instance và đăng nhập vào mysql

```bash
mysql -u DbMasterUsername -pDbMasterPassword
```

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration14.jpg) 10. Xin chúc mừng! Bây giờ bạn đã kết nối với cơ sở dữ liệu nguồn MySQL tự quản lý trên EC2. Trong các bước tiếp theo, chúng ta sẽ khám phá cơ sở dữ liệu và bảng lưu trữ bộ dữ liệu IMDb

```bash
use imdb;
```

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration15.jpg) 11. Hiển thị tất cả các bảng đã tạo;

```bash
show tables;
```

![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration16.jpg)

Đối với mục đích minh họa, dưới đây là sơ đồ logic thể hiện mối quan hệ giữa các bảng nguồn khác nhau lưu trữ tập dữ liệu IMDb.

- `title_basics` bảng có phim được xuất bản ở Mỹ sau năm 2000. là một khóa chữ và số được gán duy nhất cho mỗi bộ phim.`tconst`
- `title_akas` đã xuất bản các khu vực, ngôn ngữ và tiêu đề phim tương ứng. Đó là mối quan hệ 1:nhiều với bàn.`title_basics`
- `title_ratings` có xếp hạng phim và số phiếu bầu. Đối với bài tập này, chúng ta có thể giả định thông tin có tần suất cập nhật cao sau khi phát hành phim. Nó liên quan đến 1: 1 với bảng`title_basics`
- `title_principals` đã có thông tin về dàn diễn viên và đoàn làm phim. Đó là mối quan hệ 1:nhiều với bàn.`title_basics`
- `title_crew` có thông tin biên kịch và đạo diễn. Bảng này có liên quan đến bảng 1: 1.`title_basics`
- `name_basics` có chi tiết diễn viên và đoàn làm phim. Mỗi thành viên có giá trị duy nhất được chỉ định. `nconst`![Kiến trúc triển khai cuối cùng](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/migration31.jpg)

12. Chúng tôi sẽ tạo chế độ xem không chuẩn hóa với thông tin tĩnh 1:1 và chuẩn bị sẵn sàng để di chuyển sang bảng Amazon DynamoDB. Bây giờ, hãy tiếp tục và sao chép mã bên dưới và dán vào dòng lệnh MySQL. Các chi tiết xung quanh mô hình dữ liệu mục tiêu sẽ được thảo luận trong chương tiếp theo.

```bash
CREATE VIEW imdb.movies AS\
    SELECT tp.tconst,\
           tp.ordering,\
           tp.nconst,\
           tp.category,\
           tp.job,\
           tp.characters,\
           tb.titleType,\
           tb.primaryTitle,\
           tb.originalTitle,\
           tb.isAdult,\
           tb.startYear,\
           tb.endYear,\
           tb.runtimeMinutes,\
           tb.genres,\
           nm.primaryName,\
           nm.birthYear,\
           nm.deathYear,\
           nm.primaryProfession,\
           tc.directors,\
           tc.writers\
    FROM imdb.title_principals tp\
    LEFT JOIN imdb.title_basics tb ON tp.tconst = tb.tconst\
    LEFT JOIN imdb.name_basics nm ON tp.nconst = nm.nconst\
    LEFT JOIN imdb.title_crew tc ON tc.tconst = tp.tconst;
```

Sử dụng lệnh bên dưới để xem lại số lượng bản ghi từ dạng xem không chuẩn hóa. Tại thời điểm này, cơ sở dữ liệu nguồn của bạn đã sẵn sàng để di chuyển sang Amazon DynamoDB.

```bash
select count(*) from imdb.movies;
```