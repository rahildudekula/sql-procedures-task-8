-- 🚮 Drop if already exists (cleanup)
DROP TABLE IF EXISTS employees;
DROP TABLE IF EXISTS departments;

-- 🧱 Step 1: Create tables

CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100)
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    department_id INT,
    salary DECIMAL(10, 2),
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);

-- 📥 Step 2: Insert data

INSERT INTO departments VALUES 
(101, 'HR'),
(102, 'IT'),
(103, 'Sales');

INSERT INTO employees VALUES
(1, 'Alice', 101, 80000.00),
(2, 'Bob', 102, 60000.00),
(3, 'Charlie', 101, 75000.00),
(4, 'David', 103, 50000.00),
(5, 'Emma', 102, 90000.00);

-- 🧪 Step 3: Create stored procedure

DELIMITER //

CREATE PROCEDURE get_high_earners(IN min_salary DECIMAL(10,2))
BEGIN
    SELECT name, salary
    FROM employees
    WHERE salary > min_salary;
END //

DELIMITER ;

-- ▶️ Call stored procedure
CALL get_high_earners(70000);

-- ✅ Expected Output:
-- +---------+----------+
-- | name    | salary   |
-- +---------+----------+
-- | Alice   | 80000.00 |
-- | Charlie | 75000.00 |
-- | Emma    | 90000.00 |
-- +---------+----------+

-- 🧪 Step 4: Create function

DELIMITER //

CREATE FUNCTION get_avg_salary(dept_id INT)
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE avg_sal DECIMAL(10,2);
    SELECT AVG(salary) INTO avg_sal
    FROM employees
    WHERE department_id = dept_id;
    RETURN avg_sal;
END //

DELIMITER ;

-- ▶️ Call function
SELECT get_avg_salary(102) AS it_avg_salary;

-- ✅ Expected Output:
-- +--------------+
-- | it_avg_salary|
-- +--------------+
-- | 75000.00     |
-- +--------------+

-- ▶️ Bonus: View all department averages

SELECT 
    department_name,
    get_avg_salary(department_id) AS avg_salary
FROM departments;

-- ✅ Expected Output:
-- +-----------------+-------------+
-- | department_name | avg_salary  |
-- +-----------------+-------------+
-- | HR              | 77500.00    |
-- | IT              | 75000.00    |
-- | Sales           | 50000.00    |
-- +-----------------+-------------+
