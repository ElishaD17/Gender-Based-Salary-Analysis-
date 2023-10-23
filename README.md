# Gender-Based Salary Analysis using SQL and Tableau

---

## Introduction

This project examines gender discrepancies in salaries within a company, leveraging the power of SQL for data extraction and Tableau for visualization. The analysis uses a modified version of the familiar 'employees' database, enabling us to focus on intricate Business Intelligence tasks while working with a known dataset.

## Objectives

- Analyze the distribution of male and female employees within the company annually from 1990.
- Compare the number of male and female managers across different departments each year since 1990.
- Evaluate the average salary of male and female employees up to 2002, with department-wise granularity.
- Create an SQL stored procedure to retrieve the average salary of both genders within a user-defined salary range, and visualize this in Tableau.

## Technologies Used

- **Database**: MySQL (employees_mod database)
- **Visualization Tool**: Tableau
- **Languages**: SQL for data extraction and manipulation

## Steps to Reproduce

1. **Set Up the Database**:
   - Download the `employees_mod` database file.
   - Use MySQL Workbench to run the database script, thus setting up the `employees_mod` database.

2. **Data Extraction using SQL**:
   - For the distribution of male and female employees from 1990:
     ```sql
     SELECT 
         YEAR(d.from_date) AS calendar_year,
         e.gender,    
         COUNT(e.emp_no) AS num_of_employees
     FROM     
         t_employees e         
              JOIN    
         t_dept_emp d ON d.emp_no = e.emp_no
     GROUP BY calendar_year , e.gender 
     HAVING calendar_year >= 1990;
     ```

   - For comparing male and female managers across departments:
 SELECT 
    d.dept_name,
    ee.gender,
    dm.emp_no,
    dm.from_date,
    dm.to_date,
    e.calendar_year,
    CASE
        WHEN YEAR(dm.to_date) >= e.calendar_year AND YEAR(dm.from_date) <= e.calendar_year THEN 1
        ELSE 0
    END AS active
FROM
    (SELECT 
        YEAR(hire_date) AS calendar_year
    FROM
        t_employees
    GROUP BY calendar_year) e
        CROSS JOIN
    t_dept_manager dm
        JOIN
    t_departments d ON dm.dept_no = d.dept_no
        JOIN 
    t_employees ee ON dm.emp_no = ee.emp_no
ORDER BY dm.emp_no, calendar_year;

   - For comparing average salaries of male and female employees up to 2002:
    SELECT 
    e.gender,
    d.dept_name,
    ROUND(AVG(s.salary), 2) AS salary,
    YEAR(s.from_date) AS calendar_year
FROM
    t_salaries s
        JOIN
    t_employees e ON s.emp_no = e.emp_no
        JOIN
    t_dept_emp de ON de.emp_no = e.emp_no
        JOIN
    t_departments d ON d.dept_no = de.dept_no
GROUP BY d.dept_no , e.gender , calendar_year
HAVING calendar_year <= 2002
ORDER BY d.dept_no;

   - For extracting average salaries based on a user-defined range:
     DROP PROCEDURE IF EXISTS filter_salary;

DELIMITER $$
CREATE PROCEDURE filter_salary (IN p_min_salary FLOAT, IN p_max_salary FLOAT)
BEGIN
SELECT 
    e.gender, d.dept_name, AVG(s.salary) as avg_salary
FROM
    t_salaries s
        JOIN
    t_employees e ON s.emp_no = e.emp_no
        JOIN
    t_dept_emp de ON de.emp_no = e.emp_no
        JOIN
    t_departments d ON d.dept_no = de.dept_no
    WHERE s.salary BETWEEN p_min_salary AND p_max_salary
GROUP BY d.dept_no, e.gender;
END$$

DELIMITER ;

3. **Visualization in Tableau**:
   - Import the result sets from the SQL queries into Tableau.
   - Create individual visuals for each of the analyses.
   - Organize visuals into a cohesive dashboard, optimizing for clarity and user experience.

## Results

- Created a comprehensive dashboard in Tableau, offering insights into gender disparities within the company's salary structure.
- Identified departments with pronounced gender salary gaps.
- Assessed the gender distribution in managerial roles across the organization.

## Conclusion

This project successfully leveraged SQL and Tableau to shed light on gender-based salary discrepancies within the company. These insights are crucial for addressing potential inequalities and ensuring an inclusive work environment.

## Future Scope

- Integrate more variables, like performance metrics, to understand the complete picture of salary distributions.
- Examine other potential biases in salary, such as race or age.
