# 🗄️ Comprehensive SQL Notes

A structured, beginner-to-advanced guide to Structured Query Language (SQL) with clean formatting, practical examples, sample tables, and query outputs.

> 📚 **Navigation:** [⚡ Quick Cheat Sheet](./SQL_Cheatsheet.md) • [🎯 Interview Questions (Theory & Practical)](./Interview_Questions.md)

---

## 📑 Table of Contents

1. [Introduction to SQL & Databases](#1-introduction-to-sql--databases)
   - [What is SQL?](#what-is-sql)
   - [DBMS vs RDBMS](#dbms-vs-rdbms)
   - [SQL vs NoSQL](#sql-vs-nosql)
   - [SQL Sublanguages (DDL, DML, DQL, DCL, TCL)](#sql-sublanguages)
   - [SQL Query Execution Order](#sql-query-execution-order)
2. [Data Types & Constraints](#2-data-types--constraints)
   - [Common Data Types](#common-data-types)
   - [SQL Constraints](#sql-constraints)
3. [DDL (Data Definition Language)](#3-ddl-data-definition-language)
   - [CREATE Database & Table](#create-database--table)
   - [ALTER Table](#alter-table)
   - [DROP, TRUNCATE, and DELETE](#drop-truncate-and-delete)
4. [DML (Data Manipulation Language)](#4-dml-data-manipulation-language)
   - [INSERT INTO](#insert-into)
   - [UPDATE](#update)
   - [DELETE](#delete)
5. [DQL (Data Query Language) & Filtering](#5-dql-data-query-language--filtering)
   - [Basic SELECT](#basic-select)
   - [WHERE Clause & Comparison Operators](#where-clause--comparison-operators)
   - [Logical Operators (AND, OR, NOT)](#logical-operators)
   - [Pattern Matching (LIKE & Wildcards)](#pattern-matching-like)
   - [Range (BETWEEN) & List (IN)](#range-between--list-in)
   - [Handling NULL Values](#handling-null-values)
   - [ORDER BY (Sorting)](#order-by-sorting)
   - [LIMIT & OFFSET (Pagination)](#limit--offset-pagination)
6. [Aggregate Functions & Grouping](#6-aggregate-functions--grouping)
   - [Aggregate Functions (COUNT, SUM, AVG, MIN, MAX)](#aggregate-functions)
   - [GROUP BY Clause](#group-by-clause)
   - [HAVING vs WHERE](#having-vs-where)
7. [SQL Joins](#7-sql-joins)
   - [Sample Tables for Joins](#sample-tables-for-joins)
   - [INNER JOIN](#inner-join)
   - [LEFT (OUTER) JOIN](#left-outer-join)
   - [RIGHT (OUTER) JOIN](#right-outer-join)
   - [FULL (OUTER) JOIN](#full-outer-join)
   - [CROSS JOIN](#cross-join)
   - [SELF JOIN](#self-join)
   - [Anti-Join (Finding Unmatched Records)](#anti-join)
8. [Set Operations](#8-set-operations)
   - [UNION & UNION ALL](#union--union-all)
   - [INTERSECT](#intersect)
   - [EXCEPT / MINUS](#except--minus)
9. [Subqueries & CTEs](#9-subqueries--ctes)
   - [Scalar Subqueries](#scalar-subqueries)
   - [Multi-Row Subqueries (IN, ANY, ALL)](#multi-row-subqueries)
   - [Correlated Subqueries & EXISTS](#correlated-subqueries--exists)
   - [Common Table Expressions (WITH Clause)](#common-table-expressions-ctes)
10. [Window Functions (Analytic Functions)](#10-window-functions-analytic-functions)
    - [Concept of OVER() and PARTITION BY](#concept-of-over-and-partition-by)
    - [Ranking Functions (ROW_NUMBER, RANK, DENSE_RANK)](#ranking-functions)
    - [Value Functions (LEAD, LAG, FIRST_VALUE)](#value-functions)
    - [Running Totals & Moving Averages](#running-totals)
11. [Views, Indexes & Transactions](#11-views-indexes--transactions)
    - [Views](#views)
    - [Indexes & Performance](#indexes--performance)
    - [Transactions & ACID Properties](#transactions--acid-properties)
12. [Common Built-in Functions](#12-common-built-in-functions)
    - [Conditional Expressions (CASE WHEN)](#conditional-expressions-case-when)
    - [String Functions](#string-functions)
    - [Date & Time Functions](#date--time-functions)
13. [Top SQL Interview Queries](#13-top-sql-interview-queries)

---

## 1. Introduction to SQL & Databases

### What is SQL?
**SQL** (Structured Query Language) is the standard language used to store, manipulate, and retrieve data stored in relational databases (RDBMS).

- It is **declarative**: you tell the database *what* data you want, not *how* to retrieve it algorithmically.
- SQL keywords are **case-insensitive** (`SELECT` is the same as `select`), but writing keywords in **UPPERCASE** is the industry standard for readability.

---

### DBMS vs RDBMS

| Feature | DBMS (Database Management System) | RDBMS (Relational DBMS) |
| :--- | :--- | :--- |
| **Data Storage** | Files, XML, hierarchical structure | Tables with rows (tuples) and columns (attributes) |
| **Relationships** | Not enforced inherently | Enforced via Primary Keys and Foreign Keys |
| **Data Redundancy**| High redundancy common | Normalized to reduce redundancy |
| **Integrity Constraints** | Minimal | Enforced (NOT NULL, UNIQUE, CHECK, FK) |
| **Examples** | File system, XML database | PostgreSQL, MySQL, SQL Server, Oracle, SQLite |

---

### SQL vs NoSQL

| Criteria | SQL (Relational) | NoSQL (Non-relational) |
| :--- | :--- | :--- |
| **Schema** | Fixed, predefined schema | Flexible, dynamic schema |
| **Data Model** | Tables (rows & columns) | Documents (JSON), Key-Value, Graphs, Wide-column |
| **Scaling** | Vertical (Scale Up: more CPU/RAM) | Horizontal (Scale Out: more servers/sharding) |
| **Transactions** | Strong ACID compliance | BASE model (Eventual consistency) |
| **Best For** | Complex queries, financial systems, structured data | High throughput, big data, rapid prototyping |

---

### SQL Sublanguages

SQL statements are grouped into 5 major categories:

```
                  ┌───────────────── SQL Commands ─────────────────┐
                  │                                                │
          ┌───────┴───────┬───────────────┼───────────────┬────────┴───────┐
          ▼               ▼               ▼               ▼                ▼
         DDL             DML             DQL             DCL              TCL
     (Definition)   (Manipulation)     (Query)        (Control)      (Transaction)
     • CREATE        • INSERT        • SELECT        • GRANT          • COMMIT
     • ALTER         • UPDATE                        • REVOKE         • ROLLBACK
     • DROP          • DELETE                                         • SAVEPOINT
     • TRUNCATE
     • RENAME
```

1. **DDL (Data Definition Language)**: Defines and modifies the database schema/structure.
2. **DML (Data Manipulation Language)**: Modifies the data inside tables.
3. **DQL (Data Query Language)**: Retrieves data from tables.
4. **DCL (Data Control Language)**: Manages permissions and user access rights.
5. **TCL (Transaction Control Language)**: Manages database transactions.

---

### SQL Query Execution Order

Understanding the execution order is the **single most important concept** for debugging queries, aliases, and `WHERE` vs `HAVING` errors:

```
Written Order                          Logical Execution Order
─────────────                          ───────────────────────
1. SELECT                              1. FROM        (Locate tables)
2. FROM                                2. ON          (Join conditions)
3. JOIN                                3. JOIN        (Merge tables)
4. WHERE                               4. WHERE       (Filter raw rows)
5. GROUP BY                            5. GROUP BY    (Aggregate rows into groups)
6. HAVING                              6. HAVING      (Filter grouped rows)
7. ORDER BY                            7. SELECT      (Compute expressions & project columns)
8. LIMIT / OFFSET                      8. DISTINCT    (Remove duplicates)
                                       9. ORDER BY    (Sort resulting rows)
                                      10. LIMIT/OFFSET(Slice output rows)
```

> **Why this matters:**
> - You **cannot** use a column alias created in `SELECT` inside a `WHERE` clause because `WHERE` executes *before* `SELECT`!
> - You **can** use a column alias in `ORDER BY` because `ORDER BY` executes *after* `SELECT`.

---

## 2. Data Types & Constraints

### Common Data Types

#### 1. Numeric
- `INT` / `INTEGER`: Standard integer values (e.g., `-2147483648` to `2147483647`).
- `BIGINT`: Large integers (e.g., global IDs, timestamps).
- `DECIMAL(p, s)` / `NUMERIC(p, s)`: Exact precision numbers (e.g., `DECIMAL(10, 2)` = 10 digits total, 2 decimal places). Best for financial values.
- `FLOAT` / `REAL`: Approximate floating-point numbers.

#### 2. String / Text
- `CHAR(n)`: Fixed-length string. Padded with spaces if shorter than `n`.
- `VARCHAR(n)`: Variable-length string with a maximum length of `n`.
- `TEXT`: Variable-length string for large amounts of text (unlimited/large capacity).

#### 3. Date & Time
- `DATE`: Date only (`YYYY-MM-DD`).
- `TIME`: Time only (`HH:MI:SS`).
- `TIMESTAMP` / `DATETIME`: Date and time together (`YYYY-MM-DD HH:MI:SS`).
- `TIMESTAMPTZ`: Timestamp with timezone support.

#### 4. Boolean & Others
- `BOOLEAN`: `TRUE`, `FALSE`, or `NULL`.
- `JSON` / `JSONB`: Structured JSON objects and arrays.

---

### SQL Constraints

Constraints enforce rules on data columns to ensure data integrity:

| Constraint | Description |
| :--- | :--- |
| `NOT NULL` | Prevents column from storing `NULL` values. |
| `UNIQUE` | Ensures all values in a column are distinct. |
| `PRIMARY KEY` | Uniquely identifies each record (`NOT NULL` + `UNIQUE`). Only one per table. |
| `FOREIGN KEY` | Refers to the `PRIMARY KEY` of another table to enforce referential integrity. |
| `CHECK` | Ensures that all values in a column satisfy a specific Boolean condition. |
| `DEFAULT` | Provides a fallback value if no value is explicitly supplied on insert. |
| `AUTO_INCREMENT` / `SERIAL` | Automatically generates sequential numbers for new records. |

---

## 3. DDL (Data Definition Language)

### CREATE Database & Table

```sql
-- 1. Create a new database
CREATE DATABASE company_db;

-- 2. Switch to the database (MySQL syntax; PostgreSQL uses \c company_db)
USE company_db;

-- 3. Create Departments Table
CREATE TABLE departments (
    dept_id INT AUTO_INCREMENT PRIMARY KEY,
    dept_name VARCHAR(50) NOT NULL UNIQUE,
    location VARCHAR(100) DEFAULT 'Headquarters'
);

-- 4. Create Employees Table with Constraints
CREATE TABLE employees (
    emp_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    salary DECIMAL(10, 2) CHECK (salary >= 0),
    hire_date DATE DEFAULT (CURRENT_DATE),
    dept_id INT,
    manager_id INT,
    CONSTRAINT fk_employee_department 
        FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
        ON DELETE SET NULL
        ON UPDATE CASCADE,
    CONSTRAINT fk_employee_manager
        FOREIGN KEY (manager_id) REFERENCES employees(emp_id)
        ON DELETE SET NULL
);
```

> **Note on Foreign Key Actions:**
> - `ON DELETE CASCADE`: When the parent record is deleted, delete all related child records automatically.
> - `ON DELETE SET NULL`: When the parent record is deleted, set foreign key values in child records to `NULL`.
> - `ON DELETE RESTRICT` / `NO ACTION`: Block deletion of the parent record if children exist.

---

### ALTER Table

Use `ALTER TABLE` to modify an existing table's schema without losing existing data:

```sql
-- Add a new column
ALTER TABLE employees
ADD COLUMN phone_number VARCHAR(15);

-- Modify column data type / constraint
ALTER TABLE employees
MODIFY COLUMN phone_number VARCHAR(20) NOT NULL;  -- MySQL
-- PostgreSQL: ALTER TABLE employees ALTER COLUMN phone_number TYPE VARCHAR(20);

-- Rename a column
ALTER TABLE employees
RENAME COLUMN phone_number TO contact_number;

-- Drop a column
ALTER TABLE employees
DROP COLUMN contact_number;

-- Add a constraint to an existing table
ALTER TABLE employees
ADD CONSTRAINT chk_salary_min CHECK (salary >= 20000);

-- Drop a constraint
ALTER TABLE employees
DROP CONSTRAINT chk_salary_min;
```

---

### DROP, TRUNCATE, and DELETE

These commands all remove data or tables, but have critical differences:

| Feature | `DELETE` (DML) | `TRUNCATE` (DDL) | `DROP` (DDL) |
| :--- | :--- | :--- | :--- |
| **Action** | Deletes specific rows | Deletes ALL rows | Deletes table schema + data |
| **WHERE clause** | Supported (`WHERE id = 5`) | Not supported | Not supported |
| **Speed** | Slower (logs row by row) | Very Fast (deallocates pages) | Very Fast |
| **Rollback** | Yes (inside transaction) | Depends on RDBMS (No in MySQL) | Depends on RDBMS (No in MySQL) |
| **Identity Reset** | Does not reset auto-increment | Resets auto-increment counter | Destroys table entirely |
| **Table Structure**| Preserved | Preserved | Destroyed |

```sql
-- Delete specific rows
DELETE FROM employees WHERE dept_id = 3;

-- Wipe all rows quickly, keeping table structure
TRUNCATE TABLE employees;

-- Completely remove the table and its schema from database
DROP TABLE employees;
```

---

## 4. DML (Data Manipulation Language)

### INSERT INTO

```sql
-- Insert into specific columns
INSERT INTO departments (dept_name, location)
VALUES ('Engineering', 'Building A');

-- Insert multiple rows in a single query (Best practice for performance)
INSERT INTO departments (dept_name, location)
VALUES 
    ('Human Resources', 'Building B'),
    ('Marketing', 'Building A'),
    ('Finance', 'Building C');

-- Insert using subquery (Copying data from another table)
INSERT INTO archived_employees (emp_id, full_name, salary)
SELECT emp_id, CONCAT(first_name, ' ', last_name), salary
FROM employees
WHERE hire_date < '2020-01-01';
```

---

### UPDATE

```sql
-- Update specific columns for matching rows
UPDATE employees
SET salary = salary * 1.10,
    dept_id = 2
WHERE emp_id = 101;

-- Update multiple records using CASE
UPDATE employees
SET salary = CASE 
    WHEN dept_id = 1 THEN salary * 1.15
    WHEN dept_id = 2 THEN salary * 1.10
    ELSE salary * 1.05
END
WHERE hire_date < '2023-01-01';
```

> ⚠️ **CAUTION:** Always write the `WHERE` clause first when executing `UPDATE` or `DELETE`! Omitting `WHERE` will update or wipe **every single row** in the table.

---

### DELETE

```sql
-- Delete matching rows
DELETE FROM employees
WHERE salary < 30000 AND dept_id IS NULL;
```

---

## 5. DQL (Data Query Language) & Filtering

### Sample Data: `employees` Table

| emp_id | first_name | last_name | dept_id | salary   | hire_date  |
| :----- | :--------- | :-------- | :------ | :------- | :--------- |
| 1      | Alice      | Smith     | 1       | 85000.00 | 2021-03-15 |
| 2      | Bob        | Jones     | 1       | 62000.00 | 2022-06-01 |
| 3      | Charlie    | Brown     | 2       | 54000.00 | 2020-01-10 |
| 4      | Diana      | Prince    | 2       | 92000.00 | 2019-11-20 |
| 5      | Evan       | Wright    | NULL    | 45000.00 | 2023-04-18 |

---

### Basic SELECT

```sql
-- Select all columns (Avoid in production)
SELECT * FROM employees;

-- Select specific columns and apply aliases
SELECT 
    emp_id AS employee_id,
    first_name,
    last_name,
    salary * 12 AS annual_salary
FROM employees;

-- Distinct values (Eliminating duplicates)
SELECT DISTINCT dept_id 
FROM employees;
```

---

### WHERE Clause & Comparison Operators

```sql
-- Equal to
SELECT * FROM employees WHERE dept_id = 1;

-- Not equal to (!= or <>)
SELECT * FROM employees WHERE dept_id <> 1;

-- Greater than or equal to
SELECT * FROM employees WHERE salary >= 60000;
```

---

### Logical Operators

```sql
-- AND (Both conditions must be TRUE)
SELECT * FROM employees 
WHERE dept_id = 1 AND salary > 65000;

-- OR (At least one condition must be TRUE)
SELECT * FROM employees 
WHERE dept_id = 1 OR salary > 90000;

-- NOT (Inverts condition)
SELECT * FROM employees 
WHERE NOT (dept_id = 1);
```

---

### Pattern Matching (LIKE)

The `LIKE` operator is used with wildcards for string pattern matching:
- `%` represents **zero, one, or multiple characters**.
- `_` represents **exactly one character**.

```sql
-- Names starting with 'A'
SELECT * FROM employees WHERE first_name LIKE 'A%';

-- Names ending with 'e'
SELECT * FROM employees WHERE first_name LIKE '%e';

-- Names containing 'ar' anywhere
SELECT * FROM employees WHERE first_name LIKE '%ar%';

-- Names where 2nd character is 'o' (e.g., 'Bob')
SELECT * FROM employees WHERE first_name LIKE '_o%';

-- Case-insensitive match in PostgreSQL: use ILIKE
-- SELECT * FROM employees WHERE first_name ILIKE 'alice';
```

---

### Range (BETWEEN) & List (IN)

```sql
-- BETWEEN (inclusive of both boundaries)
SELECT * FROM employees 
WHERE salary BETWEEN 50000 AND 85000;

-- Equivalent to: salary >= 50000 AND salary <= 85000

-- IN (matches any value in a given list)
SELECT * FROM employees 
WHERE dept_id IN (1, 2, 3);

-- NOT IN
SELECT * FROM employees 
WHERE dept_id NOT IN (1, 2);
```

---

### Handling NULL Values

In SQL, `NULL` means **unknown** or **missing data**. You cannot check equality using `= NULL` or `!= NULL`.

```sql
-- Correct way to test for NULL
SELECT * FROM employees WHERE dept_id IS NULL;

-- Correct way to test for non-NULL
SELECT * FROM employees WHERE dept_id IS NOT NULL;

-- COALESCE: Returns first non-NULL argument (great for fallback values)
SELECT 
    first_name,
    COALESCE(dept_id, 0) AS assigned_dept
FROM employees;

-- IFNULL (MySQL) / NVL (Oracle)
SELECT first_name, IFNULL(dept_id, 0) FROM employees;
```

---

### ORDER BY (Sorting)

```sql
-- Ascending order (default)
SELECT * FROM employees 
ORDER BY salary ASC;

-- Descending order
SELECT * FROM employees 
ORDER BY salary DESC;

-- Multi-column sort (Primary sort by dept_id ASC, then by salary DESC)
SELECT * FROM employees 
ORDER BY dept_id ASC, salary DESC;

-- Sorting with NULLs at the end or beginning (PostgreSQL/Oracle)
-- ORDER BY dept_id ASC NULLS LAST;
```

---

### LIMIT & OFFSET (Pagination)

Used to paginate large query result sets:

```sql
-- Top 3 highest-earning employees
SELECT * FROM employees 
ORDER BY salary DESC 
LIMIT 3;

-- Page 2 of results (Items 4 to 6): Skip 3 rows, take next 3
SELECT * FROM employees 
ORDER BY salary DESC 
LIMIT 3 OFFSET 3;

-- Standard ANSI SQL alternative (SQL Server, Oracle, PostgreSQL):
-- OFFSET 3 ROWS FETCH NEXT 3 ROWS ONLY;
```

---

## 6. Aggregate Functions & Grouping

### Aggregate Functions

Aggregate functions perform a calculation on a set of rows and return a **single scalar value**:

| Function | Description | Example |
| :--- | :--- | :--- |
| `COUNT(*)` | Counts all rows, including `NULL`s | `SELECT COUNT(*) FROM employees;` |
| `COUNT(col)`| Counts non-NULL rows in `col` | `SELECT COUNT(dept_id) FROM employees;` |
| `SUM(col)` | Calculates total sum of numeric column | `SELECT SUM(salary) FROM employees;` |
| `AVG(col)` | Calculates average value of numeric column | `SELECT AVG(salary) FROM employees;` |
| `MIN(col)` | Returns minimum value | `SELECT MIN(salary) FROM employees;` |
| `MAX(col)` | Returns maximum value | `SELECT MAX(salary) FROM employees;` |

---

### GROUP BY Clause

`GROUP BY` groups rows that share values into summary rows.

```sql
SELECT 
    dept_id,
    COUNT(*) AS total_employees,
    AVG(salary) AS average_salary,
    MAX(salary) AS highest_salary
FROM employees
WHERE dept_id IS NOT NULL
GROUP BY dept_id;
```

**Output:**

| dept_id | total_employees | average_salary | highest_salary |
| :------ | :-------------- | :------------- | :------------- |
| 1       | 2               | 73500.00       | 85000.00       |
| 2       | 2               | 73000.00       | 92000.00       |

> 📌 **Rule:** Any column present in the `SELECT` list that is **not wrapped in an aggregate function** must appear in the `GROUP BY` clause!

---

### HAVING vs WHERE

This is one of the most tested SQL concepts:

| Feature | `WHERE` | `HAVING` |
| :--- | :--- | :--- |
| **Applied to** | Individual rows | Grouped rows / Aggregates |
| **Execution** | Runs **before** `GROUP BY` | Runs **after** `GROUP BY` |
| **Aggregates** | Cannot contain aggregates (`WHERE AVG(s) > 5000` is **invalid**) | Designed for aggregates (`HAVING AVG(s) > 5000`) |

```sql
-- Example combining WHERE and HAVING:
SELECT 
    dept_id,
    AVG(salary) AS avg_sal
FROM employees
WHERE salary > 50000          -- Filter individual employees earning > 50k
GROUP BY dept_id
HAVING AVG(salary) > 70000;   -- Filter departments whose average is > 70k
```

---

## 7. SQL Joins

Joins combine columns from one or more tables based on a related column between them.

```
       INNER JOIN                     LEFT JOIN
    ┌───────┬───────┐              ┌───────┬───────┐
    │       │ Match │              │ All   │ Match │
    │   A   │  A∩B  │   B          │   A   │  A∩B  │   B
    │       │       │              │       │       │
    └───────┴───────┘              └───────┴───────┘
 Only matching rows in both       All rows from A + matching B

       RIGHT JOIN                     FULL OUTER JOIN
    ┌───────┬───────┐              ┌───────┬───────┐
    │       │ Match │  All         │ All   │ Match │  All
    │   A   │  A∩B  │   B          │   A   │  A∩B  │   B
    │       │       │              │       │       │
    └───────┴───────┘              └───────┴───────┘
 All rows from B + matching A       All rows from both tables
```

---

### Sample Tables for Joins

#### Table `employees` (`E`)
| emp_id | name    | dept_id |
| :----- | :------ | :------ |
| 1      | Alice   | 10      |
| 2      | Bob     | 20      |
| 3      | Charlie | 30      |
| 4      | David   | NULL    |

#### Table `departments` (`D`)
| dept_id | dept_name    |
| :------ | :----------- |
| 10      | Engineering  |
| 20      | Marketing    |
| 40      | Finance      |

---

### INNER JOIN
Returns only records where there is a match in **both** tables.

```sql
SELECT E.emp_id, E.name, D.dept_name
FROM employees E
INNER JOIN departments D ON E.dept_id = D.dept_id;
```

**Output:**
| emp_id | name  | dept_name   |
| :----- | :---- | :---------- |
| 1      | Alice | Engineering |
| 2      | Bob   | Marketing   |

*(Charlie and David omitted because `dept_id` 30 and `NULL` have no match in `departments`. Finance omitted because no employee belongs to dept 40).*

---

### LEFT (OUTER) JOIN
Returns **all** records from the left table (`employees`), and the matched records from the right table (`departments`). Unmatched right columns contain `NULL`.

```sql
SELECT E.emp_id, E.name, D.dept_name
FROM employees E
LEFT JOIN departments D ON E.dept_id = D.dept_id;
```

**Output:**
| emp_id | name    | dept_name   |
| :----- | :------ | :---------- |
| 1      | Alice   | Engineering |
| 2      | Bob     | Marketing   |
| 3      | Charlie | NULL        |
| 4      | David   | NULL        |

---

### RIGHT (OUTER) JOIN
Returns **all** records from the right table (`departments`), and matched records from the left table (`employees`).

```sql
SELECT E.emp_id, E.name, D.dept_name
FROM employees E
RIGHT JOIN departments D ON E.dept_id = D.dept_id;
```

**Output:**
| emp_id | name  | dept_name   |
| :----- | :---- | :---------- |
| 1      | Alice | Engineering |
| 2      | Bob   | Marketing   |
| NULL   | NULL  | Finance     |

---

### FULL (OUTER) JOIN
Returns all records when there is a match in either left or right table.

```sql
SELECT E.emp_id, E.name, D.dept_name
FROM employees E
FULL OUTER JOIN departments D ON E.dept_id = D.dept_id;
```

**Output:**
| emp_id | name    | dept_name   |
| :----- | :------ | :---------- |
| 1      | Alice   | Engineering |
| 2      | Bob     | Marketing   |
| 3      | Charlie | NULL        |
| 4      | David   | NULL        |
| NULL   | NULL    | Finance     |

*(Note: In MySQL, emulate `FULL OUTER JOIN` using `LEFT JOIN UNION RIGHT JOIN`).*

---

### CROSS JOIN
Produces the Cartesian product (all possible combinations of rows from both tables).

```sql
SELECT E.name, D.dept_name
FROM employees E
CROSS JOIN departments D;
-- Result count = (Rows in Table A) × (Rows in Table B)
```

---

### SELF JOIN
A table is joined to itself. Useful for hierarchical relationships (e.g., finding an employee's manager).

```sql
-- Table has columns: emp_id, name, manager_id
SELECT 
    emp.name AS employee_name,
    mgr.name AS manager_name
FROM employees emp
LEFT JOIN employees mgr ON emp.manager_id = mgr.emp_id;
```

---

### Anti-Join
Finds rows in the first table that **do not exist** in the second table:

```sql
-- Find employees who are NOT assigned to any valid department
SELECT E.emp_id, E.name
FROM employees E
LEFT JOIN departments D ON E.dept_id = D.dept_id
WHERE D.dept_id IS NULL;
```

---

## 8. Set Operations

Set operators combine the results of two or more `SELECT` queries into a single result set.

> 📌 **Rules for Set Operations:**
> 1. Each query must have the **same number of columns**.
> 2. Columns must have **compatible data types**.
> 3. Columns must be in the **same order**.

---

### UNION & UNION ALL

- `UNION`: Combines rows and **removes duplicates** (slower due to sorting/deduplication).
- `UNION ALL`: Combines rows and **keeps all duplicates** (faster).

```sql
-- UNION (Unique customer + supplier cities)
SELECT city FROM customers
UNION
SELECT city FROM suppliers;

-- UNION ALL (Retains all rows from both tables)
SELECT city FROM customers
UNION ALL
SELECT city FROM suppliers;
```

---

### INTERSECT
Returns only rows that are present in **both** query results.

```sql
-- Cities that have BOTH customers AND suppliers
SELECT city FROM customers
INTERSECT
SELECT city FROM suppliers;
```

---

### EXCEPT / MINUS
Returns rows from the first query that are **not** present in the second query (`EXCEPT` in PostgreSQL/SQL Server, `MINUS` in Oracle).

```sql
-- Cities with customers but NO suppliers
SELECT city FROM customers
EXCEPT
SELECT city FROM suppliers;
```

---

## 9. Subqueries & CTEs

A subquery is a query nested inside another SQL statement.

### Scalar Subqueries
Returns exactly **one value** (one row, one column).

```sql
-- Find all employees who earn more than the company average salary
SELECT first_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

---

### Multi-Row Subqueries
Returns multiple rows (one column with multiple values). Used with `IN`, `NOT IN`, `ANY`, or `ALL`.

```sql
-- Find employees working in departments located in 'Building A'
SELECT first_name, last_name
FROM employees
WHERE dept_id IN (
    SELECT dept_id 
    FROM departments 
    WHERE location = 'Building A'
);
```

---

### Correlated Subqueries & EXISTS
A correlated subquery references columns from the **outer query**. It executes once for each row evaluated by the outer query.

```sql
-- Find employees who earn more than the average salary of THEIR OWN department
SELECT E.first_name, E.salary, E.dept_id
FROM employees E
WHERE E.salary > (
    SELECT AVG(sub.salary)
    FROM employees sub
    WHERE sub.dept_id = E.dept_id
);

-- Using EXISTS (Very fast: stops searching as soon as 1 match is found)
SELECT D.dept_name
FROM departments D
WHERE EXISTS (
    SELECT 1 
    FROM employees E 
    WHERE E.dept_id = D.dept_id AND E.salary > 80000
);
```

---

### Common Table Expressions (CTEs)

A **CTE** creates a temporary, named result set using the `WITH` clause. It makes complex queries significantly cleaner, modular, and easier to debug than nested subqueries.

```sql
-- Define CTE
WITH HighEarners AS (
    SELECT emp_id, first_name, last_name, dept_id, salary
    FROM employees
    WHERE salary > 70000
),
DeptStats AS (
    SELECT dept_id, COUNT(*) AS high_earner_count
    FROM HighEarners
    GROUP BY dept_id
)
-- Use CTEs in main query
SELECT D.dept_name, S.high_earner_count
FROM DeptStats S
JOIN departments D ON S.dept_id = D.dept_id;
```

#### Recursive CTE (Example: Generating 1 to 5)
```sql
WITH RECURSIVE NumberSeries AS (
    -- Anchor member
    SELECT 1 AS num
    UNION ALL
    -- Recursive member
    SELECT num + 1 
    FROM NumberSeries 
    WHERE num < 5
)
SELECT * FROM NumberSeries;
```

---

## 10. Window Functions (Analytic Functions)

Unlike standard aggregate functions (which collapse multiple rows into a single row), **Window Functions** compute values across a set of table rows while **preserving each individual row's identity**.

```sql
FUNCTION() OVER (
    PARTITION BY column_name   -- Optional: defines the group/slice of rows
    ORDER BY column_name       -- Optional: defines row sequence inside the window
    ROWS BETWEEN ...           -- Optional: frame specification
)
```

---

### Ranking Functions

Sample data sorted by `salary DESC`:

```sql
SELECT 
    first_name,
    dept_id,
    salary,
    ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS row_num,
    RANK()       OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk,
    DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS dense_rnk
FROM employees;
```

**Comparison of Ranking Functions:**

| Salary | `ROW_NUMBER()` | `RANK()` (Skips on ties) | `DENSE_RANK()` (No skipping) |
| :----- | :------------- | :----------------------- | :--------------------------- |
| 1000   | 1              | 1                        | 1                            |
| 900    | 2              | 2                        | 2                            |
| 900    | 3              | 2                        | 2                            |
| 800    | 4              | **4** (skips 3)          | **3** (does not skip)        |

> 💡 **Interview Tip:** When asked for the **"Nth highest salary"**, always use `DENSE_RANK()` so duplicate top salaries don't shift your target rank!

---

### Value Functions

- `LAG(col, offset)`: Accesses data from a previous row.
- `LEAD(col, offset)`: Accesses data from a subsequent row.

```sql
-- Calculate Month-over-Month Sales Growth
SELECT 
    sale_date,
    amount,
    LAG(amount, 1) OVER (ORDER BY sale_date) AS prev_month_amount,
    amount - LAG(amount, 1) OVER (ORDER BY sale_date) AS diff_from_last_month
FROM monthly_sales;
```

---

### Running Totals

Compute a cumulative sum per department:

```sql
SELECT 
    emp_id,
    dept_id,
    salary,
    SUM(salary) OVER (
        PARTITION BY dept_id 
        ORDER BY emp_id
    ) AS running_dept_salary_total
FROM employees;
```

---

## 11. Views, Indexes & Transactions

### Views
A **View** is a virtual table based on the result set of an SQL statement. It stores the query definition, not the actual data (unless it is a *Materialized View*).

```sql
-- Create View
CREATE VIEW v_active_engineering_employees AS
SELECT emp_id, first_name, last_name, salary
FROM employees
WHERE dept_id = 1;

-- Querying the View just like a normal table
SELECT * FROM v_active_engineering_employees WHERE salary > 60000;

-- Drop View
DROP VIEW v_active_engineering_employees;
```

---

### Indexes & Performance

An **Index** is a data structure (typically a B-Tree) that improves the speed of data retrieval operations on a database table at the cost of additional write time and storage space.

```sql
-- Single Column Index
CREATE INDEX idx_emp_email ON employees(email);

-- Composite Index (Multi-column)
CREATE INDEX idx_emp_dept_salary ON employees(dept_id, salary);

-- Unique Index
CREATE UNIQUE INDEX idx_unique_dept_name ON departments(dept_name);

-- Drop Index
DROP INDEX idx_emp_email ON employees;  -- MySQL syntax
-- DROP INDEX idx_emp_email;            -- PostgreSQL syntax
```

#### Performance Optimization Checklist:
- ✅ Add indexes on columns frequently used in `WHERE`, `JOIN ON`, and `ORDER BY`.
- ❌ Do **not** over-index tables with heavy `INSERT`/`UPDATE` workloads.
- ❌ Avoid leading wildcards in `LIKE` queries (`LIKE '%term'`) because they cause full table scans.
- ✅ Use `EXPLAIN` or `EXPLAIN ANALYZE` before queries to inspect execution plans.

---

### Transactions & ACID Properties

A transaction is a single unit of work consisting of one or more SQL operations.

```
       A ───► Atomicity   : "All or Nothing" (entire transaction succeeds or fails)
       C ───► Consistency : Data moves from one valid state to another, rules respected
       I ───► Isolation   : Concurrent transactions execute without interfering with each other
       D ───► Durability  : Once committed, changes survive server crashes/power loss
```

#### TCL Commands:

```sql
-- Start Transaction
START TRANSACTION;  -- (or BEGIN TRANSACTION)

-- Step 1: Deduct from Account A
UPDATE accounts 
SET balance = balance - 500 
WHERE account_id = 1001;

-- Step 2: Create a savepoint
SAVEPOINT transfer_step_one;

-- Step 3: Credit to Account B
UPDATE accounts 
SET balance = balance + 500 
WHERE account_id = 2002;

-- If an error occurs, rollback to savepoint:
-- ROLLBACK TO transfer_step_one;

-- If everything succeeds, permanently save changes:
COMMIT;

-- If something went critically wrong, discard all changes:
-- ROLLBACK;
```

---

## 12. Common Built-in Functions

### Conditional Expressions (CASE WHEN)

```sql
SELECT 
    first_name,
    salary,
    CASE 
        WHEN salary >= 80000 THEN 'Senior Level'
        WHEN salary >= 55000 THEN 'Mid Level'
        ELSE 'Junior Level'
    END AS career_tier
FROM employees;
```

---

### String Functions

| Function | Description | Example | Result |
| :--- | :--- | :--- | :--- |
| `CONCAT(a, b)` | Combines strings | `CONCAT('John', ' ', 'Doe')` | `'John Doe'` |
| `UPPER(str)` | Uppercase | `UPPER('sql')` | `'SQL'` |
| `LOWER(str)` | Lowercase | `LOWER('SQL')` | `'sql'` |
| `LENGTH(str)` | String length in chars | `LENGTH('Database')` | `8` |
| `SUBSTRING(s, pos, len)` | Extracts substring | `SUBSTRING('Hello World', 1, 5)`| `'Hello'` |
| `TRIM(str)` | Removes leading/trailing spaces | `TRIM('  data  ')` | `'data'` |
| `REPLACE(s, from, to)` | Replaces occurrences | `REPLACE('cat', 'c', 'b')` | `'bat'` |

---

### Date & Time Functions

| Function | Description | Example |
| :--- | :--- | :--- |
| `CURRENT_DATE` | Today's date | `SELECT CURRENT_DATE;` |
| `CURRENT_TIMESTAMP` | Current date + time | `SELECT CURRENT_TIMESTAMP;` |
| `EXTRACT(part FROM col)`| Extracts year, month, day | `EXTRACT(YEAR FROM hire_date)` |
| `DATEDIFF(date1, date2)`| Difference between two dates (MySQL)| `DATEDIFF('2024-05-10', '2024-05-01')` |
| `DATE_ADD(date, INTERVAL 7 DAY)` | Adds time interval | `DATE_ADD(CURRENT_DATE, INTERVAL 1 MONTH)` |

---

## 13. Top SQL Interview Queries

### 1. Find the 2nd (or Nth) Highest Salary
```sql
-- Solution using DENSE_RANK() (Recommended: Handles ties properly)
WITH RankedSalaries AS (
    SELECT 
        salary,
        DENSE_RANK() OVER (ORDER BY salary DESC) as rank_pos
    FROM employees
)
SELECT salary 
FROM RankedSalaries 
WHERE rank_pos = 2
LIMIT 1;

-- Alternative using Subquery (2nd highest only)
SELECT MAX(salary) 
FROM employees 
WHERE salary < (SELECT MAX(salary) FROM employees);
```

---

### 2. Find Duplicate Records in a Table
```sql
SELECT email, COUNT(*) AS count_occurrences
FROM employees
GROUP BY email
HAVING COUNT(*) > 1;
```

---

### 3. Delete Duplicate Records Keeping the Smallest ID
```sql
DELETE E1 FROM employees E1
INNER JOIN employees E2 
ON E1.email = E2.email AND E1.emp_id > E2.emp_id;

-- Standard ANSI SQL using CTE:
WITH Duplicates AS (
    SELECT emp_id,
           ROW_NUMBER() OVER (PARTITION BY email ORDER BY emp_id ASC) AS rn
    FROM employees
)
DELETE FROM employees
WHERE emp_id IN (SELECT emp_id FROM Duplicates WHERE rn > 1);
```

---

### 4. Find Employees Who Earn More Than Their Manager
```sql
SELECT 
    emp.emp_id,
    emp.first_name AS employee_name,
    emp.salary AS employee_salary,
    mgr.first_name AS manager_name,
    mgr.salary AS manager_salary
FROM employees emp
INNER JOIN employees mgr ON emp.manager_id = mgr.emp_id
WHERE emp.salary > mgr.salary;
```

---

### 5. Find Departments That Have No Employees
```sql
SELECT D.dept_id, D.dept_name
FROM departments D
LEFT JOIN employees E ON D.dept_id = E.dept_id
WHERE E.emp_id IS NULL;
```

---

### 6. Swap Gender / Values with a Single UPDATE Statement
```sql
UPDATE employees
SET gender = CASE 
    WHEN gender = 'Male' THEN 'Female'
    WHEN gender = 'Female' THEN 'Male'
END;
```

---

## 🎯 Summary Checklist

- [x] **Execution Order**: `FROM` ➔ `WHERE` ➔ `GROUP BY` ➔ `HAVING` ➔ `SELECT` ➔ `ORDER BY` ➔ `LIMIT`
- [x] **Primary Key vs Unique**: Primary Key does not allow `NULL`; Unique allows one `NULL` (in most RDBMS).
- [x] **WHERE vs HAVING**: `WHERE` filters rows before aggregation; `HAVING` filters aggregated groups.
- [x] **UNION vs UNION ALL**: `UNION` eliminates duplicates (slower); `UNION ALL` preserves duplicates (faster).
- [x] **Rank vs Dense_Rank**: `RANK()` skips positions when ties occur; `DENSE_RANK()` never skips ranking numbers.
- [x] **Delete vs Truncate**: `DELETE` is row-level DML (can rollback); `TRUNCATE` is page-level DDL (faster, resets identity).
