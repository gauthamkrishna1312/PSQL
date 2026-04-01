# PSQL

# The Complete PostgreSQL Guide
## From Basics to Advanced Management

---

## Table of Contents

1. Introduction
2. Chapter 1: SQL & PSQL Basics
3. Chapter 2: Basic to Intermediate SQL Queries
4. Chapter 3: Download, Setup & Management
5. Chapter 4: pgAdmin - Web UI Management
6. Chapter 5: User & Database Management
7. Chapter 6: Advanced Topics
8. Conclusion

---

## Introduction

PostgreSQL is a powerful, open-source relational database management system (RDBMS) that has been actively developed for over 30 years. It is known for its reliability, data integrity, and support for advanced features like JSON, full-text search, and custom data types.

This comprehensive guide covers everything you need to know about PostgreSQL, from basic SQL syntax to advanced database and user management.

---

# Chapter 1: SQL & PSQL Basics

## 1.1 What is SQL?

SQL (Structured Query Language) is a standardized language for managing relational databases. It allows you to:

- Create and modify database structures
- Insert, update, and delete data
- Query and retrieve data
- Control user access and permissions

## 1.2 What is PSQL?

PSQL is the command-line interface (CLI) for PostgreSQL. It allows you to:

- Connect to PostgreSQL databases
- Execute SQL commands
- View database metadata and structure
- Administer users, databases, and permissions

## 1.3 Key Concepts

**Database**
A database is a collection of organized tables, indexes, and other objects that store related data.

**Table**
A table is a collection of rows and columns. Each column has a name and data type, and each row represents a record.

**Column**
A column is a vertical set of data values of the same type. Each column has a name and data type.

**Primary Key**
A primary key is a unique identifier for each row in a table. No two rows can have the same primary key value.

**Foreign Key**
A foreign key is a column that references the primary key of another table, establishing relationships between tables.

## 1.4 PostgreSQL Data Types

PostgreSQL supports various data types:

```sql
-- Numeric Types
INTEGER / INT          - Standard integer (-2,147,483,648 to 2,147,483,647)
BIGINT                 - Large integer (-9,223,372,036,854,775,808 to ...)
SMALLINT               - Small integer (-32,768 to 32,767)
DECIMAL / NUMERIC      - Fixed-point number (10.5, 99.99)
FLOAT / REAL           - Floating-point number (3.14, 2.71828)

-- Character Types
CHAR(n)                - Fixed-length string
VARCHAR(n)             - Variable-length string with limit
TEXT                   - Variable-length string (no limit)

-- Date/Time Types
DATE                   - Date (2024-01-15)
TIME                   - Time (14:30:00)
TIMESTAMP              - Date and time (2024-01-15 14:30:00)
INTERVAL               - Time duration (1 day, 2 hours)

-- Boolean Type
BOOLEAN                - TRUE or FALSE

-- JSON Types
JSON                   - JSON data (slower but flexible)
JSONB                  - Binary JSON (faster, indexable)

-- Other Types
UUID                   - Universally unique identifier
ARRAY                  - Arrays of any type
BYTEA                  - Binary data
```

---

# Chapter 2: Basic to Intermediate SQL Queries

## 2.1 Creating Tables

**Theory:** A CREATE TABLE statement defines a new table with its columns, data types, and constraints.

**Basic Syntax:**
```sql
CREATE TABLE table_name (
  column_name1 data_type constraints,
  column_name2 data_type constraints,
  ...
);
```

**Example: Creating an Employees Table**
```sql
CREATE TABLE employees (
  employee_id SERIAL PRIMARY KEY,
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  hire_date DATE NOT NULL,
  salary DECIMAL(10, 2),
  department_id INTEGER,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Explanation:**
- `SERIAL`: Auto-incrementing integer
- `PRIMARY KEY`: Uniquely identifies each row
- `NOT NULL`: Column must have a value
- `UNIQUE`: All values in column must be unique
- `DEFAULT`: Default value if none is provided

## 2.2 Inserting Data

**Theory:** INSERT statements add new rows to a table.

```sql
-- Single row insert
INSERT INTO employees (first_name, last_name, email, hire_date, salary, department_id)
VALUES ('John', 'Doe', 'john@example.com', '2024-01-15', 75000, 1);

-- Multiple rows insert
INSERT INTO employees (first_name, last_name, email, hire_date, salary, department_id)
VALUES 
  ('Jane', 'Smith', 'jane@example.com', '2024-02-20', 80000, 2),
  ('Mike', 'Johnson', 'mike@example.com', '2024-03-10', 72000, 1),
  ('Sarah', 'Williams', 'sarah@example.com', '2024-03-15', 85000, 3);
```

## 2.3 Querying Data with SELECT

**Theory:** SELECT retrieves data from one or more tables.

```sql
-- Select all columns
SELECT * FROM employees;

-- Select specific columns
SELECT employee_id, first_name, last_name, salary FROM employees;

-- Select with WHERE clause
SELECT * FROM employees WHERE salary > 75000;

-- Select with ORDER BY
SELECT * FROM employees ORDER BY salary DESC;

-- Select with LIMIT
SELECT * FROM employees LIMIT 5;

-- Select with OFFSET (pagination)
SELECT * FROM employees ORDER BY employee_id LIMIT 5 OFFSET 10;
```

## 2.4 Filtering with WHERE

```sql
-- Comparison operators
SELECT * FROM employees WHERE salary > 75000;
SELECT * FROM employees WHERE hire_date >= '2024-01-01';

-- Logical operators
SELECT * FROM employees 
WHERE salary > 70000 AND department_id = 1;

SELECT * FROM employees 
WHERE department_id = 1 OR department_id = 2;

-- NOT operator
SELECT * FROM employees WHERE NOT department_id = 1;

-- IN operator
SELECT * FROM employees WHERE department_id IN (1, 2, 3);

-- BETWEEN operator
SELECT * FROM employees WHERE salary BETWEEN 70000 AND 80000;

-- LIKE operator (pattern matching)
SELECT * FROM employees WHERE first_name LIKE 'J%';  -- Starts with J
SELECT * FROM employees WHERE email LIKE '%@example.com';  -- Ends with
SELECT * FROM employees WHERE first_name LIKE '%oh%';  -- Contains 'oh'
```

## 2.5 Updating Data

```sql
-- Update single column
UPDATE employees SET salary = 78000 WHERE employee_id = 1;

-- Update multiple columns
UPDATE employees SET salary = 82000, department_id = 2 
WHERE first_name = 'John';

-- Update with condition
UPDATE employees SET salary = salary * 1.1 
WHERE department_id = 1;  -- 10% raise for dept 1
```

## 2.6 Deleting Data

```sql
-- Delete specific rows
DELETE FROM employees WHERE employee_id = 5;

-- Delete with condition
DELETE FROM employees WHERE department_id = 3;

-- Delete all rows (be careful!)
DELETE FROM employees;
```

## 2.7 Aggregate Functions

Aggregate functions compute a single result from multiple rows.

```sql
-- COUNT - number of rows
SELECT COUNT(*) FROM employees;

-- SUM - total of numeric column
SELECT SUM(salary) FROM employees;

-- AVG - average value
SELECT AVG(salary) FROM employees;

-- MIN - minimum value
SELECT MIN(salary) FROM employees;

-- MAX - maximum value
SELECT MAX(salary) FROM employees;

-- GROUP BY - group rows by column
SELECT department_id, COUNT(*), AVG(salary) 
FROM employees 
GROUP BY department_id;

-- HAVING - filter groups
SELECT department_id, AVG(salary) as avg_salary
FROM employees 
GROUP BY department_id 
HAVING AVG(salary) > 75000;
```

## 2.8 JOIN Operations

JOINs combine rows from two or more tables.

```sql
-- Create a departments table
CREATE TABLE departments (
  department_id SERIAL PRIMARY KEY,
  department_name VARCHAR(100) NOT NULL
);

-- INNER JOIN - returns matching rows from both tables
SELECT e.first_name, e.last_name, d.department_name
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id;

-- LEFT JOIN - all rows from left table, matching rows from right
SELECT e.first_name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.department_id = d.department_id;

-- RIGHT JOIN - all rows from right table, matching rows from left
SELECT e.first_name, d.department_name
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.department_id;

-- FULL OUTER JOIN - all rows from both tables
SELECT e.first_name, d.department_name
FROM employees e
FULL OUTER JOIN departments d ON e.department_id = d.department_id;

-- CROSS JOIN - Cartesian product
SELECT e.first_name, d.department_name
FROM employees e
CROSS JOIN departments d;
```

## 2.9 Subqueries

```sql
-- Subquery in WHERE clause
SELECT * FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Subquery in FROM clause
SELECT avg_salary FROM (
  SELECT department_id, AVG(salary) as avg_salary 
  FROM employees 
  GROUP BY department_id
) AS dept_avg
WHERE avg_salary > 75000;

-- EXISTS operator
SELECT * FROM employees e
WHERE EXISTS (
  SELECT 1 FROM departments d 
  WHERE d.department_id = e.department_id
);
```

## 2.10 String Functions

```sql
-- CONCAT or || - concatenate strings
SELECT first_name || ' ' || last_name as full_name FROM employees;

-- UPPER - convert to uppercase
SELECT UPPER(first_name) FROM employees;

-- LOWER - convert to lowercase
SELECT LOWER(email) FROM employees;

-- LENGTH - string length
SELECT first_name, LENGTH(first_name) FROM employees;

-- SUBSTRING - extract part of string
SELECT SUBSTRING(email, 1, 5) FROM employees;

-- TRIM - remove leading/trailing spaces
SELECT TRIM(first_name) FROM employees;

-- REPLACE - replace text
SELECT REPLACE(email, '@example.com', '') FROM employees;
```

## 2.11 Date Functions

```sql
-- CURRENT_DATE - today's date
SELECT CURRENT_DATE;

-- CURRENT_TIMESTAMP - current date and time
SELECT CURRENT_TIMESTAMP;

-- EXTRACT - extract part of date
SELECT EXTRACT(YEAR FROM hire_date) as year FROM employees;
SELECT EXTRACT(MONTH FROM hire_date) as month FROM employees;

-- AGE - calculate age
SELECT first_name, AGE(hire_date) as years_employed FROM employees;

-- DATE_PART - similar to EXTRACT
SELECT DATE_PART('year', hire_date) FROM employees;

-- DATE_ADD - add interval to date
SELECT hire_date + INTERVAL '1 year' FROM employees;

-- DATE_TRUNC - truncate to specific unit
SELECT DATE_TRUNC('month', hire_date) FROM employees;
```

---

# Chapter 3: Download, Setup & Management

## 3.1 Installing PostgreSQL on Windows

**Step 1: Download**
1. Visit https://www.postgresql.org/download/windows/
2. Download the latest version (e.g., PostgreSQL 15.x or 16.x)
3. Run the installer (.exe file)

**Step 2: Installation**
1. Click 'Next' to proceed
2. Choose installation directory (default: C:\Program Files\PostgreSQL\)
3. Select components to install (Server, pgAdmin, Command Line Tools)
4. Choose data directory
5. Set superuser password (remember this!)
6. Set port (default: 5432)
7. Complete installation

## 3.2 Installing PostgreSQL on Linux (Ubuntu/Debian)

```bash
# Update package manager
sudo apt update

# Install PostgreSQL
sudo apt install postgresql postgresql-contrib

# Install pgAdmin (optional)
sudo apt install pgadmin4

# Check PostgreSQL status
sudo systemctl status postgresql

# Start PostgreSQL service
sudo systemctl start postgresql

# Enable auto-start on boot
sudo systemctl enable postgresql
```

## 3.3 Installing PostgreSQL on macOS

```bash
# Using Homebrew
brew install postgresql

# Start PostgreSQL
brew services start postgresql

# Check version
psql --version

# Connect to default database
psql -d postgres
```

## 3.4 Connecting to PostgreSQL with PSQL

```bash
# Basic connection
psql -U postgres

# Connect to specific database
psql -U postgres -d database_name

# Connect to remote server
psql -h hostname -U username -d database_name -p 5432

# Connect without password (using .pgpass file)
# Windows: %APPDATA%\postgresql\pgpass.conf
# Linux: ~/.pgpass
# Format: hostname:port:database:username:password
```

## 3.5 Essential PSQL Commands

```sql
\h                  - Get help on SQL commands
\?                  - Get help on psql commands
\l                  - List all databases
\c database_name    - Connect to a database
\dt                 - List all tables in current database
\d table_name       - Describe table structure
\du                 - List all users/roles
\dn                 - List all schemas
\dv                 - List all views
\df                 - List all functions
\q                  - Quit psql

\e                  - Open editor to write SQL
\i filename         - Execute SQL from file
\x                  - Toggle expanded display
\a                  - Toggle aligned/unaligned output
\t                  - Toggle tuple-only output
\o filename         - Output results to file
\copy               - Copy data to/from file

\password username  - Change user password
\createdb dbname    - Create database
\dropdb dbname      - Drop database

-- Set environment variables in psql
SET search_path TO public;
SHOW search_path;
```

---

# Chapter 4: pgAdmin - Web UI Management

## 4.1 What is pgAdmin?

pgAdmin is a web-based graphical interface for PostgreSQL that allows you to manage databases, tables, users, and execute queries without using command-line tools.

## 4.2 Opening pgAdmin

1. Open web browser
2. Navigate to http://localhost:5050
3. Login with pgAdmin email/password

## 4.3 Registering a Server

1. Click 'Add New Server' in the browser panel
2. General tab: Enter server name
3. Connection tab: Enter hostname (localhost), username, and password
4. Click 'Save'

## 4.4 Creating Databases in pgAdmin

1. Right-click 'Databases' → 'Create' → 'Database'
2. Enter database name
3. Configure owner and other settings
4. Click 'Save'

## 4.5 Creating Tables in pgAdmin

1. Navigate to database → 'Schemas' → 'public' → 'Tables'
2. Right-click 'Tables' → 'Create' → 'Table'
3. Enter table name
4. Go to 'Columns' tab and add columns with their data types
5. Set primary key by clicking 'Is Primary Key' for a column
6. Click 'Save'

## 4.6 Using Query Tool

1. Right-click any database → 'Query Tool'
2. Write SQL queries in the editor
3. Click 'Execute' (F6) to run
4. View results in the 'Data Output' tab

## 4.7 Viewing Table Data

1. Right-click a table → 'View/Edit Data' → 'All Rows'
2. View all data in the table
3. Click the row to edit values
4. Click the '+' icon to add a new row

---

# Chapter 5: User & Database Management

## 5.1 Understanding Users and Roles

In PostgreSQL, 'users' and 'roles' are essentially the same thing. A role can be a user (login capability) or a group role.

**Key Concepts:**
- **Superuser:** Has all privileges, can create/drop databases and users
- **Regular User:** Limited privileges, access specific databases/tables
- **Group Role:** A role without login capability used to manage permissions

## 5.2 Creating Users

```sql
-- Create a basic user
CREATE USER username WITH PASSWORD 'password';

-- Create user with multiple attributes
CREATE USER john WITH PASSWORD 'secure_pass123' CREATEDB CREATEROLE;

-- Create user that cannot create databases
CREATE USER readonly_user WITH PASSWORD 'pass123' NOCREATEDB;

-- Create user with expiration date
CREATE USER temp_user WITH PASSWORD 'pass' VALID UNTIL '2024-12-31';

-- Create a superuser
CREATE USER superuser_name WITH SUPERUSER PASSWORD 'pass';
```

## 5.3 Modifying Users

```sql
-- Change user password
ALTER USER username WITH PASSWORD 'new_password';

-- Grant CREATEDB privilege
ALTER USER username CREATEDB;

-- Revoke CREATEDB privilege
ALTER USER username NOCREATEDB;

-- Make user a superuser
ALTER USER username SUPERUSER;

-- Prevent user from creating users
ALTER USER username NOCREATEROLE;

-- Change connection limit
ALTER USER username CONNECTION LIMIT 10;  -- Max 10 connections
```

## 5.4 Deleting Users

```sql
-- Drop a user
DROP USER username;

-- Drop user only if exists
DROP USER IF EXISTS username;

-- Drop user and reassign objects
DROP USER username;
-- First reassign owned objects to another user
REASSIGN OWNED BY old_user TO new_user;
DROP USER old_user;
```

## 5.5 Viewing Users

```sql
-- In psql, list all users
\du

-- Or use SQL query
SELECT usename, usesuper, usecreatedb FROM pg_user;

-- Get more detailed information
SELECT rolname, rolsuper, rolcreatedb, rolcanlogin, rolconnlimit 
FROM pg_roles;
```

## 5.6 Creating Databases

```sql
-- Create a basic database
CREATE DATABASE database_name;

-- Create database with specific owner
CREATE DATABASE database_name OWNER username;

-- Create database with character set
CREATE DATABASE database_name ENCODING 'UTF8' OWNER username;

-- Create database from template
CREATE DATABASE new_db TEMPLATE template0;

-- Create with connection limit
CREATE DATABASE database_name CONNECTION LIMIT 100;
```

## 5.7 Dropping Databases

```sql
-- Drop a database
DROP DATABASE database_name;

-- Drop only if exists
DROP DATABASE IF EXISTS database_name;

-- Drop database and disconnect all users
SELECT pg_terminate_backend(pid) FROM pg_stat_activity 
WHERE datname = 'database_name';
DROP DATABASE database_name;
```

## 5.8 Listing Databases

```sql
-- In psql
\l

-- Or using SQL
SELECT datname, pg_database.datdba, datacl FROM pg_database;

-- Get database size
SELECT datname, pg_size_pretty(pg_database_size(datname)) 
FROM pg_database 
ORDER BY pg_database_size(datname) DESC;
```

## 5.9 Granting Privileges

```sql
-- Grant all privileges on database to user
GRANT ALL PRIVILEGES ON DATABASE database_name TO username;

-- Grant specific privileges on database
GRANT CONNECT, CREATE ON DATABASE database_name TO username;

-- Grant all privileges on all tables in schema
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO username;

-- Grant privileges on specific table
GRANT SELECT, INSERT, UPDATE ON table_name TO username;

-- Grant with GRANT OPTION (user can grant to others)
GRANT SELECT ON table_name TO username WITH GRANT OPTION;

-- Grant privileges on sequences (for SERIAL)
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO username;

-- Grant execute on functions
GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA public TO username;
```

## 5.10 Revoking Privileges

```sql
-- Revoke all privileges on database
REVOKE ALL PRIVILEGES ON DATABASE database_name FROM username;

-- Revoke specific privileges
REVOKE SELECT, INSERT ON table_name FROM username;

-- Revoke from all tables
REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA public FROM username;

-- Revoke with CASCADE (revoke from users who got it from this user)
REVOKE GRANT OPTION FOR SELECT ON table_name FROM username CASCADE;
```

## 5.11 Practical Example: Set up a Complete Database

```sql
-- 1. Create database
CREATE DATABASE company_db OWNER postgres;

-- 2. Create users
CREATE USER app_user WITH PASSWORD 'app_secure_123';
CREATE USER report_user WITH PASSWORD 'report_secure_123';
CREATE USER admin_user WITH PASSWORD 'admin_secure_123' CREATEDB;

-- 3. Connect to the database
\c company_db

-- 4. Create tables
CREATE TABLE employees (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE,
  salary DECIMAL(10,2),
  hire_date DATE
);

CREATE TABLE projects (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  budget DECIMAL(15,2),
  start_date DATE,
  end_date DATE
);

-- 5. Grant privileges to app_user (can read/write data)
GRANT CONNECT ON DATABASE company_db TO app_user;
GRANT USAGE ON SCHEMA public TO app_user;
GRANT ALL ON ALL TABLES IN SCHEMA public TO app_user;
GRANT ALL ON ALL SEQUENCES IN SCHEMA public TO app_user;

-- 6. Grant limited privileges to report_user (read-only)
GRANT CONNECT ON DATABASE company_db TO report_user;
GRANT USAGE ON SCHEMA public TO report_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO report_user;

-- 7. Verify permissions
\du
\l
SELECT grantee, privilege_type FROM table_privileges 
WHERE table_name='employees';
```

---

# Chapter 6: Advanced Topics

## 6.1 Indexes

Indexes speed up queries by creating a fast lookup structure. Without indexes, PostgreSQL must scan every row.

```sql
-- Create a simple index
CREATE INDEX idx_email ON employees(email);

-- Create a unique index
CREATE UNIQUE INDEX idx_unique_email ON employees(email);

-- Create a composite index (on multiple columns)
CREATE INDEX idx_dept_salary ON employees(department_id, salary);

-- Create an index on expression
CREATE INDEX idx_upper_firstname ON employees(UPPER(first_name));

-- View all indexes
SELECT indexname FROM pg_indexes WHERE tablename = 'employees';

-- Drop an index
DROP INDEX idx_email;

-- Analyze index usage
EXPLAIN ANALYZE SELECT * FROM employees WHERE email = 'john@example.com';
```

## 6.2 Constraints

```sql
-- PRIMARY KEY - unique identifier
CREATE TABLE users (
  user_id SERIAL PRIMARY KEY,
  username VARCHAR(50)
);

-- UNIQUE - all values unique
CREATE TABLE emails (
  id SERIAL PRIMARY KEY,
  email VARCHAR(100) UNIQUE NOT NULL
);

-- FOREIGN KEY - references another table
CREATE TABLE orders (
  order_id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(user_id),
  order_date DATE NOT NULL
);

-- CHECK - ensure value meets condition
CREATE TABLE products (
  product_id SERIAL PRIMARY KEY,
  price DECIMAL(10,2) CHECK (price > 0),
  quantity INTEGER CHECK (quantity >= 0)
);

-- NOT NULL - value required
CREATE TABLE employees (
  employee_id SERIAL PRIMARY KEY,
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL
);

-- DEFAULT - default value
CREATE TABLE posts (
  post_id SERIAL PRIMARY KEY,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 6.3 Views

A view is a virtual table based on a query result.

```sql
-- Create a simple view
CREATE VIEW employee_summary AS
SELECT first_name, last_name, department_id, salary
FROM employees
WHERE salary > 70000;

-- Query the view
SELECT * FROM employee_summary;

-- Create a view with joins
CREATE VIEW department_avg_salary AS
SELECT d.department_name, AVG(e.salary) as avg_salary, COUNT(e.employee_id) as emp_count
FROM employees e
JOIN departments d ON e.department_id = d.department_id
GROUP BY d.department_name;

-- Query the joined view
SELECT * FROM department_avg_salary;

-- Drop a view
DROP VIEW employee_summary;

-- Drop with dependent views
DROP VIEW IF EXISTS employee_summary CASCADE;
```

## 6.4 Transactions

Transactions ensure data integrity by allowing multiple operations to succeed or fail as a unit.

```sql
-- Basic transaction
BEGIN;
UPDATE employees SET salary = 80000 WHERE employee_id = 1;
UPDATE employees SET salary = 75000 WHERE employee_id = 2;
COMMIT;

-- Rollback transaction
BEGIN;
UPDATE employees SET salary = 90000 WHERE employee_id = 1;
ROLLBACK;  -- Changes are not saved

-- Transaction with error handling
BEGIN;
  UPDATE employees SET salary = 85000 WHERE employee_id = 1;
  INSERT INTO salary_audit (employee_id, old_salary, new_salary) 
  VALUES (1, 75000, 85000);
COMMIT;

-- Savepoint (can rollback to specific point)
BEGIN;
UPDATE employees SET salary = 80000 WHERE employee_id = 1;
SAVEPOINT sp1;
UPDATE employees SET salary = 90000 WHERE employee_id = 2;
ROLLBACK TO sp1;  -- Only rollback changes after sp1
COMMIT;
```

## 6.5 Functions and Stored Procedures

```sql
-- Create a function that returns a scalar value
CREATE FUNCTION get_employee_salary(emp_id INTEGER)
RETURNS DECIMAL AS $$
BEGIN
  RETURN salary FROM employees WHERE employee_id = emp_id;
END;
$$ LANGUAGE plpgsql;

-- Create function returning a table
CREATE FUNCTION get_employees_by_dept(dept_id INTEGER)
RETURNS TABLE(emp_id INTEGER, emp_name VARCHAR, emp_salary DECIMAL) AS $$
BEGIN
  RETURN QUERY
  SELECT employee_id, first_name, salary
  FROM employees
  WHERE department_id = dept_id;
END;
$$ LANGUAGE plpgsql;

-- Call a function
SELECT get_employee_salary(1);
SELECT * FROM get_employees_by_dept(2);

-- Drop a function
DROP FUNCTION get_employee_salary(INTEGER);
```

## 6.6 Triggers

```sql
-- Create a table to track changes
CREATE TABLE salary_audit (
  audit_id SERIAL PRIMARY KEY,
  employee_id INTEGER,
  old_salary DECIMAL,
  new_salary DECIMAL,
  changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create trigger function
CREATE FUNCTION audit_salary_changes()
RETURNS TRIGGER AS $$
BEGIN
  IF NEW.salary <> OLD.salary THEN
    INSERT INTO salary_audit (employee_id, old_salary, new_salary)
    VALUES (NEW.employee_id, OLD.salary, NEW.salary);
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Create trigger
CREATE TRIGGER salary_change_trigger
AFTER UPDATE ON employees
FOR EACH ROW
EXECUTE FUNCTION audit_salary_changes();

-- Test the trigger
UPDATE employees SET salary = 85000 WHERE employee_id = 1;

-- View audit log
SELECT * FROM salary_audit;

-- Drop trigger
DROP TRIGGER salary_change_trigger ON employees;
```

## 6.7 Backing Up and Restoring

```bash
# Backup entire database using pg_dump
pg_dump -U postgres database_name > backup.sql

# Backup with verbose output
pg_dump -U postgres -v database_name > backup.sql

# Backup in custom format (compressed)
pg_dump -U postgres -Fc database_name > backup.dump

# Backup specific table
pg_dump -U postgres -t table_name database_name > table_backup.sql

# Restore from SQL file
psql -U postgres database_name < backup.sql

# Restore from custom format
pg_restore -U postgres -d database_name backup.dump

# Restore specific table
pg_restore -U postgres -d database_name -t table_name backup.dump

# Backup all databases
pg_dumpall -U postgres > all_databases.sql

# Restore all databases
psql -U postgres < all_databases.sql
```

## 6.8 Performance Tuning

```sql
-- Check query execution plan
EXPLAIN SELECT * FROM employees WHERE salary > 75000;

-- Detailed execution plan
EXPLAIN ANALYZE SELECT * FROM employees WHERE salary > 75000;

-- View database statistics
SELECT schemaname, tablename, attname, null_frac, n_distinct
FROM pg_stats
WHERE tablename = 'employees';

-- Update table statistics
ANALYZE employees;

-- Vacuum removes dead rows
VACUUM employees;

-- Vacuum with analysis
VACUUM ANALYZE employees;

-- View slow queries (requires slow query log enabled)
-- Add to postgresql.conf:
-- log_min_duration_statement = 1000  -- Log queries taking > 1000ms

-- Check active connections
SELECT pid, usename, application_name, state
FROM pg_stat_activity;

-- Kill a long-running query
SELECT pg_terminate_backend(pid) FROM pg_stat_activity
WHERE pid <> pg_backend_pid() AND state = 'active';
```

## 6.9 JSON Support

```sql
-- Create table with JSON column
CREATE TABLE user_metadata (
  user_id SERIAL PRIMARY KEY,
  data JSON
);

-- Insert JSON data
INSERT INTO user_metadata (data) VALUES 
  ('{"name": "John", "age": 30, "city": "New York"}');

-- Query JSON data
SELECT data->>'name' as name, data->>'age' as age 
FROM user_metadata;

-- Update JSON data
UPDATE user_metadata 
SET data = jsonb_set(data, '{age}', '31')
WHERE user_id = 1;

-- Check if key exists
SELECT * FROM user_metadata 
WHERE data ? 'name';

-- Filter by JSON value
SELECT * FROM user_metadata 
WHERE data->>'age' = '30';
```

## 6.10 Common Errors and Solutions

### Error: "FATAL: Ident authentication failed"

**Solution:** Edit pg_hba.conf and change 'ident' to 'md5' or 'password'

```bash
# Find pg_hba.conf location
SHOW hba_file;

# Edit the file and restart PostgreSQL
# Windows: restart from Services
# Linux: sudo systemctl restart postgresql
```

### Error: "Cannot drop database, still has 1 open connection"

```sql
-- Solution: Disconnect all users first
SELECT pg_terminate_backend(pid) FROM pg_stat_activity 
WHERE datname = 'database_name' AND pid <> pg_backend_pid();
DROP DATABASE database_name;
```

### Error: "Permission denied for schema public"

```sql
-- Solution: Grant schema permissions
GRANT USAGE ON SCHEMA public TO username;
GRANT ALL ON ALL TABLES IN SCHEMA public TO username;
```

---

# Conclusion

This comprehensive guide has covered the essential and advanced aspects of PostgreSQL. Here's a quick reference for what you've learned:

- SQL fundamentals and PSQL command-line interface
- Creating tables with various data types and constraints
- Writing queries with SELECT, WHERE, JOIN, and aggregate functions
- Installing PostgreSQL on Windows, Linux, and macOS
- Managing users, databases, and permissions
- Using pgAdmin for graphical database management
- Advanced features like indexes, views, triggers, and transactions
- Backing up, restoring, and optimizing databases

PostgreSQL is a robust and feature-rich database system. Continue practicing these concepts and exploring more advanced features to become a proficient PostgreSQL developer and administrator.

Happy querying!

# PostgreSQL Practical Reference Guide
## Hands-On Examples & Exercises

---

## Part 1: Quick Reference Commands

### Connection Commands
```bash
# Connect as postgres user
psql -U postgres

# Connect to specific database
psql -U postgres -d mydb

# Connect to remote host
psql -h 192.168.1.100 -U username -d database -p 5432

# List all databases
\l

# Switch database
\c database_name

# Quit psql
\q
```

### User Management Quick Reference
```sql
-- Create user
CREATE USER newuser WITH PASSWORD 'secure_password';

-- List all users
\du

-- Change password
ALTER USER username WITH PASSWORD 'newpass';

-- Make superuser
ALTER USER username SUPERUSER;

-- Delete user
DROP USER username;
```

### Database Operations Quick Reference
```sql
-- Create database
CREATE DATABASE mydb;

-- List databases
\l

-- Drop database
DROP DATABASE mydb;

-- Database size
SELECT pg_size_pretty(pg_database_size('mydb'));

-- List tables
\dt
```

---

## Part 2: Complete Working Example

### Scenario: Building a School Management Database

#### Step 1: Create Database and Users

```sql
-- Create database
CREATE DATABASE school_management OWNER postgres;

-- Create users
CREATE USER school_admin WITH PASSWORD 'admin_pass_123' CREATEDB;
CREATE USER teacher WITH PASSWORD 'teacher_pass_123';
CREATE USER student WITH PASSWORD 'student_pass_123';

-- Connect to new database
\c school_management
```

#### Step 2: Create Tables

```sql
-- Create students table
CREATE TABLE students (
  student_id SERIAL PRIMARY KEY,
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  phone VARCHAR(20),
  date_of_birth DATE,
  enrollment_date DATE DEFAULT CURRENT_DATE,
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create teachers table
CREATE TABLE teachers (
  teacher_id SERIAL PRIMARY KEY,
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  specialization VARCHAR(100),
  hire_date DATE NOT NULL,
  salary DECIMAL(10, 2),
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create subjects table
CREATE TABLE subjects (
  subject_id SERIAL PRIMARY KEY,
  subject_name VARCHAR(100) NOT NULL UNIQUE,
  description TEXT,
  credits INTEGER CHECK (credits > 0),
  teacher_id INTEGER REFERENCES teachers(teacher_id)
);

-- Create classes table
CREATE TABLE classes (
  class_id SERIAL PRIMARY KEY,
  class_name VARCHAR(50) NOT NULL UNIQUE,
  subject_id INTEGER REFERENCES subjects(subject_id),
  teacher_id INTEGER REFERENCES teachers(teacher_id),
  schedule VARCHAR(200),
  max_capacity INTEGER CHECK (max_capacity > 0),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create enrollments (junction table for students and classes)
CREATE TABLE enrollments (
  enrollment_id SERIAL PRIMARY KEY,
  student_id INTEGER NOT NULL REFERENCES students(student_id) ON DELETE CASCADE,
  class_id INTEGER NOT NULL REFERENCES classes(class_id) ON DELETE CASCADE,
  enrollment_date DATE DEFAULT CURRENT_DATE,
  grade CHAR(1) CHECK (grade IN ('A', 'B', 'C', 'D', 'F')),
  is_active BOOLEAN DEFAULT TRUE,
  UNIQUE(student_id, class_id)
);

-- Create attendance table
CREATE TABLE attendance (
  attendance_id SERIAL PRIMARY KEY,
  student_id INTEGER NOT NULL REFERENCES students(student_id),
  class_id INTEGER NOT NULL REFERENCES classes(class_id),
  attendance_date DATE NOT NULL,
  is_present BOOLEAN NOT NULL,
  notes VARCHAR(200)
);
```

#### Step 3: Insert Sample Data

```sql
-- Insert teachers
INSERT INTO teachers (first_name, last_name, email, specialization, hire_date, salary)
VALUES 
  ('John', 'Smith', 'john.smith@school.com', 'Mathematics', '2020-01-15', 65000),
  ('Mary', 'Johnson', 'mary.johnson@school.com', 'Physics', '2019-08-20', 68000),
  ('Robert', 'Williams', 'robert.williams@school.com', 'English', '2021-02-10', 62000),
  ('Sarah', 'Brown', 'sarah.brown@school.com', 'Chemistry', '2020-06-15', 67000);

-- Insert subjects
INSERT INTO subjects (subject_name, description, credits, teacher_id)
VALUES
  ('Mathematics 101', 'Basic mathematics and algebra', 3, 1),
  ('Physics 101', 'Introduction to physics', 4, 2),
  ('English Literature', 'Classic literature analysis', 3, 3),
  ('Chemistry 101', 'Basic chemistry principles', 4, 4);

-- Insert classes
INSERT INTO classes (class_name, subject_id, teacher_id, schedule, max_capacity)
VALUES
  ('Math-A', 1, 1, 'MWF 9:00-10:00 AM', 30),
  ('Math-B', 1, 1, 'TTh 2:00-3:30 PM', 28),
  ('Physics-A', 2, 2, 'MWF 10:00-11:30 AM', 25),
  ('English-A', 3, 3, 'TTh 9:00-10:30 AM', 32),
  ('Chemistry-A', 4, 4, 'MWF 1:00-2:30 PM', 24);

-- Insert students
INSERT INTO students (first_name, last_name, email, phone, date_of_birth)
VALUES
  ('Alice', 'Davis', 'alice.davis@student.com', '555-0001', '2006-03-15'),
  ('Bob', 'Miller', 'bob.miller@student.com', '555-0002', '2005-07-22'),
  ('Charlie', 'Wilson', 'charlie.wilson@student.com', '555-0003', '2006-11-10'),
  ('Diana', 'Moore', 'diana.moore@student.com', '555-0004', '2006-05-18'),
  ('Edward', 'Taylor', 'edward.taylor@student.com', '555-0005', '2005-12-03'),
  ('Fiona', 'Anderson', 'fiona.anderson@student.com', '555-0006', '2006-08-27'),
  ('George', 'Thomas', 'george.thomas@student.com', '555-0007', '2005-09-14'),
  ('Hannah', 'Jackson', 'hannah.jackson@student.com', '555-0008', '2006-02-20');

-- Insert enrollments
INSERT INTO enrollments (student_id, class_id, grade)
VALUES
  (1, 1, 'A'), (1, 3, 'B'),
  (2, 1, 'B'), (2, 4, 'A'),
  (3, 2, 'C'), (3, 5, 'B'),
  (4, 1, 'A'), (4, 3, 'A'),
  (5, 2, 'B'), (5, 4, 'B'),
  (6, 1, 'B'), (6, 5, 'A'),
  (7, 4, 'C'), (7, 3, 'B'),
  (8, 2, 'A'), (8, 5, 'B');

-- Insert attendance records
INSERT INTO attendance (student_id, class_id, attendance_date, is_present, notes)
VALUES
  (1, 1, '2024-03-01', TRUE, NULL),
  (1, 1, '2024-03-03', TRUE, NULL),
  (1, 1, '2024-03-05', FALSE, 'Sick'),
  (2, 1, '2024-03-01', TRUE, NULL),
  (2, 1, '2024-03-03', FALSE, 'Late arrival'),
  (3, 2, '2024-03-02', TRUE, NULL),
  (4, 1, '2024-03-01', TRUE, NULL);
```

#### Step 4: Query Examples

```sql
-- Get all students with their enrollments
SELECT s.first_name, s.last_name, c.class_name, e.grade
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN classes c ON e.class_id = c.class_id
ORDER BY s.last_name;

-- Get average grade per student
SELECT s.first_name, s.last_name, AVG(CAST(ASCII(e.grade) AS DECIMAL)) as avg_grade_value
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
GROUP BY s.student_id, s.first_name, s.last_name
ORDER BY avg_grade_value DESC;

-- Get attendance rate per student
SELECT 
  s.first_name, s.last_name,
  COUNT(*) as total_classes,
  SUM(CASE WHEN a.is_present THEN 1 ELSE 0 END) as classes_attended,
  ROUND(100.0 * SUM(CASE WHEN a.is_present THEN 1 ELSE 0 END) / COUNT(*), 2) as attendance_percentage
FROM students s
LEFT JOIN attendance a ON s.student_id = a.student_id
GROUP BY s.student_id, s.first_name, s.last_name
ORDER BY attendance_percentage DESC;

-- Get teachers and their students count
SELECT 
  t.first_name, t.last_name,
  COUNT(DISTINCT e.student_id) as total_students
FROM teachers t
LEFT JOIN subjects sub ON t.teacher_id = sub.teacher_id
LEFT JOIN classes c ON t.teacher_id = c.teacher_id
LEFT JOIN enrollments e ON c.class_id = e.class_id
GROUP BY t.teacher_id, t.first_name, t.last_name
ORDER BY total_students DESC;

-- Get students enrolled in multiple classes
SELECT s.first_name, s.last_name, COUNT(e.class_id) as classes_enrolled
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
GROUP BY s.student_id, s.first_name, s.last_name
HAVING COUNT(e.class_id) > 1
ORDER BY classes_enrolled DESC;

-- Get top performing students
SELECT 
  s.first_name, s.last_name,
  STRING_AGG(c.class_name || '(' || e.grade || ')', ', ') as grades
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN classes c ON e.class_id = c.class_id
WHERE e.grade IN ('A', 'B')
GROUP BY s.student_id, s.first_name, s.last_name
ORDER BY s.last_name;

-- Get students with attendance issues
SELECT 
  s.first_name, s.last_name,
  ROUND(100.0 * SUM(CASE WHEN a.is_present THEN 1 ELSE 0 END) / COUNT(*), 2) as attendance_percentage
FROM students s
JOIN attendance a ON s.student_id = a.student_id
GROUP BY s.student_id, s.first_name, s.last_name
HAVING ROUND(100.0 * SUM(CASE WHEN a.is_present THEN 1 ELSE 0 END) / COUNT(*), 2) < 80
ORDER BY attendance_percentage;
```

#### Step 5: Create Useful Views

```sql
-- Student transcript view
CREATE VIEW student_transcripts AS
SELECT 
  s.student_id,
  s.first_name || ' ' || s.last_name as student_name,
  c.class_name,
  sub.subject_name,
  e.grade,
  e.enrollment_date
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN classes c ON e.class_id = c.class_id
JOIN subjects sub ON c.subject_id = sub.subject_id
WHERE e.is_active = TRUE
ORDER BY s.student_id, e.enrollment_date;

-- Class roster view
CREATE VIEW class_rosters AS
SELECT 
  c.class_name,
  t.first_name || ' ' || t.last_name as teacher_name,
  s.first_name || ' ' || s.last_name as student_name,
  e.grade,
  e.enrollment_date
FROM classes c
JOIN teachers t ON c.teacher_id = t.teacher_id
JOIN enrollments e ON c.class_id = e.class_id
JOIN students s ON e.student_id = s.student_id
WHERE e.is_active = TRUE
ORDER BY c.class_name, s.last_name;

-- Attendance summary view
CREATE VIEW attendance_summary AS
SELECT 
  s.first_name || ' ' || s.last_name as student_name,
  c.class_name,
  COUNT(*) as total_sessions,
  SUM(CASE WHEN a.is_present THEN 1 ELSE 0 END) as sessions_attended,
  ROUND(100.0 * SUM(CASE WHEN a.is_present THEN 1 ELSE 0 END) / COUNT(*), 2) as attendance_percentage
FROM students s
JOIN attendance a ON s.student_id = a.student_id
JOIN classes c ON a.class_id = c.class_id
GROUP BY s.student_id, s.first_name, s.last_name, c.class_name
ORDER BY s.last_name, c.class_name;
```

#### Step 6: Create Stored Procedures

```sql
-- Procedure to enroll a student in a class
CREATE FUNCTION enroll_student(p_student_id INTEGER, p_class_id INTEGER)
RETURNS TABLE(success BOOLEAN, message TEXT) AS $$
DECLARE
  v_current_enrollment INTEGER;
  v_class_capacity INTEGER;
BEGIN
  -- Check if student already enrolled
  SELECT COUNT(*) INTO v_current_enrollment
  FROM enrollments
  WHERE student_id = p_student_id AND class_id = p_class_id;
  
  IF v_current_enrollment > 0 THEN
    RETURN QUERY SELECT FALSE, 'Student already enrolled in this class';
    RETURN;
  END IF;
  
  -- Check class capacity
  SELECT c.max_capacity INTO v_class_capacity FROM classes c WHERE c.class_id = p_class_id;
  
  IF (SELECT COUNT(*) FROM enrollments WHERE class_id = p_class_id) >= v_class_capacity THEN
    RETURN QUERY SELECT FALSE, 'Class is full';
    RETURN;
  END IF;
  
  -- Enroll student
  INSERT INTO enrollments (student_id, class_id) VALUES (p_student_id, p_class_id);
  RETURN QUERY SELECT TRUE, 'Student enrolled successfully';
END;
$$ LANGUAGE plpgsql;

-- Use the procedure
SELECT * FROM enroll_student(1, 4);
```

#### Step 7: Set Up Permissions

```sql
-- Grant permissions to teacher role
GRANT CONNECT ON DATABASE school_management TO teacher;
GRANT USAGE ON SCHEMA public TO teacher;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO teacher;
GRANT SELECT ON ALL VIEWS IN SCHEMA public TO teacher;
GRANT INSERT, UPDATE ON students, attendance TO teacher;

-- Grant permissions to student role (read-only)
GRANT CONNECT ON DATABASE school_management TO student;
GRANT USAGE ON SCHEMA public TO student;
GRANT SELECT ON student_transcripts, class_rosters, attendance_summary TO student;

-- Grant admin permissions
GRANT ALL PRIVILEGES ON DATABASE school_management TO school_admin;
```

---

## Part 3: Practice Exercises

### Exercise 1: Basic Operations
1. Create a table called `departments` with columns: dept_id (PRIMARY KEY), dept_name (NOT NULL, UNIQUE), budget (DECIMAL)
2. Insert 5 departments
3. Display all departments sorted by budget (highest first)
4. Update the budget of one department
5. Delete a department with no employees

### Exercise 2: Joins and Aggregation
1. Create an `employees` table with: emp_id (PK), name, salary, dept_id (FK to departments)
2. Insert 10 employees across different departments
3. Find the average salary per department
4. Find employees earning more than the department average
5. List departments with more than 2 employees

### Exercise 3: Subqueries and Advanced Queries
1. Find students who took more classes than the average
2. Get all classes taught by teachers with salary > 65000
3. Find students with 100% attendance
4. List classes that have no students enrolled
5. Get the top 3 students by average grade

### Exercise 4: Views and Indexes
1. Create a view showing all active enrollments
2. Create an index on student email for faster lookups
3. Demonstrate query performance difference with and without index
4. Create a view combining data from 3 tables
5. Update the view

### Exercise 5: Transactions and Data Integrity
1. Create a transaction that transfers a student from one class to another
2. Test rollback functionality
3. Create a trigger that logs class enrollment changes
4. Test the trigger
5. Create a transaction that fails and verify rollback

---

## Part 4: Common Patterns & Best Practices

### Pattern 1: Soft Deletes (Instead of deleting, mark as inactive)
```sql
-- Table with is_active flag
CREATE TABLE archive_example (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  is_active BOOLEAN DEFAULT TRUE,
  deleted_at TIMESTAMP
);

-- Create view for active records only
CREATE VIEW active_records AS
SELECT * FROM archive_example WHERE is_active = TRUE;

-- "Delete" by marking inactive
UPDATE archive_example SET is_active = FALSE, deleted_at = CURRENT_TIMESTAMP WHERE id = 1;
```

### Pattern 2: Audit Trail
```sql
-- Audit table
CREATE TABLE audit_log (
  audit_id SERIAL PRIMARY KEY,
  table_name VARCHAR(100),
  operation VARCHAR(10),
  record_id INTEGER,
  old_values JSONB,
  new_values JSONB,
  changed_by VARCHAR(100),
  changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Trigger function for auditing
CREATE FUNCTION audit_trigger()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO audit_log (table_name, operation, record_id, new_values, changed_by)
  VALUES (TG_TABLE_NAME, TG_OP, NEW.id, row_to_json(NEW), CURRENT_USER);
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

### Pattern 3: Full-Text Search
```sql
-- Create table with search column
CREATE TABLE articles (
  article_id SERIAL PRIMARY KEY,
  title VARCHAR(200),
  content TEXT,
  search_index tsvector
);

-- Create index for fast searches
CREATE INDEX idx_article_search ON articles USING gin(search_index);

-- Search articles
SELECT * FROM articles 
WHERE search_index @@ to_tsquery('english', 'database | search');
```

### Pattern 4: Hierarchical Data (Tree/Graph)
```sql
-- Category with parent-child relationship
CREATE TABLE categories (
  category_id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  parent_id INTEGER REFERENCES categories(category_id),
  CONSTRAINT no_self_reference CHECK (category_id != parent_id)
);

-- Query to get hierarchy
WITH RECURSIVE category_tree AS (
  SELECT category_id, name, parent_id, 0 as level
  FROM categories
  WHERE parent_id IS NULL
  UNION ALL
  SELECT c.category_id, c.name, c.parent_id, ct.level + 1
  FROM categories c
  JOIN category_tree ct ON c.parent_id = ct.category_id
)
SELECT * FROM category_tree ORDER BY level, name;
```

### Pattern 5: Pagination
```sql
-- Efficient pagination
SELECT * FROM large_table
ORDER BY id
LIMIT 20 OFFSET 40;  -- Page 3 (assuming 20 items per page)

-- Better for large offsets
SELECT * FROM large_table
WHERE id > (SELECT MAX(id) FROM large_table LIMIT 40)
ORDER BY id
LIMIT 20;
```

---

## Part 5: Performance Tips

```sql
-- 1. Use EXPLAIN ANALYZE to understand queries
EXPLAIN ANALYZE SELECT * FROM students WHERE last_name = 'Smith';

-- 2. Create indexes on frequently searched columns
CREATE INDEX idx_student_email ON students(email);

-- 3. Use VACUUM regularly
VACUUM ANALYZE students;

-- 4. Archive old data
CREATE TABLE students_archive AS
SELECT * FROM students WHERE enrollment_date < '2020-01-01';

-- 5. Monitor slow queries
SELECT query, mean_exec_time FROM pg_stat_statements
ORDER BY mean_exec_time DESC LIMIT 10;

-- 6. Use LIMIT for exploration
SELECT * FROM large_table LIMIT 10;

-- 7. Batch operations
BEGIN;
INSERT INTO table VALUES (...);
INSERT INTO table VALUES (...);
COMMIT;

-- 8. Use column selection instead of SELECT *
SELECT first_name, last_name, email FROM students;  -- Good
SELECT * FROM students;  -- Less efficient for large tables
```

---

## Part 6: Backup and Recovery Commands

```bash
# Backup specific database
pg_dump -U postgres school_management > school_backup_$(date +%Y%m%d).sql

# Backup all databases
pg_dumpall -U postgres > all_databases_$(date +%Y%m%d).sql

# Backup in custom format (compressed)
pg_dump -U postgres -Fc school_management > school_backup.dump

# Restore
psql -U postgres school_management < school_backup_20240101.sql

# Restore from custom format
pg_restore -U postgres -d school_management school_backup.dump

# Incremental backup using backup label (for production)
SELECT pg_start_backup('backup_label');
-- Copy data files
SELECT pg_stop_backup();
```

---

Happy learning! Use this guide as a reference and hands-on companion to master PostgreSQL.

