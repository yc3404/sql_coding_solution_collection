# Leetcode 

## LC 175 Combining Two Tables

Table: `Person`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
PersonId is the primary key column for this table.
```

Table: `Address`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
AddressId is the primary key column for this table.
```

 

Write a SQL query for a report that provides the following information for each person in the Person table, regardless if there is an address for each of those people:

```
FirstName, LastName, City, State
```

**Solution in MySQL**

```
SELECT P.FirstName, P.LastName, A.City, A.State FROM Person P
LEFT JOIN Address A ON P.PersonId=A.PersonId;
```



## LC 176 Second Highest Salary

Write a SQL query to get the second highest salary from the `Employee`table.

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

For example, given the above Employee table, the query should return `200`as the second highest salary. If there is no second highest salary, then the query should return `null`.

```
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

**Solution in SQL Server**

```
select Salary AS SecondHighestSalary from
(select Salary, dense_rank() OVER(ORDER BY Salary DESC) as s_rank from Employee) AS E1
WHERE E1.s_rank = 2;
```

**Solution in MySQL**

```
select max(salary) as SecondHighestSalary from employee
where salary < 
    (select max(salary)
     from employee) ;


```



## LC 177 Nth Highest Salary

Write a SQL query to get the *n*th highest salary from the `Employee` table.

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

For example, given the above Employee table, the *n*th highest salary where *n* = 2 is `200`. If there is no *n*th highest salary, then the query should return `null`.

```
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+
```



**Solution in MySQL**

```mysql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
DECLARE m int;
SET m = n-1;
  RETURN (

​      SELECT DISTINCT Salary
​      FROM Employee
​      ORDER BY Salary DESC
​      LIMIT m,1
​      
  );
END
```



## LC 178 Rank Scores

Write a SQL query to rank scores. If there is a tie between two scores, both should have the same ranking. Note that after a tie, the next ranking number should be the next consecutive integer value. In other words, there should be no "holes" between ranks.

```
+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
```

For example, given the above `Scores` table, your query should generate the following report (order by highest score):

```
+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```

**Solution in MySQL**

```
SELECT  Score, ROW_NUMBER() OVER (ORDER BY Score DESC) Rank
FROM Scores
ORDER BY Score DESC;
```

**Solution in SQL Server**

```
select Score, dense_rank() over (Order by Score desc) AS Rank from scores;
```



## LC 185 Department Top Three Salaries

The `Employee` table holds all employees. Every employee has an Id, and there is also a column for the department Id.

```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |
+----+-------+--------+--------------+
```

The `Department` table holds all departments of the company.

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

Write a SQL query to find employees who earn the top three salaries in each of the department. For the above tables, your SQL query should return the following rows (order of rows does not matter).

```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
```

**Explanation:**

In IT department, Max earns the highest salary, both Randy and Joe earn the second highest salary, and Will earns the third highest salary. There are only two employees in the Sales department, Henry earns the highest salary while Sam earns the second highest salary.



**Solution in SQL Server**

```
select rank_salary.Department, rank_salary.Employee,rank_salary.Salary FROM (
select Department.Name AS Department, Employee.Name AS Employee, Salary, dense_rank() OVER 
(PARTITION BY Department.Name ORDER BY Salary DESC) AS R1
FROM Employee JOIN Department ON Employee.DepartmentID = Department.Id) AS rank_salary
WHERE R1 <= 3;
```

**Solution in MySQL**

```
select Department,Employee,Salary
from 
(
select d.Name as Department, 
       e.name as Employee, 
       e.Salary as Salary, 
       DENSE_RANK() over (partition by d.Name order by e.Salary desc) as row_num
    
from Employee as e inner join Department as d on e.DepartmentId = d.Id
) as sub
where row_num <= 3
order by 1,3 desc;


```



## LC 181 Employees Earning More Than Their Managers

The `Employee` table holds all employees including their managers. Every employee has an Id, and there is also a column for the manager Id.

```
+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
```

Given the `Employee` table, write a SQL query that finds out employees who earn more than their managers. For the above table, Joe is the only employee who earns more than his manager.

```
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```

**Solution in MySQL**

```
SELECT A.Name AS Employee FROM Employee A
WHERE ManagerId IS NOT NULL AND Salary > (SELECT Salary FROM Employee WHERE Id = A.ManagerId);


```



## LC 595 Big Countries

There is a table `World`

```
+-----------------+------------+------------+--------------+---------------+
| name            | continent  | area       | population   | gdp           |
+-----------------+------------+------------+--------------+---------------+
| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
| Albania         | Europe     | 28748      | 2831741      | 12960000      |
| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
| Andorra         | Europe     | 468        | 78115        | 3712000       |
| Angola          | Africa     | 1246700    | 20609294     | 100990000     |
+-----------------+------------+------------+--------------+---------------+
```

A country is big if it has an area of bigger than 3 million square km or a population of more than 25 million.

Write a SQL solution to output big countries' name, population and area.

For example, according to the above table, we should output:

```
+--------------+-------------+--------------+
| name         | population  | area         |
+--------------+-------------+--------------+
| Afghanistan  | 25500100    | 652230       |
| Algeria      | 37100000    | 2381741      |
+--------------+-------------+--------------+
```

 **Solution in MySQL**

```
SELECT name, population, area FROM World
WHERE area > 3000000 OR population > 25000000;
```



## LC 196 Delete Duplicate Emails

Write a SQL query to **delete** all duplicate email entries in a table named `Person`, keeping only unique emails based on its *smallest* **Id**.

```
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
Id is the primary key column for this table.
```

For example, after running your query, the above `Person` table should have the following rows:

```
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```

**Note:**

Your output is the whole `Person` table after executing your sql. Use `delete` statement.

**Solution in MySQL**

```
DELETE P1 FROM Person P1, Person P2
WHERE P1.Email = P2.Email AND P1.Id > P2.Id;


```



## LC 627 Swap Salary

Given a table `salary`, such as the one below, that has m=male and f=female values. Swap all f and m values (i.e., change all f values to m and vice versa) with a **single update statement** and no intermediate temp table.

Note that you must write a single update statement, **DO NOT** write any select statement for this problem.

 

**Example:**

```
| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |
```

After running your 

update

 statement, the above salary table should have the following rows:

```
| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |
```

**Solution in MySQL**

```
UPDATE salary
SET sex = CASE WHEN sex = 'f' THEN 'm' ELSE 'f' END;


```



## LC 184 Department Highest Salary

The `Employee` table holds all employees. Every employee has an Id, a salary, and there is also a column for the department Id.

```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
```

The `Department` table holds all departments of the company.

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

Write a SQL query to find employees who have the highest salary in each of the departments. For the above tables, your SQL query should return the following rows (order of rows does not matter).

```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```

**Explanation:**

Max and Jim both have the highest salary in the IT department and Henry has the highest salary in the Sales department.



**Solution in MySQL**

```
SELECT Department.Name AS Department, Employee.Name AS Employee, Salary FROM Employee
JOIN Department ON (DepartmentId = Department.Id)
WHERE (Employee.DepartmentId, Salary) IN (
SELECT DepartmentId, MAX(Salary) FROM Employee
GROUP BY DepartmentId);


```



## LC 262 Trips and users

The `Trips` table holds all taxi trips. Each trip has a unique Id, while Client_Id and Driver_Id are both foreign keys to the Users_Id at the `Users`table. Status is an ENUM type of (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’).

```
+----+-----------+-----------+---------+--------------------+----------+
| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
+----+-----------+-----------+---------+--------------------+----------+
| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|
+----+-----------+-----------+---------+--------------------+----------+
```

The `Users` table holds all users. Each user has an unique Users_Id, and Role is an ENUM type of (‘client’, ‘driver’, ‘partner’).

```
+----------+--------+--------+
| Users_Id | Banned |  Role  |
+----------+--------+--------+
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |
+----------+--------+--------+
```

Write a SQL query to find the cancellation rate of requests made by unbanned users between **Oct 1, 2013** and **Oct 3, 2013**. For the above tables, your SQL query should return the following rows with the cancellation rate being rounded to *two* decimal places.

```
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+
```

**Credits:**
Special thanks to [@cak1erlizhou](https://leetcode.com/discuss/user/cak1erlizhou) for contributing this question, writing the problem description and adding part of the test cases.



**Solution in MySQL**

```
select Request_at as "Day",
       round(sum(case when status in ('cancelled_by_client', 'cancelled_by_driver') then 1 else 0 end) / sum(1), 2) as "Cancellation Rate"
  from Trips t
  join Users u on u.Users_Id = t.Client_Id
 where u.role = 'client' and u.banned = 'No'
   and t.Request_at between '2013-10-01' and '2013-10-03'
  group by 1
  order by 1;
```



## LC 197 Rising temperatures

Given a `Weather` table, write a SQL query to find all dates' Ids with higher temperature compared to its previous (yesterday's) dates.

```
+---------+------------------+------------------+
| Id(INT) | RecordDate(DATE) | Temperature(INT) |
+---------+------------------+------------------+
|       1 |       2015-01-01 |               10 |
|       2 |       2015-01-02 |               25 |
|       3 |       2015-01-03 |               20 |
|       4 |       2015-01-04 |               30 |
+---------+------------------+------------------+
```

For example, return the following Ids for the above `Weather` table:

```

+----+
| Id |
+----+
|  2 |
|  4 |
+----+
```

**Solution in MySQL**

```
SELECT A.Id FROM Weather A, Weather B
WHERE A.RecordDate = B.RecordDate + INTERVAL 1 DAY AND A.Temperature > B.Temperature;
```



## LC 626 Exchange Seats

Mary is a teacher in a middle school and she has a table `seat` storing students' names and their corresponding seat ids.

The column id is continuous increment.

 

Mary wants to change seats for the adjacent students.

 

Can you write a SQL query to output the result for Mary?

 

```
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Abbot   |
|    2    | Doris   |
|    3    | Emerson |
|    4    | Green   |
|    5    | Jeames  |
+---------+---------+
```

For the sample input, the output is:

 

```
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Doris   |
|    2    | Abbot   |
|    3    | Green   |
|    4    | Emerson |
|    5    | Jeames  |
+---------+---------+
```

**Note:**
If the number of students is odd, there is no need to change the last one's seat.



**Solution in MySQL**

```
SELECT (CASE WHEN MOD(id,2) != 0 AND counts = id THEN id
       WHEN MOD(id,2)!=0 AND counts != id THEN id+1
       ELSE id-1 END) AS id, student FROM seat, (SELECT COUNT(*) AS counts from seat) AS seat_counts
       ORDER BY id ASC;
```

**Solution in SQL Server**

```
select (case when id % 2 = 0 then id - 1 
        when id % 2 = 1  and id <> (select max(id) as count1 from seat)then id+1
        else id end) AS id, student from seat
        order by id;


```



## LC 180 Consecutive numbers

Write a SQL query to find all numbers that appear at least three times consecutively.

```
+----+-----+
| Id | Num |
+----+-----+
| 1  |  1  |
| 2  |  1  |
| 3  |  1  |
| 4  |  2  |
| 5  |  1  |
| 6  |  2  |
| 7  |  2  |
+----+-----+
```

For example, given the above `Logs` table, `1` is the only number that appears consecutively for at least three times.

```
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
```

**Solution in MySQL**

```
SELECT DISTINCT L1.Num AS ConsecutiveNums FROM Logs L1,Logs L2,Logs L3
WHERE L1.Id=L2.Id +1 AND L2.Id = L3.Id + 1
AND L1.Num = L2.Num 
AND L2.Num = L3.Num
AND L3.Num = L1.Num;
```



## LC 182 Duplicate Emails

Write a SQL query to find all duplicate emails in a table named `Person`.

```
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```

For example, your query should return the following for the above table:

```
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```

**Note**: All emails are in lowercase.

**Solution in MySQL**

```
SELECT Email FROM Person
GROUP BY Email 
HAVING COUNT(Email) > 1;


```



## LC 164 Second Degree Follower

In facebook, there is a `follow` table with two columns: **followee**, **follower**.

Please write a sql query to get the amount of each follower’s follower if he/she has one.

For example:

```
+-------------+------------+
| followee    | follower   |
+-------------+------------+
|     A       |     B      |
|     B       |     C      |
|     B       |     D      |
|     D       |     E      |
+-------------+------------+
```

should output:

```
+-------------+------------+
| follower    | num        |
+-------------+------------+
|     B       |  2         |
|     D       |  1         |
+-------------+------------+
```

Explaination:

Both B and D exist in the follower list, when as a followee, B's follower is C and D, and D's follower is E. A does not exist in follower list.

 

 

Note:

Followee would not follow himself/herself in all cases.

Please display the result in follower's alphabet order.



**Solution in MySQL**

```
SELECT a.follower, COUNT(DISTINCT b.follower) AS num FROM follow a
JOIN follow b ON a.follower=b.followee
GROUP BY a.follower
ORDER BY a.follower;


```



## LC 183 Customers Who Never Order

Suppose that a website contains two tables, the `Customers` table and the `Orders` table. Write a SQL query to find all customers who never order anything.

Table: `Customers`.

```
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
```

Table: `Orders`.

```
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

Using the above tables as example, return the following:

```
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

**Solution in MySQL**

```
select name as Customers from customers
where id not in
(select customerid from orders);


```



## LC 596 Classes more than 5 students

There is a table `courses` with columns: **student** and **class**

Please list out all classes which have more than or equal to 5 students.

For example, the table:

```
+---------+------------+
| student | class      |
+---------+------------+
| A       | Math       |
| B       | English    |
| C       | Math       |
| D       | Biology    |
| E       | Math       |
| F       | Computer   |
| G       | Math       |
| H       | Math       |
| I       | Math       |
+---------+------------+
```

Should output:

```
+---------+
| class   |
+---------+
| Math    |
+---------+
```

 

**Note:**
The students should not be counted duplicate in each course.

**Solution in MySQL**

```
SELECT
    class
FROM
    courses
GROUP BY class
HAVING COUNT(DISTINCT student) >= 5
;
```



## LC 585 Investments in 2016

Write a query to print the sum of all total investment values in 2016 (**TIV_2016**), to a scale of 2 decimal places, for all policy holders who meet the following criteria:

1. Have the same **TIV_2015** value as one or more other policyholders.
2. Are not located in the same city as any other policyholder (i.e.: the (latitude, longitude) attribute pairs must be unique).

**Input Format:**
The **insurance** table is described as follows:

```
| Column Name | Type          |
|-------------|---------------|
| PID         | INTEGER(11)   |
| TIV_2015    | NUMERIC(15,2) |
| TIV_2016    | NUMERIC(15,2) |
| LAT         | NUMERIC(5,2)  |
| LON         | NUMERIC(5,2)  |
```

where **PID** is the policyholder's policy ID, **TIV_2015** is the total investment value in 2015, **TIV_2016** is the total investment value in 2016, **LAT** is the latitude of the policy holder's city, and **LON** is the longitude of the policy holder's city.

**Sample Input**

```
| PID | TIV_2015 | TIV_2016 | LAT | LON |
|-----|----------|----------|-----|-----|
| 1   | 10       | 5        | 10  | 10  |
| 2   | 20       | 20       | 20  | 20  |
| 3   | 10       | 30       | 20  | 20  |
| 4   | 10       | 40       | 40  | 40  |
```

**Sample Output**

```
| TIV_2016 |
|----------|
| 45.00    |
```

**Explanation**

```
The first record in the table, like the last record, meets both of the two criteria.
The TIV_2015 value '10' is as the same as the third and forth record, and its location unique.

The second record does not meet any of the two criteria. Its TIV_2015 is not like any other policyholders.

And its location is the same with the third record, which makes the third record fail, too.

So, the result is the sum of TIV_2016 of the first and last record, which is 45.
```



**Solution in SQL Server**

```
select SUM(TIV_2016) AS TIV_2016 FROM INSURANCE WHERE TIV_2015 IN 
(SELECT TIV_2015 FROM INSURANCE GROUP BY TIV_2015 HAVING COUNT(TIV_2015) > 1)
AND CONCAT(LAT,LON) IN (SELECT CONCAT(LAT,LON) FROM INSURANCE GROUP BY CONCAT(LAT,LON) HAVING COUNT(CONCAT(LAT,LON))=1);


```



## LC 602 Friend Requests II: Who Has the Most Friends

In social network like Facebook or Twitter, people send friend requests and accept others' requests as well.

 

Table 

```
request_accepted
```

 holds the data of friend acceptance, while 

requester_id

 and 

accepter_id

 both are the id of a person.

 

```
| requester_id | accepter_id | accept_date|
|--------------|-------------|------------|
| 1            | 2           | 2016_06-03 |
| 1            | 3           | 2016-06-08 |
| 2            | 3           | 2016-06-08 |
| 3            | 4           | 2016-06-09 |
```

Write a query to find the the people who has most friends and the most friends number. For the sample data above, the result is:

```
| id | num |
|----|-----|
| 3  | 3   |
```

Note:



- It is guaranteed there is only 1 people having the most friends.

- The friend request could only been accepted once, which mean there is no multiple records with the same requester_id and accepter_id value.

   

  Explanation:

  The person with id '3' is a friend of people '1', '2' and '4', so he has 3 friends in total, which is the most number than any others.

   

  Follow-up:

  In the real world, multiple people could have the same most number of friends, can you find all these people in this case?



**Solution in SQL Server**

```
SELECT ids as id, cnt as num from(
select ids, count(*) as cnt from(
SELECT requester_id AS ids from request_accepted
UNION ALL
SELECT accepter_id FROM request_accepted) as tb1
    group by ids) as tb2
    order by cnt desc
    offset 0 rows fetch next 1 rows only;
```



## LC 603 Consecutive Available Seats

Several friends at a cinema ticket office would like to reserve consecutive available seats.

Can you help to query all the consecutive available seats order by the seat_id using the following 

```
cinema
```

 table?

```
| seat_id | free |
|---------|------|
| 1       | 1    |
| 2       | 0    |
| 3       | 1    |
| 4       | 1    |
| 5       | 1    |
```

 

Your query should return the following result for the sample case above.

 

```
| seat_id |
|---------|
| 3       |
| 4       |
| 5       |
```

Note:

- The seat_id is an auto increment int, and free is bool ('1' means free, and '0' means occupied.).
- Consecutive available seats are more than 2(inclusive) seats consecutively available.

**Solution in SQL Server**

```
select distinct s1.seat_id from cinema s1 
join cinema s2 on abs(s1.seat_id - s2.seat_id) = 1 and s1.free = 1 and s2.free = 1
order by s1.seat_id;


```



## LC 584 Find Customer Referee

Given a table `customer` holding customers information and the referee.

```
+------+------+-----------+
| id   | name | referee_id|
+------+------+-----------+
|    1 | Will |      NULL |
|    2 | Jane |      NULL |
|    3 | Alex |         2 |
|    4 | Bill |      NULL |
|    5 | Zack |         1 |
|    6 | Mark |         2 |
+------+------+-----------+
```

Write a query to return the list of customers **NOT** referred by the person with id '2'.

For the sample data above, the result is:

```
+------+
| name |
+------+
| Will |
| Jane |
| Bill |
| Zack |
+------+
```

**Solution in MySQL**

```
SELECT name FROM customer
WHERE referee_id<>2 OR referee_id IS NULL;
```



## LC 613 Shortest Distance in a Line

Table point holds the x coordinate of some points on x-axis in a plane, which are all integers.

 

Write a query to find the shortest distance between two points in these points.

 

```
| x   |
|-----|
| -1  |
| 0   |
| 2   |
```

 

The shortest distance is '1' obviously, which is from point '-1' to '0'. So the output is as below:

 

```
| shortest|
|---------|
| 1       |
```

 

Note:

 Every point is unique, which means there is no duplicates in table point.



 

Follow-up:

 What if all these points have an id and are arranged from the left most to the right most of x axis?



**Solution in MySQL**

```
SELECT MIN(ABS(p1.x-p2.x)) AS shortest FROM point p1, point p2
WHERE p1.x != p2.x;


```



## LC 620 Not boring movies

X city opened a new cinema, many people would like to go to this cinema. The cinema also gives out a poster indicating the movies’ ratings and descriptions.

Please write a SQL query to output movies with an odd numbered ID and a description that is not 'boring'. Order the result by rating.

 

For example, table `cinema`:

```
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   1     | War       |   great 3D   |   8.9     |
|   2     | Science   |   fiction    |   8.5     |
|   3     | irish     |   boring     |   6.2     |
|   4     | Ice song  |   Fantacy    |   8.6     |
|   5     | House card|   Interesting|   9.1     |
+---------+-----------+--------------+-----------+
```

For the example above, the output should be:

```
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   5     | House card|   Interesting|   9.1     |
|   1     | War       |   great 3D   |   8.9     |
+---------+-----------+--------------+-----------+
```



**Solution in MySQL**

```
SELECT * FROM cinema
WHERE (id%2) <> 0 AND description NOT LIKE '%boring%'
ORDER BY rating DESC;


```



## LC 619 Biggest Single Number

Table `my_numbers` contains many numbers in column **num** including duplicated ones.
Can you write a SQL query to find the biggest number, which only appears once.

```
+---+
|num|
+---+
| 8 |
| 8 |
| 3 |
| 3 |
| 1 |
| 4 |
| 5 |
| 6 | 
```

For the sample data above, your query should return the following result:

```
+---+
|num|
+---+
| 6 |
```

Note:

If there is no such number, just output null.



**Solution in MySQL**

```
SELECT MAX(num) num FROM (SELECT num FROM number
GROUP BY num
HAVING COUNT(num)=1) as t;


```



## LC 610 Triangle Judgement

A pupil Tim gets homework to identify whether three line segments could possibly form a triangle.

 

However, this assignment is very heavy because there are hundreds of records to calculate.

 

Could you help Tim by writing a query to judge whether these three sides can form a triangle, assuming table 

```
triangle
```

 holds the length of the three sides x, y and z.

 

```
| x  | y  | z  |
|----|----|----|
| 13 | 15 | 30 |
| 10 | 20 | 15 |
```

For the sample data above, your query should return the follow result:

```
| x  | y  | z  | triangle |
|----|----|----|----------|
| 13 | 15 | 30 | No       |
| 10 | 20 | 15 | Yes      |
```



**Solution in MySQL**

```
SELECT x,y,z,
CASE WHEN x + y <= z THEN 'No'
     WHEN x + z <= y THEN 'No' 
     WHEN y + z <= x THEN 'No' 
     ELSE 'Yes' END AS triangle
     FROM triangle;


```



## LC 607 Sales person

**Description**

Given three tables: `salesperson`, `company`, `orders`.
Output all the **names** in the table `salesperson`, who didn’t have sales to company 'RED'.

**Example**
**Input**

Table: `salesperson`

```
+----------+------+--------+-----------------+-----------+
| sales_id | name | salary | commission_rate | hire_date |
+----------+------+--------+-----------------+-----------+
|   1      | John | 100000 |     6           | 4/1/2006  |
|   2      | Amy  | 120000 |     5           | 5/1/2010  |
|   3      | Mark | 65000  |     12          | 12/25/2008|
|   4      | Pam  | 25000  |     25          | 1/1/2005  |
|   5      | Alex | 50000  |     10          | 2/3/2007  |
+----------+------+--------+-----------------+-----------+
```

The table 

```
salesperson
```

 holds the salesperson information. Every salesperson has a 

sales_id

 and a 

name

.

Table: `company`

```
+---------+--------+------------+
| com_id  |  name  |    city    |
+---------+--------+------------+
|   1     |  RED   |   Boston   |
|   2     | ORANGE |   New York |
|   3     | YELLOW |   Boston   |
|   4     | GREEN  |   Austin   |
+---------+--------+------------+
```

The table 

```
company
```

 holds the company information. Every company has a 

com_id

 and a 

name

.

Table: `orders`

```
+----------+------------+---------+----------+--------+
| order_id | order_date | com_id  | sales_id | amount |
+----------+------------+---------+----------+--------+
| 1        |   1/1/2014 |    3    |    4     | 100000 |
| 2        |   2/1/2014 |    4    |    5     | 5000   |
| 3        |   3/1/2014 |    1    |    1     | 50000  |
| 4        |   4/1/2014 |    1    |    4     | 25000  |
+----------+----------+---------+----------+--------+
```

The table 

```
orders
```

 holds the sales record information, salesperson and customer company are represented by sales_id and 

com_id.

**output**

```
+------+
| name | 
+------+
| Amy  | 
| Mark | 
| Alex |
+------+
```

**Explanation**

According to order '3' and '4' in table `orders`, it is easy to tell only salesperson 'John' and 'Alex' have sales to company 'RED',
so we need to output all the other **names** in table `salesperson`.

**Solution in SQL Server**

```
select name from salesperson
where sales_id NOT IN (
SELECT SALES_ID FROM ORDERS
JOIN COMPANY ON ORDERS.COM_ID = COMPANY.COM_ID
WHERE COMPANY.NAME = 'RED');
```

**Solution in MySQL**

```
select salesperson.name from salesperson 
JOIN orders ON SALESPERSON.SALES_ID = ORDERS.SALES_ID
JOIN company ON ORDERS.COM_ID = COMPANY.COM_ID;


```



## LC 597 Friend Requests I: Overall Acceptance Rate

In social network like Facebook or Twitter, people send friend requests and accept others’ requests as well. Now given two tables as below:

 

Table: 

```
friend_request
```



```
| sender_id | send_to_id |request_date|
|-----------|------------|------------|
| 1         | 2          | 2016_06-01 |
| 1         | 3          | 2016_06-01 |
| 1         | 4          | 2016_06-01 |
| 2         | 3          | 2016_06-02 |
| 3         | 4          | 2016-06-09 |
```

 

Table: 

```
request_accepted
```



```
| requester_id | accepter_id |accept_date |
|--------------|-------------|------------|
| 1            | 2           | 2016_06-03 |
| 1            | 3           | 2016-06-08 |
| 2            | 3           | 2016-06-08 |
| 3            | 4           | 2016-06-09 |
| 3            | 4           | 2016-06-10 |
```

 

Write a query to find the overall acceptance rate of requests rounded to 2 decimals, which is the number of acceptance divide the number of requests.

 

For the sample data above, your query should return the following result.

 

```
|accept_rate|
|-----------|
|       0.80|
```

 

Note:



- The accepted requests are not necessarily from the table `friend_request`. In this case, you just need to simply count the total accepted requests (no matter whether they are in the original requests), and divide it by the number of requests to get the acceptance rate.
- It is possible that a sender sends multiple requests to the same receiver, and a request could be accepted more than once. In this case, the ‘duplicated’ requests or acceptances are only counted once.
- If there is no requests at all, you should return 0.00 as the accept_rate.

 

Explanation:

 There are 4 unique accepted requests, and there are 5 requests in total. So the rate is 0.80.

 

Follow-up:

- Can you write a query to return the accept rate but for every month?
- How about the cumulative accept rate for every day?

**Solution in MySQL**

```
select
(select count(distinct requester_id, accepter_id) from request_accepted)
/
(select count(distinct sender_id, send_to_id) from friend_request) 
as accepted_rate;
```



## LC 586 Customer Placing the Largest Number of Orders

Query the **customer_number** from the **orders** table for the customer who has placed the largest number of orders.

It is guaranteed that exactly one customer will have placed more orders than any other customer.

The **orders** table is defined as follows:

```
| Column            | Type      |
|-------------------|-----------|
| order_number (PK) | int       |
| customer_number   | int       |
| order_date        | date      |
| required_date     | date      |
| shipped_date      | date      |
| status            | char(15)  |
| comment           | char(200) |
```

**Sample Input**

```
| order_number | customer_number | order_date | required_date | shipped_date | status | comment |
|--------------|-----------------|------------|---------------|--------------|--------|---------|
| 1            | 1               | 2017-04-09 | 2017-04-13    | 2017-04-12   | Closed |         |
| 2            | 2               | 2017-04-15 | 2017-04-20    | 2017-04-18   | Closed |         |
| 3            | 3               | 2017-04-16 | 2017-04-25    | 2017-04-20   | Closed |         |
| 4            | 3               | 2017-04-18 | 2017-04-28    | 2017-04-25   | Closed |         |
```

**Sample Output**

```
| customer_number |
|-----------------|
| 3               |
```

**Explanation**

```
The customer with number '3' has two orders, which is greater than either customer '1' or '2' because each of them  only has one order. 
So the result is customer_number '3'.
```

***Follow up:** What if more than one customer have the largest number of orders, can you find all the customer_number in this case?*

**Solution in MySQL**

```
SELECT customer_number FROM orders
GROUP BY customer_number
ORDER BY COUNT(*) DESC LIMIT 1;
```



## LC 580 Count Student Number in Departments

A university uses 2 data tables, **student** and **department**, to store data about its students and the departments associated with each major.

Write a query to print the respective department name and number of students majoring in each department for all departments in the **department** table (even ones with no current students).

Sort your results by descending number of students; if two or more departments have the same number of students, then sort those departments alphabetically by department name.

The **student** is described as follow:

```
| Column Name  | Type      |
|--------------|-----------|
| student_id   | Integer   |
| student_name | String    |
| gender       | Character |
| dept_id      | Integer   |
```

where student_id is the student's ID number, student_name is the student's name, gender is their gender, and dept_id is the department ID associated with their declared major.

And the **department** table is described as below:

```
| Column Name | Type    |
|-------------|---------|
| dept_id     | Integer |
| dept_name   | String  |
```

where dept_id is the department's ID number and dept_name is the department name.

Here is an example **input**:
**student** table:

```
| student_id | student_name | gender | dept_id |
|------------|--------------|--------|---------|
| 1          | Jack         | M      | 1       |
| 2          | Jane         | F      | 1       |
| 3          | Mark         | M      | 2       |
```

**department** table:

```
| dept_id | dept_name   |
|---------|-------------|
| 1       | Engineering |
| 2       | Science     |
| 3       | Law         |
```

The **Output** should be:

```
| dept_name   | student_number |
|-------------|----------------|
| Engineering | 2              |
| Science     | 1              |
| Law         | 0              |
```



**Solution in MySQL**

```
SELECT dept_name, COUNT(student_name) AS student_number FROM student
RIGHT JOIN department ON student.dept_id = department.dept_id
GROUP BY dept_name
ORDER BY student_number DESC, dept_name;
```



## LC 578 Get Highest Answer Rate Question

Get the highest answer rate question from a table `survey_log` with these columns: **uid**, **action**, **question_id**, **answer_id**, **q_num**, **timestamp**.

uid means user id; action has these kind of values: "show", "answer", "skip"; answer_id is not null when action column is "answer", while is null for "show" and "skip"; q_num is the numeral order of the question in current session.

Write a sql query to identify the question which has the highest answer rate.

**Example:**

```
Input:
+------+-----------+--------------+------------+-----------+------------+
| uid  | action    | question_id  | answer_id  | q_num     | timestamp  |
+------+-----------+--------------+------------+-----------+------------+
| 5    | show      | 285          | null       | 1         | 123        |
| 5    | answer    | 285          | 124124     | 1         | 124        |
| 5    | show      | 369          | null       | 2         | 125        |
| 5    | skip      | 369          | null       | 2         | 126        |
+------+-----------+--------------+------------+-----------+------------+
Output:
+-------------+
| survey_log  |
+-------------+
|    285      |
+-------------+
Explanation:
question 285 has answer rate 1/1, while question 369 has 0/1 answer rate, so output 285.
```

 

**Note:** The highest answer rate meaning is: answer number's ratio in show number in the same question.



**Solution in SQL Server**

```
select question_id as survey_log from survey_log 
group by question_id
order by count(answer_id)/count(question_id) desc
offset 0 rows fetch next 1 rows only;
```

**Solution in MySQL**

```
SELECT question_id AS survey_log FROM survey_log
GROUP BY question_id
ORDER BY COUNT(answer_id)/COUNT(question_id) DESC LIMIT 1;


```



## LC 577 Employee Bonus

Select all employee's name and bonus whose bonus is < 1000.

Table:`Employee`

```
+-------+--------+-----------+--------+
| empId |  name  | supervisor| salary |
+-------+--------+-----------+--------+
|   1   | John   |  3        | 1000   |
|   2   | Dan    |  3        | 2000   |
|   3   | Brad   |  null     | 4000   |
|   4   | Thomas |  3        | 4000   |
+-------+--------+-----------+--------+
empId is the primary key column for this table.
```

Table: `Bonus`

```
+-------+-------+
| empId | bonus |
+-------+-------+
| 2     | 500   |
| 4     | 2000  |
+-------+-------+
empId is the primary key column for this table.
```

Example ouput:

```
+-------+-------+
| name  | bonus |
+-------+-------+
| John  | null  |
| Dan   | 500   |
| Brad  | null  |
+-------+-------+
```



**Solution in MySQL**

```
SELECT Employee.name, Bonus.bonus FROM Employee
LEFT JOIN Bonus ON Employee.empId = Bonus.empId
WHERE bonus < 1000 OR bonus IS NULL;
```



## LC 574 Winning Candidate

Table: `Candidate`

```
+-----+---------+
| id  | Name    |
+-----+---------+
| 1   | A       |
| 2   | B       |
| 3   | C       |
| 4   | D       |
| 5   | E       |
+-----+---------+  
```

Table: `Vote`

```
+-----+--------------+
| id  | CandidateId  |
+-----+--------------+
| 1   |     2        |
| 2   |     4        |
| 3   |     3        |
| 4   |     2        |
| 5   |     5        |
+-----+--------------+
id is the auto-increment primary key,
CandidateId is the id appeared in Candidate table.
```

Write a sql to find the name of the winning candidate, the above example will return the winner `B`.

```
+------+
| Name |
+------+
| B    |
+------+
```

**Notes:**

1. You may assume **there is no tie**, in other words there will be **at most one** winning candidate.

 

**Solution in SQL Server**

```
SELECT name AS Name FROM CANDIDATE JOIN (select  top 1 candidateid AS CID, count(*) as c from vote 
group by candidateid 
order by c desc) AS winner
on Candidate.id = winner.CID;
```

**Solution in MySQL**

```
with c1 as (select  candidateid, count(*) as c from vote 
group by candidateid 
order by c desc
LIMIT 1)
select Name from Candidate
where id = c1.candidateid;
```



## LC 570 Managers with at Least 5 Direct Reports

The `Employee` table holds all employees including their managers. Every employee has an Id, and there is also a column for the manager Id.

```
+------+----------+-----------+----------+
|Id    |Name 	  |Department |ManagerId |
+------+----------+-----------+----------+
|101   |John 	  |A 	      |null      |
|102   |Dan 	  |A 	      |101       |
|103   |James 	  |A 	      |101       |
|104   |Amy 	  |A 	      |101       |
|105   |Anne 	  |A 	      |101       |
|106   |Ron 	  |B 	      |101       |
+------+----------+-----------+----------+
```

Given the `Employee` table, write a SQL query that finds out managers with at least 5 direct report. For the above table, your SQL query should return:

```
+-------+
| Name  |
+-------+
| John  |
+-------+
```

**Note:**
No one would report to himself.



**Solution in SQL Server**

```
SELECT Name FROM EMPLOYEE AS T1
JOIN (SELECT ManagerId FROM Employee group by managerid having count(managerid) >= 5) AS T2
ON T1.ID = T2.MANAGERID;
```

