# 절차형 SQL 작성하기
## 프로시저
- 프로시저 : 절차형 SQL을 활용하여 특정 기능을 수행할 수 있는 트랜잭션언어이다.
프로시저 호출을 통해 실행되며, 이를 통해 일련의 SQL 작업을 포함하는 데이터 조작어를 수행한다.

데이터 조작어(DML) : SELCET, INSERT, UPDATE, DELETE

프로시저 구성
- 선언부
CREATE PROCEDURE ~~
(외부변수)
IS
(내부변수)

- 시작/종료부
BEGIN

- 제어부
IF ~ THEN ~
SET
END IF;

- SQL
SELCET ~~
~~

- 예외부
EXCEPTION
~~

- 실행부
COMMIT;
END;

## 사용자 정의함수
사용자 정의함수 : 절차형 SQL을 활용하여 일련의 SQL 처리를 수행하고, 수행 결과를 단일값으로 반환할 수 있는 절차형 SQL
DBMS에서 제공되는 공통적 함수 이외에 사용자가 직접 정의하고 작성한다.

사용자 함수 구성
- 선언부
CREATE FUNCTION
(입력변수 선언)
IS

- 시작 / 종료부
BEGIN
(변수선언 등)
- 제어부
IF ~~ THEN
SET
~~
END IF;

- SQL
SELECT, UPDATE, DELETE ~~
~~
- 반환부
__RETURN__ (리턴받을 값);
END;


## 트리거
- 특정 테이블에 삽입, 수정, 삭제 등 데이터 변경 이벤트가 발생하면 DBMS에서 자동적으로 실행되도록 구현된 프로그램
- 뷰, 테이블, DB작업을 대상으로 정의할 수 있다.

- 트리거의 종류
-- 행 트리거 : 데이터 변화가 생길 때마다 실행
-- 문장 트리거 : 트리거에 의해 단 한 번 실행

- 트리거는 반환 값이 없음. 프로시저와 유사
- EVENT 명령을 통해 트리거 실행을 위한 이벤트를 인지한다는 점, 외부 변수 IN/OUT이 없다는 점이 다름.

- 선언부
CREATE TRIGGER (트리거 이름)

- 이벤트부
AFTER/BEFORE UPDATE OR DELETE
테이블 명, 뷰 이름 등
FOR EACH ROW
__테이블 수정 및 삭제 후 해당 트리거 실행__

- 시작 / 종료부
BEGIN

- 제어부
IF UPDATING
THEN
업데이트 연산이 발생할 경우

- SQL
INSERT INTO ~
VALUES ~

ELSE IF DELETING
THEN
INSERT INTO ~
VALUES ~

END IF;
END;

- 트리거 작성 시 주의사항
-- TCL 사용 불가 : COMMIT, ROLLBACK 등 트랜잭션 제어어 사용 시 컴파일 에러 발생
-- 오류에 주의 : 트리거 실행 중 오류가 발생하면 트리거 실행 원인을 제공한 데이터 작업에도 영향


# 응용 SQL 작성하기
## 집계성 SQL 작성
- 데이터 분석 함수의 개념 : 총합, 평균 등 데이터 분석을 위해서는 복수 행 기준의 데이터를 모아서 처리하는 것을 목적으로 하는 다중 행 함수이다.
단일 행을 기반으로 산출하지 않고 복수 행을 그룹별로 모아놓고 그룹당 단일 계산 결과를 반환한다.
GROUP BY 구문을 활용하여 복수 행을 그룹핑한다.
SELECT, HAVING, ORDER BY 등의 구문에 활용한다.

- GROUP BY : 복수 ROW 대상의 데이터 분석 시 그룹핑 대상이 되는 부분을 선별할 필요가 있다. 그런 경우 사용하며
NULL 값을 가지는 ROW는 제외한 후 산출한다.
SELECT에서 사용하는 것과 같은 ALIAS 사용이 불가하다.
WHERE 구문 안에 포함되지 않는다.
WHERE 구문은 GROUP BY보다 먼저 실행되고, 대상이 되는 단일 행을 사전에 선별하는 역할을 한다.

- HAVING : WHERE 구문 내에는 사용할 수 없는 집계 함수의 구문을 적용하여 복수 행의 계산 결과를 조건별로 적용하는데 사용된다.
일반적으로 GROUP BY 뒤에 기재하며, GROUP BY 구문의 기준 항목이나 소그룹 집계 함수를 활용한 조건을 적용하는 데 사용한다.
GROUP BY 및 집계 함수에 대한 WHERE 구문인 셈.


### 집계 함수
- 여러 행 또는 테이블 전체 행으로부터 __하나의 결괏값을 반환__ 하는 함수


SELECT 컬럼1, 컬럼2, 집계함수
FROM 테이블명
[WHERE] 조건
GROUP BY 컬럼1, 컬럼2
[HAVING 조건식(집계함수 포함]

- 집계 함수의 종류
COUNT - 복수 행의 줄 수
SUM - 복수 행의 해당 컬럼 간의 합계
AVG - 평균
MAX - 최댓값
MIN - 최솟값
STDDEV - 표준편차
VARIAN - 분산

### 그룹 함수
- 소그룹 간의 소계 및 중계 등의 __중간 합계 분석 데이터를 산출__ 하는 함수

- ROLLUP함수
ROLLUP에 의해 지정된 컬럼은 소계 등 중간 집계 값을 산출하기 위한 그룹함수이다.
지정 컬럼 수보다 하나 더 큰 레벨만큼의 중간 집계 값이 생성된다.
ROLLUP의 지정 컬럼은 계층별로 구성되기 때문에 순서가 바뀌면 수행결과가 바뀜.

SELECT 컬럼1, 컬럼2, 그룹함수
FROM 테이블 명
[WHERE]
GROUP BY [컬럼] ROLLUP 컬럼
[HAVING]
[ORDER BY]

ex )
SELCET DEPT, JOB, SUM(SALARY)
FROM DEPT_SALARY
GROUP BY ROLLUP(DEPT, JOB)

ORDER BY를 사용해 계층 내 정럴에 사용이 가능

---

- CUBE 함수
결함 가능한 모든값에 대해 다차원 집계를 생성하는 그룹함수
연산이 많아 시스템에 부담

SELECT 컬럼1,.. 그룹함수
FROM 테이블명
[WHERE]
GROUP BY [컬럼1, ] CUBE(컬럼명a, )
[HAVING]
[ORDER BY]

ROLLUP은 소계, 중간 집계를 보여주지만, CUBE는 결합 가능한 모든 값에 대해 다차원 집계 형성, 세분화된 소계가 구해짐

---

- GROUPING SETS
집계 대상 컬럼들에 대한 개별 집계를 구할 수 있으며, ROLLUP, CUBE와는 달리 컬럼 간 순서와 무관한 결과를 얻을 수 있다.

SELECT 컬럼명1,.. 그룹함수
FROM 테이블 명
[WHERE]
GROUP BY [컬럼명1, ] GROUPING SETS(컬럼명1, )
[HAVING]
[ORDER BY]


### 윈도 함수
-  __데이터베이스를 사용한 온라인 분석 처리 용도__ 로 사용하기 위해서 표준 SQL에 추가된 기능

SELECT 함수명(파라미터)
OVER
([PARTITION BY 컬럼1, 컬럼2, ])
[ORDER BY 컬럼A, ]
FROM 테이블명

- 순위함수 : 레코드의 순위를 계산하는 함수
RANK - 특정 항목에 대한 순위를 구하는 함수, 동일 순위 존재 시 후순위는 넘어감(1,2,2,2,5,6)
DENSE_RANK - 레코드의 순위를 계산, 동일 순위 존재시에도 후순위를 넘어가지 않음(1,2,2,2,3,4)
ROW_NUMBER - 레코드의 순위를 계산, 동일 순위 존재시에도 이와 무관하게 연속 번호를 부여

SELECT NAME,
  SALARY
  RANK() OVER (ORDER BY SALARY DESC) A,
  DENSE_RANK() OVER (ORDER BY SALARY DESC) B,
  ROW_NUMBER() OVER (ORDER BY SALARY DESC) C,
FROM EMPLOYEE

---
- 행순서 함수 : 레코드에서 가장 먼저 나오거나 가장 뒤에 나오는 값, 이전/이후 값들을 출력하는 함수
FIRST_VALUE : 파티션별 윈도에서 가장 먼저 나오는 값 찾음
LAST_VALUE : 가장 늦게 나오는 값 찾음
LAG : 이전 로우의 값 반환
LEAD : 이후 로우의 값 반환

SELECT NAME,
  SALARY,
  FIRST_VALUE(NAME) OVER (ORDER BY SALARY DESC) A,
  LAST_VALUE(NAME) OVER (ORDER BY SALARY DESC) B,
  LAG(NAME) OVER (ORDER BY SALARY DESC) C,
  LEAD(NAME) OVER (ORDER BY SALARY DESC) D
FROM EMPLOYEE

---

- 그룹 내 비율 함수 : 백분율을 보여주거나 행의 순서별 백분율 등 비율과 관련된 통계를 보여줌
RATIO_TO_REPORT : 주어진 그룹에 대해 합을 기준으로 각 로우의 상대적 비율을 반환하는 함수, 결괏값은 0~1 범위 값을 가짐
PERCENT_RANK : 주어진 그룹에 대해 제일 먼저 나오는 것을 0으로, 제일 늦게 나오는 것을 1로 하여, 값이 아닌 행의 순서별 백분율을 구하는 함수, 결괏값은 0~1범위를 가짐

SELECT NAME,
  SALARY
  RATIO_TO_REPORT(NAME) OVER (ORDER BY SALARY DESC) A,
  PERCENT_RANK(NAME) OVER (ORDER BY SALARY DESC) B,
FROM EMPLOYEE;


---

## 특정 기능 수행 SQL문 작성
- 자바 데이터베이스 연결 (JDBC)
connection = dataSource.getConnection();
sql_exec = connection.preparestatement(SQL_QUERY);
sql_exec.setString(1, user_name);
sql_exec.setString(2, age);
sql_result = sql_exec.excuteQuery();

- MyBatis
SQL Mapping 기반 오픈소스 Access Framework로 DBMS에 질의하기 위한 SQL쿼리를 별도의 XML 파일로 분리하고, Mapping을 통해서 SQL을 실행한다.
DBMS의존도가 높고, SQL질의 언어를 사용하기 때문에 SQL친화적
복잡한 JDBC코드를 단순화할 수 있다.
SQL을 거의 그대로 사용 가능
Spring 기반 프레임워크와 통합 기능을 제공
우수한 성능
 #{파라미터명} 으로 처리함

<mapper>
  <select id = "findID" resultType = "map">
    SELECT USER_ID
      FROM USER_INFO
    WHERE USER_NM = #{user_nm}
      AND BIRTH_DAY = #{user_birth_day}
  </select>
</mapper>

- 동적 SQL
MyBatis를 이용한 SQL의 강점 중 하나는 조건에 따라 SQL 구문 자체를 변경할 수 있다는 점이다.
이를 동적 SQL이라 하며, 응용 프로그램 실행 시 수행할 SQL문이 결정된다.
입력 파라미터만 변경하는 것이 아니라, SQL에 포함 된 다양한 부분을 조건에
따라 변경 가능하다.

<mapper>
  <select id = "findID" resultType = "map">
    SELECT USER_ID
      FROM USER_INFO
    WHERE USER_NM = #{user_nm}


    <if input_para = "user_birth_day != null">
      AND EMAIL = #{user_email}
    </if>

    <if input_para = "user_email != null">
      AND EMAIL #{user_email}
    </if>
  </select>
</mapper>

if, foreach, choose when otherwise 등이 사용 됨.


- 절차형 SQL 호출
MyBatis에서 절차형 SQL인 사용자 정의 함수, 트리거, 프로시저의 실행이 가능하다.
사용자 정의함수는 DQL이나 DML에 포함하여 사용하기 때문에 별도 호출이 의미가 없으며, 트리거 역시 DBMS에서 바로 실행되므로 별도 호출이 불필요하다.
MyBatis에서는 주로 프로시저를 이용

<select id = "execSaleDailyBatch">
  resultType = "map"
  statementType = "CALLABLE"
  {CALL RUN_SALES_AMT_BATCH(#{TARGET_DATE})}
</select>

프로시저를 호출하는 경우 statementType을 반드시 CALLABLE로 설정해야하고, 프로시저 호출 전에도 'CALL' 문장을 사용해야 한다.


## 데이터 제어어 명령문 작성
- 데이터보안, 무결성 유지, 병행 제어, 회복을 위해 관리자가 사용하는 제어용 언어

- GRANT - 사용 권한 부여
GRANT 권한 ON 테이블 TO 사용자
[WITH 권한 옵션];

- REVOKE - 사용 권한 취소
REVOKE 권한 ON 테이블 FROM 사용자
[CASECADE CONSTRAINT]

CASECADE는 연쇄적 권한을 해제할 때 옵션
