# SQL Constraints: Complete Guide

## What are SQL Constraints?

**Constraints** are rules applied to database tables that ensure data integrity, accuracy, and reliability. They help maintain the quality and consistency of data in your database.

---

## Types of SQL Constraints

### 1. PRIMARY KEY Constraint
**Uniquely identifies each row in a table.**

```sql
-- Single column primary key
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50)
);

-- Composite primary key (multiple columns)
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (order_id, product_id)
);

-- Auto-incrementing primary key
CREATE TABLE customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50)
);
```

**Characteristics:**
- ✅ Must be unique
- ✅ Cannot be NULL
- ✅ Only one per table
- ✅ Automatically indexed

---

### 2. FOREIGN KEY Constraint
**Creates relationships between tables and maintains referential integrity.**

```sql
-- Basic foreign key
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);

-- Foreign key with cascade options
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE CASCADE    -- Delete orders when customer is deleted
        ON UPDATE CASCADE    -- Update order customer when customer ID changes
);

-- Multiple foreign keys
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

**Cascade Options:**
- `CASCADE`: Automatically update/delete related records
- `SET NULL`: Set foreign key to NULL when referenced record is deleted
- `RESTRICT`: Prevent deletion/update if related records exist
- `NO ACTION`: Similar to RESTRICT

---

### 3. UNIQUE Constraint
**Ensures all values in a column are unique (but can be NULL).**

```sql
-- Single column unique constraint
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,  -- Each email must be unique
    first_name VARCHAR(50)
);

-- Composite unique constraint
CREATE TABLE student_courses (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    UNIQUE(student_id, course_id)  -- Student can't enroll in same course twice
);

-- Unique constraint with custom name
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_code VARCHAR(20),
    CONSTRAINT uk_product_code UNIQUE(product_code)
);
```

**Use Cases:**
- Email addresses
- Usernames
- Product codes
- Social security numbers

---

### 4. NOT NULL Constraint
**Prevents NULL values in a column.**

```sql
-- Basic NOT NULL constraint
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    hire_date DATE NOT NULL
);

-- NOT NULL with default value
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE NOT NULL DEFAULT CURRENT_DATE,
    status VARCHAR(20) NOT NULL DEFAULT 'Pending'
);
```

**Best Practices:**
- Use for required fields
- Combine with DEFAULT values
- Consider business rules carefully

---

### 5. CHECK Constraint
**Ensures values meet specific conditions.**

```sql
-- Basic CHECK constraint
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    salary DECIMAL(10,2) CHECK (salary > 0),  -- Salary must be positive
    age INT CHECK (age >= 18 AND age <= 65)   -- Age between 18-65
);

-- CHECK constraint with multiple conditions
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) CHECK (price >= 0),
    category VARCHAR(50) CHECK (category IN ('Electronics', 'Clothing', 'Books')),
    stock_quantity INT CHECK (stock_quantity >= 0)
);

-- Complex CHECK constraint
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE NOT NULL,
    delivery_date DATE,
    total_amount DECIMAL(10,2) CHECK (total_amount > 0),
    CHECK (delivery_date IS NULL OR delivery_date >= order_date)  -- Delivery date must be after order date
);
```

**Common CHECK Patterns:**
- Range validation: `CHECK (value BETWEEN min AND max)`
- List validation: `CHECK (value IN ('A', 'B', 'C'))`
- Pattern matching: `CHECK (email LIKE '%@%.%')`
- Logical conditions: `CHECK (condition1 AND condition2)`

---

### 6. DEFAULT Constraint
**Sets a default value for a column when no value is specified.**

```sql
-- Basic DEFAULT constraint
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    hire_date DATE DEFAULT CURRENT_DATE,
    status VARCHAR(20) DEFAULT 'Active'
);

-- DEFAULT with functions
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_by VARCHAR(50) DEFAULT USER(),
    total_amount DECIMAL(10,2) DEFAULT 0.00
);

-- DEFAULT with expressions
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    created_date DATE DEFAULT (CURRENT_DATE),
    is_active BOOLEAN DEFAULT TRUE
);
```

**Common DEFAULT Values:**
- `CURRENT_DATE` - Current date
- `CURRENT_TIMESTAMP` - Current date and time
- `USER()` - Current user
- `0` - Numeric defaults
- `'Active'` - Status defaults

---

### 7. INDEX Constraint
**Improves query performance by creating indexes on columns.**

```sql
-- Single column index
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100) UNIQUE,
    department_id INT,
    INDEX idx_last_name (last_name),  -- Index on last_name
    INDEX idx_department (department_id)  -- Index on department_id
);

-- Composite index
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    status VARCHAR(20),
    INDEX idx_customer_date (customer_id, order_date)  -- Composite index
);

-- Unique index
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_code VARCHAR(20),
    UNIQUE INDEX idx_product_code (product_code)  -- Unique index
);
```

**Index Best Practices:**
- Index foreign key columns
- Index frequently searched columns
- Index columns used in ORDER BY and GROUP BY
- Avoid over-indexing (slows down INSERT/UPDATE)

---

## Advanced Constraint Examples

### Example 1: Employee Management System

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL UNIQUE,
    location VARCHAR(100) DEFAULT 'Main Office',
    budget DECIMAL(12,2) CHECK (budget >= 0)
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(20),
    hire_date DATE NOT NULL DEFAULT CURRENT_DATE,
    salary DECIMAL(10,2) CHECK (salary >= 30000 AND salary <= 200000),
    department_id INT,
    manager_id INT,
    status VARCHAR(20) DEFAULT 'Active' CHECK (status IN ('Active', 'Inactive', 'Terminated')),
    FOREIGN KEY (department_id) REFERENCES departments(department_id),
    FOREIGN KEY (manager_id) REFERENCES employees(employee_id),
    CHECK (hire_date <= CURRENT_DATE),
    CHECK (manager_id IS NULL OR manager_id != employee_id)  -- Can't be own manager
);
```

### Example 2: E-commerce System

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(20),
    birth_date DATE CHECK (birth_date <= CURRENT_DATE),
    registration_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(200) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) CHECK (price >= 0),
    category VARCHAR(50) CHECK (category IN ('Electronics', 'Clothing', 'Books', 'Home')),
    stock_quantity INT DEFAULT 0 CHECK (stock_quantity >= 0),
    is_active BOOLEAN DEFAULT TRUE
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    delivery_date DATE,
    total_amount DECIMAL(10,2) CHECK (total_amount >= 0),
    status VARCHAR(20) DEFAULT 'Pending' 
        CHECK (status IN ('Pending', 'Processing', 'Shipped', 'Delivered', 'Cancelled')),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    CHECK (delivery_date IS NULL OR delivery_date >= DATE(order_date))
);
```

---

## Constraint Management

### Adding Constraints to Existing Tables

```sql
-- Add primary key
ALTER TABLE employees ADD PRIMARY KEY (employee_id);

-- Add foreign key
ALTER TABLE employees 
ADD CONSTRAINT fk_employee_department 
FOREIGN KEY (department_id) REFERENCES departments(department_id);

-- Add unique constraint
ALTER TABLE employees ADD UNIQUE (email);

-- Add check constraint
ALTER TABLE employees 
ADD CONSTRAINT chk_salary 
CHECK (salary > 0);

-- Add not null constraint
ALTER TABLE employees MODIFY first_name VARCHAR(50) NOT NULL;
```

### Removing Constraints

```sql
-- Drop primary key
ALTER TABLE employees DROP PRIMARY KEY;

-- Drop foreign key
ALTER TABLE employees DROP FOREIGN KEY fk_employee_department;

-- Drop unique constraint
ALTER TABLE employees DROP INDEX email;

-- Drop check constraint
ALTER TABLE employees DROP CONSTRAINT chk_salary;
```

---

## Constraint Best Practices

### 1. **Naming Conventions**
```sql
-- Use descriptive names for constraints
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    email VARCHAR(100),
    salary DECIMAL(10,2),
    CONSTRAINT pk_employees PRIMARY KEY (employee_id),
    CONSTRAINT uk_employee_email UNIQUE (email),
    CONSTRAINT chk_employee_salary CHECK (salary > 0)
);
```

### 2. **Choose Appropriate Constraints**
```sql
-- Good: Comprehensive constraints
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) CHECK (price >= 0),
    category VARCHAR(50) CHECK (category IN ('A', 'B', 'C')),
    stock_quantity INT DEFAULT 0 CHECK (stock_quantity >= 0)
);

-- Avoid: Missing important constraints
CREATE TABLE products (
    product_id INT,  -- No primary key
    product_name VARCHAR(100),  -- No NOT NULL
    price DECIMAL(10,2),  -- No CHECK constraint
    category VARCHAR(50)  -- No validation
);
```

### 3. **Performance Considerations**
```sql
-- Create indexes for foreign keys
CREATE INDEX idx_employee_dept ON employees(department_id);
CREATE INDEX idx_order_customer ON orders(customer_id);

-- Use appropriate data types
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,  -- Good: Integer for ID
    salary DECIMAL(10,2),        -- Good: Decimal for money
    hire_date DATE               -- Good: Date for dates
);
```

### 4. **Data Validation Strategy**
```sql
-- Application-level validation (recommended for complex logic)
-- Database-level validation (for basic integrity)

-- Database constraints for basic validation
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    age INT CHECK (age >= 13),
    registration_date DATE DEFAULT CURRENT_DATE
);

-- Application handles complex validation like:
-- - Password strength
-- - Email format validation
-- - Business rule validation
```

---

## Common Constraint Patterns

### Pattern 1: Audit Trail
```sql
CREATE TABLE audit_log (
    log_id INT PRIMARY KEY,
    table_name VARCHAR(50) NOT NULL,
    action VARCHAR(20) CHECK (action IN ('INSERT', 'UPDATE', 'DELETE')),
    record_id INT NOT NULL,
    old_values JSON,
    new_values JSON,
    created_by VARCHAR(50) DEFAULT USER(),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Pattern 2: Soft Delete
```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    is_deleted BOOLEAN DEFAULT FALSE,
    deleted_at TIMESTAMP NULL,
    CHECK (is_deleted = FALSE OR deleted_at IS NOT NULL)
);
```

### Pattern 3: Status Management
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    status VARCHAR(20) DEFAULT 'Pending',
    status_changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CHECK (status IN ('Pending', 'Processing', 'Shipped', 'Delivered', 'Cancelled'))
);
```

---

## Summary

| Constraint Type | Purpose | Example |
|-----------------|---------|---------|
| **PRIMARY KEY** | Unique row identifier | `employee_id INT PRIMARY KEY` |
| **FOREIGN KEY** | Table relationships | `FOREIGN KEY (dept_id) REFERENCES departments(id)` |
| **UNIQUE** | Prevent duplicate values | `email VARCHAR(100) UNIQUE` |
| **NOT NULL** | Require values | `first_name VARCHAR(50) NOT NULL` |
| **CHECK** | Validate data | `salary DECIMAL(10,2) CHECK (salary > 0)` |
| **DEFAULT** | Set default values | `status VARCHAR(20) DEFAULT 'Active'` |
| **INDEX** | Improve performance | `INDEX idx_last_name (last_name)` |

**Constraints** are essential for maintaining data integrity and ensuring your database contains accurate, reliable information. Use them strategically to balance data quality with performance needs.
