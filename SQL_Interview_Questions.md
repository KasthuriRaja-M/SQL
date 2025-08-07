# Top SQL Interview Questions from IT Companies

## Most Asked SQL Interview Questions

### **Companies Covered:**
- Google, Amazon, Microsoft, Meta (Facebook)
- Apple, Netflix, Uber, Airbnb
- Oracle, IBM, Salesforce, Adobe
- Startups and Tech Companies

---

## **BASIC SQL QUESTIONS**

### 1. **Difference between WHERE and HAVING**
**Companies:** Google, Amazon, Microsoft

```sql
-- WHERE filters individual rows
SELECT department_id, AVG(salary)
FROM employees
WHERE salary > 50000
GROUP BY department_id;

-- HAVING filters grouped results
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 50000;
```

**Answer:** WHERE filters rows before grouping, HAVING filters after grouping.

---

### 2. **Types of JOINs**
**Companies:** All major tech companies

```sql
-- INNER JOIN: Only matching records
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;

-- LEFT JOIN: All from left table + matching from right
SELECT e.name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;

-- RIGHT JOIN: All from right table + matching from left
SELECT e.name, d.department_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;

-- FULL JOIN: All records from both tables
SELECT e.name, d.department_name
FROM employees e
FULL JOIN departments d ON e.dept_id = d.dept_id;
```

---

### 3. **Difference between DELETE, TRUNCATE, and DROP**
**Companies:** Amazon, Oracle, Microsoft

```sql
-- DELETE: Removes specific rows, can be rolled back
DELETE FROM employees WHERE department_id = 5;

-- TRUNCATE: Removes all rows, faster, cannot be rolled back
TRUNCATE TABLE employees;

-- DROP: Removes entire table structure
DROP TABLE employees;
```

**Key Differences:**
- DELETE: Conditional, rollback possible, slower
- TRUNCATE: All rows, no rollback, faster
- DROP: Removes table structure completely

---

### 4. **Primary Key vs Foreign Key**
**Companies:** Google, Meta, Apple

```sql
-- Primary Key: Unique identifier for each row
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,  -- Primary Key
    name VARCHAR(100)
);

-- Foreign Key: References primary key in another table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    employee_id INT,  -- Foreign Key
    FOREIGN KEY (employee_id) REFERENCES employees(employee_id)
);
```

**Answer:** Primary key uniquely identifies rows, foreign key creates relationships between tables.

---

### 5. **Indexes and Performance**
**Companies:** Amazon, Google, Netflix

```sql
-- Create indexes for better performance
CREATE INDEX idx_employee_email ON employees(email);
CREATE INDEX idx_order_date ON orders(order_date);

-- Composite index
CREATE INDEX idx_employee_dept_salary ON employees(department_id, salary);
```

**Common Questions:**
- When to use indexes?
- What are the trade-offs?
- How to optimize slow queries?

---

## **INTERMEDIATE SQL QUESTIONS**

### 6. **Window Functions**
**Companies:** Google, Amazon, Microsoft

```sql
-- Rank employees by salary within each department
SELECT 
    name,
    department_id,
    salary,
    RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) as salary_rank
FROM employees;

-- Running total
SELECT 
    order_date,
    total_amount,
    SUM(total_amount) OVER (ORDER BY order_date) as running_total
FROM orders;
```

**Common Window Functions:**
- ROW_NUMBER(), RANK(), DENSE_RANK()
- LAG(), LEAD()
- SUM(), AVG(), COUNT() OVER()

---

### 7. **Subqueries vs JOINs**
**Companies:** Meta, Apple, Uber

```sql
-- Subquery approach
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- JOIN approach (for different scenarios)
SELECT e.name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;
```

**When to use which:**
- Subqueries: When you need a single value or list
- JOINs: When you need data from multiple tables

---

### 8. **Common Table Expressions (CTEs)**
**Companies:** Google, Amazon, Microsoft

```sql
-- Recursive CTE for hierarchical data
WITH RECURSIVE employee_hierarchy AS (
    -- Base case: top-level employees
    SELECT employee_id, name, manager_id, 1 as level
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case: subordinates
    SELECT e.employee_id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT * FROM employee_hierarchy;
```

---

### 9. **Pivot Tables**
**Companies:** Amazon, Google, Meta

```sql
-- Pivot data using CASE statements
SELECT 
    department_id,
    SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) as male_count,
    SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) as female_count
FROM employees
GROUP BY department_id;
```

---

### 10. **Data Quality and Validation**
**Companies:** All companies

```sql
-- Find duplicate records
SELECT email, COUNT(*) as count
FROM users
GROUP BY email
HAVING COUNT(*) > 1;

-- Find missing data
SELECT COUNT(*) as null_count
FROM employees
WHERE department_id IS NULL;
```

---

## **ADVANCED SQL QUESTIONS**

### 11. **Complex Aggregations**
**Companies:** Google, Amazon, Netflix

```sql
-- Calculate moving average
SELECT 
    order_date,
    total_amount,
    AVG(total_amount) OVER (
        ORDER BY order_date 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) as moving_avg_3_days
FROM orders;

-- Percentile calculations
SELECT 
    department_id,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) as median_salary
FROM employees
GROUP BY department_id;
```

---

### 12. **Performance Optimization**
**Companies:** Amazon, Google, Meta

```sql
-- Query optimization techniques
-- 1. Use appropriate indexes
CREATE INDEX idx_composite ON orders(customer_id, order_date, status);

-- 2. Avoid SELECT *
SELECT customer_id, order_date, total_amount
FROM orders
WHERE order_date >= '2024-01-01';

-- 3. Use EXISTS instead of IN for large datasets
SELECT customer_id
FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o 
    WHERE o.customer_id = c.customer_id
);
```

---

### 13. **Data Modeling Scenarios**
**Companies:** All major companies

```sql
-- Design a social media database
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50) UNIQUE,
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE posts (
    post_id INT PRIMARY KEY,
    user_id INT,
    content TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

CREATE TABLE likes (
    like_id INT PRIMARY KEY,
    user_id INT,
    post_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (post_id) REFERENCES posts(post_id),
    UNIQUE(user_id, post_id)  -- Prevent duplicate likes
);
```

---

### 14. **Analytical Queries**
**Companies:** Google, Amazon, Meta

```sql
-- Cohort analysis
WITH user_cohorts AS (
    SELECT 
        user_id,
        DATE_TRUNC('month', MIN(created_at)) as cohort_month
    FROM users
    GROUP BY user_id
),
user_activities AS (
    SELECT 
        u.user_id,
        c.cohort_month,
        DATE_TRUNC('month', u.created_at) as activity_month
    FROM users u
    JOIN user_cohorts c ON u.user_id = c.user_id
)
SELECT 
    cohort_month,
    activity_month,
    COUNT(DISTINCT user_id) as active_users
FROM user_activities
GROUP BY cohort_month, activity_month
ORDER BY cohort_month, activity_month;
```

---

### 15. **Database Design Questions**
**Companies:** All companies

**Common Questions:**
- Design a URL shortener database
- Design an e-commerce database
- Design a ride-sharing database
- Handle many-to-many relationships
- Design for scalability

```sql
-- URL shortener example
CREATE TABLE urls (
    id BIGINT PRIMARY KEY,
    original_url TEXT NOT NULL,
    short_code VARCHAR(10) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

CREATE TABLE url_clicks (
    click_id BIGINT PRIMARY KEY,
    url_id BIGINT,
    ip_address INET,
    user_agent TEXT,
    clicked_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (url_id) REFERENCES urls(id)
);
```

---

## **COMPANY-SPECIFIC QUESTIONS**

### **Google Questions:**
- Complex analytical queries
- Performance optimization
- Data warehousing concepts
- BigQuery specific questions

### **Amazon Questions:**
- Database scaling
- ACID properties
- Transaction management
- AWS RDS specific questions

### **Microsoft Questions:**
- SQL Server specific features
- T-SQL programming
- Performance tuning
- Azure SQL questions

### **Meta Questions:**
- Social media data modeling
- Graph database concepts
- Real-time analytics
- Large-scale data processing

---

## **COMMON SCENARIOS**

### 16. **Employee Management System**
```sql
-- Find employees who earn more than their department average
SELECT e.name, e.salary, e.department_id
FROM employees e
WHERE e.salary > (
    SELECT AVG(salary) 
    FROM employees 
    WHERE department_id = e.department_id
);
```

### 17. **E-commerce Analytics**
```sql
-- Top customers by total spend
SELECT 
    c.customer_id,
    c.name,
    SUM(o.total_amount) as total_spent,
    COUNT(o.order_id) as order_count
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name
ORDER BY total_spent DESC
LIMIT 10;
```

### 18. **Social Media Metrics**
```sql
-- User engagement analysis
SELECT 
    u.user_id,
    COUNT(p.post_id) as posts_count,
    COUNT(l.like_id) as likes_received,
    COUNT(DISTINCT f.follower_id) as followers_count
FROM users u
LEFT JOIN posts p ON u.user_id = p.user_id
LEFT JOIN likes l ON p.post_id = l.post_id
LEFT JOIN follows f ON u.user_id = f.followee_id
GROUP BY u.user_id;
```

---

## **INTERVIEW TIPS**

### **Before the Interview:**
1. **Practice Common Patterns:**
   - Self-joins for hierarchies
   - Window functions for rankings
   - CTEs for complex queries
   - Subqueries vs JOINs

2. **Know Your Database:**
   - Index types and usage
   - Performance optimization
   - Transaction management
   - Data types and constraints

3. **Prepare Examples:**
   - Real-world scenarios
   - Performance bottlenecks
   - Data quality issues
   - Scalability challenges

### **During the Interview:**
1. **Clarify Requirements:**
   - Ask about data volume
   - Understand performance requirements
   - Consider edge cases

2. **Think Aloud:**
   - Explain your approach
   - Discuss trade-offs
   - Consider alternatives

3. **Write Clean Code:**
   - Use meaningful aliases
   - Format queries properly
   - Add comments for complex logic

### **Common Mistakes to Avoid:**
- Not considering performance
- Ignoring data quality
- Not testing edge cases
- Forgetting about indexes
- Not understanding business context

---

## **PRACTICE PROBLEMS**

### **Easy Level:**
1. Find duplicate emails in users table
2. Calculate average salary by department
3. Get top 5 customers by order count

### **Medium Level:**
1. Find employees who earn more than their manager
2. Calculate month-over-month growth
3. Design a URL shortener database

### **Hard Level:**
1. Implement a recommendation system
2. Design a real-time analytics system
3. Optimize a slow-running query

---

## **SUMMARY**

**Most Important Topics:**
1. **JOINs** - All types and when to use each
2. **Window Functions** - RANK, ROW_NUMBER, LAG/LEAD
3. **Performance** - Indexes, query optimization
4. **Data Modeling** - Normalization, relationships
5. **Analytics** - Aggregations, time-series analysis

**Key Skills:**
- Write efficient queries
- Optimize performance
- Design database schemas
- Handle large datasets
- Solve real-world problems

Remember: **Practice, practice, practice!** The more real-world scenarios you solve, the better prepared you'll be for any SQL interview.
