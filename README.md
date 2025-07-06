-- 📦 Drop old tables if they exist
DROP TABLE IF EXISTS employees;
DROP TABLE IF EXISTS departments;

-- 🏗 Create departments table
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100)
);

-- 🏗 Create employees table
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    department_id INT,
    salary DECIMAL(10, 2),
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);

-- 📥 Insert sample data
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

-- 🧪 Create stored procedure to get employees above a certain salary
DELIMITER //

CREATE PROCEDURE get_high_earners(IN min_salary DECIMAL(10,2))
BEGIN
    SELECT name, salary
    FROM employees
    WHERE salary > min_salary;
END //

DELIMITER ;

-- ▶️ Example: Call the procedure
CALL get_high_earners(70000);

-- 🎯 Expected Output:
-- +---------+----------+
-- | name    | salary   |
-- +---------+----------+
-- | Alice   | 80000.00 |
-- | Charlie | 75000.00 |
-- | Emma    | 90000.00 |
-- +---------+----------+

-- 🧪 Create function to return average salary by department
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

-- ▶️ Example: Call the function
SELECT get_avg_salary(102) AS it_avg_salary;

-- 🎯 Expected Output:
-- +--------------+
-- | it_avg_salary|
-- +--------------+
-- | 75000.00     |
-- +--------------+

-- 🧪 Bonus: Use function for all departments
SELECT 
    d.department_name,
    get_avg_salary(d.department_id) AS avg_salary
FROM departments AS d;

-- 🎯 Expected Output:
-- +-----------------+-------------+
-- | department_name | avg_salary  |
-- +-----------------+-------------+
-- | HR              | 77500.00    |
-- | IT              | 75000.00    |
-- | Sales           | 50000.00    |
