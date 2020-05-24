# SQL 기본

## 7. GROUP BY, HAVING 절

#### 1. 집계 함수(Aggregate Function)

- 여러 행들의 그룹이 모여 그룹 당 단 하나의 결과를 리턴하는 함수

- GROUP BY 절은 행들을 소그룹화함

- SELECT 절, HAVING 절, ORDER BY절에 사용 가능

  | 집계함수                           | 사용 목적                                     |
| ---------------------------------- | --------------------------------------------- |
  | COUNT(*)                           | NULL을 포함한 행의 수 출력                    |
| COUNT(표현식)                      | 표현식의 값이 NULL인 것을 제외한 행의 수 출력 |
  | SUM([ DISTINCT \| ALL ] 표현식)    | 표현식의 NULL 값을 제외한 합계 출력           |
| AVG([ DISTINCT \| ALL ] 표현식)    | 표현식의 NULL 값을 제외한 평균 출력           |
  | MAX([ DISTINCT \| ALL ] 표현식)    | 표현식의 최대값 출력                          |
| MIN([ DISTINCT \| ALL ] 표현식)    | 표현식의 최소값 출력                          |
  | STDDEV([ DISTINCT \| ALL ] 표현식) | 표현식의 표준 편차를 출력                     |
| VARIAN([ DISTINCT \| ALL ] 표현식) | 표현식의 분산 출력                            |

- 집계함수는 일반적으로 `GROUP BY`절과 같이 사용되지만,

  테이블 전체가 하나의 그룹이 되는 경우에는 `GROUP BY`절 없이 단독으로 사용 가능

  ```sql
  SELECT COUNT(*) "전체 행수", COUNT(HEIGHT) "키 건수", MAX(HEIGHT) 최대키, MIN(HEIHGT) 최소키, ROUND(AVG(HEIGHT), 2) 평균키
  FROM PLAYER;	
  ```

  ```
  전체 행수 | 키 건수 | 최대키 |최소키 | 평균키
  480     | 447    | 196   | 165  | 179.31
  ```

  결과를 보면, COUNT(HEIGHT)는 NULL이 아닌 값만 출력하므로 COUNT(*)보다 33 작은 결과값을 가짐

  **COUNT(*) 함수에 사용된 와일드카드는 전체 칼럼을 의미, 전체 칼럼이 NULL인 행은 존재할 수 없기 때문에 전체 행의 개수 출력**

  

#### 2. GROUP BY 절

- WHERE 절을 통해 조건에 맞는 데이터를 조회했지만, 테이블에 1차적으로 존재하는 데이터 이외의 정보(각 팀별로 선수가 몇 명인지, 선수들의 평균 신장과 몸무게가 얼마나 되는지)인 2차 가공 정보도 필요

- GROUP BY 절은 SQL 문에서 **FROM 절과 WHERE절 뒤에 오며,** 데이터들을 작은 그룹으로 분류하여 소그룹에 대한 항목별로 통계 정보를 얻을 때 추가로 사용됨

  ```SQL
  SELECT [DISTINCT] 칼럼명 [ALIAS명]
  FROM 테이블명
  [WHERE 조건식]
  [GROUP BY 칼럼/표현식]
  [HAVING 그룹조건식];
  ```

- GROUP BY절의 특징
  - GROUP BY 절을 통해 소그룹별 기준을 정한 후, SELECT 절에 집계 함수를 사용
  - 집계 함수의 통계 정보는 NULL 값을 가진 행을 제외하고 수행
  - GROUP BY 절에서는 SELECT 절과 달리 ALIAS 명을 사용할 수 없음
  - 집계 함수는 **WHERE 절에 올 수 없음**
  - WHERE 절은 전체 데이터를 GROUP으로 나누기 전에 행들을 미리 제거
  - HAVING 절은 GROUP BY 절의 기준 항목이나 소그룹의 집계 함수를 이용한 조건 표시 가능
  - GROUP BY 절에 의한 소그룹별로 만들어진 집계 데이터 중, HAVING 절의 제한 조건을 만족하는 내용만 출력
  - HAVING 절은 일반적으로 GROUP BY 절 뒤에 위치

- 예제 - K 리그 선수들의 포지션별 평균키는 어떻게 되는가

  ```SQL
  SELECT POSITION 포지션, AVG(HEIGHT) 평균키 // 1행 오류
  FROM PLAYER;
  ```

  구하고자 하는 것이 포지션 별 평균키이므로 **GROUP BY POSITION**을 적어주어야 함

- 예제 - SELECT 절에서 사용된 포지션이라는 한글 ALIAS를 GROUP BY 절의 기준으로 사용

  ```SQL
  SELECT POSITION 포지션, AVG(HEIGHT) 평균키
  FROM PLAYER
  GROUP BY POSITION 포지션; // 3행 오류
  ```

  ALIAS는 `SELECT 절에서 정의`하고, `ORDER BY 절에서 재활용 가능` 

  하지만, `GROUP BY 절에서는 사용 불가`

- 예제 - 포지션 별 최대키, 최소키, 평균키 출력 (포지션 별이라는 소그룹의 조건을 제시했으므로 GROUP BY절 사용)

  ```SQL
  SELECT POSITION 포지션, COUNT(*) 인원수, COUNT(HEIGHT) 키대상, MAX(HEIGHT) 최대키, MIN(HEIHGT) 최소키, ROUND(AVG(HEIGHT),2) 평균키
  FROM PLAYER
  GROUP BY POSITION;
  ```

  ```
  포지션 인원수 키대상 최대키 최소키 평균키 
  ----- ----- ----- ----- ------ ----- 
    3    0     
    GK  43    43    196  174  186.26 
    DF  172   142   190  170  180.21 
    FW  100   100   194  168  179.91 
    MF  162   162   189  165  176.31
  5개의 행이 선택되었다.
  ```

  포지션과 키 정보가 없는 선수가 3명이라는 정보를 얻을 수 있음

  포지션 DF인 172명중 30명은 키에 대한 정보가 없음 >> NULL인 값은 제외하고 계산



#### 3. HAVING 절

- 예제 - K리그 선수들의 포지션별 평균키를 구하는데, 평균키가 180 센티미터 이상인 정보만 표시

  ```SQL
  SELECT POSITION 포지션, ROUND(AVG(HEIGHT),2) 평균키
  FROM PLAYER
  WHERE AVG(HEIGHT) >= 180 // 오류 - 집계함수 불허
  GROUP BY POSITION;
  ```

- 예제 - HAVING 조건절에는 GROUP BY 절에서 정의한 소그룹의 집계 함수를 이용한 조건을 표시 가능

  ```SQL
  SELECT POSITION 포지션, ROUND(AVG(HEIGHT),2)
  FROM PLAYER
  GROUP BY POSITION
  HAVING AVG(HEIGHT) >= 180;
  ```

- 에제 - **SQL 문장은 GROUP BY 와 HAVING 의 순서를 바꿔서 수행**

  ```SQL
  SELECT POSITION 포지션, AVG(HEIGHT) 평균키
  FROM PLAYER
  HAVING AVG(HEIGHT) >= 180
  GROUP BY POSITION;
  ```

- 예제 - K리그 선수들 중 삼성블루윙즈(K02)와 FC서울(K009)의 인원수

  ```SQL
  SELECT TEAM_ID 팀ID, COUNT(*) 인원수
  FROM PLAYER
  WHERE TEAM_ID IN ('K09', 'K02')
  GROUP BY TEAM_ID;
  ```

  ```
  팀ID 인원수 
  ---- ----- 
  K02   49 
  K09 49
  ```

  ```SQL
  SELECT TEAM_ID 팀 ID, COUNT(*) 인원수
  FROM PLAYER
  GROUP BY TEAM_ID
  HAVING TEAM_ID IN ('K09', 'K02');
  ```

  위의 두 가지 방법 중(WHERE + GROUP BY | GROUP BY + HAVING)

  가능하면 WHERE 절에 조건절을 적용하여 **GROUP BY 계산 대상을 줄이는 것이 효율적**

- 예제 - 포지션별 평균 키 출력 + 최대키가 190CM 이상인 선수를 가지고 있는 포지션의 정보만 출력

  ```SQL
  SELECT POSITION 포지션, ROUND(AVG(HEIGHT), 2) 평균키
  FROM PLAYER
  GROUP BY POSITION
  HAVING MAX(HEIGHT) >= 190;
  ```

  ```
  포지션 평균키 
  ------ ----- 
  GK    186.26 
  DF    180.21 
  FW    179.91
  ```

  WHERE 절의 조건 변경은 대상 데이터의 개수가 변경되므로 결과 데이터 값이 변경될 수 있지만,

  HAVING 절의 조건 변경은 데이터의 변경은 없고 출력되는 레코드의 개수만 변경될 수 있음



#### 4. CASE 표현을 활용한 월별 데이터 집계

ffff
