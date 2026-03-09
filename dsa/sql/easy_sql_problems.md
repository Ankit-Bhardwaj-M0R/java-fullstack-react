# Easy SQL Problems – HackerRank Style

---

## Schema Reference (Used Across Problems)

```sql
-- EMPLOYEES table
CREATE TABLE EMPLOYEES (
    ID          INT PRIMARY KEY,
    NAME        VARCHAR(100),
    DEPARTMENT  VARCHAR(100),
    SALARY      DECIMAL(10,2),
    MANAGER_ID  INT,
    HIRE_DATE   DATE
);

-- DEPARTMENTS table
CREATE TABLE DEPARTMENTS (
    ID    INT PRIMARY KEY,
    NAME  VARCHAR(100),
    CITY  VARCHAR(100)
);

-- ORDERS table
CREATE TABLE ORDERS (
    ID          INT PRIMARY KEY,
    CUSTOMER_ID INT,
    AMOUNT      DECIMAL(10,2),
    ORDER_DATE  DATE
);

-- CUSTOMERS table
CREATE TABLE CUSTOMERS (
    ID      INT PRIMARY KEY,
    NAME    VARCHAR(100),
    CITY    VARCHAR(100),
    COUNTRY VARCHAR(100)
);

-- STUDENTS table
CREATE TABLE STUDENTS (
    ID      INT PRIMARY KEY,
    NAME    VARCHAR(100),
    MARKS   INT,
    GRADE   VARCHAR(2)
);
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

---

## 5. LIMIT – Top N Records

**Problem:** Find the top 3 highest-paid employees.

```sql
SELECT NAME, SALARY
FROM EMPLOYEES
ORDER BY SALARY DESC
LIMIT 3;
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

**Problem:** Find the average salary of all employees.

```sql
SELECT AVG(SALARY) AS AVG_SALARY
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

---

## 14. LIKE – Pattern Matching

**Problem:** Find all employees whose name starts with 'A'.

```sql
SELECT NAME FROM EMPLOYEES
WHERE NAME LIKE 'A%';
```

**Problem:** Find employees whose name contains 'an'.

```sql
SELECT NAME FROM EMPLOYEES
WHERE NAME LIKE '%an%';
```

**Problem:** Find employees whose name ends with 'n'.

```sql
SELECT NAME FROM EMPLOYEES
WHERE NAME LIKE '%n';
```

---

## 15. IS NULL / IS NOT NULL

**Problem:** Find employees who do not have a manager (MANAGER_ID is NULL).

```sql
SELECT NAME FROM EMPLOYEES
WHERE MANAGER_ID IS NULL;
```

**Problem:** Find employees who do have a manager.

```sql
SELECT NAME FROM EMPLOYEES
WHERE MANAGER_ID IS NOT NULL;
```

---

## 16. GROUP BY – Aggregate per Group

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
GROUP BY DEPARTMENT;
```

---

## 17. GROUP BY with AVG

**Problem:** Find the average salary per department.

```sql
SELECT DEPARTMENT, AVG(SALARY) AS AVG_SALARY
FROM EMPLOYEES
GROUP BY DEPARTMENT;
```

---

## 18. HAVING – Filter After GROUP BY

**Problem:** Find departments with more than 3 employees.

```sql
SELECT DEPARTMENT, COUNT(*) AS EMPLOYEE_COUNT
FROM EMPLOYEES
GROUP BY DEPARTMENT
HAVING COUNT(*) > 3;
```

**Note:** Use `WHERE` to filter rows before grouping. Use `HAVING` to filter after grouping.

---

## 19. INNER JOIN

**Problem:** Get employee names along with their department city.

```sql
SELECT E.NAME, D.CITY
FROM EMPLOYEES E
INNER JOIN DEPARTMENTS D ON E.DEPARTMENT = D.NAME;
```

---

## 20. LEFT JOIN

**Problem:** Get all employees and their department city (include employees with no matching department).

```sql
SELECT E.NAME, D.CITY
FROM EMPLOYEES E
LEFT JOIN DEPARTMENTS D ON E.DEPARTMENT = D.NAME;
```

---

## 21. RIGHT JOIN

**Problem:** Get all departments and their employees (include departments with no employees).

```sql
SELECT E.NAME, D.NAME AS DEPARTMENT
FROM EMPLOYEES E
RIGHT JOIN DEPARTMENTS D ON E.DEPARTMENT = D.NAME;
```

---

## 22. Aliases (AS)

**Problem:** Select employee name and salary with readable column aliases.

```sql
SELECT NAME AS "Employee Name", SALARY AS "Monthly Salary"
FROM EMPLOYEES;
```

---

## 23. Subquery – WHERE Clause

**Problem:** Find employees who earn more than the average salary.

```sql
SELECT NAME, SALARY
FROM EMPLOYEES
WHERE SALARY > (SELECT AVG(SALARY) FROM EMPLOYEES);
```

---

## 24. Subquery – Find Second Highest Salary

**Problem:** Find the second highest salary in the EMPLOYEES table.

```sql
SELECT MAX(SALARY) AS SECOND_HIGHEST
FROM EMPLOYEES
WHERE SALARY < (SELECT MAX(SALARY) FROM EMPLOYEES);
```

**Alternative using LIMIT:**
```sql
SELECT SALARY
FROM EMPLOYEES
ORDER BY SALARY DESC
LIMIT 1 OFFSET 1;
```

---

## 25. String Functions

**Problem:** Display employee names in uppercase and lowercase.

```sql
SELECT UPPER(NAME) AS UPPER_NAME, LOWER(NAME) AS LOWER_NAME
FROM EMPLOYEES;
```

**Problem:** Get the length of each employee's name.

```sql
SELECT NAME, LENGTH(NAME) AS NAME_LENGTH
FROM EMPLOYEES;
```

**Problem:** Concatenate first and last name columns.

```sql
SELECT CONCAT(FIRST_NAME, ' ', LAST_NAME) AS FULL_NAME
FROM EMPLOYEES;
```

---

## 26. Date Functions

**Problem:** Find employees hired in the year 2022.

```sql
SELECT NAME, HIRE_DATE
FROM EMPLOYEES
WHERE YEAR(HIRE_DATE) = 2022;
```

**Problem:** Find employees hired in the last 30 days.

```sql
SELECT NAME, HIRE_DATE
FROM EMPLOYEES
WHERE HIRE_DATE >= DATE_SUB(CURDATE(), INTERVAL 30 DAY);
```

---

## 27. CASE Statement (Conditional Column)

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

---

## 28. COUNT with DISTINCT

**Problem:** Count the number of unique departments.

```sql
SELECT COUNT(DISTINCT DEPARTMENT) AS UNIQUE_DEPARTMENTS
FROM EMPLOYEES;
```

---

## 29. Find Duplicate Records

**Problem:** Find names that appear more than once in the EMPLOYEES table.

```sql
SELECT NAME, COUNT(*) AS COUNT
FROM EMPLOYEES
GROUP BY NAME
HAVING COUNT(*) > 1;
```

---

## 30. NOT IN

**Problem:** Find customers who have NOT placed any orders.

```sql
SELECT NAME
FROM CUSTOMERS
WHERE ID NOT IN (SELECT CUSTOMER_ID FROM ORDERS);
```

---

## 31. EXISTS

**Problem:** Find customers who have placed at least one order.

```sql
SELECT NAME
FROM CUSTOMERS C
WHERE EXISTS (
    SELECT 1 FROM ORDERS O WHERE O.CUSTOMER_ID = C.ID
);
```

---

## 32. UPDATE Statement

**Problem:** Give all IT department employees a 10% salary raise.

```sql
UPDATE EMPLOYEES
SET SALARY = SALARY * 1.10
WHERE DEPARTMENT = 'IT';
```

---

## 33. DELETE Statement

**Problem:** Delete all employees with salary below 30000.

```sql
DELETE FROM EMPLOYEES
WHERE SALARY < 30000;
```

---

## 34. INSERT Statement

**Problem:** Insert a new employee record.

```sql
INSERT INTO EMPLOYEES (ID, NAME, DEPARTMENT, SALARY, HIRE_DATE)
VALUES (101, 'John Doe', 'IT', 75000, '2024-01-15');
```

---

## 35. RANK and ROW_NUMBER (Window Functions)

**Problem:** Rank employees by salary within each department.

```sql
SELECT NAME, DEPARTMENT, SALARY,
    RANK() OVER (PARTITION BY DEPARTMENT ORDER BY SALARY DESC) AS RANK_IN_DEPT
FROM EMPLOYEES;
```

**Problem:** Assign a unique row number to each employee ordered by salary.

```sql
SELECT NAME, SALARY,
    ROW_NUMBER() OVER (ORDER BY SALARY DESC) AS ROW_NUM
FROM EMPLOYEES;
```

---

## 36. Nth Highest Salary (Generic)

**Problem:** Find the Nth highest salary (e.g., 3rd highest).

```sql
-- Using subquery
SELECT SALARY
FROM EMPLOYEES e1
WHERE (N - 1) = (
    SELECT COUNT(DISTINCT SALARY)
    FROM EMPLOYEES e2
    WHERE e2.SALARY > e1.SALARY
);

-- Replace N with 3 for 3rd highest:
SELECT SALARY
FROM EMPLOYEES e1
WHERE 2 = (
    SELECT COUNT(DISTINCT SALARY)
    FROM EMPLOYEES e2
    WHERE e2.SALARY > e1.SALARY
);
```

---

## 37. Students with Marks Above Average

**Problem:** Find students who scored above the class average.

```sql
SELECT NAME, MARKS
FROM STUDENTS
WHERE MARKS > (SELECT AVG(MARKS) FROM STUDENTS)
ORDER BY MARKS DESC;
```

---

## 38. Top Earner per Department

**Problem:** Find the highest-paid employee in each department.

```sql
SELECT DEPARTMENT, NAME, SALARY
FROM EMPLOYEES e1
WHERE SALARY = (
    SELECT MAX(SALARY)
    FROM EMPLOYEES e2
    WHERE e2.DEPARTMENT = e1.DEPARTMENT
);
```

---

## 39. Self Join – Find Employees and Their Managers

**Problem:** List each employee along with their manager's name.

```sql
SELECT E.NAME AS EMPLOYEE, M.NAME AS MANAGER
FROM EMPLOYEES E
LEFT JOIN EMPLOYEES M ON E.MANAGER_ID = M.ID;
```

---

## 40. UNION – Combine Results

**Problem:** Get all cities from both CUSTOMERS and DEPARTMENTS tables (no duplicates).

```sql
SELECT CITY FROM CUSTOMERS
UNION
SELECT CITY FROM DEPARTMENTS;
```

**With duplicates (UNION ALL):**
```sql
SELECT CITY FROM CUSTOMERS
UNION ALL
SELECT CITY FROM DEPARTMENTS;
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
| `COUNT(*)` | Total rows |
| `COUNT(col)` | Non-null values |
| `SUM(col)` | Total sum |
| `AVG(col)` | Average |
| `MAX(col)` | Maximum value |
| `MIN(col)` | Minimum value |

### JOIN Types
| Type | Description |
|------|-------------|
| `INNER JOIN` | Only matching rows in both tables |
| `LEFT JOIN` | All rows from left + matches from right |
| `RIGHT JOIN` | All rows from right + matches from left |
| `FULL OUTER JOIN` | All rows from both tables |
| `SELF JOIN` | Join a table with itself |

### String Functions
| Function | Description |
|----------|-------------|
| `UPPER(col)` | Convert to uppercase |
| `LOWER(col)` | Convert to lowercase |
| `LENGTH(col)` | Length of string |
| `CONCAT(a, b)` | Concatenate strings |
| `SUBSTRING(col, start, len)` | Extract substring |
| `TRIM(col)` | Remove leading/trailing spaces |
| `REPLACE(col, old, new)` | Replace substring |

### Date Functions
| Function | Description |
|----------|-------------|
| `NOW()` | Current datetime |
| `CURDATE()` | Current date |
| `YEAR(date)` | Extract year |
| `MONTH(date)` | Extract month |
| `DAY(date)` | Extract day |
| `DATE_SUB(date, INTERVAL n DAY)` | Subtract days |
| `DATEDIFF(d1, d2)` | Difference in days |

### WHERE vs HAVING
| | WHERE | HAVING |
|-|-------|--------|
| Used with | Individual rows | Grouped results |
| Executed | Before GROUP BY | After GROUP BY |
| Can use aggregates? | No | Yes |

### Common Patterns
```sql
-- Count per group
SELECT col, COUNT(*) FROM table GROUP BY col;

-- Filter group
SELECT col, COUNT(*) FROM table GROUP BY col HAVING COUNT(*) > n;

-- Subquery for comparison
SELECT * FROM table WHERE col > (SELECT AVG(col) FROM table);

-- Avoid NULL issues
SELECT COALESCE(col, 0) FROM table;   -- Replace NULL with 0
SELECT IFNULL(col, 'N/A') FROM table; -- MySQL specific
```
