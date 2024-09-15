---
title : "Explore Source Model"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 1.5.4. </b> "
---

**IMDb** [(Internet Movie Database)](https://www.imdb.com/interfaces/) là một trong những tên tuổi nổi tiếng nhất với bộ sưu tập cơ sở dữ liệu trực tuyến toàn diện về phim, phim truyền hình, series TV, và nhiều hơn nữa. Bài tập này sẽ sử dụng một phần của bộ dữ liệu IMDb (có sẵn ở định dạng TSV). Workshop này sẽ sử dụng 6 bộ dữ liệu IMDb liên quan đến các bộ phim ở Mỹ kể từ năm 2000. Bộ dữ liệu này có khoảng hơn 106.000 phim, đánh giá, bình chọn và thông tin về dàn diễn viên/đội ngũ sản xuất.

Mẫu CloudFormation đã khởi chạy một phiên bản Amazon Linux 2 EC2 với MySQL đã được cài đặt và chạy. Nó đã tạo cơ sở dữ liệu IMDb, 6 bảng mới (mỗi bảng cho một bộ dữ liệu IMDb), tải các tệp TSV IMDb vào thư mục cục bộ trên máy chủ MySQL và tải các tệp này vào 6 bảng mới. Để khám phá bộ dữ liệu, hãy làm theo các hướng dẫn dưới đây để đăng nhập vào máy chủ EC2. Mẫu này cũng đã cấu hình một người dùng MySQL từ xa dựa trên tham số đầu vào của CloudFormation.

1. Truy cập [EC2 console](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:instanceState=running)
2. Chọn **MySQL-Instance** và nhấp vào **Connect**.  
   ![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/7.jpg)
3. Đảm bảo **ec2-user** đã được điền vào trường **User name**. Nhấp vào **Connect**.  
   ![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/8.jpg)
4. Tăng quyền hạn của bạn bằng cách sử dụng lệnh sudo:
    
    ```bash
    sudo su
    ```
    
   ![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/9.jpg)

5. Truy cập vào thư mục tệp:
    
    ```bash
    cd /var/lib/mysql-files/
    ls -lrt
    ```
    
6. Bạn có thể thấy tất cả 6 tệp được sao chép từ bộ dữ liệu IMDB vào thư mục cục bộ của EC2.  
   ![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/10.jpg)

7. Hãy thoải mái khám phá các tệp này.
8. Truy cập [AWS CloudFormation Stacks](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringStatus=active&filteringText=&viewNested=true&hideStacks=false) và nhấp vào stack bạn đã tạo trước đó. Truy cập tab **Parameters** và sao chép tên người dùng và mật khẩu được đề cập bên cạnh **DbMasterUsername** và **DbMasterPassword**.  
   ![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/11.jpg)

9. Quay lại bảng điều khiển EC2 Instance và đăng nhập vào MySQL:

```bash
mysql -u DbMasterUsername -pDbMasterPassword
```

![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/12.jpg)  

10. Chúc mừng bạn! Bạn đã kết nối thành công với cơ sở dữ liệu MySQL tự quản lý trên EC2. Ở các bước tiếp theo, chúng ta sẽ khám phá cơ sở dữ liệu và các bảng lưu trữ bộ dữ liệu IMDb:

```bash
use imdb;
```

![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/13.jpg)  

11. Hiển thị tất cả các bảng đã được tạo:

```bash
show tables;
```

![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/14.jpg)

Để minh họa, dưới đây là sơ đồ logic thể hiện mối quan hệ giữa các bảng nguồn lưu trữ bộ dữ liệu IMDb:

- Bảng `title_basics` chứa các phim được phát hành ở Mỹ sau năm 2000. `tconst` là một khóa chữ-số duy nhất được gán cho mỗi bộ phim.
- Bảng `title_akas` chứa các vùng phát hành, ngôn ngữ và tiêu đề phim tương ứng. Đây là mối quan hệ 1:n với bảng `title_basics`.
- Bảng `title_ratings` chứa đánh giá phim và số phiếu bầu. Đối với bài tập này, chúng ta có thể giả định thông tin này được cập nhật với tần suất cao sau khi phim được phát hành. Đây là mối quan hệ 1:1 với bảng `title_basics`.
- Bảng `title_principals` chứa thông tin về dàn diễn viên và đội ngũ sản xuất. Đây là mối quan hệ 1:n với bảng `title_basics`.
- Bảng `title_crew` chứa thông tin về biên kịch và đạo diễn. Bảng này có mối quan hệ 1:1 với bảng `title_basics`.
- Bảng `name_basics` chứa chi tiết về dàn diễn viên và đội ngũ sản xuất. Mỗi thành viên có giá trị `nconst` duy nhất được gán.  
  ![Kiến trúc Triển khai Cuối cùng](/images/1/1.5/15.jpg)

12. Chúng ta sẽ tạo một view đã phi chuẩn hóa với thông tin tĩnh 1:1 và chuẩn bị sẵn sàng để di chuyển sang bảng Amazon DynamoDB. Bây giờ, hãy sao chép và dán đoạn mã dưới đây vào dòng lệnh MySQL. Chi tiết về mô hình dữ liệu mục tiêu sẽ được thảo luận trong chương tiếp theo.

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

Sử dụng lệnh dưới đây để xem số lượng bản ghi từ view đã phi chuẩn hóa. Ở thời điểm này, cơ sở dữ liệu nguồn của bạn đã sẵn sàng để di chuyển sang Amazon DynamoDB.

```bash
select count(*) from imdb.movies;
```