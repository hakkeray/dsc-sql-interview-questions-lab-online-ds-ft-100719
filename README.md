
# SQL Interview Questions - Lab

## Introduction

In this lab, we'll test our SQL skills against some real-world interview questions from major companies!

## Objectives

You will be able to:

* Write SQL queries to filter and order results
* Decide and perform whichever type of join is best for retrieving desired data
* Write subqueries to decompose complex queries

## Getting Started

In this lab, we'll see four different interview questions that test your SQL knowledge. We didn't write these questions -- instead, we found them out in the real-world. These are questions that have been used in the past by major technology companies such as Facebook, Amazon, and Twitter. Our goal here isn't to memorize the questions or anything like that -- after all, it's extremely unlikely that these questions are still in use, now that they've become publicly available on the interwebs. Instead, our goal is to treat these questions as if they are the real thing, and give us some insight into the types of questions we'll need to be able to answer in order pass an interview involving SQL. 

If these questions seem hard to you, don't sweat it, they're supposed to be tough! These are meant to help you identify any areas of knowledge where you still need to grow! Use these questions as a way to see where your SQL knowledge is strong, and where it's a bit weak. Then, go study and **practice** in the areas where you still need work!

### A Note on Answering These Questions

Since these are interview questions, they'll almost always be posed as hypotheticals. This means that you won't have a real database to work with and test your code on. This also means that there are multiple different solutions to any given problem listed here. Be sure to doublecheck the code you write for bugs and errors. It's much harder to write bug-free code when you aren't able to test it against a database!

If these questions seem hard, that's normal. These are real questions that have been reported to online forums from job seekers at major companies. Obviously, it's unlikely that they're still in use at these companies, but they still represent a great way for us to test our skills against the kinds of questions we can expect to be asked in an interview!



### What are the 4 main datatypes in SQLite3? Can we use other common types from other kinds of SQL?

INTEGER (non-decimal, int), REAL (decimal,float), TEXT (string), BLOB (e.g. image data)

### Explain the relationship between Primary Keys and Foreign Keys.

Primary keys: unique keys for each record/observation in the table. 
Foreign key: column in table pointing to primary key of another table that it has a relationship with.
Need a primary key and foreign key to make a JOIN statement.

### Explain the different types of relationships entities can have in a SQL database.

one-to-one, one-to-many, and many-to-many

* 1to1: each record in table is linked to 1 record in another table (e.g. customer code, sales rep number)

* 1toMany: each record in table is linked to multiple records of another table (e.g. customer code, order numbers) 

* Many2Many: multiple records in one table are linked to multiple records of another table (order numbers, product numbers)

### Explain the various types of JOINs possible with SQL.

The main JOIN types are Inner, Outer, and Left/Right. We can also do a full JOIN to create a Cartesian Product, but this isn't that useful and isn't often used.

The easiest way to think about JOINs is to picture a Venn Diagram, with each circle representing a different table. The area where the circles overlap represents the rows where the Primary Keys from one table match the Foreign Keys from another.

An INNER JOIN returns only the overlapping portion of the Venn Diagram, where the record's key matches a key in the other table.

An OUTER JOIN returns only the rows from each table where there is no corresponding match in with the other table.

A LEFT JOIN returns every record in the left table, and any rows from the right table that happen to have a key that matches with the left table. A Right table is the same thing, but flipped, returning every row in the right table and only the matches from the left.

### Explain the relationship between Aggregate functions and GROUP BY statements.

Aggregate functions are used to compute things like a COUNT or a MEAN. These functions perform these calculations on a 'group' that we must specify. For instance, assume we have a table containing the population for each country in the world, for each year in the last 20 years. If we wanted to get the mean of the population column, this could mean two different things. Without a GROUP BY statement, the SQL server would not know if we meant the mean population of a country across all 20 years, or the mean population of all countries for each given year. Both are mean values, and one is not more correct than the other, so the SQL server would throw an error because of the ambiguity. To make this work, we would need to specify whether we wanted the mean when the data is grouped by country (which would return the mean population over the 20 year span) or grouped by year (which would return the mean population of the average country for each of the separate years).

### What role do Associative Entities play (JOIN Tables) in many-to-many JOINs?

Associative Entities, also called JOIN Tables, are a type of table used to allow us to perform many-to-many JOINs. These tables act as a list that keep track of each of the different connections between an entity in one table and all of that entity's connections in another table. While we can JOIN tables directly for one-to-one and one-to-many relationships, we must use an Associative Entity in order to pull of a JOIN between two tables with a many-to-many relationship.

## Question 1

From Facebook:

Assume we have a table of employee information, which includes salary information. Write a query to find the names and salaries of the top 5 highest paid employees, in descending order.


```python
c.execute("""SELECT name, salary
            FROM employees
            ORDER BY salary DESC
            LIMIT 5;""")
```

## Question 2

From Amazon:

Assume we have two SQL tables: `authors` and `books`. The authors table has a few million rows, and looks like this: 

| author_name | book_name |
|:-----------:|:---------:|
|   author_1  |   book_1  |
|   author_1  |   book_2  |
|   author_2  |   book_3  |
|   author_2  |   book_4  |
|   author_2  |   book_5  |
|   author_3  |   book_6  |

The books dataset also has a few million rows, and looks like this:

| book_name | copies_sold |
|:---------:|:-----------:|
|   book_1  |    10000    |
|   book_2  |     2575    |
|   book_3  |    60000    |
|   book_4  |    98000    |
|   book_5  |     5250    |
|   book_6  |    19775    |

Write an SQL query that shows the top 3 authors who sold the most total books. 


```python
c.execute("""SELECT author_name 
            FROM authors
            JOIN books
            USING(book_name)
            GROUP BY 1
            HAVING MAX(copies_sold)
            LIMIT 3;
            """)
```

## Question 3

From Amazon:

Assume you have two tables, `customers` and `orders`. Write a SQL query to select all customers who purchased at least 2 items on two separate days. 


```python
c.execute("""SELECT customerName
            FROM customers
            JOIN orders
            USING(orderNumber)
            WHERE quantity in (SELECT quantity, date
                                        FROM orders
                                        GROUP BY quantity
                                        HAVING SUM(orderQuantity) > 2 AND COUNT(DISTINCT(date) >= 2;
                                        """)
df = pd.DataFrame(c.fetchall())
df.columns = [x[0] for x in c.description]
df
```

## Question 4

From Twitter:

A company uses 2 data tables, `Employee` and `Department`, to store data about its employees and departments. 

Table Name: Employee   
Attributes:   
ID Integer,   
NAME String,   
SALARY Integer,   
DEPT_ID Integer   

Table Name: Department   
Attributes:   
DEPT_ID Integer,   
NAME String,   
LOCATION String   

Write a query to print the respective Department Name and number of employees for all departments in the Department table (even unstaffed ones). 

Sort your result in descending order of employees per department; if two or more departments have the same number of employees, then sort those departments alphabetically by Department Name.


```python
SELECT NAME, ID, DEPT_ID
FROM Employee
JOIN Department
USING(DEPT_ID)
WHERE DEPT_ID in (SELECT DEPT_ID
                     FROM Department
                     GROUP BY DEPT_ID
                     HAVING SUM(ID) >=0
                     ORDER BY NAME ASC
                );
```

## Summary

In this lab, we tested our knowledge of SQL queries against some real-world interview questions!
