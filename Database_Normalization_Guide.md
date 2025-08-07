# Database Normalization: Complete Guide

## What is Normalization?

**Normalization** is a database design technique that organizes data to reduce redundancy and improve data integrity. It involves breaking down large tables into smaller, more manageable tables while maintaining relationships between them.

## Goals of Normalization

1. **Eliminate Data Redundancy** - Remove duplicate data
2. **Ensure Data Integrity** - Maintain consistency and accuracy
3. **Simplify Database Structure** - Make it easier to maintain
4. **Prevent Anomalies** - Avoid insertion, update, and deletion problems
5. **Improve Performance** - Optimize query performance

---

## Normal Forms

### First Normal Form (1NF)

**A table is in 1NF if:**
- All values are atomic (indivisible)
- No repeating groups or arrays
- Each column contains only one value

#### Example: Violating 1NF

```sql
-- BAD: Not in 1NF (repeating groups)
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100),
    phone_numbers VARCHAR(200)  -- "555-1234, 555-5678, 555-9012"
);
```

#### Example: Following 1NF

```sql
-- GOOD: In 1NF (atomic values)
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE student_phones (
    phone_id INT PRIMARY KEY,
    student_id INT,
    phone_number VARCHAR(20),
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

### Second Normal Form (2NF)

**A table is in 2NF if:**
- It's in 1NF
- All non-key attributes depend on the entire primary key (no partial dependencies)

#### Example: Violating 2NF

```sql
-- BAD: Not in 2NF (partial dependency)
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    product_name VARCHAR(100),  -- Depends only on product_id, not order_id
    quantity INT,
    unit_price DECIMAL(10,2),
    PRIMARY KEY (order_id, product_id)
);
```

**Problem:** `product_name` depends only on `product_id`, not the entire primary key.

#### Example: Following 2NF

```sql
-- GOOD: In 2NF (separated tables)
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    unit_price DECIMAL(10,2)
);

CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

---

### Third Normal Form (3NF)

**A table is in 3NF if:**
- It's in 2NF
- No transitive dependencies (non-key attributes don't depend on other non-key attributes)

#### Example: Violating 3NF

```sql
-- BAD: Not in 3NF (transitive dependency)
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    department_id INT,
    department_name VARCHAR(100),  -- Depends on department_id, not employee_id
    department_location VARCHAR(100)  -- Depends on department_id, not employee_id
);
```

**Problem:** `department_name` and `department_location` depend on `department_id`, not directly on `employee_id`.

#### Example: Following 3NF

```sql
-- GOOD: In 3NF (separated tables)
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100),
    department_location VARCHAR(100)
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

---

### Boyce-Codd Normal Form (BCNF)

**A table is in BCNF if:**
- It's in 3NF
- Every determinant is a candidate key

#### Example: Violating BCNF

```sql
-- BAD: Not in BCNF
CREATE TABLE student_courses (
    student_id INT,
    course_id INT,
    instructor_id INT,
    PRIMARY KEY (student_id, course_id)
);
```

**Problem:** If one instructor teaches one course, then `instructor_id` determines `course_id`, but `instructor_id` is not a candidate key.

#### Example: Following BCNF

```sql
-- GOOD: In BCNF
CREATE TABLE course_instructors (
    course_id INT PRIMARY KEY,
    instructor_id INT
);

CREATE TABLE student_courses (
    student_id INT,
    course_id INT,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (course_id) REFERENCES course_instructors(course_id)
);
```

---

## Complete Normalization Example

### Step 1: Unnormalized Data

```sql
-- BAD: Unnormalized table with many problems
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    customer_email VARCHAR(100),
    customer_phone VARCHAR(20),
    product_name VARCHAR(100),
    product_category VARCHAR(50),
    product_price DECIMAL(10,2),
    quantity INT,
    order_date DATE
);
```

**Problems:**
- Customer data repeated for each order
- Product data repeated for each order
- Multiple values in single columns
- Update anomalies

### Step 2: First Normal Form (1NF)

```sql
-- GOOD: 1NF - Atomic values, no repeating groups
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    customer_email VARCHAR(100),
    customer_phone VARCHAR(20)
);

CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    product_category VARCHAR(50),
    product_price DECIMAL(10,2)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

### Step 3: Second Normal Form (2NF)

```sql
-- GOOD: 2NF - No partial dependencies
-- (Already achieved in step 2)
```

### Step 4: Third Normal Form (3NF)

```sql
-- GOOD: 3NF - No transitive dependencies
CREATE TABLE categories (
    category_id INT PRIMARY KEY,
    category_name VARCHAR(50)
);

CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    category_id INT,
    product_price DECIMAL(10,2),
    FOREIGN KEY (category_id) REFERENCES categories(category_id)
);
```

---

## Denormalization

**Denormalization** is the process of adding redundant data to improve performance, often at the cost of some data integrity.

### When to Denormalize

1. **Performance Requirements** - When queries are too slow
2. **Reporting Needs** - For complex analytical queries
3. **Read-Heavy Applications** - When reads far exceed writes
4. **Data Warehousing** - For analytical processing

### Example: Denormalization for Performance

```sql
-- Normalized (3NF)
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- Denormalized for performance
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    customer_name VARCHAR(100),  -- Redundant data
    customer_email VARCHAR(100), -- Redundant data
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

---

## Normalization Benefits

### 1. **Data Integrity**
```sql
-- Normalized: Data consistency
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100) UNIQUE
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

### 2. **Elimination of Anomalies**

#### Insert Anomaly
```sql
-- Problem: Can't add department without employee
-- Solution: Separate departments table
```

#### Update Anomaly
```sql
-- Problem: Update department name in multiple places
-- Solution: Update once in departments table
```

#### Delete Anomaly
```sql
-- Problem: Deleting last employee deletes department
-- Solution: Separate tables prevent this
```

### 3. **Flexibility**
```sql
-- Easy to add new attributes
ALTER TABLE departments ADD COLUMN location VARCHAR(100);
ALTER TABLE departments ADD COLUMN budget DECIMAL(12,2);
```

---

## Normalization Levels Summary

| Normal Form | Requirement | Example Fix |
|-------------|-------------|-------------|
| **1NF** | Atomic values, no repeating groups | Split arrays into separate tables |
| **2NF** | No partial dependencies | Move dependent columns to separate tables |
| **3NF** | No transitive dependencies | Remove derived attributes |
| **BCNF** | Every determinant is a candidate key | Further decompose tables |

---

## Practical Guidelines

### When to Normalize

✅ **Normalize when:**
- Data integrity is critical
- Update operations are frequent
- Storage space is limited
- Complex queries are rare

### When to Denormalize

✅ **Denormalize when:**
- Query performance is critical
- Read operations far exceed writes
- Reporting and analytics are primary
- Storage space is not a concern

### Best Practices

1. **Start with 3NF** - Most practical for most applications
2. **Consider BCNF** - For complex business rules
3. **Monitor Performance** - Normalize first, denormalize if needed
4. **Document Decisions** - Keep track of normalization choices
5. **Test Thoroughly** - Ensure data integrity after changes

---

## Real-World Example: E-commerce System

### Unnormalized (Problems)
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    customer_email VARCHAR(100),
    customer_address TEXT,
    product_name VARCHAR(100),
    product_price DECIMAL(10,2),
    quantity INT,
    order_date DATE
);
```

### Normalized (3NF)
```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    customer_email VARCHAR(100) UNIQUE,
    customer_address TEXT
);

CREATE TABLE categories (
    category_id INT PRIMARY KEY,
    category_name VARCHAR(50)
);

CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    category_id INT,
    product_price DECIMAL(10,2),
    FOREIGN KEY (category_id) REFERENCES categories(category_id)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    unit_price DECIMAL(10,2),
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

---

## Summary

**Normalization** is a systematic approach to organizing database tables to minimize redundancy and maximize data integrity. While it may seem complex, following the normal forms (1NF → 2NF → 3NF → BCNF) ensures your database is well-structured and maintainable.

**Key Takeaways:**
- Start with 3NF for most applications
- Normalize for data integrity, denormalize for performance
- Consider your specific use case and requirements
- Test thoroughly after any structural changes
- Document your normalization decisions

Remember: **"Normalize until it hurts, denormalize until it works."**
