# Codewars

Note: Here all the solutions are in **PostgreSQL**.

## SQL Statistics: MIN, MEDIAN, MAX

For this challenge you need to create a simple SELECT statement. Your task is to calculate the MIN, MEDIAN and MAX scores of the students from the results table.

### Tables and relationship below:

![img](http://i.imgur.com/Qdt9DqU.png)



### Resultant table:

- min
- median
- max

**Solution**

```plsql
select min(score) as min, percentile_cont(0.5) within group(order by score) as median,
max(score) as max from result;


```



## SQL Basics: Simple PIVOTING data

For this challenge you need to PIVOT data. You have two tables, products and details. Your task is to pivot the rows in products to produce a table of products which have rows of their detail. Group and Order by the name of the Product.

### Tables and relationship below:

![img](http://i.imgur.com/81Ww3YH.png)



You must use the CROSSTAB statement to create a table that has the schema as below:

##### CROSSTAB table.

- name
- good
- ok
- bad

**Solution**

```plsql
CREATE EXTENSION tablefunc;
select * from crosstab('select name, detail, count(details.id) from products
join details on products.id = details.product_id 
group by name, detail order by 1,2') As ct(name text,
bad bigint, good bigint, ok bigint);
```



## Using window functions to get top N per group

### Description

Given the schema presented below write a query, which uses a **window function**, that returns two most viewed posts for every category.

Order the result set by:

1. category name alphabetically
2. number of post views largest to lowest
3. post id lowest to largest

##### Note:

- Some categories may have less than two or no posts at all.
- Two or more posts within the category can be tied by (have the same) the number of views. Use post id as a tie breaker - a post with a lower id gets a higher rank.

### Schema

#### categories

```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
id          | integer                     | not null
category    | character varying(255)      | not null
```

#### posts

```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
id          | integer                     | not null
category_id | integer                     | not null
title       | character varying(255)      | not null
views       | integer                     | not null
```

### Desired Output

The desired output should look like this:

```
category_id | category | title                             | views | post_id
------------+----------+-----------------------------------+-------+--------
5           | art      | Most viewed post about Art        | 9234  | 234
5           | art      | Second most viewed post about Art | 9234  | 712
2           | business | NULL                              | NULL  | NULL
7           | sport    | Most viewed post about Sport      | 10    | 126
...
```

- `category_id` - category id
- `category` - category name
- `title` - post title
- `views` - the number of post views
- `post_id` - post id

**Solution**

```plsql
select
c.id category_id,
c.category,
p.title,
p.views,
p.post_id
from categories c left join (
select
category_id,
title,
views,
id post_id,
row_number() over (partition by category_id order by views desc, id) rn
from posts) p on p.category_id = c.id and p.rn < 3
order by category, views desc, post_id;
```



## Length based SELECT with LIKE

You will need to create SELECT statement in conjunction with LIKE.

Please list people which have first_name with at least 6 character long

### names table schema

- id
- first_name
- last_name

### results table schema

- first_name
- last_name

**Solution**

```plsql
select first_name, last_name from names
where first_name LIKE '%______';
```



## Simple JOIN with COUNT

For this challenge you need to create a simple SELECT statement that will return all columns from the `people` table, and join to the `toys`table so that you can return the COUNT of the toys

### people table schema

- id
- name

### toys table schema

- id
- name
- people_id

You should return all people fields as well as the toy count as "toy_count".

> NOTE: You're solution should use pure SQL. Ruby is used within the test cases to do the actual testing.

**Solution**

```plsql
select people.id,people.name,count(toys.id) as toy_count from people
join toys on people.id = toys.people_id
group by people.id;
```

   

## Simple HAVING

For this challenge you need to create a simple HAVING statement, you want to count how many people have the same age and return the groups with 10 or more people who have that age.

### people table schema

- id
- name
- age

### return table schema

- age
- total_people

> NOTE: Your solution should use pure SQL. Ruby is used within the test cases to do the actual testing.

**Solution**

```plsql
select age, count(*) as total_people from people
group by age
having count(*)>=10;
```



## Calculating Batting average

In baseball, the batting average is a simple and most common way to measure a hitter's performace. Batting average is calculated by taking all the players `hits` and dividing it by their number of `at_bats`, and it is usually displayed as a 3 digit decimal (i.e. 0.300).

Given a `yankees` table with the following schema,

-`player_id` STRING

-`player_name` STRING

-`primary_position` STRING

-`games` INTEGER

-`at_bats` INTEGER

-`hits` INTEGER

return a table with `player_name`, `games`, and `batting_average`.

We want `batting_average` to be rounded to the nearest thousandth, since that is how baseball fans are used to seeing it. Format it as text and make sure it has 3 digits to the right of the decimal (pad with zeroes if neccesary).

Next, order our resulting table by `batting_average`, with the highest average in the first row.

Finally, since `batting_average` is a rate statistic, a small number of `at_bats` can change the average dramatically. To correct for this, exclude any player who doesn't have at least 100 at bats.

Expected Output Table

-`player_name` STRING

-`games` INTEGER

-`batting_average` STRING



**Solution**

```plsql
select player_name, games,round(hits::numeric/at_bats,3)::text as batting_average from yankees
where at_bats >= 100
order by batting_average desc;
```



## SQL with Sailor Moon: Thinking about JOINs

Practise some SQL fundamentals by making a simple database on a topic you feel familiar with. Or use mine, populated with a wealth of Sailor Moon trivia.

sailorsenshi schema

- id
- senshi_name
- real_name_jpn
- school_id
- cat_id

cats schema

- id
- name

schools schema

- id
- school

Return a results table - *sailor_senshi, real_name, cat* and *school* - of all characters, containing each character's high school, their civilian name and the cat who introduced them to their magical crime-fighting destiny.

Keep in mind some senshi were not initiated by a cat guardian and one is not in high school. The field can be left blank if this is the case.



**Solution**

```plsql
select senshi_name as sailor_senshi, real_name_jpn as real_name, cats.name as cat,
schools.school as school
from sailorsenshi
left join cats on sailorsenshi.cat_id = cats.id
left join schools on sailorsenshi.school_id = schools.id;
```



## Grocery store: Support Local Products

You are the owner of the Grocery Store. All your products are in the database, that you have created after CodeWars SQL excercises!:)

You care about local market, and want to check how many products come from United States of America or Canada.

Please use SELECT statement and IN to filter out other origins.

In the results show how many products are from United States of America and Canada respectively.

Order by number of products, descending.

### products table schema

- id
- name
- price
- stock
- producer
- country

### results table schema

- products
- country

**Solution**

```plsql
select count(*) as products, country from products
where country in ('United States of America','Canada')
group by country
order by products desc;
```



## SQL: Concatenating columns

Given the table below:

** names table schema **

- id
- prefix
- first
- last
- suffix

Your task is to use a select statement to return a single column table containing the full title of the person (concatenate all columns together except id), as follows:

** output table schema **

- title

Don't forget to add spaces.



**Solution**

```plsql
select concat(prefix,' ',first,' ',last,' ',suffix) as title from names;
```



## Best-selling Books

You work at a book store. It's the end of the month, and you need to find out the 5 bestselling books at your store. Use a select statement to list names, authors, and number of copies sold of the 5 books which were sold most.

books table schema

- name
- author
- copies_sold

NOTE: Your solution should use pure SQL. Ruby is used within the test cases to do the actual testing.



**Solution**

```plsql
select name, author, copies_sold from books
order by copies_sold desc
limit 5;
```



## SQL with Street Fighter: Total Wins

It's time to assess which of the world's greatest fighters are through to the 6 coveted places in the semi-finals of the Street Fighter World Fighting Championship. Every fight of the year has been recorded and each fighter's wins and losses need to be added up.

Each row of the table `fighters` records, alongside the fighter's name, whether they won (1) or lost (0), as well as the type of move that ended the bout.

- id
- name
- won
- lost
- move_id

winning_moves

- id
- move

However, due to new health and safety regulations, all ki blasts have been outlawed as a potential fire hazard. Any bout that ended with `Hadoken, Shouoken` or `Kikoken` should not be counted in the total wins and losses.

So, your job:

- Return `name, won,` and `lost` columns displaying the name, total number of wins and total number of losses. Group by the fighter's name.
- Do not count any wins or losses where the winning move was `Hadoken, Shouoken` or `Kikoken`.
- Order from most-wins to least
- Return the top 6. Don't worry about ties.

**Solution**

```plsql
with no_win as(
select id, move from winning_moves
where move != 'Hadoken' and Move != 'Shouoken' and move != 'Kikoken')
select name, sum(won) as won, sum(lost) as lost from fighters
where move_id in (select id from no_win)
group by name
order by won desc
limit 6;
```



## Repeat and Reverse

Using our monsters table with the following schema:

**monsters table schema**

- id
- name
- legs
- arms
- characteristics

return the following table:

** output schema**

- name
- characteristics

Where the name is the original string repeated three times (do not add any spaces), and the characteristics are the original strings in reverse (e.g. 'abc, def, ghi' becomes 'ihg ,fed ,cba').



**Solution**

```
select repeat(name, 3) as name, reverse(characteristics) as characteristics from monsters;
```



## Top 10 customers by total payments

## Overview

For this kata we will be using the [DVD Rental database](http://www.postgresqltutorial.com/postgresql-sample-database/).

Your are working for a company that wants to reward its top 10 customers with a free gift. You have been asked to generate a simple report that returns the top 10 customers by total amount spent. Total number of payments has also been requested.

The query should output the following columns:

- customer_id [int4]
- email [varchar]
- payments_count [int]
- total_amount [float]

and has the following requirements:

- only returns the 10 top customers, ordered by total amount spent

## Database Schema

![Database Schema](http://www.postgresqltutorial.com/wp-content/uploads/2013/05/PostgreSQL-Sample-Database.png)



**Solution**

```
select customer.customer_id, customer.email, 
count(payment.payment_id) as payments_count, sum(payment.amount)::float as total_amount
from customer join rental on customer.customer_id = rental.customer_id
join payment on rental.rental_id = payment.rental_id
group by customer.customer_id
order by total_amount desc 
limit 10;
```



## Calculating Month-Over-Month Percentage Growth Rate

### Description

Given a `posts` table that contains a `created_at` timestamp column write a query that returns a first date of the month, a number of posts created in a given month and a month-over-month growth rate.

The resulting set should be ordered chronologically by `date`.

**Note:**

- percent growth rate can be negative
- percent growth rate should be rounded to one digit after the decimal point and immediately followed by a percent symbol "%". See the desired output below for the reference.

### Desired Output

The resulting set should look similar to the following:

```
date       | count | percent_growth
-----------+-------+---------------
2016-02-01 |   175 |           null
2016-03-01 |   338 |          93.1%
2016-04-01 |   345 |           2.1%
2016-05-01 |   295 |         -14.5%
2016-06-01 |   330 |          11.9%
...
```

- date - (DATE) a first date of the month
- count - (INT) a number of posts in a given month
- percent_growth - (TEXT) a month-over-month growth rate expressed in percents

**Solution**

```
with date1 as(
select TO_CHAR(created_at,'yyyy-mm-01')::date as date,count(*) as count1 from posts
group by date),
date2 as(
select date, count1,lag(count1) OVER (ORDER BY date) as last_amount from date1)
select date, count1 as count,
round(-100 * (last_amount-count1)/last_amount::numeric,1) || '%' as percent_growth
from date2
order by date;
```



## Challenge: Two actors who cast together the most

Given the the schema presented below find two actors who cast together the most and list titles of only those movies they were casting together. Order the result set alphabetically by the movie title.

#### Table film_actor

```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
actor_id    | smallint                    | not null
film_id     | smallint                    | not null
...
```

#### Table actor

```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
actor_id    | integer                     | not null 
first_name  | character varying(45)       | not null
last_name   | character varying(45)       | not null
...
```

#### Table film

```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
film_id     | integer                     | not null
title       | character varying(255)      | not null
...
```

#### The desired output:

```
first_actor | second_actor | title
------------+--------------+--------------------
John Doe    | Jane Doe     | The Best Movie Ever
...
```

- `first_actor` - Full name (First name + Last name separated by a space)
- `second_actor` - Full name (First name + Last name separated by a space)
- `title` - Movie title

**Note:** `actor_id` of the first_actor should be lower then `actor_id` of the second_actor



**Solution**

```
with a1 as(
select f1.actor_id as f1, f2.actor_id as f2,count(*) as c from film_actor f1
join film_actor f2 on f1.film_id = f2.film_id
where f1.actor_id < f2.actor_id
group by f1.actor_id, f2.actor_id
order by c desc
limit 1),
fc as(
SELECT fa.actor_id as actor1, fb.actor_id as actor2, f.title as title1
FROM film f
JOIN film_actor fa on fa.film_id = f.film_id
join film_actor fb on fb.film_id = f.film_id
WHERE fa.actor_id = (select f1 from a1) and fb.actor_id = (select f2 from a1)),
fuc as(
select concat(c1.first_name,' ',c1.last_name) as first_actor, c1.actor_id as fuc1, c2.actor_id as fuc2,
concat(c2.first_name,' ',c2.last_name) as second_actor
from actor as c1, actor as c2
where c1.actor_id = (select f1 from a1) and c2.actor_id = (select f2 from a1))
select first_actor,second_actor, title1 as title from fuc
join fc on fuc.fuc1 = fc.actor1
order by title asc;
```



## Relational division: Find all movies two actors cast in together

Given `film_actor` and `film` tables from the [DVD Rental](http://www.postgresqltutorial.com/postgresql-sample-database/) sample database find all movies both Sidney Crowe (`actor_id = 105`) and Salma Nolte (`actor_id = 122`) cast in together and order the result set alphabetically.

#### film schema

```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
title       | character varying(255)      | not null
film_id     | smallint                    | not null
```

#### film_actor schema

```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
actor_id    | smallint                    | not null
film_id     | smallint                    | not null
last_update | timestamp without time zone | not null 
```

#### actor schema

```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
actor_id    | integer                     | not null 
first_name  | character varying(45)       | not null
last_name   | character varying(45)       | not null
last_update | timestamp without time zone | not null 
```

The desired output:

```
title
-------------
Film Title 1
Film Title 2
...
```

- `title` - Film title

**Solution**

```
with f2 as(select film_id from film_actor
where actor_id = 122),
f1 as(select film_id from film_actor
where actor_id = 105)
select title from film
where film_id in
(select f1.film_id from f1 join
f2 on f1.film_id = f2.film_id);
```



## Group By Day

There is an `events` table used to track different key activities taken on a website. For this task you need to filter the `name` field to only show "trained" events. Events should be grouped by the day they happened and **counted**. The `description` field is used to distinguish which items the events happened on.

### "events" Table Schema

- id
- name (String)
- created_at (DateTime)
- description (String)

The expected results is provided so that you can see what the expected output is supposed to look like. Your "actual" output needs to match the expected output.

> NOTE: Data is regenerated on each run, so don't expect to get the same data back twice.

**Solution**

```
select created_at::date as day, description,
count(*) from events
where name = 'trained'
group by day, description;
```



## SQL Basics: Simple Hierarchical structure

For this challenge you need to create a RECURSIVE Hierarchical query. You have a table `employees` of employees, you must order each employee by level. You must use a WITH statement and name it `employee_levels` after that has been defined you must select from it.

### employees table schema

- id
- first_name
- last_name
- manager_id (can be NULL)

### resultant schema

- level
- id
- first_name
- last_name
- manager_id (can be NULL)

> NOTE: refer to the `Results: expected` table if you're stuck with how it should be displayed.

**Solution**

```
WITH recursive employee_levels AS (SELECT id,first_name,last_name,manager_id, 1 as level 
FROM Employees
WHERE MANAGER_ID IS NULL
UNION ALL
SELECT E.id, E.first_name,E.last_name, E.manager_id, CTE.level + 1 
FROM EMPloyees E
INNER JOIN employee_levels cte ON E.manager_id = CTE.id
WHERE E.manager_id IS NOT NULL)
SELECT *
FROM employee_levels
ORDER BY LEVEL;
```



## Calculating Running Total

### Description

Given a `posts` table that contains a `created_at` timestamp column write a query that returns date (without time component), a number of posts for a given date and a running (cumulative) total number of posts up until a given date. The resulting set should be ordered chronologically by `date`.

### Desired Output

The resulting set should look similar to the following:

```
date       | count | total
-----------+-------+-------
2017-01-26 |    20 |    20
2017-01-27 |    17 |    37
2017-01-28 |     7 |    44
2017-01-29 |     8 |    52
...
```

- date - (DATE) date
- count - (INT) number of posts for a date
- total - (INT) a running (cumulative) number of posts up until a date

**Solution**

```
select created_at::date as date, count(title) as count,
cast(sum(count(title)) over(order by created_at::date rows unbounded preceding) as integer) as total
from posts

group by date
order by date;
```



## Monsters using CASE

ou have access to two tables named top_half and bottom_half, as follows:

**top_half schema**

- id
- heads
- arms

**bottom_half schema**

- id
- legs
- tails

You must return a table with the format as follows:

**output schema**

- id
- heads
- legs
- arms
- tails
- species

The IDs on the tables match to make a full monster. For heads, arms, legs and tails you need to draw in the data from each table.

For the species, if the monster has more heads than arms, more tails than legs, or both, it is a 'BEAST' else it is a 'WEIRDO'. This needs to be captured in the species column.

All rows should be returned (10).

Tests require the use of CASE. Order by species.

Please use pure SQL, only testing is done in Ruby.



**Solution**

```
select top_half.id, heads,legs,arms,tails,
(case when 
heads > arms or tails > legs then 'BEAST'
ELSE 'WEIRDO' end) AS species from top_half
join bottom_half on top_half.id = bottom_half.id
order by species;
```



## SQL Bug Fixing: Fix the QUERY - Totaling

Oh no! Timmys been moved into the database divison of his software company but as we know Timmy loves making mistakes. Help Timmy keep his job by fixing his query...

Timmy works for a statistical analysis company and has been given a task of totaling the number of sales on a given day grouped by each department name and then each day.

### Resultant table:

- day (type: date) {group by} [order by asc]
- department (type: text) {group by} [In a real world situation it is bad practice to name a column after a table]
- sale_count (type: int)

### Tables and relationship below:

![img](https://i.imgur.com/kBkwsbi.png)



**Solution**

```
SELECT 
  date(s.transaction_date) as day,
  d.name as department,
  COUNT(s.id) as sale_count from department as d
    JOIN sale s on d.id = s.DEPARTMENT_id
  group by day, d.name
  order by day;

```



## Simple JOIN and RANK

For this challenge you need to create a simple SELECT statement that will return all columns from the `people` table, and join to the `sales` table so that you can return the COUNT of all sales and RANK each person by their sale_count.

### people table schema

- id
- name

### sales table schema

- id
- people_id
- sale
- price

You should return all people fields as well as the sale count as "sale_count" and the rank as "sale_rank".

> NOTE: Your solution should use pure SQL. Ruby is used within the test cases to do the actual testing.

**Solution**

```
select people.id, name, count(sales.sale) as sale_count, 
rank() over(order by count(sales.sale) desc) as sale_rank from people
join sales on people.id = sales.people_id
group by people.id;
```



## Simple EXISTS

For this challenge you need to create a SELECT statement, this SELECT statement will use an EXISTS to check whether a department has had a sale with a price over 98.00 dollars.

### departments table schema

- id
- name

### sales table schema

- id
- department_id (department foreign key)
- name
- price
- card_name
- card_number
- transaction_date

### resultant table schema

- id
- name

> NOTE: Your solution should use pure SQL. Ruby is used within the test cases to do the actual testing.

**Solution**

```
select id,name from departments
where exists(select department_id from sales where price > 98);
```



## GROCERY STORE: Inventory

You are the owner of the Grocery Store. All your products are in the database, that you have created after CodeWars SQL excercises!:)

Today you are going to CompanyA warehouse

You need to check what products are running out of stock, to know which you need buy in a CompanyA warehouse.

Use SELECT to show id, name, stock from products which are only 2 or less item in the stock and are from CompanyA.

Order the results by product id

### products table schema

- id
- name
- price
- stock
- producent

### results table schema

- id
- name
- stock

**Solution**

```
select id,name,stock from products
where stock <=2 and producent = 'CompanyA'
order by id;
```



## Simple WITH

For this challenge you need to create a SELECT statement, this SELECT statement will use an IN to check whether a department has had a sale with a price over 90.00 dollars BUT the sql MUST use the WITH statement which will be used to select all columns from `sales` where the price is greater than 90.00, you must call this sub-query `special_sales`.

### departments table schema

- id
- name

### sales table schema

- id
- department_id (department foreign key)
- name
- price
- card_name
- card_number
- transaction_date

### resultant table schema

- id
- name

> NOTE: Your solution should use pure SQL. Ruby is used within the test cases to do the actual testing.



**Solution**

```
with special_sales as (
select * from sales where price > 90)
select id, name from departments
where id in (select department_id from special_sales);
```



## Up and Down

Given a table of random numbers as follows:

** numbers table schema **

- id
- number1
- number2

Your job is to return table with similar structure and headings, where if the sum of a column is odd, the column shows the minimum value for that column, and when the sum is even, it shows the max value. You must use a case statement.

** output table schema **

- number1
- number2

**Solution**

```
select case when mod(sum(number1),2) = 0 then max(number1) 
when mod(sum(number1),2) <> 0 then min(number1)end as number1,
case when mod(sum(number2),2) = 0 then max(number2) 
when mod(sum(number2),2) <> 0 then min(number2) end as number2
from numbers;
```



## Conditional Count

Given a `payment` table, which is a part of [DVD Rental Sample Database](http://www.postgresqltutorial.com/postgresql-sample-database/), with the following schema

```
Column       | Type                        | Modifiers
-------------+-----------------------------+----------
payment_id   | integer                     | not null 
customer_id  | smallint                    | not null
staff_id     | smallint                    | not null
rental_id    | integer                     | not null
amount       | numeric(5,2)                | not null
payment_date | timestamp without time zone | not null
```

produce a result set for the report that shows a side-by-side comparison of the number and total amounts of payments made in Mike's and Jon's stores broken down by months.

The resulting data set should be ordered by `month` using natural order (Jan, Feb, Mar, etc.).

Note: *You don't need to worry about the year component. Months are never repeated because the sample data set contains payment information only for one year.*

The desired output for the report

```
month | total_count | total_amount | mike_count | mike_amount | jon_count | jon_amount
------+-------------+--------------+------------+-------------+-----------+-----------
2     |             |              |            |             |           |           
5     |             |              |            |             |           |           
...
```

- `month` - number of the month (1 - January, 2 - February, etc.)
- `total_count` - total number of payments
- `total_amount` - total payment amount
- `mike_count` - total number of payments accepted by Mike (`staff_id = 1`)
- `mike_amount` - total amount of payments accepted by Mike (`staff_id = 1`)
- `jon_count` - total number of payments accepted by Jon (`staff_id = 2`)
- `jon_amount` - total amount of payments accepted by Jon (`staff_id = 2`)



**Solution**

```
select extract(month from payment_date) as month,count(payment_id) as total_count,
sum(amount) as total_amount, COUNT(CASE WHEN staff_id = 1 then payment_id end) as mike_count,
SUM(CASE WHEN staff_id = 1 then amount end) as mike_amount, 
COUNT(CASE WHEN staff_id = 2 then payment_id end) as jon_count,
SUM(CASE WHEN staff_id = 2 then amount end) as jon_amount from payment
group by month
order by month;

```



## SQL with LOTR: Eleven Wildcards

Deep within the fair realm of Lothlórien, you have been asked to create a shortlist of candidates for a recently vacated position on the council.

Of so many worthy elves, who to choose for such a lofty position? After much thought you decide to select candidates by name, which are often closely aligned to an elf's skill and temperament.

Choose those with *tegil* appearing anywhere in their **first name**, as they are likely to be good calligraphers, **OR** those with *astar* anywhere in their **last name**, who will be faithful to the role.

Elves table:

- firstname
- lastname

*all names are in lowercase*

To aid the scribes, return the *firstname* and *lastname* column concatenated, separated by a space, into a single *shortlist* column, and capitalise the first letter of each name.



**Solution**

```
select initcap(concat(firstname,' ',lastname)) as shortlist from elves
where firstname like '%tegil%' OR lastname like '%astar%';
```



## Grocery store: Logistic Optimisation

You are the owner of the Grocery Store. All your products are in the database, that you have created after CodeWars SQL excercises!:)

You have reached a conclusion that you waste to much time because you have to many different warehouse to visit each week.

You have to find out how many unique products have each of the Producer. If you take only few items from some of them you will stop going there to save the gasoline:)

In the results show producer and unique_products which you buy from him.

Order the result by unique_products (DESC) then by producer (ASC) in case there are duplicate amounts,

### products table schema

- id
- name
- price
- stock
- producer

### results table schema

- unique_products
- producer

**Solution**

```
select count(distinct id) as unique_products, producer from products
group by producer
order by unique_products desc, producer asc;
```



## Counting and Grouping

Given a demographics table in the following format:

** demographics table schema **

- id
- name
- birthday
- race

you need to return a table that shows a count of each race represented, ordered by the count in descending order as:

** output table schema **

- race
- count



**Solution**

```
select race, count(race) as count from demographics
group by race
order by count desc;
```



