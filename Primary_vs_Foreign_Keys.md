# Primary Key vs Foreign Key: Complete Guide

## What are Keys in SQL?

**Keys** are database constraints that help maintain data integrity and establish relationships between tables. The two most important types are **Primary Keys** and **Foreign Keys**.

---

## Primary Key

### Definition
A **Primary Key** is a column or combination of columns that uniquely identifies each row in a table. It ensures that no duplicate values exist and cannot be NULL.

### Characteristics
- ✅ **Unique**: Each value must be unique across the entire table
- ✅ **Not NULL**: Cannot contain NULL values
- ✅ **Single per table**: Only one primary key per table
- ✅ **Indexed**: Automatically creates an index for faster queries
- ✅ **Stable**: Should not change frequently

### Examples

```sql
-- Single column primary key
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,  -- Primary key
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100)
);

-- Composite primary key (multiple columns)
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (order_id, product_id)  -- Composite primary key
);

-- Auto-incrementing primary key
CREATE TABLE customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,  -- Auto-incrementing
    first_name VARCHAR(50),
    last_name VARCHAR(50)
);
```

### Use Cases
- **Employee ID** in employees table
- **Customer ID** in customers table
- **Product ID** in products table
- **Order ID** in orders table

---

## Foreign Key

### Definition
A **Foreign Key** is a column or combination of columns that creates a link between two tables. It references the primary key of another table to establish relationships.

### Characteristics
- 🔗 **References another table**: Points to primary key in another table
- 🔗 **Can be NULL**: Foreign keys can contain NULL values
- 🔗 **Multiple per table**: Can have multiple foreign keys in one table
- 🔗 **Maintains referential integrity**: Ensures data consistency
- 🔗 **Can be composite**: Can reference multiple columns

### Examples

```sql
-- Single foreign key
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    department_id INT,  -- Foreign key
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);

-- Multiple foreign keys
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,  -- Foreign key to customers
    employee_id INT,  -- Foreign key to employees
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (employee_id) REFERENCES employees(employee_id)
);

-- Composite foreign key
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

### Use Cases
- **Department ID** in employees table (references departments)
- **Customer ID** in orders table (references customers)
- **Employee ID** in orders table (references employees)
- **Product ID** in order_items table (references products)

---

## Key Differences

| Aspect | Primary Key | Foreign Key |
|--------|-------------|-------------|
| **Purpose** | Uniquely identifies each row | Creates relationships between tables |
| **Uniqueness** | Must be unique | Can have duplicate values |
| **NULL Values** | Cannot be NULL | Can be NULL |
| **Quantity** | One per table | Multiple per table |
| **References** | No external references | References primary key of another table |
| **Indexing** | Automatically indexed | May be indexed for performance |
| **Data Type** | Any data type | Must match referenced primary key type |

---

## Visual Examples

### Example 1: Employee-Department Relationship

```sql
-- Departments table (Parent table)
CREATE TABLE departments (
    department_id INT PRIMARY KEY,  -- Primary key
    department_name VARCHAR(100),
    location VARCHAR(100)
);

-- Employees table (Child table)
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,    -- Primary key
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    department_id INT,              -- Foreign key
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);

-- Insert sample data
INSERT INTO departments VALUES 
(1, 'Engineering', 'Building A'),
(2, 'Sales', 'Building B'),
(3, 'Marketing', 'Building C');

INSERT INTO employees VALUES 
(101, 'John', 'Doe', 1),      -- References Engineering
(102, 'Jane', 'Smith', 1),    -- References Engineering
(103, 'Bob', 'Johnson', 2),   -- References Sales
(104, 'Alice', 'Brown', NULL); -- No department (NULL allowed)
```

### Example 2: Customer-Order-Product Relationship

```sql
-- Customers table
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100)
);

-- Products table
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    price DECIMAL(10,2)
);

-- Orders table (references customers)
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,  -- Foreign key to customers
    order_date DATE,
    total_amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- Order_items table (references both orders and products)
CREATE TABLE order_items (
    order_id INT,     -- Foreign key to orders
    product_id INT,   -- Foreign key to products
    quantity INT,
    unit_price DECIMAL(10,2),
    PRIMARY KEY (order_id, product_id),  -- Composite primary key
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

---

## Referential Integrity

### What is Referential Integrity?
Foreign keys enforce **referential integrity**, ensuring that relationships between tables remain consistent.

### Rules
1. **INSERT**: Cannot insert a foreign key value that doesn't exist in the referenced table
2. **UPDATE**: Cannot update a primary key if it's referenced by foreign keys (unless CASCADE)
3. **DELETE**: Cannot delete a primary key if it's referenced by foreign keys (unless CASCADE)

### Example with Constraints

```sql
-- Create tables with referential integrity
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100)
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
        ON DELETE CASCADE    -- Delete employees when department is deleted
        ON UPDATE CASCADE    -- Update employee department when department ID changes
);

-- This will work
INSERT INTO departments VALUES (1, 'Engineering');
INSERT INTO employees VALUES (101, 'John', 1);

-- This will FAIL (department_id 2 doesn't exist)
INSERT INTO employees VALUES (102, 'Jane', 2);

-- This will work (CASCADE will delete all employees in Engineering)
DELETE FROM departments WHERE department_id = 1;
```

---

## Best Practices

### Primary Key Best Practices

1. **Use Surrogate Keys**
```sql
-- Good: Auto-incrementing surrogate key
CREATE TABLE employees (
    employee_id INT AUTO_INCREMENT PRIMARY KEY,
    employee_code VARCHAR(10) UNIQUE,  -- Business identifier
    first_name VARCHAR(50)
);
```

2. **Keep Primary Keys Simple**
```sql
-- Good: Simple integer primary key
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100)
);

-- Avoid: Complex composite primary keys unless necessary
CREATE TABLE orders (
    order_id INT,
    customer_id INT,
    order_date DATE,
    PRIMARY KEY (order_id, customer_id, order_date)  -- Too complex
);
```

### Foreign Key Best Practices

1. **Use Descriptive Names**
```sql
-- Good: Clear naming
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,  -- Clearly indicates it references customers
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

2. **Consider Cascade Options**
```sql
-- Choose appropriate cascade behavior
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
        ON DELETE CASCADE,    -- Delete items when order is deleted
    FOREIGN KEY (product_id) REFERENCES products(product_id)
        ON DELETE RESTRICT    -- Prevent deletion of products with orders
);
```

3. **Index Foreign Keys**
```sql
-- Create indexes for better performance
CREATE INDEX idx_employee_dept ON employees(department_id);
CREATE INDEX idx_order_customer ON orders(customer_id);
```

---

## Common Patterns

### Pattern 1: One-to-Many Relationship
```sql
-- One department can have many employees
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100)
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

### Pattern 2: Many-to-Many Relationship
```sql
-- Many students can take many courses
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    first_name VARCHAR(50)
);

CREATE TABLE courses (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(100)
);

CREATE TABLE student_courses (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

### Pattern 3: Self-Referencing (Hierarchical)
```sql
-- Employees can have managers (who are also employees)
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    manager_id INT,  -- References the same table
    FOREIGN KEY (manager_id) REFERENCES employees(employee_id)
);
```

---

## Summary

| Feature | Primary Key | Foreign Key |
|---------|-------------|-------------|
| **Role** | Row identifier | Relationship creator |
| **Uniqueness** | Always unique | Can have duplicates |
| **NULL** | Never NULL | Can be NULL |
| **Quantity** | One per table | Multiple per table |
| **References** | Self-contained | References other table |
| **Purpose** | Data integrity | Data relationships |

**Primary Keys** ensure each row is uniquely identifiable, while **Foreign Keys** create relationships between tables and maintain referential integrity. Together, they form the foundation of relational database design.
