# 10 SQL Written Query Problems

## Database Schema

For these practice problems, assume you have the following tables:

```sql
-- Employees table
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    hire_date DATE,
    salary DECIMAL(10,2),
    department_id INT,
    manager_id INT
);

-- Departments table
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100),
    location VARCHAR(100)
);

-- Orders table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    employee_id INT,
    order_date DATE,
    total_amount DECIMAL(10,2),
    status VARCHAR(20)
);

-- Customers table
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    city VARCHAR(50),
    country VARCHAR(50)
);
```

---

## Query Problems

### Problem 1: Basic Employee List
**Write a query to retrieve all employees with their first name, last name, email, and salary. Sort the results by last name in alphabetical order.**

**Expected Output**: A list of all employees with their basic information, sorted alphabetically by last name.

---

### Problem 2: High Salary Employees
**Write a query to find all employees who earn more than $70,000. Display their first name, last name, salary, and department ID. Sort by salary in descending order.**

**Expected Output**: Only employees with salary > $70,000, showing their details sorted from highest to lowest salary.

---

### Problem 3: Department Statistics
**Write a query to calculate the average salary for each department. Show the department name, number of employees, and average salary. Only include departments that have at least 2 employees.**

**Expected Output**: Department statistics including name, employee count, and average salary for departments with 2+ employees.

---

### Problem 4: Employee-Manager Relationship
**Write a query to show employees along with their manager's name. Display employee first name, last name, and their manager's first and last name. Use table aliases for clarity.**

**Expected Output**: Employee names paired with their manager's name (self-join required).

---

### Problem 5: Recent Hires
**Write a query to find employees hired in the last 6 months. Show their first name, last name, hire date, and department name. Sort by hire date (most recent first).**

**Expected Output**: Employees hired within the last 6 months with their department information.

---

### Problem 6: Salary Ranges
**Write a query to categorize employees by salary range and count how many fall into each category. Use these ranges: Low (< $50,000), Medium ($50,000 - $75,000), High (> $75,000).**

**Expected Output**: Salary range categories with the count of employees in each range.

---

### Problem 7: Top Performers
**Write a query to find the top 3 highest-paid employees in each department. Show their first name, last name, salary, and department name. Use window functions.**

**Expected Output**: Top 3 employees by salary within each department.

---

### Problem 8: Customer Order Analysis
**Write a query to find customers who have placed more than 2 orders. Show customer first name, last name, email, and their total number of orders. Sort by order count in descending order.**

**Expected Output**: Customers with more than 2 orders, showing their order count.

---

### Problem 9: Sales Performance
**Write a query to calculate the total sales amount for each employee in 2024. Show employee first name, last name, and total sales. Only include employees with sales > $10,000. Sort by total sales in descending order.**

**Expected Output**: Employee sales performance for 2024, showing only those with sales > $10,000.

---

### Problem 10: Complex Business Report
**Write a query to create a comprehensive report showing:**
- Department name
- Number of employees in the department
- Average salary in the department
- Total sales amount for the department (sum of all orders by employees in that department)
- Only include departments that have both employees and sales data
- Sort by total sales in descending order

**Expected Output**: A comprehensive business report combining employee and sales data by department.

---

## Sample Data for Testing

```sql
-- Insert sample data to test your queries
INSERT INTO departments VALUES 
(1, 'Engineering', 'Building A'),
(2, 'Sales', 'Building B'),
(3, 'Marketing', 'Building C'),
(4, 'HR', 'Building D');

INSERT INTO employees VALUES 
(1, 'John', 'Doe', 'john.doe@company.com', '2023-01-15', 75000.00, 1, NULL),
(2, 'Jane', 'Smith', 'jane.smith@company.com', '2023-02-20', 80000.00, 1, 1),
(3, 'Bob', 'Johnson', 'bob.johnson@company.com', '2023-03-10', 65000.00, 2, 1),
(4, 'Alice', 'Brown', 'alice.brown@company.com', '2023-04-05', 70000.00, 1, 2),
(5, 'Charlie', 'Wilson', 'charlie.wilson@company.com', '2023-05-12', 85000.00, 2, 1),
(6, 'Diana', 'Davis', 'diana.davis@company.com', '2023-06-18', 90000.00, 2, 5);

INSERT INTO customers VALUES 
(101, 'Mike', 'Taylor', 'mike.taylor@email.com', 'New York', 'USA'),
(102, 'Sarah', 'Wilson', 'sarah.wilson@email.com', 'London', 'UK'),
(103, 'David', 'Lee', 'david.lee@email.com', 'Toronto', 'Canada');

INSERT INTO orders VALUES 
(1, 101, 3, '2024-01-15', 1500.00, 'Completed'),
(2, 102, 5, '2024-01-20', 2300.00, 'Completed'),
(3, 101, 3, '2024-02-05', 800.00, 'Completed'),
(4, 103, 5, '2024-02-10', 3200.00, 'Completed'),
(5, 102, 6, '2024-02-15', 950.00, 'Completed'),
(6, 101, 3, '2024-03-01', 1200.00, 'Completed');
```

---

## Tips for Solving These Problems

1. **Start Simple**: Begin with basic SELECT statements and add complexity gradually
2. **Use Aliases**: Table aliases make queries more readable
3. **Test Incrementally**: Build your query step by step and test each part
4. **Check Data Types**: Ensure you're comparing compatible data types
5. **Use Window Functions**: For ranking and partitioning problems
6. **Consider Performance**: Use appropriate indexes for large datasets
7. **Validate Results**: Make sure your output matches the expected format

Try solving these problems in order, as they progress from basic to more complex scenarios!
