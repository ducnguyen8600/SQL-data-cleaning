# SQL-data-cleaning
This is an educational project on data cleaning and preparation using SQL. The original database in CSV format is located in the file club_member_info.csv. Here, we will explore the steps that need to be applied to obtain a cleansed version of the dataset.

## Inspect data
```sql
SELECT *
FROM club_member_info
LIMIT 10
```
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

## Create a new table for cleaning
```sql
CREATE TABLE club_member_info_cleaned (
	full_name VARCHAR(50),
	age INTEGER,
	martial_status VARCHAR(50),
	email VARCHAR(50),
	phone NVARCHAR(50),
	full_address NVARCHAR(50),
	job_title VARCHAR(50),
	membership_date NVARCHAR(50)
);
```
## Copy all values from original table
```sql
INSERT INTO club_member_info_cleaned 
SELECT * FROM club_member_info;
```
## Clean data

### Update column full_name

##### Update full_name to uppercase and eliminate spaces
```sql
UPDATE club_member_info_cleaned
SET full_name = TRIM(UPPER(full_name))
```
##### Update full_name from empty value to NULL
```sql
UPDATE club_member_info_cleaned
SET full_name = NULL
WHERE full_name = ''
```

### Update column age

##### Inspect data
```sql
SELECT age
FROM club_member_info_cleaned
WHERE age < 18 or age > 100
```

|age|
|---|
|399|
|555|
|544|
|499|
|522|
||
|277|
||
|288|
|588|
|599|
|677|
|322|
|644|
||
|411|
|633|
|222|


##### Convert age out of range (18-100) and empty to NULL
```sql
UPDATE club_member_info_cleaned
SET age = NULL
WHERE age < 18 or age > 100
```
### Update column martial_status

##### Inspect column 
```sql
SELECT DISTINCT martial_status 
FROM club_member_info_cleaned
```
|martial_status|
|--------------|
|married|
|divorced|
||
|single|
|divored|

##### Update misspelling data and convert empty value to NULL
```sql
UPDATE club_member_info_cleaned
SET martial_status = 
		CASE 
			WHEN martial_status = '' THEN NULL
			WHEN martial_status = 'divored' THEN 'divorced'
			ELSE martial_status
		END
```
### Update column phone

##### Inspect wrong phone numbers (phone number must have 10 digits) and empty value

```sql
SELECT phone, CAST(REPLACE(phone,'-','') as int) as cp
FROM club_member_info_cleaned
WHERE cp = 0 or length(cp) <> 10
```
Use Cast function to check: If phone number have string inside or empty value then cp = 0

|phone|cp|
|-----|--|
|814-2985|8142985|
|14-900-1376|149001376|
||0|
||0|
||0|
||0|
||0|
||0|
|43-892-2116|438922116|
||0|
|575-8072|5758072|
|559-115310|559115310|
||0|
||0|
|814-2985|8142985|
|14-900-1376|149001376|
||0|
||0|
||0|
||0|
||0|
||0|
|43-892-2116|438922116|
||0|
|575-8072|5758072|
|559-115310|559115310|
||0|
||0|

##### Convert wrong phone numbers and empty value to NULL

```sql
UPDATE club_member_info_cleaned
SET phone = NULL
WHERE LENGTH(REPLACE(phone,'-','')) <> 10
```
### Update column job_title

##### Convert empty value to NULL
```sql
UPDATE club_member_info_cleaned
SET job_title = NULL
WHERE job_title = ''
```
### Update column membership_date

##### Inspect data
```sql
SELECT age, CAST(SUBSTR(membership_date,-4)as int) as y
FROM club_member_info_cleaned
WHERE y >= 2025 or y < 2000
```
|age|y|
|---|-|
|46|1921|
|52|1912|
|51|1916|
|38|1912|
|36|1919|
|33|1913|
|41|1912|
|37|1914|
|46|1916|
|27|1915|
|52|1915|
|51|1917|
|19|1915|
|67|1921|
|61|1915|
|38|1915|
|46|1921|
|52|1912|
|51|1916|
|38|1912|
|36|1919|
|33|1913|
|41|1912|
|37|1914|
|46|1916|
|27|1915|
|52|1915|
|51|1917|
|19|1915|
|67|1921|
|61|1915|
|38|1915|

Year in membership date is wrong because it's smaller than 192x
##### Convert wrong memembership_date to NULL
```sql
UPDATE club_member_info_cleaned
SET membership_date = NULL
WHERE CAST(SUBSTR(membership_date,-4)as int) >= 2025 or CAST(SUBSTR(membership_date,-4)as int) <2000
```
