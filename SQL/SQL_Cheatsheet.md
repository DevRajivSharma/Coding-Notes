# ⚡ SQL Quick Reference & Cheat Sheet

A concise, high-yield reference guide for daily SQL query writing, debugging, and interview preparation.

> 📚 **Navigation:** [📖 Full SQL Notes](./README.md) • [🎯 Interview Questions (Theory & Practical)](./Interview_Questions.md)

---

## 📌 1. Query Execution Order

```
1. FROM / JOIN     👉 Identify source tables and join conditions
2. WHERE           👉 Filter individual rows
3. GROUP BY        👉 Group rows into summary buckets
4. HAVING          👉 Filter aggregated groups
5. SELECT          👉 Compute expressions and project columns
6. DISTINCT        👉 Remove duplicate output rows
7. ORDER BY        👉 Sort output
8. LIMIT / OFFSET  👉 Paginate result set
```

---

## 🧱 2. DDL Commands (Schema)

```sql
-- Create Table
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL,
    age INT CHECK (age >= 18),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Add Column
ALTER TABLE users ADD COLUMN bio TEXT;

-- Drop Column
ALTER TABLE users DROP COLUMN bio;

-- Modify Column
ALTER TABLE users MODIFY COLUMN username VARCHAR(100);

-- Rename Column
ALTER TABLE users RENAME COLUMN email TO user_email;

-- Clear Table vs Delete Table
TRUNCATE TABLE users; -- Empties rows, keeps structure, resets identity
DROP TABLE users;     -- Deletes structure and data completely
```

---

## ✍️ 3. DML Commands (Data)

```sql
-- Insert Single Row
INSERT INTO users (username, email, age) 
VALUES ('johndoe', 'john@example.com', 25);

-- Insert Multiple Rows
INSERT INTO users (username, email, age) VALUES 
('alice', 'alice@example.com', 28),
('bob', 'bob@example.com', 32);

-- Update Rows
UPDATE users 
SET age = age + 1, email = 'new_email@example.com' 
WHERE id = 1;

-- Delete Rows
DELETE FROM users 
WHERE age < 18;
```

---

## 🔍 4. Filtering & Operators (DQL)

| Operator | Meaning | Example |
| :--- | :--- | :--- |
| `=`, `!=` or `<>` | Equal / Not equal | `WHERE status = 'active'` |
| `<`, `>`, `<=`, `>=` | Relational | `WHERE age >= 21` |
| `BETWEEN a AND b` | Inclusive range | `WHERE price BETWEEN 10 AND 50` |
| `IN (v1, v2, ...)` | Value list match | `WHERE country IN ('US', 'UK', 'CA')` |
| `NOT IN (...)` | Exclude list | `WHERE role NOT IN ('admin', 'mod')` |
| `LIKE 'a%'` | Starts with 'a' | `WHERE name LIKE 'A%'` |
| `LIKE '%a'` | Ends with 'a' | `WHERE name LIKE '%z'` |
| `LIKE '%a%'` | Contains 'a' | `WHERE name LIKE '%tech%'` |
| `LIKE '_a%'` | 2nd character is 'a' | `WHERE code LIKE '_9%'` |
| `IS NULL` | Missing value | `WHERE manager_id IS NULL` |
| `IS NOT NULL` | Present value | `WHERE manager_id IS NOT NULL` |
| `COALESCE(a, b, c)` | First non-null | `SELECT COALESCE(phone, email, 'N/A')` |

---

## 📊 5. Aggregation & Grouping

```sql
-- Standard Aggregations
SELECT 
    dept_id,
    COUNT(*) AS total_count,
    COUNT(DISTINCT role) AS distinct_roles,
    SUM(salary) AS total_payroll,
    AVG(salary) AS avg_salary,
    MIN(salary) AS min_salary,
    MAX(salary) AS max_salary
FROM employees
WHERE is_active = TRUE         -- Row-level filter (BEFORE grouping)
GROUP BY dept_id
HAVING COUNT(*) >= 5           -- Group-level filter (AFTER grouping)
ORDER BY avg_salary DESC;
```

---

## 🔗 6. SQL Joins Cheat Sheet

| Join Type | Description | What It Returns |
| :--- | :--- | :--- |
| **`INNER JOIN`** | Only matching records | Intersection of Table A and Table B |
| **`LEFT JOIN`** | All left + matching right | All Table A rows; NULL if no match in B |
| **`RIGHT JOIN`** | All right + matching left | All Table B rows; NULL if no match in A |
| **`FULL JOIN`** | Everything | All rows from both; NULL on unmatched sides |
| **`CROSS JOIN`**| Cartesian product | (Count A) × (Count B) rows |
| **`SELF JOIN`** | Join table with itself | E.g. Employee with Manager |

```sql
-- Inner Join Example
SELECT E.name, D.dept_name
FROM employees E
INNER JOIN departments D ON E.dept_id = D.dept_id;

-- Left Anti-Join (Find records in A that are NOT in B)
SELECT E.name
FROM employees E
LEFT JOIN departments D ON E.dept_id = D.dept_id
WHERE D.dept_id IS NULL;
```

---

## 🔀 7. Set Operations

- `UNION`: Combines results & **removes duplicates** (slower).
- `UNION ALL`: Combines results & **keeps duplicates** (faster).
- `INTERSECT`: Only rows appearing in **both** queries.
- `EXCEPT` / `MINUS`: Rows in query 1 that are **not** in query 2.

```sql
SELECT email FROM leads
UNION ALL
SELECT email FROM customers;
```

---

## 🪟 8. Window Functions

```sql
FUNCTION() OVER (
    PARTITION BY group_column 
    ORDER BY sort_column
)
```

| Function | Behavior |
| :--- | :--- |
| `ROW_NUMBER()` | Sequential integer `1, 2, 3, 4` (no ties) |
| `RANK()` | Ties get same rank, skips next: `1, 2, 2, 4` |
| `DENSE_RANK()` | Ties get same rank, does NOT skip: `1, 2, 2, 3` |
| `LAG(col, 1)` | Previous row's value |
| `LEAD(col, 1)` | Next row's value |
| `SUM(col) OVER(...)` | Running / cumulative total |

```sql
-- Running Total Example
SELECT 
    emp_id,
    dept_id,
    salary,
    SUM(salary) OVER(PARTITION BY dept_id ORDER BY emp_id) AS running_salary
FROM employees;
```

---

## 💡 9. Common Table Expressions (CTE)

```sql
WITH RankedEmployees AS (
    SELECT 
        emp_id,
        first_name,
        salary,
        dept_id,
        DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM employees
)
SELECT emp_id, first_name, salary, dept_id
FROM RankedEmployees
WHERE rnk = 1; -- Top earner per department
```

---

## 🛠️ 10. Conditional Logic (CASE)

```sql
SELECT 
    product_name,
    stock_quantity,
    CASE 
        WHEN stock_quantity = 0 THEN 'Out of Stock'
        WHEN stock_quantity < 10 THEN 'Low Stock'
        ELSE 'In Stock'
    END AS inventory_status
FROM products;
```

---

## ⚡ 11. Useful Query Templates

### Find Nth Highest Salary
```sql
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET (N - 1); -- e.g., OFFSET 1 for 2nd highest
```

### Find and Count Duplicate Emails
```sql
SELECT email, COUNT(*) 
FROM users 
GROUP BY email 
HAVING COUNT(*) > 1;
```

### Delete Duplicate Rows (Keep Lowest ID)
```sql
WITH CTE AS (
    SELECT id, ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn
    FROM users
)
DELETE FROM users WHERE id IN (SELECT id FROM CTE WHERE rn > 1);
```
