# 서브쿼리 및 상관 서브 쿼리
서브쿼리는 다른 SQL문 내에 중첩된 SELECT 문입니다. 복잡한 조건이나 계산을 수행할 때 유용합니다.
1. 서브쿼리 유형
   - 스칼라 서브쿼리: 단일 값을 반환
   - 행 서브쿼리: 단일 행을 반환
   - 테이블 서브쿼리: 하나 이상의 행을 반환
2. 서브쿼리 위치에 따른 분류
   - WHERE 절 서브 쿼리
   - FROM 절 서브쿼리(파생 테이블)
   - SELECT 절 서브쿼리
3. 서브쿼리 예시
   - WHERE 절 서브쿼리
   ```sql
    SELECT product_name, price
    FROM products
    WHERE price > (SELECT AVG(price) FROM products);
   ```
   - FROM 절 서브쿼리(파생 테이블)
   ```sql
    SELCT dept_name, avg_salary
    FROM (
        SELECT department_id, AVG(salary) as avg_salary
        FROM employees
        GROUP BY department_id
    ) AS dept_salaries
    JOIN departments ON departments.id = dept_salaries.department_id;
   ```
   - SELECT 절 서브쿼리
   ```sql
    SELECT 
        employee_name,
        salary,
        (SELECT AVG(salary) FROM employees) AS company_avg_salary
    FROM employees;
   ```
    
4. 상관 서브쿼리
   - 상관 서브쿼리는 외부 쿼리의 값을 참조하는 서브쿼리입니다. 외부 쿼리의 각 행에 대해 서브쿼리가 실행됩니다.
   ```sql
    SELECT employee_name, salary
    FROM employees e 
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
        WHERE department_id = e.department_id
    );
   ```
