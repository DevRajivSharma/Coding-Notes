# 🎯 Top SQL Interview Questions (Theory & Practical)

A curated compilation of the most frequently asked SQL interview questions for Software Engineers, Data Engineers, Data Analysts, and Backend Developers.

> 📚 **Navigation:** [📖 Full SQL Notes](./README.md) • [⚡ Quick Cheat Sheet](./SQL_Cheatsheet.md)

---

## 📑 Table of Contents

- [Part 1: Core Theoretical Questions](#part-1-core-theoretical-questions)
  - [Q1: What is the logical query execution order in SQL?](#q1-what-is-the-logical-query-execution-order-in-sql)
  - [Q2: What is the difference between WHERE and HAVING?](#q2-what-is-the-difference-between-where-and-having)
  - [Q3: What is the difference between DELETE, TRUNCATE, and DROP?](#q3-what-is-the-difference-between-delete-truncate-and-drop)
  - [Q4: Difference between PRIMARY KEY and UNIQUE KEY?](#q4-difference-between-primary-key-and-unique-key)
  - [Q5: Explain Database Normalization (1NF, 2NF, 3NF, BCNF)](#q5-explain-database-normalization-1nf-2nf-3nf-bcnf)
  - [Q6: Difference between Clustered and Non-Clustered Index?](#q6-difference-between-clustered-and-non-clustered-index)
  - [Q7: What are ACID Properties in RDBMS?](#q7-what-are-acid-properties-in-rdbms)
  - [Q8: Explain Transaction Isolation Levels & Read Phenomena](#q8-explain-transaction-isolation-levels--read-phenomena)
  - [Q9: Difference between UNION and UNION ALL?](#q9-difference-between-union-and-union-all)
  - [Q10: Difference between COUNT(*), COUNT(1), and COUNT(column)?](#q10-difference-between-count-count1-and-countcolumn)
  - [Q11: Difference between ROW_NUMBER(), RANK(), and DENSE_RANK()?](#q11-difference-between-row_number-rank-and-dense_rank)
  - [Q12: Stored Procedure vs User-Defined Function (UDF)?](#q12-stored-procedure-vs-user-defined-function-udf)
  - [Q13: What is a View vs a Materialized View?](#q13-what-is-a-view-vs-a-materialized-view)
  - [Q14: What is a Correlated Subquery and why can it be slow?](#q14-what-is-a-correlated-subquery-and-why-can-it-be-slow)
  - [Q15: How does SQL handle NULL values? (Three-Valued Logic)](#q15-how-does-sql-handle-null-values-three-valued-logic)
  - [Q16: How do you optimize a slow SQL query?](#q16-how-do-you-optimize-a-slow-sql-query)
- [Part 2: Essential Practical & Coding Questions](#part-2-essential-practical--coding-questions)
  - [P1: Find the Nth Highest Salary](#p1-find-the-nth-highest-salary)
  - [P2: Find and Delete Duplicate Records (Keep Smallest ID)](#p2-find-and-delete-duplicate-records-keep-smallest-id)
  - [P3: Employees Earning More Than Their Direct Manager](#p3-employees-earning-more-than-their-direct-manager)
  - [P4: Find the Top Earner in Each Department (Top N per Group)](#p4-find-the-top-earner-in-each-department-top-n-per-group)
  - [P5: Find Customers Who Never Placed an Order](#p5-find-customers-who-never-placed-an-order)
  - [P6: Consecutive Login Days / Consecutive Records (Gaps & Islands)](#p6-consecutive-login-days--consecutive-records-gaps--islands)
  - [P7: Calculate Running Total / Cumulative Sum](#p7-calculate-running-total--cumulative-sum)
  - [P8: Month-over-Month (MoM) Growth Percentage](#p8-month-over-month-mom-growth-percentage)
  - [P9: Second Most Recent Order per User](#p9-second-most-recent-order-per-user)
  - [P10: Pivot Data Without PIVOT Function (Conditional Aggregation)](#p10-pivot-data-without-pivot-function-conditional-aggregation)
  - [P11: Swap Values with a Single UPDATE Statement](#p11-swap-values-with-a-single-update-statement)
  - [P12: Find Management Hierarchy (Recursive CTE)](#p12-find-management-hierarchy-recursive-cte)
  - [P13: Users Who Bought Both Product A and Product B](#p13-users-who-bought-both-product-a-and-product-b)
- [Part 3: Tricky Edge Cases & Output Questions](#part-3-tricky-edge-cases--output-questions)
  - [T1: Why does `WHERE col NOT IN (...)` return empty when NULL is present?](#t1-why-does-where-col-not-in--return-empty-when-null-is-present)
  - [T2: Does `WHERE NULL = NULL` evaluate to TRUE?](#t2-does-where-null--null-evaluate-to-true)
  - [T3: Behavior of NULL in Math & Aggregations](#t3-behavior-of-null-in-math--aggregations)

---

# Part 1: Core Theoretical Questions

---

### Q1: What is the logical query execution order in SQL?

**Answer:**
Although an SQL query is written starting with `SELECT`, the database engine processes clauses in a distinct logical order:

```
Step 1: FROM & JOIN     👉 Identify and combine source tables
Step 2: WHERE           👉 Filter individual rows (before grouping)
Step 3: GROUP BY        👉 Group remaining rows into summary buckets
Step 4: HAVING          👉 Filter grouped/aggregated rows
Step 5: SELECT          👉 Compute expressions, evaluate window functions
Step 6: DISTINCT        👉 Remove duplicate output rows
Step 7: ORDER BY        👉 Sort the final projected output
Step 8: LIMIT / OFFSET  👉 Restrict row count returned to client
```

**Key Follow-up:**
- *Why can't we use a column alias defined in `SELECT` inside the `WHERE` clause?*
  Because `WHERE` is evaluated in **Step 2**, while `SELECT` (and its aliases) is evaluated in **Step 5**.

---

### Q2: What is the difference between WHERE and HAVING?

| Feature | `WHERE` Clause | `HAVING` Clause |
| :--- | :--- | :--- |
| **Applied to** | Individual rows | Groups of rows created by `GROUP BY` |
| **Execution Point** | Runs **before** aggregation / `GROUP BY` | Runs **after** aggregation / `GROUP BY` |
| **Aggregates** | Cannot contain aggregate functions (`SUM`, `AVG`, etc.) | Can and typically does contain aggregate functions |
| **Can exist alone?**| Yes, does not require `GROUP BY` | Can be used without `GROUP BY` (treats entire table as 1 group), but usually paired with `GROUP BY` |

```sql
-- WHERE filters individual employee salaries; HAVING filters department averages
SELECT dept_id, AVG(salary) AS avg_sal
FROM employees
WHERE salary > 30000          -- Filter individual rows first
GROUP BY dept_id
HAVING AVG(salary) > 60000;   -- Filter grouped results
```

---

### Q3: What is the difference between DELETE, TRUNCATE, and DROP?

| Feature | `DELETE` | `TRUNCATE` | `DROP` |
| :--- | :--- | :--- | :--- |
| **Category** | DML (Data Manipulation) | DDL (Data Definition) | DDL (Data Definition) |
| **Scope** | Selected rows or all rows | All rows | Entire table schema + data |
| **`WHERE` Clause** | Yes (`WHERE id = 5`) | No | No |
| **Speed** | Slower (logs row by row) | Very fast (deallocates data pages) | Very fast |
| **Rollback** | Fully rollbackable in transactions | Rollbackable in PostgreSQL/SQL Server; Not in MySQL | Rollbackable in PostgreSQL; Not in MySQL |
| **Triggers** | Fires `DELETE` triggers | Does **not** fire row-level triggers | Does **not** fire triggers |
| **Identity/Auto-inc**| Does not reset counter | Resets counter to initial value | Table destroyed completely |

---

### Q4: Difference between PRIMARY KEY and UNIQUE KEY?

| Feature | PRIMARY KEY | UNIQUE KEY |
| :--- | :--- | :--- |
| **NULL values** | Strictly **no NULL** allowed | Allows **NULL** values (typically 1 in MySQL/SQL Server, multiple in PostgreSQL) |
| **Quantity** | Only **one** Primary Key per table | Multiple Unique keys allowed per table |
| **Default Index** | Automatically creates a **Clustered Index** (by default in SQL Server/MySQL InnoDB) | Automatically creates a **Non-Clustered Index** |
| **Purpose** | Uniquely identifies a record in the table | Enforces column uniqueness across rows |

---

### Q5: Explain Database Normalization (1NF, 2NF, 3NF, BCNF)

**Normalization** is the process of organizing relational tables to minimize data redundancy and prevent insert, update, and delete anomalies.

1. **First Normal Form (1NF):**
   - Each column contains atomic (indivisible) values.
   - No repeating groups or arrays stored in a single cell.
   - Each row is unique (Primary Key exists).

2. **Second Normal Form (2NF):**
   - Must be in **1NF**.
   - **No Partial Dependency**: Every non-key column must be fully dependent on the *entire* primary key (relevant when the primary key is composite).

3. **Third Normal Form (3NF):**
   - Must be in **2NF**.
   - **No Transitive Dependency**: Non-key columns must not depend on other non-key columns ($X \to Y$ and $Y \to Z$). "Every column must depend on the key, the whole key, and nothing but the key."

4. **Boyce-Codd Normal Form (BCNF):**
   - A stricter version of 3NF. For every functional dependency $X \to Y$, $X$ must be a super key.

> **When is Denormalization used?**
> In Read-heavy systems, data warehouses, and reporting databases (OLAP) to eliminate expensive `JOIN` operations and improve query read performance.

---

### Q6: Difference between Clustered and Non-Clustered Index?

```
Clustered Index                           Non-Clustered Index
(Dictionary: Data sorted in-place)         (Index in back of book: Points to page)
┌───────┬──────────────┬────────┐         ┌───────┬──────────────┐
│ Key   │ Column A     │ Col B  │         │ Key   │ Pointer (RID)│
├───────┼──────────────┼────────┤         ├───────┼──────────────┤
│ 1     │ Alice        │ 50000  │         │ Alice │ Row 1        │
│ 2     │ Bob          │ 62000  │         │ Bob   │ Row 2        │
└───────┴──────────────┴────────┘         └───────┴──────────────┘
```

| Feature | Clustered Index | Non-Clustered Index |
| :--- | :--- | :--- |
| **Physical Storage**| Determines physical order of data rows on disk | Separate structure; contains pointers to the actual data |
| **Limit per Table** | Only **1** per table | **Multiple** allowed per table (e.g., up to 999 in SQL Server) |
| **Leaf Node** | Contains the **actual data rows** | Contains the **index key + pointer (Row ID / Clustered Key)** |
| **Lookup Speed** | Faster for range scans (`BETWEEN 10 AND 50`) | Extra lookup hop required if not a "Covering Index" |

---

### Q7: What are ACID Properties in RDBMS?

- **A - Atomicity**: "All or Nothing." If one query fails inside a transaction, the entire transaction is rolled back.
- **C - Consistency**: Database transitions only between valid states satisfying all constraints, schemas, and triggers.
- **I - Isolation**: Concurrent transactions execute independently without reading intermediate, uncommitted changes from one another.
- **D - Durability**: Once committed, changes are permanent and will not be lost even during system failures or power loss (persisted to Write-Ahead Logs / disk).

---

### Q8: Explain Transaction Isolation Levels & Read Phenomena

#### Read Phenomena:
1. **Dirty Read**: Reading uncommitted data from another transaction that might later be rolled back.
2. **Non-Repeatable Read**: Re-reading the same row within a transaction produces different column values because another transaction modified and committed it.
3. **Phantom Read**: Re-running a range query produces additional/fewer rows because another transaction inserted/deleted rows and committed.

#### Isolation Levels:

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read |
| :--- | :---: | :---: | :---: |
| **Read Uncommitted** | ⚠️ Yes | ⚠️ Yes | ⚠️ Yes |
| **Read Committed** (Default in PG/Oracle) | ❌ No | ⚠️ Yes | ⚠️ Yes |
| **Repeatable Read** (Default in MySQL InnoDB)| ❌ No | ❌ No | ⚠️ Yes (Avoided via MVCC in MySQL) |
| **Serializable** | ❌ No | ❌ No | ❌ No |

---

### Q9: Difference between UNION and UNION ALL?

| Feature | `UNION` | `UNION ALL` |
| :--- | :--- | :--- |
| **Duplicates** | Removes duplicate records | Retains all duplicates |
| **Performance**| Slower (performs implicit sorting and deduplication) | Faster (simply concatenates record sets) |
| **When to use**| When unique rows are strictly required across datasets | When duplicates are allowed or data is guaranteed distinct |

---

### Q10: Difference between COUNT(*), COUNT(1), and COUNT(column)?

- **`COUNT(*)`**: Counts **all rows** returned by the query, including rows with `NULL`s. Modern query optimizers treat `COUNT(*)` and `COUNT(1)` with identical execution plans and performance.
- **`COUNT(1)`**: Same as `COUNT(*)`; evaluates constant `1` for every row.
- **`COUNT(column_name)`**: Counts only rows where `column_name` is **`NOT NULL`**. If all rows in `column_name` are `NULL`, it returns `0`.

---

### Q11: Difference between ROW_NUMBER(), RANK(), and DENSE_RANK()?

Given salaries: `[100, 90, 90, 80]`

| Salary | `ROW_NUMBER()` | `RANK()` | `DENSE_RANK()` |
| :--- | :---: | :---: | :---: |
| 100 | 1 | 1 | 1 |
| 90 | 2 | 2 | 2 |
| 90 | 3 | 2 | 2 |
| 80 | 4 | **4** *(skips 3)* | **3** *(does not skip)* |

- **`ROW_NUMBER()`**: Generates an uninterrupted consecutive integer per row ($1, 2, 3, 4$). Ties get arbitrary different numbers.
- **`RANK()`**: Ties share the same rank, but subsequent ranks are **skipped** based on tie count ($1, 2, 2, 4$).
- **`DENSE_RANK()`**: Ties share the same rank, and subsequent ranks are **not skipped** ($1, 2, 2, 3$). Always use `DENSE_RANK()` for finding the $N^{\text{th}}$ highest/lowest values.

---

### Q12: Stored Procedure vs User-Defined Function (UDF)?

| Feature | Stored Procedure | Function |
| :--- | :--- | :--- |
| **Return Value** | May return zero, single, or multiple values (OUT params) | Must return a single scalar value or a table |
| **DML Operations** | Can execute `INSERT`, `UPDATE`, `DELETE` | Read-only in most engines; cannot execute modifying DML |
| **Usage in Queries**| Cannot be called inside `SELECT` / `WHERE` clauses | Can be called directly in `SELECT`, `WHERE`, `JOIN` |
| **Transactions** | Can manage transactions (`COMMIT`, `ROLLBACK`) | Cannot manage transactions |

---

### Q13: What is a View vs a Materialized View?

- **Standard View**: A saved SQL query. It stores **no data on disk**. Every time you query the view, the database executes the underlying query on the fly.
- **Materialized View**: Stores the **actual physical result of the query on disk**. It dramatically accelerates expensive joins/aggregations on large datasets, but requires periodic refreshing (`REFRESH MATERIALIZED VIEW`) to stay in sync with underlying tables.

---

### Q14: What is a Correlated Subquery and why can it be slow?

A **Correlated Subquery** is an inner query that depends on values from the outer query for its evaluation.
- **Why slow?** Unlike regular subqueries (which execute once and pass the result to the outer query), a correlated subquery executes **once per row** evaluated by the outer query ($O(N \times M)$ complexity).
- **Optimization:** Rewrite correlated subqueries using `JOIN`s or Window Functions whenever possible.

---

### Q15: How does SQL handle NULL values? (Three-Valued Logic)

In SQL, comparisons with `NULL` evaluate to **`UNKNOWN`** (neither `TRUE` nor `FALSE`):
- `5 = NULL` ➔ `UNKNOWN`
- `NULL = NULL` ➔ `UNKNOWN`
- `NULL <> NULL` ➔ `UNKNOWN`

`WHERE` clauses only pass rows whose conditions evaluate strictly to **`TRUE`**. Conditions evaluating to `UNKNOWN` or `FALSE` are filtered out.
- To check for nullability, always use: `IS NULL` or `IS NOT NULL`.
- To substitute null values, use: `COALESCE(col, fallback)` or `IFNULL(col, fallback)`.

---

### Q16: How do you optimize a slow SQL query?

1. **Inspect the Execution Plan**: Run `EXPLAIN` or `EXPLAIN ANALYZE` to identify Table Scans vs Index Scans.
2. **Add Appropriate Indexes**: Index columns frequently used in `WHERE`, `JOIN ON`, and `ORDER BY`.
3. **Avoid `SELECT *`**: Fetch only required columns to reduce I/O and enable *Index Covering*.
4. **Avoid Leading Wildcards**: `LIKE '%abc'` invalidates B-tree indexes; `LIKE 'abc%'` can use an index.
5. **Avoid Functions on Indexed Columns**: `WHERE YEAR(created_at) = 2024` prevents index usage; use `WHERE created_at >= '2024-01-01' AND created_at < '2025-01-01'`.
6. **Prefer `UNION ALL` over `UNION`**: If duplicate removal is unnecessary.
7. **Use CTEs & Window Functions**: Replace expensive self-joins and correlated subqueries.
8. **Batch Bulk Writes**: Avoid inserting one row at a time in loops.

---

# Part 2: Essential Practical & Coding Questions

---

### P1: Find the Nth Highest Salary

Given the `employees` table: `(emp_id, first_name, salary)`

#### Approach 1: Window Function (Most robust & handles ties)
```sql
WITH RankedSalaries AS (
    SELECT 
        salary,
        DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
)
SELECT salary 
FROM RankedSalaries 
WHERE rnk = 2 -- Change 2 to N
LIMIT 1;
```

#### Approach 2: Correlated Subquery (Engine-independent)
```sql
-- Find 2nd highest salary: exactly 1 salary is greater than this salary
SELECT DISTINCT E1.salary
FROM employees E1
WHERE (
    SELECT COUNT(DISTINCT E2.salary)
    FROM employees E2
    WHERE E2.salary > E1.salary
) = 1; -- (N - 1)
```

#### Approach 3: LIMIT & OFFSET (Simple, but watch out for duplicates)
```sql
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1; -- OFFSET (N - 1)
```

---

### P2: Find and Delete Duplicate Records (Keep Smallest ID)

#### Table `users`:
| id | email |
| :- | :---- |
| 1  | test@example.com |
| 2  | bob@example.com  |
| 3  | test@example.com |

#### Query to FIND duplicates:
```sql
SELECT email, COUNT(*) AS occurrences
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

#### Query to DELETE duplicates (Keep smallest `id`):
```sql
-- Method 1: Self Join (MySQL / PostgreSQL)
DELETE u1 FROM users u1
INNER JOIN users u2 
ON u1.email = u2.email AND u1.id > u2.id;

-- Method 2: Using CTE with ROW_NUMBER() (Standard ANSI SQL / SQL Server)
WITH Duplicates AS (
    SELECT id,
           ROW_NUMBER() OVER (PARTITION BY email ORDER BY id ASC) as rn
    FROM users
)
DELETE FROM users 
WHERE id IN (SELECT id FROM Duplicates WHERE rn > 1);
```

---

### P3: Employees Earning More Than Their Direct Manager

#### Table `employees`:
| emp_id | name  | salary | manager_id |
| :----- | :---- | :----- | :--------- |
| 1      | Joe   | 70000  | 3          |
| 2      | Henry | 80000  | 4          |
| 3      | Sam   | 60000  | NULL       |
| 4      | Max   | 90000  | NULL       |

#### Solution (Self Join):
```sql
SELECT 
    emp.name AS employee_name,
    emp.salary AS employee_salary,
    mgr.name AS manager_name,
    mgr.salary AS manager_salary
FROM employees emp
INNER JOIN employees mgr ON emp.manager_id = mgr.emp_id
WHERE emp.salary > mgr.salary;
```

**Output:**
| employee_name | employee_salary | manager_name | manager_salary |
| :------------ | :-------------- | :----------- | :------------- |
| Joe           | 70000           | Sam          | 60000          |

---

### P4: Find the Top Earner in Each Department (Top N per Group)

#### Table `employees`: `(emp_id, name, salary, dept_id)`

#### Solution:
```sql
WITH RankedDepartmentSalaries AS (
    SELECT 
        dept_id,
        name,
        salary,
        DENSE_RANK() OVER (
            PARTITION BY dept_id 
            ORDER BY salary DESC
        ) AS rnk
    FROM employees
)
SELECT dept_id, name, salary
FROM RankedDepartmentSalaries
WHERE rnk = 1; -- For top 3 earners per department, change to: rnk <= 3
```

---

### P5: Find Customers Who Never Placed an Order

#### Tables: `customers (id, name)`, `orders (id, customer_id, amount)`

#### Approach 1: LEFT JOIN with NULL check (Anti-Join - Recommended)
```sql
SELECT C.id, C.name
FROM customers C
LEFT JOIN orders O ON C.id = O.customer_id
WHERE O.customer_id IS NULL;
```

#### Approach 2: NOT EXISTS (Highly efficient)
```sql
SELECT C.id, C.name
FROM customers C
WHERE NOT EXISTS (
    SELECT 1 
    FROM orders O 
    WHERE O.customer_id = C.id
);
```

---

### P6: Consecutive Login Days / Consecutive Records (Gaps & Islands)

**Problem:** Find all user IDs who logged in for at least 3 consecutive days.

#### Table `user_logins`: `(user_id, login_date)`

```sql
WITH DistinctLogins AS (
    -- Remove multiple logins on same date
    SELECT DISTINCT user_id, login_date
    FROM user_logins
),
RankedLogins AS (
    SELECT 
        user_id,
        login_date,
        -- Group identifier: subtracting rank from date remains constant for consecutive dates
        login_date - INTERVAL ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) DAY AS grp_date
    FROM DistinctLogins
)
SELECT user_id
FROM RankedLogins
GROUP BY user_id, grp_date
HAVING COUNT(*) >= 3;
```

---

### P7: Calculate Running Total / Cumulative Sum

#### Table `orders`: `(order_id, order_date, customer_id, order_amount)`

```sql
SELECT 
    order_id,
    order_date,
    customer_id,
    order_amount,
    -- Running total per customer
    SUM(order_amount) OVER (
        PARTITION BY customer_id 
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS customer_running_total,
    -- Overall company running total
    SUM(order_amount) OVER (
        ORDER BY order_date
    ) AS company_running_total
FROM orders;
```

---

### P8: Month-over-Month (MoM) Growth Percentage

#### Table `monthly_revenue`: `(year_month, revenue)`

```sql
WITH RevenueComparison AS (
    SELECT 
        year_month,
        revenue,
        LAG(revenue, 1) OVER (ORDER BY year_month) AS prev_month_revenue
    FROM monthly_revenue
)
SELECT 
    year_month,
    revenue,
    prev_month_revenue,
    ROUND(
        ((revenue - prev_month_revenue) / prev_month_revenue) * 100.0, 
        2
    ) AS mom_growth_percentage
FROM RevenueComparison;
```

---

### P9: Second Most Recent Order per User

#### Table `orders`: `(order_id, user_id, order_date, amount)`

```sql
WITH RankedOrders AS (
    SELECT 
        order_id,
        user_id,
        order_date,
        amount,
        ROW_NUMBER() OVER (
            PARTITION BY user_id 
            ORDER BY order_date DESC
        ) AS order_rank
    FROM orders
)
SELECT user_id, order_id, order_date, amount
FROM RankedOrders
WHERE order_rank = 2;
```

---

### P10: Pivot Data Without PIVOT Function (Conditional Aggregation)

Transform row-based data into column-based summary:

#### Input Table `sales`:
| year | quarter | revenue |
| :--- | :------ | :------ |
| 2023 | Q1      | 1000    |
| 2023 | Q2      | 1500    |
| 2023 | Q3      | 1200    |
| 2023 | Q4      | 1800    |

#### Solution:
```sql
SELECT 
    year,
    SUM(CASE WHEN quarter = 'Q1' THEN revenue ELSE 0 END) AS Q1_Revenue,
    SUM(CASE WHEN quarter = 'Q2' THEN revenue ELSE 0 END) AS Q2_Revenue,
    SUM(CASE WHEN quarter = 'Q3' THEN revenue ELSE 0 END) AS Q3_Revenue,
    SUM(CASE WHEN quarter = 'Q4' THEN revenue ELSE 0 END) AS Q4_Revenue
FROM sales
GROUP BY year;
```

**Output:**
| year | Q1_Revenue | Q2_Revenue | Q3_Revenue | Q4_Revenue |
| :--- | :--------- | :--------- | :--------- | :--------- |
| 2023 | 1000       | 1500       | 1200       | 1800       |

---

### P11: Swap Values with a Single UPDATE Statement

**Problem:** Swap `'m'` to `'f'` and `'f'` to `'m'` in `salary` table with no intermediate variable.

```sql
UPDATE salary
SET sex = CASE 
    WHEN sex = 'm' THEN 'f'
    ELSE 'm'
END;
```

---

### P12: Find Management Hierarchy (Recursive CTE)

Given `employees (emp_id, name, manager_id)`:

```sql
WITH RECURSIVE OrgChart AS (
    -- Anchor: Top boss (no manager)
    SELECT emp_id, name, manager_id, 1 AS org_level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive member: Find subordinates
    SELECT e.emp_id, e.name, e.manager_id, o.org_level + 1
    FROM employees e
    INNER JOIN OrgChart o ON e.manager_id = o.emp_id
)
SELECT * FROM OrgChart
ORDER BY org_level, emp_id;
```

---

### P13: Users Who Bought Both Product A and Product B

#### Table `orders`: `(user_id, product_name)`

```sql
SELECT user_id
FROM orders
WHERE product_name IN ('Product A', 'Product B')
GROUP BY user_id
HAVING COUNT(DISTINCT product_name) = 2;
```

---

# Part 3: Tricky Edge Cases & Output Questions

---

### T1: Why does `WHERE col NOT IN (...)` return empty when NULL is present?

Consider:
```sql
SELECT * FROM users 
WHERE id NOT IN (1, 2, NULL);
```

**Result:** Returns **0 rows**!
**Reason:**
`id NOT IN (1, 2, NULL)` expands to:
`id != 1 AND id != 2 AND id != NULL`
Since `id != NULL` evaluates to `UNKNOWN`, the entire expression becomes `UNKNOWN`. Because `WHERE` requires `TRUE`, no rows qualify.

**Correct Fix:** Always filter out `NULL` from subqueries or use `NOT EXISTS`:
```sql
SELECT * FROM users u
WHERE NOT EXISTS (
    SELECT 1 FROM restricted_ids r WHERE r.id = u.id
);
```

---

### T2: Does `WHERE NULL = NULL` evaluate to TRUE?

```sql
SELECT * FROM employees WHERE NULL = NULL;
```
**Result:** Returns **empty set**.
**Reason:** In SQL, `NULL` represents an unknown value. One unknown cannot be proven equal to another unknown. Always use `IS NULL`.

---

### T3: Behavior of NULL in Math & Aggregations

```sql
SELECT 10 + NULL;          -- Returns: NULL
SELECT CONCAT('A', NULL);  -- MySQL returns: NULL; Postgres/SQL Server returns: 'A'
SELECT AVG(val) FROM (SELECT 10 AS val UNION ALL SELECT NULL UNION ALL SELECT 20) t;
-- Returns: 15.0 (Aggregate functions ignore NULL values; divisor is 2, not 3)
```

---

## 🚀 Quick Preparation Checklist Before Your Interview

- [x] Can you explain **execution order** without hesitating?
- [x] Do you know the exact difference between `RANK()` vs `DENSE_RANK()`?
- [x] Can you write an **Anti-Join** using `LEFT JOIN ... WHERE right_col IS NULL`?
- [x] Do you remember that `WHERE` cannot accept aggregate functions?
- [x] Are you prepared to write a **CTE** with `WITH` instead of deeply nested subqueries?
- [x] Do you know the danger of `NOT IN` with subqueries containing `NULL`?
