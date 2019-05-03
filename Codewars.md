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

