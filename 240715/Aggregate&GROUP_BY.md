# 집계 함수 및 GROUP BY
집계함수는 여러 행의 데이터를 요약하여 하나의 결과를 반환하는 함수입니다.
GROUP BY와 함께 사용하면 데이터를 그룹화하고 각 그룹에 대한 요약 정보를 얻을 수 있습니다.

1. 주요 집계 함수
   - COUNT(): 행의 개수를 반환
   - SUM(): 숫자 컬럼의 합계를 계산
   - AVG(): 숫자 컬럼의 평균을 계산
   - MAX(): 컬럼의 최대값을 반환
   - MIN(): 컬럼의 최소값을 반환
2. GROUP BY
   - 지정된 컬럼의 고유한 값을 기준으로 행을 그룹화합니다.
   ```sql
    SELECT column1, column2, aggregate_function(column3)
    FROM table_name
    GROUP BY column1, column2;
   
    SELECT department_id, AVG(salary) as avg_salary
    FROM employees
    GROUP BY department_id;
   ```
3. HAVING 절
   - HAVIN은 GROUP BY의 결과에 조건을 적용할 때 사용됩니다. WHERE가 개별행에 조건을 적용하는 반면, HAVING은 그룹화된 결과에 조건을 적용합니다.
   ```sql
    SELECT department_id, AVG(salary) as avg_salary
    FROM employees
    GROUP BY department_id
    HAVING AVG(salary) > 50000;
   ```
4. 복합 
   - 여러 칼럼을 기준으로 그룹화할 수 있습니다.
   ```sql
    SELECT department_id, job_title, COUNT(*) as employee_count
    FROM employees
    GROUP BY department_id, job_title;
   ```
5. WITH ROLLUP
   - 그룹화된 결과에 대한 총계를 추가로 계산합니다.
   ```sql
    SELECT department_id, SUM(salary) as total_salary
    FROM employees
    GROUP BY department_id WITH ROLLUP;
   ```
   - 지정된 그룹화 컬럼들의 계층적 집계 결과를 제공합니다. 오른쪽에서 왼쪽으로 그룹화 컬럼을 하나씩 제거하면서 중간 집계와 총 집계를 생성합니다.
   - 요약 보고서 생성에 매우 유용합니다. 또한 각 레벨의 소계와 총계를 한 번의 쿼리로 얻을 수 있어 효율적입니다.
6. 주의사항
   - SELECT 절에 있는 모든 non-aggregate 칼럼은 GROUP BY 절에 포함되어야 합니다.
   - WHERE 절은 GROUP BY 전에 실행되므로, 집계 결과에 대한 조건은 HAVING을 사용해야 합니다.
