# Dự án Làm sạch và Chuẩn bị Dữ liệu bằng SQL

**Tập dữ liệu gốc ở định dạng CSV nằm trong tệp `club_member_info.csv`.**

### 10 dòng đầu tiên trong tệp `club_member_info`.

***Query:***
```SQL
SELECT * fROM club_member_info LIMIT 10;
```
***The result:***
|full_name|age|martial_status|email|phone|full_address|job_title|membership_date|
|---------|---|--------------|-----|-----|------------|---------|---------------|
|addie lush|40|married|alush0@shutterfly.com|254-389-8708|3226 Eastlawn Pass,Temple,Texas|Assistant Professor|7/31/2013|
|      ROCK CRADICK|46|married|rcradick1@newsvine.com|910-566-2007|4 Harbort Avenue,Fayetteville,North Carolina|Programmer III|5/27/2018|
|Sydel Sharvell|46|divorced|ssharvell2@amazon.co.jp|702-187-8715|4 School Place,Las Vegas,Nevada|Budget/Accounting Analyst I|10/6/2017|
|Constantin de la cruz|35||co3@bloglines.com|402-688-7162|6 Monument Crossing,Omaha,Nebraska|Desktop Support Technician|10/20/2015|
|  Gaylor Redhole|38|married|gredhole4@japanpost.jp|917-394-6001|88 Cherokee Pass,New York City,New York|Legal Assistant|5/29/2019|
|Wanda del mar       |44|single|wkunzel5@slideshare.net|937-467-6942|10864 Buhler Plaza,Hamilton,Ohio|Human Resources Assistant IV|3/24/2015|
|Joann Kenealy|41|married|jkenealy6@bloomberg.com|513-726-9885|733 Hagan Parkway,Cincinnati,Ohio|Accountant IV|4/17/2013|
|   Joete Cudiff|51|divorced|jcudiff7@ycombinator.com|616-617-0965|975 Dwight Plaza,Grand Rapids,Michigan|Research Nurse|11/16/2014|
|mendie alexandrescu|46|single|malexandrescu8@state.gov|504-918-4753|34 Delladonna Terrace,New Orleans,Louisiana|Systems Administrator III|3/12/1921|
| fey kloss|52|married|fkloss9@godaddy.com|808-177-0318|8976 Jackson Park,Honolulu,Hawaii|Chemical Engineer|11/5/2014|

## Làm sạch dữ liệu
Tạo bảng mới có cấu trúc giống bảng gốc dùng để làm sạch, nhưng không ảnh hưởng đến bảng gốc.

### Tạo bảng mới.
***Query:***
```SQL
CREATE TABLE club_member_info_cleaned (
	full_name VARCHAR(50),
	age INTEGER,
	martial_status VARCHAR(50),
	email VARCHAR(50),
	phone VARCHAR(50),
	full_address VARCHAR(50),
	job_title VARCHAR(50),
	membership_date VARCHAR(50)
);
```
### Sao chép dữ liệu từ bảng gốc.
***Query:***
```SQL
INSERT INTO club_member_info_cleaned SELECT * FROM club_member_info;
```
### Chạy các lệnh sau để làm sạch dữ liệu
```SQL
 -- Chuẩn hóa chữ thường và xóa khoảng trắng
    UPDATE club_member_info_cleaned
    SET 
	full_name = CONCAT(UPPER(SUBSTRING(TRIM(full_name), 1, 1)), LOWER(SUBSTRING(TRIM(full_name), 2))),
	email = LOWER(TRIM(email)),
	job_title = LOWER(TRIM(job_title)),
	martial_status = LOWER(TRIM(martial_status)),
	phone = LOWER(TRIM(phone)),
	membership_date = LOWER(TRIM(membership_date)),
		full_address = LOWER(TRIM(full_address)),
		age = LOWER(TRIM(age));
    
-- Kiểm tra tuổi có hợp lệ không (<18 hoặc >100 hoặc age = null)
    SELECT * FROM club_member_info_cleaned
    WHERE age < 18 or age > 100 or age is null;

-- Thay thế tuổi không hợp bằng giá trị xuất hiện nhiều nhất
    UPDATE club_member_info_cleaned 
    SET  age = (SELECT MODE(age) FROM club_member_info_cleaned)
    WHERE age < 18 or age > 100 or age is null;

-- Kiểm tra hợp lệ martial_status
    SELECT * FROM club_member_info_cleaned 
    WHERE martial_status NOT IN ("married", "divorced", "single")
 	
-- Thay thế các giá trị rỗng ở cột martial_status bằng giá trị NULL
    UPDATE club_member_info_cleaned 
    SET  martial_status = "NULL"
    WHERE martial_status NOT IN ("married", "divorced", "single")

 -- Kiểm tra job_title có bị miss không
    SELECT * FROM club_member_info_cleaned cmic
    WHERE job_title = "";
 
 -- Thay thế bằng giá trị null cho cột martial_status
    UPDATE club_member_info_cleaned 
    SET  martial_status = "NULL"
    WHERE job_title = "";
```

## Kết quả sau khi được làm sạch:

|full_name|age|martial_status|email|phone|full_address|job_title|membership_date|
|---------|---|--------------|-----|-----|------------|---------|---------------|
|Addie lush|40|married|alush0@shutterfly.com|254-389-8708|3226 eastlawn pass,temple,texas|assistant professor|7/31/2013|
|Rock cradick|46|married|rcradick1@newsvine.com|910-566-2007|4 harbort avenue,fayetteville,north carolina|programmer iii|5/27/2018|
|Sydel sharvell|46|divorced|ssharvell2@amazon.co.jp|702-187-8715|4 school place,las vegas,nevada|budget/accounting analyst i|10/6/2017|
|Constantin de la cruz|35||co3@bloglines.com|402-688-7162|6 monument crossing,omaha,nebraska|desktop support technician|10/20/2015|
|Gaylor redhole|38|married|gredhole4@japanpost.jp|917-394-6001|88 cherokee pass,new york city,new york|legal assistant|5/29/2019|
|Wanda del mar|44|single|wkunzel5@slideshare.net|937-467-6942|10864 buhler plaza,hamilton,ohio|human resources assistant iv|3/24/2015|
|Joann kenealy|41|married|jkenealy6@bloomberg.com|513-726-9885|733 hagan parkway,cincinnati,ohio|accountant iv|4/17/2013|
|Joete cudiff|51|divorced|jcudiff7@ycombinator.com|616-617-0965|975 dwight plaza,grand rapids,michigan|research nurse|11/16/2014|
|Mendie alexandrescu|46|single|malexandrescu8@state.gov|504-918-4753|34 delladonna terrace,new orleans,louisiana|systems administrator iii|3/12/1921|
|Fey kloss|52|married|fkloss9@godaddy.com|808-177-0318|8976 jackson park,honolulu,hawaii|chemical engineer|11/5/2014|


## Vấn đề thảo luận:
Trong quá trình thu thập và lưu trữ dữ liệu, việc nhập trùng lặp có thể xảy ra do người dùng nhập lại thông tin. Điều này dẫn đến dữ liệu dư thừa và ảnh hưởng đến chất lượng phân tích. Chẳng hạn như giá trị của cột email nên chỉ được sử dụng 1 lần.
### Kiểm tra các bản ghi trùng lặp.
***Query:***
```SQL
Select email, count(*) From club_member_info_cleaned group by email having count(email) >= 2
```
***result***
|email|count(*)|
|-----|--------|
|ehuxterm0@marketwatch.com|3|
|gprewettfl@mac.com|2|
|greglar4r@answers.com|2|
|hbradenri@freewebs.com|2|
|mmorralleemj@wordpress.com|2|
|nfilliskirkd5@newsvine.com|2|
|omaccaughen1o@naver.com|2|
|slamble81@amazon.co.uk|2|
|tdunkersley8u@dedecms.com|2|

### Loại bỏ các bản ghi trùng lặp, chỉ giữ bản ghi cuối cùng
***Query:***
```SQL
Delete from club_member_info_cleaned where ROWID in (Select ROWID
							FROM club_member_info_cleaned o
							JOIN (SELECT email, MAX(ROWID) as maxID FROM club_member_info_cleaned cmic  
 							GROUP BY email) as n ON o.email = n.email
							where n.maxID > o.ROWID)
```
**Giải thích:**
	`SELECT email, MAX(ROWID) as maxID FROM club_member_info_cleaned cmic  GROUP BY email`: lấy ROWID lớn nhất cho mỗi email trong bảng. Mục đích xác định bản ghi cuối cùng nhập vào đối với mỗi email
	`Select 	
	 ROWID FROM club_member_info_cleaned o
	 JOIN (SELECT email, MAX(ROWID) as maxID FROM club_member_info_cleaned cmic  
	 GROUP BY email) as n ON o.email = n.email
	 where n.maxID > o.ROWID`: Lấy danh sách ROWID cần xóa để loại bỏ bản ghi trùng. Chỉ giữ lại bản ghi có ROWID lớn nhất cho mỗi email vì có thể đó là bản ghi chính xác nhất.
	 ***Chú ý***: Vì trong table không có cột id nên chúng ta sử dụng ROWID(chỉ dành riêng cho SQLite) để thay thế 























