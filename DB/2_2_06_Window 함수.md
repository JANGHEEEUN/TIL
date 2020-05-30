# SQL 활용

## 6. 윈도우 함수

- SUM 함수

  ```SQL
  SELECT MGR, ENAME, SAL, SUM(SAL) OVER (PARTITION BY MGR) MGR_SUM
  FROM EMP;
  ```

  ```
  MGR ENAME SAL MGR_SUM 
  ---- ------ ---- ------- 
  7566 FORD 3000 6000 
  7566 SCOTT 3000 6000 
  7698 JAMES 950 6550 
  7698 ALLEN 1600 6550 
  7698 WARD 1250 6550 
  7698 TURNER 1500 6550 
  7698 MARTIN 1250 6550 
  7782 MILLER 1300 1300 
  7788 ADAMS 1100 1100 
  7839 BLAKE 2850 8275 
  7839 JONES 2975 8275 
  7839 CLARK 2450 8275 
  7902 SMITH 800 800 
        KING 5000 5000
  ```

- MAX 함수

  ```SQL
  SELECT MGR, ENAME, SAL, MAX(SAL) OVER (PARTITION BY MGR) AS MGR_MAX
  FROM EMP;	
  ```

  ```
  MGR ENAME SAL MGR_MAX 
  ---- ----- ---- ------- 
  7566 FORD 3000 3000 
  7566 SCOTT 3000 3000 
  7698 JAMES 950 1600 
  7698 ALLEN 1600 1600 
  7698 WARD 1250 1600 
  7698 TURNER 1500 1600 
  7698 MARTIN 1250 1600 
  7782 MILLER 1300 1300 
  7788 ADAMS 1100 1100 
  7839 BLAKE 2850 2975 
  7839 JONES 2975 2975 
  7839 CLARK 2450 2975 
  7902 SMITH 800 800 
  KING 5000 5000
  ```

  - 추가로, INLINE VIEW를 이용해 파티션별 최대값을 가진 행만 출력 가능

    ```SQL
    SELECT MGR, ENAME, SAL
    FROM (SELECT MGR, ENAME, SAL, MAX(SAL) OVER (PARTITION BY MGR) AS IV_MAX_SAL
          FROM EMP)
    WHERE SAL = IV_MAX_SAL;
    ```

    ```
    MGR ENAME SAL 
    ------ ------- ----- 
    7566 FORD 3000 
    7566  SCOTT 3000 
    7698 ALLEN 1600 
    7782 MILLER 1300 
    7788 ADAMS 1100 
    7839 JONES 2975 
    7902 SMITH 800 
         KING 5000
    ```

    MGR 7566의 SCOTT,FORD는 같은 최대값을 가지므로 두 건 모두 추출됨

- MIN 함수

  ```SQL
  SELECT MGR, ENAME, MIN(SAL) OVER (PARTITION BY MGR ORDER BY HIREDATE) AS MGR_MIN
  FROM EMP;
  ```

- AVG 함수

  AVG 함수와 파티션별 ROWS 윈도울르 통해 원하는 조건에 맞는 데이터에 대한 통계값 구할 수 있음

  ```SQL
  SELECT MGR, ENAME, HIREDATE, SAL, ROUND(AVG(SAL) OVER (PARTITION BY MGR ORDER BY HIREDATE ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING)) AS MGR_AVG
  FROM EMP;
  ```

  ```
  MGR ENAME HIREDATE SAL MGR_AVG 
  ---- ------ -------- ---- ------- 
  7566 FORD  1981-12-03 3000 3000 
  7566 SCOTT 1987-07-13 3000 3000 
  7698 ALLEN 1981-02-20 1600 1425 
  7698 WARD 1981-02-22 1250 1450 
  7698 TURNER 1981-09-08 1500 1333 
  7698 MARTIN 1981-09-28 1250 1233 
  7698 JAMES 1981-12-03 950 1100 
  7782 MILLER 1982-01-23 1300 1300 
  7788 ADAMS 1987-07-13 1100 1100 
  7839 JONES 1981-04-02 2975 2913 
  7839 BLAKE 1981-05-01 2850 2758 
  7839 CLARK 1981-06-09 2450 2650 
  7902 SMITH 1980-12-17 800 800 
       KING 1981-11-17 5000 5000
  ```

- COUNT 함수

  COUNT 함수와 파티션별 ROWS 윈도우를 이용해 원하는 조건에 맞는 데이터에 대한 통계값 도출 가능

  - 사원들을 급여 기준으로 정렬하고, 본인의 급여보다 50이하가 적거나 150 이하로 많은 급여를 받는 인원수를 출력

    ```SQL
    SELECT ENAME, SAL, COUNT(*) OVER (ORDER BY SAL RANGE BETWEEN 50 PRECEDING AND 150 FOLLOWING) AS SIM_CNT
    FROM EMP;
    ```

    ```
    ENAME SAL SIM_CNT ( 범위값 ) 
    ------ ---- ------ --------- 
    SMITH 800 2 ( 750~ 950)
    JAMES 950 2 ( 900~1100)
    ADAMS ** 1100 3 (1050~1250) 
    WARD 1250 3 (1200~1400)
    MARTIN 1250 3 (1200~1400) 
    MILLER 1300 3 (1250~1450) 
    TURNER 1500 2 (1450~1650)
    ALLEN 1600 1 (1550~1750)
    CLARK 2450 1 (2400~2600) 
    BLAKE 2850 4 (2800~3000) 
    JONES 2975 3 (2925~3125) 
    SCOTT 3000 3 (2950~3100) 
    FORD 3000 3 (2950~3100) 
    KING 5000 1 (4950~5100)
    ```



#### 4. 그룹 내 행 순서 함수

- FIRST_VALUE 함수

  파티션별 윈도우에서 가장 먼저 나온 값을 도출

  SQL SERVER에서는 지원하지 않음

  MIN 함수를 활용해 같은 결과 도출 가능

  - 예제 - 부서별 직원들을 연봉이 높은 순서부터 정렬, 파티션 내에 가장 먼저 나온 값을 출력

    ```SQL
    SELECT DEPTNO, ENAME, SAL, FIRST_VALUE(ENMAE) OVER (PARTITION BY DEPTNO ORDER BY SAL DESC ROWS UNBOUNDED PRECIDING) AS DEPT_RICH
    FROM EMP;
    ```

    ```
    DEPTNO ENAME SAL DEPT_RICH 
    ------ ------- ---- -------- 
    10 KING 5000 KING 
    10 CLARK 2450 KING 
    10 MILLER 1300 KING 
    20 SCOTT * 3000 SCOTT 
    20 FORD * 3000 SCOTT 
    20 JONES 2975 SCOTT 
    20 ADAMS 1100 SCOTT 
    20 SMITH 800 SCOTT 
    30 BLAKE 2850 BLAKE 
    30 ALLEN 1600 BLAKE 
    30 TURNER 1500 BLAKE 
    30 MARTIN 1250 BLAKE 
    30 WARD 1250 BLAKE 
    30 JAMES 950 BLAKE
    ```

    FIRST_VALUE는 공동 등수를 인정하지 않고 처음 나온 행만 처리

    공동 등수가 있을 경우, 의도적으로 세부 항목을 정렬하고 싶다면 별도의 정렬 조건을 가진 INLINE VIEW를 사용하거나 OVER() 내의 ORDER BY절에 칼럼을 추가해야 함

  - 예제 - 앞의 SQL 문장에서 같은 값을 가진 FIRST_VALUE를 처리하기 위해 ORDER BY 정렬 조건 추가

    ```SQL
    SELECT DEPTNO, ENAME, SAL, FIRST_VALUE(ENMAE) OVER (PARTITION BY DEPTNO ORDER BY SAL DESC, ENAME ASC ROWS UNBOUNDED PRECIDING) AS DEPT_RICH
    FROM EMP;
    ```

- LAST_VALUE 함수

  파티션별 윈도우에서 가장 나중에 나온 값을 구함

  SQL 서버에서는 지원하지 않음

  MAX 함수를 통해 같은 결과 도출 가능

  - 예제 - 부서별 직원들을 연봉이 높은 순서부터 정렬하고, 파티션 내에서 가장 마지막에 나온 값을 출력

    ```SQL
    SELECT DEPTNO, ENMAE, SAL, LAST_VALUE(ENAME) OVER (PARTITION BY DEPTNO ORDER BY SAL DESC ROWS BEWTWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS DEPT_POOR
    FROM EMP;
    ```

    ```
    DEPTNO ENAME SAL DEPT_POOR 
    ------ ------- ---- --------- 
    10 KING 5000 MILLER
    10 CLARK 2450 MILLER 
    10 MILLER 1300 MILLER 
    20 SCOTT 3000 SMITH 
    20 FORD 3000 SMITH 
    20 JONES 2975 SMITH 
    20 ADAMS 1100 SMITH 
    20 SMITH 800 SMITH 
    30 BLAKE 2850 JAMES 
    30 ALLEN 1600 JAMES 
    30 TURNER 1500 JAMES 
    30 MARTIN 1250 JAMES 
    30 WARD 1250 JAMES 
    30 JAMES 950 JAMES
    ```

    LAST_VALUE는 다른 함수와 달리 공동 등수를 인정하지 않고 가장 나중에 나온 행만을 처리

    공동 등수가 있을 경우, 의도적으로 세부 항목을 정렬하고 싶다면 별도의 정렬 조건을 가진 INLINE VIEW를 사용하거나 OVER() 내의 ORDER BY절에 칼럼을 추가해야 함

- LAG 함수

  LAG 함수를 통해 파티션별 윈도우에서 이전 몇 번째 행의 값을 가져올 수 있음

  SQL SERVER는 지원하지 않음

  - 예제 - 직원들을 입사일자가 빠른 기준으로 정렬하고, 본인보다 입사일자가 한  명 앞선 사원의 급여를 본인의 급여와 함께 출력

    ```SQL
    SELECT ENAME, HIREDATE, SAL, LAG(SAL,2,0) OVER (ORDER BY HIREDATE) AS PREV_SAL
    FROM EMP
    WHERE JOB = 'SALESMAN';
    ```

    `LAG(SAL, 2, 0) `: 두 행 앞의 SALARY를 가져오고 없는 경우 0으로 처리

    LAG 함수는 3개의 ARGUMENTS까지 사용 가능

    - 2번째 인자: 몇 번째 앞의 행을 가져올지 결정
    - 3번째 인자: 값이 없을 경우 NULL이 아닌 다른 값으로 바꿀지 결정 = `ISNULL` `NVL`

- LEAD 함수

  LAG 함수를 통해 파티션별 윈도우에서 이후 몇 번째 행의 값을 가져올 수 있음

  - 에제 -  직원들을 입사일자가 빠른 기준으로 정렬을 하고, 바로 다음에 입사한 인력의 입사일자를 함
    께 출력한다.

    ```SQL
    SELECT ENAME, HIREDATE,LEAD(HIREDATE, 1) OVER (ORDER BY HIREDATE) AS "NEXTHIRED"
    FROM EMP;
    ```



#### 5. 그룹 내 비율 함수

- RATIO_TO_REPORT 함수

  RATIO_TO_REPORT 함수를 이용해 파티션 내 전체 SUM(칼럼) 값에 대한 행별 칼럼 값의 백분율을 소수점으로 구할 수 있음

  결과는 0< X <=1

  개별 RATIO의 합을 구하면 1이 됨

  - 예제 - JOB이 SALESMAN인 사원들을 대상으로 전체 급여에서 본인이 차지하는 비율 출력

    ```SQL
    SELECT ENAME, SAL, ROUND(RATIO_TO_REPORT(SAL) OVER(), 2) AS R_R
    FROM EMP
    WHERE JOB = 'SALESMAN';
    ```

    ```
    ENAME SAL R_R 
    ------ ---- ---- 
    ALLEN 1600 0.29 (1600 / 5600) 
    WARD 1250 0.22 (1250 / 5600) 
    MARTIN 1250 0.22 (1250 / 5600) 
    TURNER 1500 0.27 500 / 5600)
    ```

- PERCENT_RANK 함수

  PERCENT_RANK 함수를 이용해 파티션별 윈도우에서 제일 먼저 나오는 것을 0, 제일 늦게 나오는 것을 1로 하여 값이 아닌 행의 순서별 백분율을 구함

  결과는 0< X <=1

  - 예제 - 같은 부서 소속 사원들의 집합에서 본인의 급여가 순서상 몇 번째쯤 위치해있는지 0과 1사이의 값으로 출력

    ```SQL
    SELECT DEPTNO, ENAME, SAL, PERCENT_RANK() OVER (PARTITION BY DEPTNO ORDER BY SAL DESC) AS P_R FROM EMP;
    ```

    ```
    DEPTNO ENAME SAL P_R 
    ------ ------ ---- ---- 
    10 KING 5000 0 
    10 CLARK 2450 0.5
    10 MILLER 1300 1 
    20 SCOTT 3000 0 
    20 FORD 3000 0 
    20 JONES 2975 0.5 
    20 ADAMS 1100 0.75 
    20 SMITH 800 1 
    30 BLAKE 2850 0 
    30 ALLEN 1600 0.2 
    30 TURNER 1500 0.4 
    30 MARTIN 1250 0.6 
    30 WARD 1250 0.6 
    30 JAMES 950 1
    ```

- CUME_DIST 함수

  CUME_DIST함수를 이용해 파티션별 윈도우의 전체 건수에서 현재 행보다 작거나 같은 건수에 대한 누적 백분율을 구함

  결과는 0< X <=1

  - 예제 - 같은 부서 소속 사원들의 집합에서 본인의 급여가 누적 순서상 몇 번째 위치해있는지 0과 1 사이 값으로 출력

    ```SQL
    SLEECT DEPTNO, ENAME, SAL, CUME_DIST() OVER (PARTITION BY DEPTNO ORDER BY SAL DESC) AS CUME_DIST
    FROM EMP;
    ```

    ```
    DEPTNO ENAME SAL CUME_DIST 
    ------ ------ ---- --------
    10 KING 5000 0.3333
    10 CLARK 2450 0.6667 
    10 MILLER 1300 1.0000 
    20 SCOTT * 3000 0.4000 
    20 FORD * 3000 0.4000 
    20 JONES 2975 0.6000 
    20 ADAMS 1100 0.8000 
    20 SMITH 800 1.0000 
    30 BLAKE 2850 0.1667 
    30 ALLEN 1600 0.3333 
    30 TURNER 1500 0.5000 
    30 MARTIN ** 1250 0.8333 
    30 WARD ** 1250 0.8333 
    30 JAMES 950 1.0000
    ```

    *이 붙은 SCOTT, FORD와 MARTIN, WARD는 ORDER BY SAL에 의해 SAL이 같으므로 같은 ORDER로 취급

    다른 윈도우 함수의 경우 동일 순서면 앞 행의 함수 결과를 따르는데,

    CUME_DIST의 경우 동일 순서면 뒤 행의 함수 결과값을 기준으로 함

- NTILE 함수

  NTILE 함수를 통해 파티션별 전체 건수를 ARGUMNET 값으로 N등분한 결과를 구할 수 있음

  - 예제 - 전체 사원의 급여가 높은 순서로 정렬하고, 급여를 기준으로 4개의 그룹으로 분류

    ```SQL
    SELECT ENAME, SAL, NTILE(4) OVER (ORDER BY SAL DESC) AS QUAR_TILE
    FROM EMP;
    ```

    ```
    DEPTNO ENAME SAL QUAR_TILE 
    ------ ------- ---- -------- 
    10 KING 5000 1 
    10 FORD 3000 1 
    10 SCOTT 3000 1 
    20 JONES 2975 1 
    20 BLAKE 2850 2 
    20 CLARK 2450 2 
    20 ALLEN 1600 2 
    20 TURNER 1500 2 
    30 MILLER 1300 3 
    30 WARD 1250 3 
    30 MARTIN 1250 3 
    30 ADAMS 1100 4 
    30 JAMES 950 4 
    30 SMITH 800 4
    ```

    14명을 4로 나누면 4,4,3,3 명으로 나누게 됨