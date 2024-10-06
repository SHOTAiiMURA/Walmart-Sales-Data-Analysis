# Walmart-Sales-Data-Analysis

### CREATE TABLE
```sql
create table if not exists sales(
	invoice_id varchar(30) not null primary key,
	branch varchar(5) not null,
	city varchar(30) not null,
	customer_type varchar(30) not null,
	gender varchar(10) not null,
	product_line varchar(100) not null,     
	unit_price decimal(10, 2) not null,
	quantity int not null,  
	vat float(6, 4) not null,
	total_revenue decimal(12, 4) not null,
	date datetime not null,
	time time not null,
	payment_mothod varchar(15) not null,
	cogs decimal(10, 2) not null, 
	gross_margin_pct float(11, 9), 
	gross_income decimal(12, 4) not null,
	rating float(2, 1)
);
```
### import csv file from walmart data
select * from salesDataWalmart.sales;
### feature engineering: this will help use generate some new columns form existing ones.
### time of day ---- categorize morning afternoon or evening.
```sql
select
	time,
    (case
		when 'time' between "00:00:00" and "12:00:00" then "Morning"
        when 'time' between "12:01:00" and "16:00:00" then "Afternoon"
        else "Evening"
    end ) as time_of_date
from sales;
```
### add column of time of day
```sql
alter table sales add column time_of_day varchar(20);
```
### update sales
```sql
set time_of_day = (
case
	when 'time' between "00:00:00" and "12:00:00" then "Morning"
	when 'time' between "12:01:00" and "16:00:00" then "Afternoon"
	else "Evening"
end
);
```
### day_name
```sql
select
	date,
    dayname(date)
from sales;

alter table sales add column day_name varchar(30);

update sales
set day_name = dayname(date);
```
### month_name
```sql
select
	date,
    monthname(date)
from sales;

alter table sales add column month_name varchar(20);

update sales
set month_name = monthname(date);
```
## Answering Business Assignments
### Generic
* Q: How many unique cities does the data have?
```sql
select
	distinct city
from sales;
* Q: In city which branch have?
select
	distinct branch
from sales;
```
* Q: individual branch?
```sql
select distinct city, branch from sales;
```
## Product
* Q: How many unique product lines does the data have?
```sql
select count(distinct product_line) from sales;
```
* Q: what is the most common payment method?
```sql
select * from sales;
select
	payment_mothod,
	count(payment_mothod) as cnt
from sales
group by payment_mothod
order by cnt desc;
```
* Q: what is the most selling product line?
```sql
select
	product_line,
	count(product_line) as cnt
from sales
group by product_line
order by cnt desc;
```
* Q: what is the total revenue of month?
```sql
select
	month_name as month,
    sum(total_revenue) as total_revenue
from sales
group by month_name
order by total_revenue desc;
```
* Q: what month is the most largest cost of goods?
```sql
select
	month_name as month,
    sum(cogs) as cogs
from sales
group by month_name
order by cogs desc;
```
* Q: Fetch each product line and add a column to those product line showing "Good", "Bad". Good if its greater than sales.
 
 
 * Q: Which branch sold more products than average product sold ?
```sql
select
	branch,
    sum(quantity) as qty
from sales
group by branch
having sum(quantity) > (select avg(quantity) from sales);
```
* Q: what is the most common produts line by gender?
```sql
select
	gender,
    product_line,
    count(gender) as total_cnt
from sales
group by gender, product_line
order by total_cnt desc;
```
## Sales
* Q: number of sales made in each time of the day per weekday
```sql
select * from sales;

select
	time_of_day,
    count(*) as total_sales
from sales
where day_name = "Saturday"
group by time_of_day
order by total_sales desc;
```
* Q: which of the cutomer types brings the most revenue?
```sql
select
	customer_type,
    sum(total_revenue) as total_revenue
from sales
group by customer_type
order by total_revenue desc;
* Q: which city has the largest tax percent/VAT?
select
	city,
    avg(vat) as vat
from sales
group by city
order by vat desc;
```
* Q: which cusomer type pays the most in VAT?
```sql
select
	customer_type,
    avg(vat) as vat
from sales
group by customer_type
order by vat desc;
```
## Customer
* Q: How many unique customer types does the data have?
```sql
select
	distinct customer_type
from sales;
```
* Q: How many unique payment methods does the data have?
```sql
select distinct payment_mothod
from sales;
```
* Q: What time the most common customer type?

* Q: Which customer type buys the most?
```sql
select
	customer_type,
    count(*) as cnt
from sales
group by customer_type;
```
* Q: What is the gender of most of the customer?
```sql
select
	gender,
    count(*) as gender
from sales
group by gender
order by gender desc; 
```
* Q: w
hich time of the day do customers give most rating?
```sql
select
	time_of_day,
    avg(rating) as avg_rating
from sales
group by time_of_day
order by avg_rating desc;
```
