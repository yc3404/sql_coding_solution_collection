# SQL-EX exercises

  **Short database description "Computer firm":**

The database scheme consists of four tables:
Product(maker, model, type)
PC(code, model, speed, ram, hd, cd, price)
Laptop(code, model, speed, ram, hd, screen, price)
Printer(code, model, color, type, price)
The Product table contains data on the maker, model number, and type of product ('PC', 'Laptop', or 'Printer'). It is assumed that model numbers in the Product table are unique for all makers and product types. Each personal computer in the PC table is unambiguously identified by a unique code, and is additionally characterized by its model (foreign key referring to the Product table), processor speed (in MHz) – speed field, RAM capacity (in Mb) - ram, hard disk drive capacity (in Gb) – hd, CD-ROM speed (e.g, '4x') - cd, and its price. The Laptop table is similar to the PC table, except that instead of the CD-ROM speed, it contains the screen size (in inches) – screen. For each printer model in the Printer table, its output type (‘y’ for color and ‘n’ for monochrome) – color field, printing technology ('Laser', 'Jet', or 'Matrix') – type, and price are specified.  

**Computer firm schema**

![img](http://www.sql-ex.com/images/computers.gif)



**Exercise 1**

  Find the model number, speed and hard drive capacity for all the PCs with prices below $500.
Result set: model, speed, hd.

  **Solution**

```
SELECT model, speed, hd FROM PC
WHERE price < 500;
```



**Exercise 2**

List all printer makers. Result set: maker.

**Solution**

```
SELECT DISTINCT MAKER FROM Product
WHERE type = 'Printer';

```



**Exercise 3**

Find the model number, RAM and screen size of the laptops with prices over $1000.

**Solution**

```
SELECT model, ram, screen from Laptop
where price > 1000;

```



**Exercise 4**

Find all records from the Printer table containing data about color printers.

**Solution**

```
SELECT * FROM Printer
where color = 'y';

```



**Exercise 5**

Find the model number, speed and hard drive capacity of PCs cheaper than $600 having a 12x or a 24x CD drive.

**Solution**

```
SELECT model,speed,hd FROM PC
WHERE price < 600 AND (cd = '12x' OR cd = '24x');

```



**Exercise 6**

For each maker producing laptops with a hard drive capacity of 10 Gb or higher, find the speed of such laptops. Result set: maker, speed.

**Solution**

```
SELECT distinct maker,speed FROM LAPTOP
join product ON laptop.model = product.model
where laptop.hd >= 10
order by speed,maker;

```



**Exercise 7**

Get the models and prices for all commercially available products (of any type) produced by maker B.

**Solution**

```
with t1 as(SELECT model,price FROM PC
UNION
SELECT model,price FROM Laptop
UNION
SELECT model,price FROM Printer)
SELECT t1.model,t1.price FROM t1
JOIN Product ON t1.model = Product.model
WHERE product.maker = 'B';

```



**Exercise 8**

Find the makers producing PCs but not laptops.

**Solution**

```
SELECT maker FROM product WHERE type = 'PC'
EXCEPT 
SELECT maker FROM product WHERE type = 'Laptop';

```



**Exercise 9**

Find the makers of PCs with a processor speed of 450 MHz or more. Result set: maker.

**Solution**

```
SELECT DISTINCT maker FROM Product
JOIN PC ON product.model = PC.model
WHERE PC.speed >= 450;

```



**Exercise 10**

Find the printer models having the highest price. Result set: model, price.

**Solution**

```
SELECT model,price FROM Printer
where price IN
(Select price FROM Printer
order by price DESC
OFFSET 0 ROWS FETCH NEXT 1 ROWS ONLY);

```



**Exercise 11**

Find out the average speed of PCs.

**Solution**

```
SELECT AVG(speed) FROM pc;

```



**Exercise 12**

Find out the average speed of the laptops priced over $1000.

**Solution**

```
SELECT AVG(speed) FROM Laptop
WHERE price > 1000;

```



**Exercise 13**

Find out the average speed of the PCs produced by maker A.

**Solution**

```
SELECT AVG(speed) FROM PC
JOIN Product ON PC.model = Product.model
WHERE Product.maker = 'A';

```



**Exercise 14**

Get the makers who produce only one product type and more than one model. Output: maker, type.

**Solution**

```
Select distinct maker, type from product
where maker in 
(select distinct maker from product
group by maker
having count(distinct type) = 1 and count(model) > 1);

```



**Exercise 15**

Get hard drive capacities that are identical for two or more PCs. 
Result set: hd.

**Solution**

```
SELECT DISTINCT hd FROM PC
group by hd
HAVING COUNT(*) >= 2;

```



**Exercise 16**

Get pairs of PC models with identical speeds and the same RAM capacity. Each resulting pair should be displayed only once, i.e. (i, j) but not (j, i). 
Result set: model with the bigger number, model with the smaller number, speed, and RAM.

**Solution**

```
SELECT distinct P.model, L.model, P.speed, P.ram
FROM PC P JOIN pc L
ON (P.speed = L.speed AND P.ram = L.ram AND P.model > L.model);
```



**Exercise 17**

Get the laptop models that have a speed smaller than the speed of any PC. 
Result set: type, model, speed.

**Solution**

```
SELECT distinct type, laptop.model, laptop.speed FROM Laptop
JOIN Product ON laptop.model = product.model
WHERE speed < ALL
(select speed FROM PC );

```



**Exercise 18**

  Find the makers of the cheapest color printers.
Result set: maker, price.

  **Solution**

```
select distinct maker, price from printer
join product on printer.model = product.model
where color = 'y' and price in
(select min(price) from printer
where color = 'y');

```



**Exercise 19**

For each maker having models in the Laptop table, find out the average screen size of the laptops he produces. 
Result set: maker, average screen size.

**Solution**

```
SELECT maker, AVG(screen) from laptop
join product on laptop.model = product.model
group by maker;
```



**Exercise 20**

  Find the makers producing at least three distinct models of PCs.
Result set: maker, number of PC models.

  **Solution**

```
SELECT maker, count(distinct model) from product
where type = 'PC'
group by maker
having count(distinct model) >= 3
```



**Exercise 21**

Find out the maximum PC price for each maker having models in the PC table. Result set: maker, maximum price.

**Solution**

```
SELECT distinct maker, max(price) from PC
JOIN Product ON pc.model = product.model
group by maker;

```



**Exercise 22**

For each value of PC speed that exceeds 600 MHz, find out the average price of PCs with identical speeds.
Result set: speed, average price.

**Solution**

```
with pc1 as(
select * from pc
where speed > 600)
select speed, avg(price) as Avg_price FROM PC1
GROUP BY speed;
```



**Exercise 23**

Get the makers producing both PCs having a speed of 750 MHz or higher and laptops with a speed of 750 MHz or higher. 
Result set: maker

**Solution**

```
SELECT maker from product
join pc on product.model = pc.model
where pc.speed >= 750
INTERSECT
SELECT maker FROM product
join laptop on product.model = laptop.model
where laptop.speed >= 750;

```



**Exercise 24**

List the models of any type having the highest price of all products present in the database.

**Solution**

```
with all_table as (
SELECT model, price from PC
UNION
SELECT model,price from laptop
UNION
SELECT model,price from printer),
rank_table as(
select model, dense_rank() over(order by price desc) as rank from all_table)
select model from rank_table
where rank = 1;
```



**Exercise 25**

Find the printer makers also producing PCs with the lowest RAM capacity and the highest processor speed of all PCs having the lowest RAM capacity. 
Result set: maker.

**Solution**

```
SELECT DISTINCT maker FROM Product WHERE type = 'printer' AND maker IN 
  ( SELECT maker FROM Product WHERE model IN ( 
       SELECT model FROM Pc 
          WHERE ram = (SELECT MIN(ram) FROM PC)   
          AND speed = (SELECT MAX( speed) FROM 
             (SELECT speed FROM Pc WHERE 
                 ram = (SELECT MIN(ram) FROM Pc)) as z4) 
             )
  );

```



**Exercise 26**

Find out the average price of PCs and laptops produced by maker A.
Result set: one overall average price for all items.

**Solution**

```
WITH ALL_TABLE AS(SELECT price,maker from PC
JOIN Product ON pc.model = product.model
where product.maker = 'A'
UNION ALL
SELECT price,maker from LAPTOP
JOIN Product ON LAPTOP.model = product.model
where product.maker = 'A')
SELECT AVG(PRICE) FROM ALL_TABLE;

```



**Exercise 27**

Find out the average hard disk drive capacity of PCs produced by makers who also manufacture printers.
Result set: maker, average HDD capacity.

**Solution**

```
SELECT maker, avg(hd)

FROM PC INNER JOIN Product

ON PC.model=Product.model

GROUP BY maker

HAVING maker IN (SELECT DISTINCT maker FROM Product WHERE type='Printer');

```



**Exercise 28**

Using Product table, find out the number of makers who produce only one model.

**Solution**

```
with c1 as(select maker from product group by maker having count(model)=1)
select count(*) from c1;

```



 

------

 **Short database description "Recycling firm":**

The firm owns several buy-back centers for collection of recyclable materials. Each of them receives funds to be paid to the recyclables suppliers. Data on funds received is recorded in the table Income_o(point, date, inc)
The primary key is (point, date), where point holds the identifier of the buy-back center, and date corresponds to the calendar date the funds were received. The date column doesn’t include the time part, thus, money (inc) arrives no more than once a day for each center. Information on payments to the recyclables suppliers is held in the table Outcome_o(point, date, out)
In this table, the primary key (point, date) ensures each buy-back center reports about payments (out) no more than once a day, too. 
For the case income and expenditure may occur more than once a day, another database schema with tables having a primary key consisting of the single column code is used:
Income(code, point, date, inc)
Outcome(code, point, date, out)
Here, the date column doesn’t include the time part, either.  

**Recycling firm schema**

![img](http://www.sql-ex.com/images/income.gif)

**Exercise 29**

Under the assumption that receipts of money (inc) and payouts (out) are registered not more than once a day for each collection point [i.e. the primary key consists of (point, date)], write a query displaying cash flow data (point, date, income, expense). 
Use Income_o and Outcome_o tables.

**Solution**

```
SELECT income_o.point, income_o.date, inc, out from income_o
left join outcome_o on income_o.point = outcome_o.point
and income_o.date = outcome_o.date
union
select outcome_o.point,outcome_o.date,inc,out from outcome_o
left join income_o on outcome_o.point = income_o.point
and outcome_o.date = income_o.date;

```



**Exercise 30**

Under the assumption that receipts of money (inc) and payouts (out) can be registered any number of times a day for each collection point [i.e. the code column is the primary key], display a table with one corresponding row for each operating date of each collection point.
Result set: point, date, total payout per day (out), total money intake per day (inc). 
Missing values are considered to be NULL.

**Solution**

```
SELECT X.POINT,X.DATE,SUM(OUT),SUM(INC) FROM
(
Select INCOME.POINT, INCOME.DATE,NULL AS OUT,SUM(INC) AS INC FROM INCOME
LEFT JOIN OUTCOME ON INCOME.CODE = OUTCOME.CODE
GROUP BY INCOME.POINT,INCOME.DATE
UNION
Select OUTCOME.POINT, OUTCOME.DATE,SUM(OUT) AS OUT,NULL AS INC FROM OUTCOME
LEFT JOIN INCOME ON OUTCOME.CODE = INCOME.CODE
GROUP BY OUTCOME.POINT,OUTCOME.DATE) AS X
GROUP BY POINT,DATE;

```



------

  **Short database description "Ships":**

The database of naval ships that took part in World War II is under consideration. The database consists of the following relations: 
Classes(class, type, country, numGuns, bore, displacement) 
Ships(name, class, launched) 
Battles(name, date) 
Outcomes(ship, battle, result) 
Ships in classes all have the same general design. A class is normally assigned either the name of the first ship built according to the corresponding design, or a name that is different from any ship name in the database. The ship whose name is assigned to a class is called a lead ship.
The Classes relation includes the name of the class, type (can be either bb for a battle ship, or bc for a battle cruiser), country the ship was built in, the number of main guns, gun caliber (bore diameter in inches), and displacement (weight in tons). The Ships relation holds information about the ship name, the name of its corresponding class, and the year the ship was launched. The Battles relation contains names and dates of battles the ships participated in, and the Outcomes relation - the battle result for a given ship (may be sunk, damaged, or OK, the last value meaning the ship survived the battle unharmed). 
Notes: 1) The Outcomes relation may contain ships not present in the Ships relation. 2) A ship sunk can’t participate in later battles. 3) For historical reasons, lead ships are referred to as head ships in many exercises.4) A ship found in the Outcomes table but not in the Ships table is still considered in the database. This is true even if it is sunk.

**Ships schema**

![img](http://www.sql-ex.com/images/ships.gif)



**Exercise 31**

For ship classes with a gun caliber of 16 in. or more, display the class and the country.

**Solution**

```
SELECT class,country FROM CLASSES
WHERE bore >= 16;

```



**Exercise 33**

Get the ships sunk in the North Atlantic battle. 
Result set: ship.

**Solution**

```
SELECT SHIP FROM OUTCOMES
WHERE BATTLE = 'North Atlantic' AND result = 'sunk';

```



**Exercise 34**

  In accordance with the Washington Naval Treaty concluded in the beginning of 1922, it was prohibited to build battle ships with a displacement of more than 35 thousand tons. 
Get the ships violating this treaty (only consider ships for which the year of launch is known). 
List the names of the ships.

**Solution**

```
SELECT SHIPS.NAME FROM SHIPS
JOIN CLASSES ON SHIPS.CLASS = CLASSES.CLASS
AND DISPLACEMENT > 35000 
AND SHIPS.LAUNCHED >= 1922
AND CLASSES.TYPE = 'bb';

```



**Exercise 35 (computer firm database)**

  Find models in the Product table consisting either of digits only or Latin letters (A-Z, case insensitive) only.
Result set: model, type.

**Solution**

```
SELECT model,type FROM Product
WHERE model NOT LIKE '%[^0-9]%' OR model NOT LIKE '%[^A-Z]%';
```

