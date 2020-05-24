# SQL 기본

## 8. ORDER BY 절

#### 1. ORDER BY 정렬

- SQL 문장으로 조회된 데잍터들을 다양한 목적에 맞게 특정 칼럼을 기준으로 정렬하여 출력하는데 사용

- 칼럼명 대신 SELECT절에서 사용한 ALIAS 명이나 칼럼 순서를 나타내는 정수 사용 가능

- DEFAULT  = ASC | DESC

  ```mssql
  SELECT 칼럼명 [ALIAS 명]
  FROM 테이블명
  [WHERE 조건식]
  [GROUP BY 칼럼이나 표현식]
  [HAVING 그룹 조건식]
  [ORDER BY 칼럼이나 표현식[ASC|DESC]];
  ```

- 예제 - 선수 테이블에서 선수들의 이름, 포지션, 백넘버를 출력하는데 사람 이름을 내림차순으로 정렬해 출력

  ```SQL
  SELECT PLAYER_NAME 선수명, POSITION 포지션, BACK_NO 백넘버
  FROM PLAYER
  ORDER BY PLAYER_NAME DESC;
  ```

- 예제 - 선수 테이블에서 선수들의 이름, 포지션, 백넘버를 출력하는데 포지션을 내림차순으로 출력, ALIAS 사용

  ```SQL
  SELECT PLAYER_NAME 선수명, POSITION 포지션, BACK_NO 백넘버
  FROM PLAYER
  ORDER BY 포지션 DESC;
  ```

  ```
  선수명   포지션  백넘버 키 
  ------ ------ ----- --- 
  정학범               173 
  차상광               186 
  안익수               174
  백영철    MF    22   173 
  조태용    MF    7    192 
  올리베    MF   29    190 
  김리네    MF   26    188 
  쟈스민    MF   33    186
  ```

  실행결과에서 포지션에 아무 것도 없는 값들이 있음

  내림차순 정렬에서 NULL 값이 맨 앞에 출력되었다는 것은 ORACLE이 NULL값을 가장 큰 값으로 취급했음을 알 수 있다

  반면 SQL SERVER는 반대의 정렬순서를 가짐

- GROUP BY 특징

  - 정렬순서 기본 = ASC

  - 숫자형 데이터 타입은 오름차순 정렬시 작은 값부터 출력

  - 날짜형 데이터 타입은 오름차순 정렬시 날짜 값이 빠른 값 먼저 출력(과거일수록 먼저 출력)

    EX) 01-JAN-2012 가 01-SEP-2012보다 먼저 출력

  - ORACLE - NULL을 가장 큰 값으로 간주
  - SQL - NULL을 가장 작은 값으로 간주

- 예제 - 한 개의 칼럼이 아닌 여러 가지 칼럼을 기준으로 정렬

  키가 큰 순서대로 정렬, 키가 같으면 백넘버 순으로 ORDER BY 절을 사용해 SQL 문장 적성

  키가 NULL인 데이터는 제외

  ```SQL
  SELECT PLAYER_NAME 선수이름, POSITION 포지션, BACK_NO 백넘버, HEIGHT 키
  FROM PALYER
  WHERE HEIGHT IS NOT NULL
  ORDER BY HEIGHT DESC, BACK_NO;
  ```

- 예제 - 선수 테이블에서 선수 이름, 포지션, 백넘버를 출력하는데 선수들의 백넘버 내림차순, 백넘버가 같은 경우 포지션, 포지션이 같은 경우는 선수명 순서로 출력

  BACK_NO가 NULL인 경우는 제외

  칼럼명이나 ALIAS가 아닌 칼럼 INDEX를 매핑해서 사용

  ```SQL
  SELECT PLAYER_NAME 선수이름, POSITION 포지션, BACK_NO 백넘버
  FROM PALYER
  WHERE BACK_NO IS NOT NULL
  ORDER BY 3 DESC, 2, 1;
  ```

- 예제 - DEPT 테이블 정보를 부서명, 지역, 부서번호 내림차순으로 정렬해 출력

  - CASE1 - 칼럼명 사용 | ORDER BY 절 사용

    ```SQL
    SELECT DNAME, LOC, DEPTNO
    FROM DEPT
    ORDER BY DNAME, LOC, DEPTNNO DESC;
    ```

  - CASE2 - 칼럼명 + ALIAS 사용 | ORDER BY 절 사용

    ```SQL
    SELECT DMANE DEPT, LOC AREA, DEPTNO
    FROM DEPT
    ORDER BY DNAME, AREA, DEPTNO DESC;
    ```

  - CASE3 - 칼럼 INDEX + ALIAS 사용 | ORDER BY절 사용

    ```SQL
    SELECT DNAME, LOC AREA, DEPTNO
    FROM DEPT
    ORDER BY 1, AREA, 3 DESC;
    ```

    

#### 2. SELECT 문장 실행 순서

- GROUP BY 절과 ORDER BY절이 같이 사용될 때 SELECT 문장은 6개의 절로 구성되며 문장 수행 단계는 아래와 같음

  ```SQL
  5. SELECT 칼럼명 [ALIAS 명]
  1. FROM 테이블명
  2. [WHERE 조건식]
  3. [GROUP BY 칼럼이나 표현식]
  4. [HAVING 그룹 조건식]
  6. [ORDER BY 칼럼이나 표현식[ASC|DESC]];
  ```

  1. FROM - 발췌 대상 테이블을 참조
  2. WHERE - 발췌 대상 데이터가 아닌 것은 제거
  3. GROUP BY - 행들을 소그룹화
  4. HAVING - 그룹핑된 값의 조건에 맞는 것만 출력
  5. SELECT - 데이터의 값을 출력/계산
  6. ORDER BY - 데이터를 정렬

- FROM 절에서 정의되지 않은 테이블의 칼럼을 WHERE 절, GROUP BY 절, HAVING 절, SELECT 절, ORDER BY 절에서 사용하면 에러 발생

  그러나, ORDER BY 절에는 SELECT 목록에 나타나지 않은 문자형 항목이 포함 가능

  단, SELECT DISTINCT를 지정하거나 SQL 문장에 GROUP BY 절이 있거나 SELECT 문에 UNION 연산자가 있으면 열 정의가 SELECT 목록에 표시되어야 함

- 예제 - SELECT 절에 없는 EMP 칼럼을 ORDER BY 절에 사용

  ```SQL
  SELECT EMPNO, ENAME
  FROM EMP
  ORDER BY MGR;
  ```

- 예제 - 인라인 뷰에 정의된 SELECT 칼럼을 메인쿼리에서 사용

  ```SQL
  SELECT EMPNO
  FROM (SELECT EMPNO, ENAME
        FROM EMP
        ORDER BY MGR);
  ```

  인라인 뷰의 SELECT 절에 정의한 칼럼(EMPNO, ENAME)은 메인 쿼리에서도 사용 가능

- 예제 - 인라인 뷰에 미정의된 칼럼을 메인쿼리에 사용

  ```SQL
  SELECT MGR //부적합한 식별자 에러
  FROM (SELECT EMPNO, ENAME
        FROM EMP
        ORDER BY MGR);
  ```

  서브쿼리의 SELECT 절에서 선택되지 않은 칼럼들은 서브쿼리 범위를 벗어나면 더 이상 사용 불가

- 예제 - GROUP BY 절 사용시 SELECT 절에 일반 칼럼 사용

  ```sql
  SELECT JOB, SAL //ERROR: GROUP BY 표현식이 아니다.
  FROM EMP
  GROUP BY JOB
  HAVING COUNT(*)>0
  ORDER BY SAL;
  ```

  GROUP BY 절에서 그루핑 기준을 정의하게 되면

  데잍터베이스는 일반 SELECT 문장처럼 FROM 절에 정의된 테이블 구조를 그대로 가지고 가는 것이 아니라,

  GROUP BY 절의 그루핑 기준에 사용된 칼럼과 집계 함수에 사용될 수 있는 숫자형 데이터 칼럼들의 집합을 새로 만듦

  이때, 개별 데이터는 필요 없으므로 저장하지 않음

  **따라서 GROUP BY 이후 수행 절인 SELECT 절이나 ORDER BY 절에서 개별 데이터를 사용하는 경우 에러 발생**

- 예제 - GROUP BY 절 사용시 ORDER BY 절에 집계 칼럼 사용

  ```SQL
  SELECT JOB
  FROM EMP
  GROUP BY JOB
  HAVING COUNT(*) > 0
  ORDER BY MAX(EMPNO), MAX(MGR), SUM(SAL), COUNT(DEPTNO), MAX(HIREDATE);
  ```

  GROUP BY절이 사용되었기 때문에 SELECT 절에서 정의하지 않은 MAX, SUM, COUNT 집계함수도 ORDER BY 절에서 사용 가능함을 확인



#### 3. TOP N 쿼리

- ROWNUM

  오라클에서 순위가 높은 N개의 로우를 추출하기 위해 ORDER BY 절과 WHERE 절의 ROWNUM 조건을 같이 사용하는 경우가 있는데 이 두 조건으로는 원하는 결과를 얻을 수 없음

  ORACLE의 경우 정렬이 완료된 후 데이터의 일부가 출력되는 것이 아니라,

  데이터 일부가 먼저 추출된 후 정렬작업이 일어나므로 주의

  - 잘못된 예제 - 사원 테이블에서 급여가 높은 3명만 내림차순으로 출력

    ```SQL
    SELECT ENAME, SAL
    FROM EMP
    WHERE ROWNUM < 4
    ORDER BY SAL DESC;
    ```

    실행 결과의 3명은 급여 상위 3명이 아니라 무작위로 추출된 3명에 한해 급여를 내림차순으로 정렬한 결과이므로 원하는 결과가 아님

  - 예제 - ORDER BY 절이 없으면 ORACLE의 ROWNUM 조건과 SQL의 TOP 절은 같은 결과를 보임

    하지만, ORDER BY 절이 사용되는 경우 ORACLE은 ROWNUM 조건을 ORDER BY 절보다 먼저 처리되는 WHERE 절에서 처리하므로, 정렬 후 원하는 데이터를 얻기 위해서는 인라인 뷰에서 먼저 데이터 정렬을 수행한 후 메인쿼리에서 ROWNUM 조건을 사용해야 함

    ```SQL
    SELECT ENAME, SAL
    FROM (SELECT ENAME, SAL
          FROM EMP
          ORDER BY SAL DESC)
    WHERE ROWNUM < 4;
    ```

- TOP()

  SQL은 TOP 조건을 사용하면 별도의 처리 없이 ORDER BY 절의 데이터 정렬 후 원하는 일부 데이터만 쉽게 출력 가능

  ```SQL
  TOP (EXPRESSION) [PERCENT] [WITH TIES]
  ```

  - WITH TIES: ORDER BY 절의 조건 기준으로 TOP N의 마지막 행으로 표시되는 추가 행의 데이터가 같을 경우 N+ 동일 정렬 순서 데이터를 추가 반환하도록 지정하는 옵션

  - 예제 - 사원 테이블에서 급여가 높은 2명을 내림차순으로 출력

    ```SQL
    SELECT TOP(2) ENAME, SAL
    FROM EMP
    ORDER BY SAL DESC;
    ```

  - 사원 테이블에서 급여가 높은 3명을 내림차순으로 출력, 같은 급여를 받는 사원이 있으면 같이 출력

    ```SQL
    SELECT TOP(2) WITH TIES ENAME, SAL
    FROM EMP
    ORDER BY SAL DESC;
    ```

    