# Easy SQL Problems – HackerRank Style (MySQL)

> All queries are written for **MySQL 8.0+**.
> Window functions (`RANK`, `ROW_NUMBER`, `DENSE_RANK`) require MySQL 8.0+.

---

## Schema Reference (Used Across Problems)

```sql
-- EMPLOYEES table
CREATE TABLE EMPLOYEES (
    ID          INT AUTO_INCREMENT PRIMARY KEY,
    FIRST_NAME  VARCHAR(50)     NOT NULL,
    LAST_NAME   VARCHAR(50)     NOT NULL,
    NAME        VARCHAR(100)    GENERATED ALWAYS AS (CONCAT(FIRST_NAME, ' ', LAST_NAME)) STORED,
    DEPARTMENT  VARCHAR(100),
    SALARY      DECIMAL(10,2)   DEFAULT 0.00,
    MANAGER_ID  INT             DEFAULT NULL,
    HIRE_DATE   DATE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- DEPARTMENTS table
CREATE TABLE DEPARTMENTS (
    ID    INT AUTO_INCREMENT PRIMARY KEY,
    NAME  VARCHAR(100) NOT NULL,
    CITY  VARCHAR(100)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- ORDERS table
CREATE TABLE ORDERS (
    ID          INT AUTO_INCREMENT PRIMARY KEY,
    CUSTOMER_ID INT,
    AMOUNT      DECIMAL(10,2),
    ORDER_DATE  DATE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- CUSTOMERS table
CREATE TABLE CUSTOMERS (
    ID      INT AUTO_INCREMENT PRIMARY KEY,
    NAME    VARCHAR(100) NOT NULL,
    CITY    VARCHAR(100),
    COUNTRY VARCHAR(100)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- STUDENTS table
CREATE TABLE STUDENTS (
    ID      INT AUTO_INCREMENT PRIMARY KEY,
    NAME    VARCHAR(100) NOT NULL,
    MARKS   INT,
    GRADE   VARCHAR(2)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

---

## 1. Select All Records

**Problem:** Retrieve all columns from the EMPLOYEES table.

```sql
SELECT * FROM EMPLOYEES;
```

---

## 2. Select Specific Columns

**Problem:** Retrieve only the NAME and SALARY of all employees.

```sql
SELECT NAME, SALARY FROM EMPLOYEES;
```

---

## 3. Filter with WHERE

**Problem:** Find all employees with a salary greater than 50000.

**Example Output:**
```
NAME       SALARY
--------   -------
Alice      75000
Bob        60000
```

```sql
SELECT NAME, SALARY
FROM EMPLOYEES
WHERE SALARY > 50000;
```

---

## 4. ORDER BY – Sort Results

**Problem:** List all employees ordered by salary in descending order.

```sql
SELECT NAME, SALARY
FROM EMPLOYEES
ORDER BY SALARY DESC;
```

**Multiple column sort:**
```sql
SELECT NAME, DEPARTMENT, SALARY
FROM EMPLOYEES
ORDER BY DEPARTMENT ASC, SALARY DESC;
```

---

## 5. LIMIT – Top N Records

**Problem:** Find the top 3 highest-paid employees.

```sql
SELECT NAME, SALARY
FROM EMPLOYEES
ORDER BY SALARY DESC
LIMIT 3;
```

**With OFFSET (pagination):**
```sql
-- Skip first 3, get next 3
SELECT NAME, SALARY
FROM EMPLOYEES
ORDER BY SALARY DESC
LIMIT 3 OFFSET 3;
```

---

## 6. COUNT – Total Records

**Problem:** Count the total number of employees.

```sql
SELECT COUNT(*) AS TOTAL_EMPLOYEES
FROM EMPLOYEES;
```

---

## 7. SUM – Total Salary

**Problem:** Find the total salary paid to all employees.

```sql
SELECT SUM(SALARY) AS TOTAL_SALARY
FROM EMPLOYEES;
```

---

## 8. AVG – Average Salary

**Problem:** Find the average salary, rounded to 2 decimal places.

```sql
SELECT ROUND(AVG(SALARY), 2) AS AVG_SALARY
FROM EMPLOYEES;
```

---

## 9. MAX and MIN

**Problem:** Find the highest and lowest salary in the company.

```sql
SELECT MAX(SALARY) AS HIGHEST, MIN(SALARY) AS LOWEST
FROM EMPLOYEES;
```

---

## 10. DISTINCT – Remove Duplicates

**Problem:** List all unique departments in the EMPLOYEES table.

```sql
SELECT DISTINCT DEPARTMENT
FROM EMPLOYEES;
```

---

## 11. WHERE with Multiple Conditions (AND / OR)

**Problem:** Find employees in the 'IT' department with salary > 60000.

```sql
SELECT NAME, DEPARTMENT, SALARY
FROM EMPLOYEES
WHERE DEPARTMENT = 'IT' AND SALARY > 60000;
```

**Problem:** Find employees in 'IT' OR 'HR' departments.

```sql
SELECT NAME, DEPARTMENT
FROM EMPLOYEES
WHERE DEPARTMENT = 'IT' OR DEPARTMENT = 'HR';
```

---

## 12. IN Operator

**Problem:** Find employees who work in 'IT', 'HR', or 'Finance'.

```sql
SELECT NAME, DEPARTMENT
FROM EMPLOYEES
WHERE DEPARTMENT IN ('IT', 'HR', 'Finance');
```

---

## 13. BETWEEN Operator

**Problem:** Find employees with salary between 40000 and 80000.

```sql
SELECT NAME, SALARY
FROM EMPLOYEES
WHERE SALARY BETWEEN 40000 AND 80000;
```

**Date range using BETWEEN:**
```sql
SELECT NAME, HIRE_DATE
FROM EMPLOYEES
WHERE HIRE_DATE BETWEEN '2022-01-01' AND '2022-12-31';
```

---

## 14. LIKE – Pattern Matching

**Problem:** Find all employees whose name starts with 'A'.

```sql
SELECT NAME FROM EMPLOYEES
WHERE NAME LIKE 'A%';
```

**Contains 'an' (case-insensitive in MySQL by default):**
```sql
SELECT NAME FROM EMPLOYEES
WHERE NAME LIKE '%an%';
```

**Ends with 'n':**
```sql
SELECT NAME FROM EMPLOYEES
WHERE NAME LIKE '%n';
```

**Exactly 5 characters long (`_` matches one char):**
```sql
SELECT NAME FROM EMPLOYEES
WHERE NAME LIKE '_____';
```

---

## 15. REGEXP – Advanced Pattern Matching (MySQL Specific)

**Problem:** Find employees whose name starts with a vowel.

```sql
SELECT NAME FROM EMPLOYEES
WHERE NAME REGEXP '^[AEIOUaeiou]';
```

**Name contains only alphabets:**
```sql
SELECT NAME FROM EMPLOYEES
WHERE NAME REGEXP '^[A-Za-z]+$';
```

---

## 16. IS NULL / IS NOT NULL

**Problem:** Find employees who do not have a manager (MANAGER_ID is NULL).

```sql
SELECT NAME FROM EMPLOYEES
WHERE MANAGER_ID IS NULL;
```

**Use IFNULL to replace NULL with a default value (MySQL specific):**
```sql
SELECT NAME, IFNULL(MANAGER_ID, 'No Manager') AS MANAGER
FROM EMPLOYEES;
```

**COALESCE – works across all SQL dialects:**
```sql
SELECT NAME, COALESCE(MANAGER_ID, 0) AS MANAGER_ID
FROM EMPLOYEES;
```

---

## 17. GROUP BY – Aggregate per Group

**Problem:** Find the number of employees in each department.

**Example Output:**
```
DEPARTMENT    COUNT
----------    -----
IT            5
HR            3
Finance       4
```

```sql
SELECT DEPARTMENT, COUNT(*) AS EMPLOYEE_COUNT
FROM EMPLOYEES
GROUP BY DEPARTMENT
ORDER BY EMPLOYEE_COUNT DESC;
```

---

## 18. GROUP BY with AVG and ROUND

**Problem:** Find the average salary per department, rounded to 2 decimals.

```sql
SELECT DEPARTMENT, ROUND(AVG(SALARY), 2) AS AVG_SALARY
FROM EMPLOYEES
GROUP BY DEPARTMENT
ORDER BY AVG_SALARY DESC;
```

---

## 19. HAVING – Filter After GROUP BY

**Problem:** Find departments with more than 3 employees.

```sql
SELECT DEPARTMENT, COUNT(*) AS EMPLOYEE_COUNT
FROM EMPLOYEES
GROUP BY DEPARTMENT
HAVING COUNT(*) > 3;
```

**Combined WHERE + HAVING:**
```sql
-- Active employees only, departments with avg salary > 50000
SELECT DEPARTMENT, ROUND(AVG(SALARY), 2) AS AVG_SALARY
FROM EMPLOYEES
WHERE SALARY > 0
GROUP BY DEPARTMENT
HAVING AVG(SALARY) > 50000;
```

> **Note:** `WHERE` filters rows before grouping. `HAVING` filters after grouping.

---

## 20. INNER JOIN

**Problem:** Get employee names along with their department city.

```sql
SELECT E.NAME, D.CITY
FROM EMPLOYEES E
INNER JOIN DEPARTMENTS D ON E.DEPARTMENT = D.NAME;
```

---

## 21. LEFT JOIN

**Problem:** Get all employees and their department city (include employees with no matching department).

```sql
SELECT E.NAME, IFNULL(D.CITY, 'Unknown') AS CITY
FROM EMPLOYEES E
LEFT JOIN DEPARTMENTS D ON E.DEPARTMENT = D.NAME;
```

---

## 22. RIGHT JOIN

**Problem:** Get all departments and their employees (include departments with no employees).

```sql
SELECT IFNULL(E.NAME, 'No Employee') AS EMPLOYEE, D.NAME AS DEPARTMENT
FROM EMPLOYEES E
RIGHT JOIN DEPARTMENTS D ON E.DEPARTMENT = D.NAME;
```

---

## 23. FULL OUTER JOIN (MySQL Workaround)

**Problem:** Get all employees and all departments, matched where possible.

> MySQL does NOT support `FULL OUTER JOIN` directly. Simulate it with `LEFT JOIN UNION RIGHT JOIN`.

```sql
SELECT E.NAME, D.NAME AS DEPARTMENT
FROM EMPLOYEES E
LEFT JOIN DEPARTMENTS D ON E.DEPARTMENT = D.NAME

UNION

SELECT E.NAME, D.NAME AS DEPARTMENT
FROM EMPLOYEES E
RIGHT JOIN DEPARTMENTS D ON E.DEPARTMENT = D.NAME;
```

---

## 24. Aliases (AS)

**Problem:** Select employee name and salary with readable column aliases.

```sql
SELECT
    NAME        AS `Employee Name`,
    SALARY      AS `Monthly Salary`,
    DEPARTMENT  AS `Dept`
FROM EMPLOYEES;
```

> In MySQL, use backticks `` ` `` for aliases with spaces, or double quotes if `ANSI_QUOTES` mode is enabled.

---

## 25. Subquery – WHERE Clause

**Problem:** Find employees who earn more than the average salary.

```sql
SELECT NAME, SALARY
FROM EMPLOYEES
WHERE SALARY > (SELECT AVG(SALARY) FROM EMPLOYEES)
ORDER BY SALARY DESC;
```

---

## 26. Subquery – Find Second Highest Salary

**Problem:** Find the second highest salary in the EMPLOYEES table.

```sql
-- Using nested MAX
SELECT MAX(SALARY) AS SECOND_HIGHEST
FROM EMPLOYEES
WHERE SALARY < (SELECT MAX(SALARY) FROM EMPLOYEES);
```

**Using LIMIT + OFFSET (MySQL preferred):**
```sql
SELECT DISTINCT SALARY
FROM EMPLOYEES
ORDER BY SALARY DESC
LIMIT 1 OFFSET 1;
```

---

## 27. String Functions (MySQL)

**Problem:** Display employee names in uppercase and lowercase.

```sql
SELECT UPPER(NAME) AS UPPER_NAME, LOWER(NAME) AS LOWER_NAME
FROM EMPLOYEES;
```

**Length of name:**
```sql
SELECT NAME, CHAR_LENGTH(NAME) AS NAME_LENGTH
FROM EMPLOYEES;
```
> Use `CHAR_LENGTH()` in MySQL for character count (handles multibyte), `LENGTH()` returns byte count.

**Concatenate first and last name:**
```sql
SELECT CONCAT(FIRST_NAME, ' ', LAST_NAME) AS FULL_NAME
FROM EMPLOYEES;
```

**CONCAT_WS (with separator):**
```sql
SELECT CONCAT_WS(' ', FIRST_NAME, LAST_NAME) AS FULL_NAME
FROM EMPLOYEES;
```

**Extract substring:**
```sql
SELECT SUBSTRING(NAME, 1, 3) AS SHORT_NAME FROM EMPLOYEES;
-- OR
SELECT LEFT(NAME, 3) AS FIRST3 FROM EMPLOYEES;
SELECT RIGHT(NAME, 3) AS LAST3 FROM EMPLOYEES;
```

**Trim whitespace:**
```sql
SELECT TRIM(NAME) FROM EMPLOYEES;
SELECT LTRIM(NAME) FROM EMPLOYEES;  -- Left trim
SELECT RTRIM(NAME) FROM EMPLOYEES;  -- Right trim
```

**Replace text:**
```sql
SELECT REPLACE(DEPARTMENT, 'IT', 'Tech') FROM EMPLOYEES;
```

---

## 28. Date Functions (MySQL)

**Problem:** Find employees hired in the year 2022.

```sql
SELECT NAME, HIRE_DATE
FROM EMPLOYEES
WHERE YEAR(HIRE_DATE) = 2022;
```

**Hired in a specific month:**
```sql
SELECT NAME, HIRE_DATE
FROM EMPLOYEES
WHERE MONTH(HIRE_DATE) = 6;  -- June
```

**Hired in last 30 days:**
```sql
SELECT NAME, HIRE_DATE
FROM EMPLOYEES
WHERE HIRE_DATE >= DATE_SUB(CURDATE(), INTERVAL 30 DAY);
```

**Format dates for display:**
```sql
SELECT NAME, DATE_FORMAT(HIRE_DATE, '%d-%m-%Y') AS FORMATTED_DATE
FROM EMPLOYEES;
-- Output: 15-01-2024
```

**Parse a string into a date:**
```sql
SELECT STR_TO_DATE('15-01-2024', '%d-%m-%Y') AS PARSED_DATE;
```

**Difference between two dates:**
```sql
SELECT NAME, DATEDIFF(CURDATE(), HIRE_DATE) AS DAYS_EMPLOYED
FROM EMPLOYEES;
```

**Add/subtract intervals:**
```sql
SELECT DATE_ADD('2024-01-01', INTERVAL 1 MONTH);   -- 2024-02-01
SELECT DATE_SUB('2024-01-01', INTERVAL 7 DAY);     -- 2023-12-25
```

**Current date/time:**
```sql
SELECT NOW();       -- datetime: 2024-03-09 10:30:00
SELECT CURDATE();   -- date only: 2024-03-09
SELECT CURTIME();   -- time only: 10:30:00
```

---

## 29. CASE Statement (Conditional Column)

**Problem:** Categorize employees by salary range.

**Example Output:**
```
NAME    SALARY   CATEGORY
----    ------   --------
Alice   90000    High
Bob     55000    Medium
Carol   30000    Low
```

```sql
SELECT NAME, SALARY,
    CASE
        WHEN SALARY >= 80000 THEN 'High'
        WHEN SALARY >= 50000 THEN 'Medium'
        ELSE 'Low'
    END AS CATEGORY
FROM EMPLOYEES;
```

**Simple CASE (match exact value):**
```sql
SELECT NAME,
    CASE DEPARTMENT
        WHEN 'IT'      THEN 'Technology'
        WHEN 'HR'      THEN 'Human Resources'
        WHEN 'Finance' THEN 'Accounts'
        ELSE 'Other'
    END AS DEPT_FULL_NAME
FROM EMPLOYEES;
```

---

## 30. COUNT with DISTINCT

**Problem:** Count the number of unique departments.

```sql
SELECT COUNT(DISTINCT DEPARTMENT) AS UNIQUE_DEPARTMENTS
FROM EMPLOYEES;
```

---

## 31. Find Duplicate Records

**Problem:** Find names that appear more than once in the EMPLOYEES table.

```sql
SELECT NAME, COUNT(*) AS OCCURRENCES
FROM EMPLOYEES
GROUP BY NAME
HAVING COUNT(*) > 1;
```

---

## 32. NOT IN

**Problem:** Find customers who have NOT placed any orders.

```sql
SELECT NAME
FROM CUSTOMERS
WHERE ID NOT IN (
    SELECT CUSTOMER_ID FROM ORDERS
    WHERE CUSTOMER_ID IS NOT NULL  -- Avoids NULL trap in NOT IN
);
```

> **MySQL Tip:** Always filter NULLs in the subquery with `WHERE col IS NOT NULL` when using `NOT IN`, or use `NOT EXISTS` instead.

---

## 33. EXISTS / NOT EXISTS

**Problem:** Find customers who have placed at least one order.

```sql
SELECT C.NAME
FROM CUSTOMERS C
WHERE EXISTS (
    SELECT 1 FROM ORDERS O WHERE O.CUSTOMER_ID = C.ID
);
```

**Customers with NO orders:**
```sql
SELECT C.NAME
FROM CUSTOMERS C
WHERE NOT EXISTS (
    SELECT 1 FROM ORDERS O WHERE O.CUSTOMER_ID = C.ID
);
```

---

## 34. GROUP_CONCAT (MySQL Specific)

**Problem:** List all employees in each department as a comma-separated string.

**Example Output:**
```
DEPARTMENT   EMPLOYEES
----------   ---------
IT           Alice, Bob, Carol
HR           Dave, Eve
```

```sql
SELECT DEPARTMENT,
       GROUP_CONCAT(NAME ORDER BY NAME SEPARATOR ', ') AS EMPLOYEES
FROM EMPLOYEES
GROUP BY DEPARTMENT;
```

---

## 35. UPDATE Statement

**Problem:** Give all IT department employees a 10% salary raise.

```sql
UPDATE EMPLOYEES
SET SALARY = SALARY * 1.10
WHERE DEPARTMENT = 'IT';
```

**Update with CASE:**
```sql
UPDATE EMPLOYEES
SET SALARY = SALARY * CASE
    WHEN DEPARTMENT = 'IT'      THEN 1.15
    WHEN DEPARTMENT = 'HR'      THEN 1.10
    WHEN DEPARTMENT = 'Finance' THEN 1.12
    ELSE 1.05
END;
```

---

## 36. DELETE Statement

**Problem:** Delete all employees with salary below 30000.

```sql
DELETE FROM EMPLOYEES
WHERE SALARY < 30000;
```

**Delete with JOIN (MySQL specific):**
```sql
-- Delete employees who belong to a closed department
DELETE E
FROM EMPLOYEES E
INNER JOIN DEPARTMENTS D ON E.DEPARTMENT = D.NAME
WHERE D.CITY = 'Closed';
```

---

## 37. INSERT Statement

**Problem:** Insert a new employee record.

```sql
INSERT INTO EMPLOYEES (FIRST_NAME, LAST_NAME, DEPARTMENT, SALARY, HIRE_DATE)
VALUES ('John', 'Doe', 'IT', 75000.00, '2024-01-15');
```

**Insert multiple rows:**
```sql
INSERT INTO EMPLOYEES (FIRST_NAME, LAST_NAME, DEPARTMENT, SALARY, HIRE_DATE)
VALUES
    ('Jane', 'Smith',   'HR',      60000.00, '2024-02-01'),
    ('Mark', 'Johnson', 'Finance', 70000.00, '2024-03-10'),
    ('Sara', 'Lee',     'IT',      80000.00, '2024-04-05');
```

**INSERT ... ON DUPLICATE KEY UPDATE (Upsert):**
```sql
INSERT INTO EMPLOYEES (ID, FIRST_NAME, LAST_NAME, SALARY)
VALUES (1, 'John', 'Doe', 90000.00)
ON DUPLICATE KEY UPDATE SALARY = 90000.00;
```

---

## 38. RANK, DENSE_RANK, ROW_NUMBER (MySQL 8.0+ Window Functions)

**Problem:** Rank employees by salary within each department.

```sql
SELECT NAME, DEPARTMENT, SALARY,
    RANK()       OVER (PARTITION BY DEPARTMENT ORDER BY SALARY DESC) AS `RANK`,
    DENSE_RANK() OVER (PARTITION BY DEPARTMENT ORDER BY SALARY DESC) AS `DENSE_RANK`,
    ROW_NUMBER() OVER (PARTITION BY DEPARTMENT ORDER BY SALARY DESC) AS `ROW_NUM`
FROM EMPLOYEES;
```

> **Difference:**
> - `RANK()` — gaps in ranking after ties (1, 2, 2, 4)
> - `DENSE_RANK()` — no gaps (1, 2, 2, 3)
> - `ROW_NUMBER()` — always unique (1, 2, 3, 4)

---

## 39. Nth Highest Salary

**Problem:** Find the Nth highest salary (e.g., 3rd highest).

**Using LIMIT + OFFSET (MySQL preferred):**
```sql
SELECT DISTINCT SALARY
FROM EMPLOYEES
ORDER BY SALARY DESC
LIMIT 1 OFFSET 2;  -- 0-indexed: OFFSET 2 = 3rd highest
```

**Using subquery (works for any N):**
```sql
-- Replace N=3
SELECT DISTINCT SALARY
FROM EMPLOYEES e1
WHERE 2 = (
    SELECT COUNT(DISTINCT SALARY)
    FROM EMPLOYEES e2
    WHERE e2.SALARY > e1.SALARY
);
```

**Using window function (MySQL 8.0+):**
```sql
SELECT SALARY FROM (
    SELECT SALARY,
           DENSE_RANK() OVER (ORDER BY SALARY DESC) AS rnk
    FROM EMPLOYEES
) ranked
WHERE rnk = 3;  -- Change 3 to N
```

---

## 40. Students with Marks Above Average

**Problem:** Find students who scored above the class average.

```sql
SELECT NAME, MARKS
FROM STUDENTS
WHERE MARKS > (SELECT AVG(MARKS) FROM STUDENTS)
ORDER BY MARKS DESC;
```

---

## 41. Top Earner per Department

**Problem:** Find the highest-paid employee in each department.

**Using correlated subquery:**
```sql
SELECT DEPARTMENT, NAME, SALARY
FROM EMPLOYEES e1
WHERE SALARY = (
    SELECT MAX(SALARY)
    FROM EMPLOYEES e2
    WHERE e2.DEPARTMENT = e1.DEPARTMENT
);
```

**Using window function (MySQL 8.0+):**
```sql
SELECT DEPARTMENT, NAME, SALARY FROM (
    SELECT DEPARTMENT, NAME, SALARY,
           RANK() OVER (PARTITION BY DEPARTMENT ORDER BY SALARY DESC) AS rnk
    FROM EMPLOYEES
) ranked
WHERE rnk = 1;
```

---

## 42. Self Join – Find Employees and Their Managers

**Problem:** List each employee along with their manager's name.

```sql
SELECT
    E.NAME                              AS EMPLOYEE,
    IFNULL(M.NAME, 'No Manager')        AS MANAGER
FROM EMPLOYEES E
LEFT JOIN EMPLOYEES M ON E.MANAGER_ID = M.ID;
```

---

## 43. UNION – Combine Results

**Problem:** Get all cities from both CUSTOMERS and DEPARTMENTS (no duplicates).

```sql
SELECT CITY FROM CUSTOMERS
UNION
SELECT CITY FROM DEPARTMENTS;
```

**With duplicates (UNION ALL — faster, no dedup):**
```sql
SELECT CITY FROM CUSTOMERS
UNION ALL
SELECT CITY FROM DEPARTMENTS;
```

---

## 44. TRUNCATE vs DELETE (MySQL)

| | `DELETE` | `TRUNCATE` |
|-|----------|------------|
| WHERE clause | Yes | No |
| Rollback | Yes (transactional) | No (DDL) |
| Resets AUTO_INCREMENT | No | Yes |
| Speed | Slower | Faster |

```sql
-- Remove all rows but keep structure; resets AUTO_INCREMENT
TRUNCATE TABLE EMPLOYEES;

-- Remove all rows (transactional, can rollback)
DELETE FROM EMPLOYEES;
```

---

## 45. Useful MySQL Admin Queries

```sql
SHOW DATABASES;                 -- List all databases
SHOW TABLES;                    -- List all tables in current DB
DESCRIBE EMPLOYEES;             -- Show table structure
SHOW CREATE TABLE EMPLOYEES;    -- Show full CREATE statement
EXPLAIN SELECT * FROM EMPLOYEES WHERE SALARY > 50000;  -- Query execution plan
```

---

## Quick Reference Cheat Sheet

### Clause Execution Order
```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```

### Aggregate Functions
| Function | Description |
|----------|-------------|
| `COUNT(*)` | Total rows including NULLs |
| `COUNT(col)` | Non-null values only |
| `SUM(col)` | Total sum |
| `ROUND(AVG(col), 2)` | Average (rounded) |
| `MAX(col)` | Maximum value |
| `MIN(col)` | Minimum value |
| `GROUP_CONCAT(col)` | Concatenate values per group |

### JOIN Types
| Type | Description |
|------|-------------|
| `INNER JOIN` | Only matching rows in both tables |
| `LEFT JOIN` | All rows from left + matches from right |
| `RIGHT JOIN` | All rows from right + matches from left |
| `FULL OUTER JOIN` | Not supported — use `LEFT JOIN UNION RIGHT JOIN` |
| `SELF JOIN` | Join a table with itself |
| `CROSS JOIN` | Cartesian product of both tables |

### MySQL String Functions
| Function | Description |
|----------|-------------|
| `UPPER(col)` | Uppercase |
| `LOWER(col)` | Lowercase |
| `CHAR_LENGTH(col)` | Character count (use over LENGTH for UTF-8) |
| `CONCAT(a, b)` / `CONCAT_WS(sep, a, b)` | Concatenate |
| `SUBSTRING(col, start, len)` | Extract substring |
| `LEFT(col, n)` / `RIGHT(col, n)` | First/last N chars |
| `TRIM(col)` / `LTRIM` / `RTRIM` | Remove whitespace |
| `REPLACE(col, old, new)` | Replace substring |
| `INSTR(col, substr)` | Position of substring |
| `LPAD(col, len, pad)` / `RPAD` | Pad string |
| `REGEXP` | Regular expression match |

### MySQL Date Functions
| Function | Description |
|----------|-------------|
| `NOW()` | Current datetime |
| `CURDATE()` | Current date |
| `CURTIME()` | Current time |
| `YEAR(date)` / `MONTH(date)` / `DAY(date)` | Extract parts |
| `DATE_FORMAT(date, fmt)` | Format date (`'%d-%m-%Y'`) |
| `STR_TO_DATE(str, fmt)` | Parse string to date |
| `DATE_ADD(date, INTERVAL n unit)` | Add interval |
| `DATE_SUB(date, INTERVAL n unit)` | Subtract interval |
| `DATEDIFF(d1, d2)` | Days between two dates |
| `TIMESTAMPDIFF(unit, d1, d2)` | Difference in given unit |

### NULL Handling
| Function | Description |
|----------|-------------|
| `IFNULL(col, default)` | Replace NULL with default (MySQL specific) |
| `COALESCE(a, b, c)` | Return first non-NULL value |
| `NULLIF(a, b)` | Return NULL if a = b, else return a |
| `IS NULL` / `IS NOT NULL` | NULL check in WHERE |

### WHERE vs HAVING
| | WHERE | HAVING |
|-|-------|--------|
| Used with | Individual rows | Grouped results |
| Executed | Before GROUP BY | After GROUP BY |
| Can use aggregates? | No | Yes |
| Can use aliases? | No | No (use in ORDER BY only) |

### Common Patterns
```sql
-- Count per group, filter groups
SELECT col, COUNT(*) AS cnt
FROM table
GROUP BY col
HAVING cnt > 5;

-- Subquery comparison
SELECT * FROM table
WHERE col > (SELECT AVG(col) FROM table);

-- NULL-safe NOT IN
SELECT * FROM table
WHERE id NOT IN (
    SELECT foreign_id FROM other_table WHERE foreign_id IS NOT NULL
);

-- Upsert
INSERT INTO table (id, col) VALUES (1, 'val')
ON DUPLICATE KEY UPDATE col = 'val';

-- Pagination
SELECT * FROM table ORDER BY id LIMIT 10 OFFSET 20;

-- Conditional aggregation
SELECT
    SUM(CASE WHEN DEPARTMENT = 'IT' THEN SALARY ELSE 0 END) AS IT_TOTAL,
    SUM(CASE WHEN DEPARTMENT = 'HR' THEN SALARY ELSE 0 END) AS HR_TOTAL
FROM EMPLOYEES;
```
