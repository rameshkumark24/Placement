# 📘 **FULL MYSQL NOTES — COMPLETE CLEAN MARKDOWN (ALL PAGES MERGED)**

# # ⭐ SQL (Structured Query Language)

---

# ## 1️⃣ MySQL Setup Notes

**MySQL Password:** `RameshRee@sd`

### Steps:

* Open MySQL Workbench → Create a connection instance
* Work on UI or Run SQL on Command Line

---

# # ⭐ Basic SQL Operations

---

## ## Create Database

```sql
CREATE DATABASE test;
```

---

# ## Create Table

```sql
CREATE TABLE test_demo (sno INT);
```

💡

* Lightning icon = Execute
* Select the query block → Run

---

# ## Insert Values

```sql
INSERT INTO test_demo (sno) VALUES (1);
```

---

# ## Select Entire Table

```sql
SELECT * FROM test_demo;
```

---

# # ⭐ MySQL in Jupyter Notebook Setup

---

### Commands (run in CMD):

```bash
mkdir sql-notebook
cd sql-notebook
python -m venv env
env\Scripts\activate
pip install --upgrade pip
pip install notebook
pip install ipython-sql
pip install mysql-connector-python
jupyter notebook
```

---

# ## In Notebook:

Load SQL extension:

```python
%load_ext sql
```

Connect MySQL:

```python
%sql mysql+mysqlconnector://root:root@localhost/test
```

Run SQL in each cell using:

```python
%%sql
SELECT * FROM table_name;
```

---

# # ⭐ SQL Categories

---

# ## 1️⃣ DDL (Data Definition Language)

| Command      | Purpose                       |
| ------------ | ----------------------------- |
| **CREATE**   | Create database, table, index |
| **ALTER**    | Modify existing objects       |
| **DROP**     | Delete objects                |
| **TRUNCATE** | Remove all rows               |
| **RENAME**   | Rename objects                |

---

# ## 2️⃣ TCL (Transaction Control Language)

| Command       | Purpose                     |
| ------------- | --------------------------- |
| **COMMIT**    | Save transaction            |
| **ROLLBACK**  | Undo transaction            |
| **SAVEPOINT** | Create rollback checkpoints |

---

# ## 3️⃣ DML (Data Manipulation Language)

| Command    | Purpose     |
| ---------- | ----------- |
| **SELECT** | Query data  |
| **INSERT** | Add data    |
| **UPDATE** | Modify data |
| **DELETE** | Remove rows |

---

# ## 4️⃣ DCL (Data Control Language)

| Command    | Purpose       |
| ---------- | ------------- |
| **GRANT**  | Give access   |
| **REVOKE** | Remove access |

---

# # ⭐ Database Operations

---

## 1️⃣ Show Databases

```sql
SHOW DATABASES;
```

---

## 2️⃣ Create Database

```sql
CREATE DATABASE sample_01;
```

---

## 3️⃣ Use Database

```sql
USE sample_01;
```

Switch database:

```sql
USE sample_02;
```

---

## 4️⃣ Delete Database

```sql
DROP DATABASE sample_02;
```

---

# # ⭐ Table Operations

---

## 5️⃣ Create Table

```sql
CREATE TABLE test (
  sno INT,
  name VARCHAR(20)
);
```

---

## 6️⃣ Select All

```sql
SELECT * FROM test;
```

---

## 7️⃣ Insert

```sql
INSERT INTO test (sno, name) VALUES (1, 'Ramesh');
```

---

# ## 8️⃣ Update

```sql
UPDATE test SET name='Ramesh Kumar' WHERE sno=1;
```

---

# ## 9️⃣ Update Multiple Columns

```sql
UPDATE test SET name='Ramesh', sno=2 WHERE sno=1;
```

---

# ## 🔟 Delete

```sql
DELETE FROM test WHERE sno=3;
```

---

# ## 1️⃣1️⃣ Truncate Table (delete all rows)

```sql
TRUNCATE TABLE test;
```

---

# ## 1️⃣2️⃣ Drop Table

```sql
DROP TABLE test;
```

---

# # ⭐ Advanced Table Creation

---

# ## 1️⃣3️⃣ Normal Table Creation Example

```sql
CREATE TABLE Employees (
  employee_id INT PRIMARY KEY,
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL,
  hire_date DATE NOT NULL,
  salary DECIMAL(10,2)
);
```

---

# ## 1️⃣4️⃣ Create Table From Another Table With Condition

```sql
CREATE TABLE Sample_Employee AS
SELECT employee_id, first_name, last_name, salary
FROM Employees
WHERE salary > 60000;
```

---

# # ⭐ Temporary Table

---

# ## 1️⃣5️⃣ Create Temporary Table

```sql
CREATE TEMPORARY TABLE tempemployee (
  employee_id INT,
  salary DECIMAL(10,2)
);
```

Temporary = for particular session only.

---

# ## Insert From Another Table

(Same as copy table)

```sql
INSERT INTO tempemployee
SELECT employee_id, salary FROM Employees;
```

---

# # ⭐ CTE (Common Table Expression)

---

# ## 1️⃣6️⃣ Using WITH

```sql
WITH high_salary AS (
  SELECT employee_id, first_name, last_name, salary
  FROM Employees
  WHERE salary > 70000
)
SELECT * FROM high_salary;
```

---

# ## Create Table From CTE

```sql
CREATE TABLE high_salary_employees AS
SELECT * FROM high_salary;
```

---

# # ⭐ ALTER TABLE

---

# ## 1️⃣7️⃣ Add Column

```sql
ALTER TABLE Employees ADD Email VARCHAR(100);
```

---

# ## 1️⃣8️⃣ Rename Table

```sql
ALTER TABLE Employees RENAME TO Emp_123;
```

---

# ## 1️⃣9️⃣ WHERE Clause Example

```sql
SELECT * FROM table WHERE fee > 50000;
```

---

# ## 2️⃣0️⃣ Describe Columns

```sql
DESC table;
```

---

# # ⭐ Filtering & Conditions

---

## 2️⃣1️⃣ ORDER BY

```sql
SELECT * FROM table WHERE fee > 500 ORDER BY fee;
```

Default = ASC.

---

## 2️⃣2️⃣ AND / OR / NOT

Used for conditions.

---

# # ⭐ Constraints

---

## 2️⃣3️⃣ Primary Key

* Avoid duplicate rows
* Must be UNIQUE + NOT NULL

Syntax:

```sql
column_name datatype PRIMARY KEY
```

---

## 2️⃣4️⃣ Composite Primary Key

```sql
PRIMARY KEY (column1, column2)
```

---

## 2️⃣5️⃣ UNIQUE

* Allows NULL
* Prevents duplicate values

---

## 2️⃣6️⃣ NOT NULL

* Value MUST be present

---

## 2️⃣7️⃣ CHECK Constraint

```sql
fee DECIMAL(10,2) CHECK (fee > 0);
```

---

# # ⭐ ER Diagram (Entity Relationship Diagram)

Represents relationships between tables.

---

# # ⭐ Foreign Key

---

## 2️⃣8️⃣ Foreign Key Definition

```sql
FOREIGN KEY (column) REFERENCES driver(driver_id)
```

---

### Delete Cascade

```sql
ON DELETE CASCADE
```

If parent deleted → child rows deleted.

---

# # ⭐ Soft Delete (Flag-Based)

---

## 2️⃣9️⃣ Soft Delete Logic:

Add a column:

```sql
ALTER TABLE rider ADD is_delete BOOLEAN DEFAULT FALSE;
```

Mark as deleted:

```sql
UPDATE rider SET is_delete = TRUE WHERE rider_id=102;
```

Used to hide data without physically deleting it.

---

# ## 3️⃣0️⃣ DEFAULT Value

```sql
ALTER TABLE test ADD status VARCHAR(10) DEFAULT 'active';
```

---

# # ⭐ Keys Summary

* **Primary Key** = main identifier
* **Natural Key** = real-world unique value
* **Surrogate Key** = artificial key
* **Candidate Key** = minimal primary key
* **Super Key** = any unique-set key

---

# # ⭐ Aggregations

---

## 3️⃣1️⃣ Count

```sql
SELECT COUNT(*) FROM Customers;
```

---

## 3️⃣2️⃣ SUM / MIN / MAX / AVG

```sql
SELECT SUM(amount), MIN(amount), MAX(amount), AVG(amount)
FROM Customers;
```

---

# # ⭐ Group By

---

## 3️⃣3️⃣ GROUP BY Example

```sql
SELECT login, SUM(amount) AS total
FROM Customer
GROUP BY login;
```

---

## 3️⃣4️⃣ HAVING Example

```sql
SELECT login, SUM(amount) AS total
FROM Customer
GROUP BY login
HAVING SUM(amount) > 8000;
```

---

# # ⭐ Conditional Logic

---

## 3️⃣5️⃣ CASE Statement

```sql
SELECT column,
CASE
   WHEN amount > 10 THEN 'true'
   ELSE 'false'
END AS text
FROM table;
```

---

# ## 3️⃣6️⃣ BETWEEN

```sql
SELECT * FROM table WHERE amount BETWEEN 2000 AND 4000;
```

---

# ## 3️⃣7️⃣ NULL Handling

```sql
COALESCE(amount, 1000)
```

Replaces NULL with 1000.

---

# # ⭐ String Functions

---

## 3️⃣8️⃣ Important Functions:

* `LENGTH()`
* `UPPER()`
* `LOWER()`
* `CONCAT()`
* `SUBSTRING(col, 1, 5)`
* `LTRIM()`, `RTRIM()`
* `LPAD()`, `RPAD()`
* `REPLACE()`
* `REVERSE()`
* `LEFT()`, `RIGHT()`
* `INSERT(col, pos, len, new_value)`

---

# # ⭐ DISTINCT

```sql
SELECT DISTINCT column FROM table;
```

---

# # ⭐ Views

## 3️⃣9️⃣ View Creation

```sql
CREATE VIEW high_earners AS
SELECT employee_id FROM employees WHERE salary > 50000;
```

---

# # ⭐ Joins

---

## 4️⃣0️⃣ Types:

* INNER JOIN
* LEFT JOIN
* RIGHT JOIN
* FULL JOIN (not in MySQL)

---

## JOIN Example

```sql
SELECT r.name, o.order_date
FROM Restaurants r
JOIN Orders o
  ON o.rest_id = r.id;
```

---

# ## 4️⃣1️⃣ Self Join

```sql
SELECT e.name AS employee_name,
       m.name AS manager_name
FROM employees e
JOIN employees m
ON e.manager_id = m.id;
```

---

# # ⭐ Window Functions

---

## 4️⃣2️⃣ Types:

### Aggregate:

* SUM()
* AVG()
* COUNT()
* MAX()
* MIN()

### Ranking:

* ROW_NUMBER()
* RANK()
* DENSE_RANK()
* PERCENT_RANK()
* NTILE()

### Value:

* LAG()
* LEAD()
* FIRST_VALUE()
* LAST_VALUE()

---

## Window Syntax:

```sql
OVER (
  PARTITION BY dept
  ORDER BY score DESC
)
```

---

## Window Function Example:

```sql
SELECT studentID, studentName, examScore,
RANK() OVER (ORDER BY examScore DESC) AS score_rank
FROM Students;
```

---

# # ⭐ UNION / UNION ALL

```sql
SELECT * FROM t1
UNION
SELECT * FROM t2;
```

---

# # ⭐ Index

---

## 4️⃣3️⃣ Create Index

```sql
CREATE INDEX idx_amount ON Customer (amount);
```

---

# # ⭐ EXPLAIN / EXPLAIN ANALYZE

Used for **query optimization**.

```sql
EXPLAIN SELECT * FROM orders WHERE amount > 5000;
```

---

# # ⭐ Partitioning

---

## Example:

```sql
PARTITION BY RANGE (YEAR(order_date)) (
  PARTITION p_before_2020 VALUES LESS THAN (2020),
  PARTITION p_2020 VALUES LESS THAN (2021),
  PARTITION p_2021 VALUES LESS THAN (2022),
  PARTITION p_future VALUES LESS THAN MAXVALUE
);
```

---

# # ⭐ Date & Time

---

## Functions:

* DATETIME
* TIMESTAMP DEFAULT CURRENT_TIMESTAMP
* DATE_FORMAT()
* CONVERT_TZ()

---

# # ⭐ Regex

```sql
SELECT * FROM users WHERE email REGEXP '@gmail\.com$';
```

---

# # ⭐ COMMIT & ROLLBACK

```sql
START TRANSACTION;
UPDATE table ...
COMMIT;
ROLLBACK;
```

---

# # ⭐ Normalization

---

## 1NF

* Single value per column

## 2NF

* Remove partial dependency

## 3NF

* Remove transitive dependency

---

# # ⭐ SCD (Slowly Changing Dimensions)

Used for historical tracking.

---

# # ⭐ ACID Properties

1. **Atomicity** – All or nothing
2. **Consistency** – Must satisfy rules
3. **Isolation** – Transactions separate
4. **Durability** – Saved permanently

---


# 🔥 **TOP 100 SQL INTERVIEW QUESTIONS & ANSWERS (COMPANY LEVEL)**

*(Short + Powerful + Precise)*

---

# 🟦 **SECTION 1 — SQL BASICS (FOUNDATION)**

---

### **1. What is SQL?**

SQL = Structured Query Language used to store, manipulate, and retrieve data in a relational database.

---

### **2. What is a database?**

A structured collection of data stored electronically.

---

### **3. What is a table?**

A table = rows (records) + columns (fields).

---

### **4. What is a primary key?**

Uniquely identifies each record (Unique + Not Null).

---

### **5. What is a foreign key?**

Links one table to another. Maintains referential integrity.

---

### **6. What is a unique key?**

Ensures uniqueness but allows one NULL.

---

### **7. What is a composite key?**

A primary/unique key made of multiple columns.

---

### **8. What is a candidate key?**

All possible keys that can act as primary key.

---

### **9. What is a super key?**

Any column/set of columns that uniquely identify a row (includes candidate keys).

---

### **10. What is normalization?**

Process of organizing data to reduce redundancy.

---

---

# 🟦 **SECTION 2 — NORMALIZATION & FORMS**

### **11. What is 1NF?**

Each column must contain atomic (single) values.

---

### **12. What is 2NF?**

No partial dependency (applies to composite keys).

---

### **13. What is 3NF?**

No transitive dependency (non-key → non-key).

---

### **14. What is denormalization?**

Opposite of normalization. Used to improve read performance.

---

### **15. What are anomalies?**

Update, Insert, Delete issues caused by poor design.

---

---

# 🟦 **SECTION 3 — JOINS**

### **16. What is a JOIN?**

Combines rows from two tables based on related columns.

---

### **17. Types of JOINs?**

* INNER JOIN
* LEFT JOIN
* RIGHT JOIN
* FULL OUTER JOIN
* CROSS JOIN
* SELF JOIN

---

### **18. INNER JOIN meaning**

Returns only matching rows from both tables.

---

### **19. LEFT JOIN meaning**

Returns all rows from left table + matched rows from right.

---

### **20. RIGHT JOIN meaning**

Opposite of left join.

---

### **21. FULL JOIN meaning**

Returns all rows when matched in either table (MySQL uses UNION of LEFT + RIGHT).

---

### **22. SELF JOIN meaning**

Table joined with itself.

---

### **23. CROSS JOIN meaning**

Cartesian product of two tables.

---

### **24. Write syntax for INNER JOIN**

```sql
SELECT *
FROM A
INNER JOIN B
ON A.id = B.id;
```

---

### **25. Difference between JOIN and UNION?**

JOIN → combines columns
UNION → combines rows

---

---

# 🟦 **SECTION 4 — AGGREGATIONS**

### **26. What are aggregate functions?**

SUM(), COUNT(), AVG(), MIN(), MAX()

---

### **27. Difference between COUNT(*) and COUNT(column)?**

COUNT(*) – counts all rows
COUNT(col) – ignores NULL

---

### **28. What is GROUP BY?**

Groups rows to apply aggregate functions.

---

### **29. Why do we use HAVING?**

HAVING filters groups (after aggregation).
WHERE filters rows (before aggregation).

---

### **30. Write GROUP BY example**

```sql
SELECT dept, SUM(salary)
FROM employees
GROUP BY dept;
```

---

### **31. HAVING example**

```sql
HAVING SUM(salary) > 50000;
```

---

---

# 🟦 **SECTION 5 — FILTERING & CONDITIONS**

### **32. What is WHERE clause?**

Filters rows before grouping.

---

### **33. BETWEEN example**

```sql
amount BETWEEN 1000 AND 5000;
```

---

### **34. IN example**

```sql
WHERE status IN ('Paid','Pending');
```

---

### **35. LIKE examples**

```sql
WHERE name LIKE 'A%';   -- starts with A
WHERE name LIKE '%A';   -- ends with A
WHERE name LIKE '%A%';  -- contains A
```

---

### **36. IS NULL vs IS NOT NULL**

Checks for NULL values specifically.

---

### **37. What is ORDER BY?**

Sorts results (ASC default, DESC optional).

---

### **38. What is DISTINCT?**

Removes duplicates.

---

### **39. LIMIT usage**

```sql
SELECT * FROM table LIMIT 5;
```

---

### **40. CASE WHEN example**

```sql
CASE WHEN marks > 50 THEN 'PASS' ELSE 'FAIL' END
```

---

---

# 🟦 **SECTION 6 — INSERT, UPDATE, DELETE**

### **41. Insert example**

```sql
INSERT INTO emp (id,name) VALUES (1,'Ramesh');
```

---

### **42. Update example**

```sql
UPDATE emp SET salary = 50000 WHERE id = 1;
```

---

### **43. Delete example**

```sql
DELETE FROM emp WHERE id = 1;
```

---

### **44. TRUNCATE vs DELETE?**

DELETE → row-by-row, slower, can rollback
TRUNCATE → removes all rows instantly, no rollback

---

### **45. DROP vs TRUNCATE**

DROP → removes table completely
TRUNCATE → clears data, keeps table

---

---

# 🟦 **SECTION 7 — FUNCTIONS**

### **46. LENGTH(), LOWER(), UPPER()**

String manipulation.

---

### **47. SUBSTRING example**

```sql
SUBSTRING(name, 1, 3)
```

---

### **48. REPLACE example**

```sql
REPLACE(name, 'ram', 'RAM')
```

---

### **49. CONCAT example**

```sql
CONCAT(first, ' ', last)
```

---

### **50. COALESCE usage**

Returns first non-null value.

```sql
COALESCE(amount, 0)
```

---

---

# 🟦 **SECTION 8 – KEYS & CONSTRAINTS**

### **51. What is NOT NULL constraint?**

Column must have a value.

---

### **52. CHECK constraint example**

```sql
CHECK (salary > 0)
```

---

### **53. FOREIGN KEY with CASCADE**

```sql
REFERENCES dept(id) ON DELETE CASCADE
```

---

### **54. What is ON UPDATE CASCADE?**

Child updated automatically when parent key changes.

---

### **55. Difference between primary & unique key?**

Primary → one per table, no NULL
Unique → many allowed, NULL allowed

---

---

# 🟦 **SECTION 9 — ADVANCED SQL (INTERVIEW FAVOURITE)**

### **56. What is a VIEW?**

A virtual table (stored query).

---

### **57. Create view example**

```sql
CREATE VIEW high_salary AS 
SELECT * FROM emp WHERE salary > 50000;
```

---

### **58. What is a stored procedure?**

Reusable SQL code saved in database.

---

### **59. Stored procedure example**

```sql
CREATE PROCEDURE getEmp() 
BEGIN 
   SELECT * FROM emp;
END;
```

---

### **60. What is a trigger?**

Auto executes when an event occurs.

---

### **61. Trigger example**

```sql
CREATE TRIGGER update_log
AFTER UPDATE ON emp
FOR EACH ROW
INSERT INTO log_table VALUES (...);
```

---

### **62. What is an index?**

Improves read speed using B-Trees.

---

### **63. When NOT to use indexes?**

On small tables
Columns with frequently changing values
Columns with many duplicates

---

### **64. Composite index?**

Index on multiple columns.

---

### **65. EXPLAIN command**

Used to analyze query performance.

---

---

# 🟦 **SECTION 10 — WINDOW FUNCTIONS**

### **66. What is a window function?**

Performs calculations across a set of rows without grouping.

---

### **67. ROW_NUMBER example**

```sql
ROW_NUMBER() OVER (ORDER BY salary DESC)
```

---

### **68. RANK vs DENSE_RANK**

RANK → skips numbers
DENSE_RANK → no gaps

---

### **69. LAG() usage**

Get previous row value.

---

### **70. LEAD() usage**

Get next row value.

---

### **71. PARTITION BY usage**

Used to calculate within groups.

---

---

# 🟦 **SECTION 11 — SUBQUERIES**

### **72. What is a subquery?**

Query inside another query.

---

### **73. Subquery example**

```sql
SELECT *
FROM emp
WHERE salary > (SELECT AVG(salary) FROM emp);
```

---

### **74. Correlated subquery?**

Subquery depends on outer query.

---

### **75. EXISTS example**

```sql
WHERE EXISTS (SELECT 1 FROM dept d WHERE d.id = e.dept_id);
```

---

---

# 🟦 **SECTION 12 — SET OPERATIONS**

### **76. UNION vs UNION ALL**

UNION → removes duplicates
UNION ALL → keeps duplicates

---

### **77. INTERSECT**

Common rows between queries.

---

### **78. EXCEPT / MINUS**

Rows from first query not in second.

---

---

# 🟦 **SECTION 13 — TRANSACTIONS**

### **79. What is a transaction?**

A group of SQL statements executed together.

---

### **80. ACID properties**

Atomicity
Consistency
Isolation
Durability

---

### **81. COMMIT**

Saves changes.

---

### **82. ROLLBACK**

Undoes changes.

---

### **83. SAVEPOINT**

Partial rollback point.

---

---

# 🟦 **SECTION 14 — PERFORMANCE OPTIMIZATION**

### **84. Why indexing improves speed?**

Reduces full table scan.

---

### **85. Why too many indexes reduce speed?**

Slows INSERT/UPDATE/DELETE.

---

### **86. What is query optimization?**

Techniques to improve execution speed (indices, partitioning, joins rewriting).

---

### **87. What is partitioning?**

Splitting large table into smaller logical parts.

---

### **88. Horizontal vs Vertical Partitioning**

Horizontal → rows
Vertical → columns

---

---

# 🟦 **SECTION 15 — DATA TYPES**

### **89. CHAR vs VARCHAR**

CHAR = fixed length
VARCHAR = variable length

---

### **90. INT vs BIGINT**

BIGINT stores larger values.

---

### **91. DECIMAL vs FLOAT**

DECIMAL = exact precision
FLOAT = approximate precision

---

### **92. DATE vs DATETIME vs TIMESTAMP**

DATE = only date
DATETIME = date + time
TIMESTAMP = stored in UTC, auto-updated

---

---

# 🟦 **SECTION 16 — REAL-WORLD / SCENARIO QUESTIONS**

### **93. Find second highest salary**

```sql
SELECT MAX(salary)
FROM emp
WHERE salary < (SELECT MAX(salary) FROM emp);
```

---

### **94. Find duplicate records**

```sql
SELECT name, COUNT(*)
FROM emp
GROUP BY name
HAVING COUNT(*) > 1;
```

---

### **95. Delete duplicates but keep one**

```sql
DELETE e1
FROM emp e1
JOIN emp e2
ON e1.name = e2.name
AND e1.id > e2.id;
```

---

### **96. Find employees who never made an order**

```sql
SELECT e.*
FROM emp e
LEFT JOIN orders o ON e.id = o.emp_id
WHERE o.emp_id IS NULL;
```

---

### **97. Retrieve top 5 salaries**

```sql
SELECT * FROM emp ORDER BY salary DESC LIMIT 5;
```

---

### **98. Find employees with salary > department average**

```sql
SELECT e.*
FROM emp e
WHERE salary >
  (SELECT AVG(salary) FROM emp WHERE dept = e.dept);
```

---

### **99. Show department with highest total salary**

```sql
SELECT dept, SUM(salary) AS total_salary
FROM emp
GROUP BY dept
ORDER BY total_salary DESC
LIMIT 1;
```

---

### **100. Why do companies test SQL?**

To check:

* Logic thinking
* Data handling skill
* Understanding of joins & aggregations
* Real-world scenario problem solving

---
