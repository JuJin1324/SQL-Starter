# SQL-Starter
SQL Query 문법 관련 정리

## DBMS
### Table Inner/Outer Join 판별법
> 테이블 Join 시에 해당 테이블과 Inner Join 을 할 수 있는지 아니면 Outer Join 으로 해야하는지 판별은 다음과 같다.  
> 해당 판별법은 내가 설계한 DB 가 아닌 남이 설계해서 인수인계 받았을 때 DB 관계 파악에 도움이 되기 위한 용도이다.  
> ```sql
> -- TABLE STUDENT
> --    STD_KEY  | STD_NAME
> --     1       |      A
> --     2       |      B
> --     3       |      C
> --     4       |      D
>
> -- TABLE GRADE
> --    STD_KEY  | SCORE
> --     1       |  10
> --     2       |  20
> --     4       |  40
>
> -- Student 테이블을 기준으로 GRADE 테이블과 조인한다고 가정
> SELECT * 
> FROM STUDENT STD  -- 부모
> WHERE NOT EXISTS (
>   SELECT 1 
>   FROM GRADE GRD  -- 자식
>   WHERE GRD.STD_KEY = STD.STD_KEY
> );
> ```
> 위의 EXISTS 문은 다음을 설명한다: GRADE 테이블에 STUDENT 테이블에는 없는 ROW 가 존재하는가?  
> 해당 쿼리의 결과 값이 나온다면(STUDENT(부모) 테이블에만 있고 GRADE(자식) 테이블에는 없는 ROW 가 나온다면)   
> 1:1 관계가 아니므로 STUDENT(부모) 테이블이 모두 나오도록 JOIN 하기 위해서는 STUDENT(부모) 테이블 기준으로 LEFT OUTER JOIN 한다.   
> tip: GRADE(자식) 테이블에만 있고 STUDENT(부모) 테이블에는 없는 경우는 존재하지 않는다.  

### Key 와 Index
> 대부분의 DB 에서는 Key(PK/UK/FK) 생성 시에 모두 자동으로 Index 를 생성해준다. 하지만 Index 가 자동으로 생성되면 시스템에서 자동으로 붙여준 이름으로 생성이 된다.
> Index 를 이용한 쿼리 및 Index 관리 측면에서 Index 이름을 지정해서 생성한 다음에 Table 의 PK 혹은 UK 로 사용하는 것이 바람직하다.

### 1:N(일대다) 테이블 관계
> 예시 테이블
> ```sql
> CREATE TABLE DEPARTMENT
> (
>   DPT_KEY  NUMBER       NOT NULL,
>   DPT_NAME VARCHAR2(20) NOT NULL,
>   DPT_CODE VARCHAR2(20) NOT NULL
> );
> ALTER TABLE DEPARTMENT ADD (CONSTRAINT DPT_PK PRIMARY KEY (DPT_KEY) USING INDEX DPT_PK);
>
> CREATE TABLE EMPLOYEE
> (
>   EMP_KEY  NUMBER       NOT NULL,
>   EMP_NAME VARCHAR2(20) NOT NULL,
>   EMP_CODE VARCHAR2(20) NOT NULL,
>   DPT_KEY  NUMBER       NOT NULL
> );
> ALTER TABLE EMPLOYEE ADD (CONSTRAINT EMP_PK PRIMARY KEY (EMP_KEY) USING INDEX EMP_PK);
> ALTER TABLE EMPLOYEE ADD CONSTRAINT EMP_FK FOREIGN KEY (DPT_KEY) REFERENCES DEPARTMENT (DPT_KEY) ON DELETE CASCADE;
> ```
> 위의 테이블은 일대다 관계 테이블이다: 다(EMPLOYEE) 대 일(DEPARTMENT)  
> 일(DEPARTMENT)인 테이블이 부모 테이블이며 다(EMPLOYEE)인 테이블이 자식 테이블이다.  
> 외래키를 가진 테이블(EMPLOYEE)이 자식 테이블이고, 참조되는 테이블(DEPARTMENT)이 부모 테이블이다.  
> ERD 에서 까치발을 가지고 있는 테이블이 다(자식) 테이블 / '|' 을 가지고 있는 테이블이 일(부모) 테이블이다.  

### Foreign Key 가 NOT NULL 인 경우
> Foreign Key 를 가지고 있는 자식 테이블에서 Foreign Key 가 NOT NULL 인 경우 
> 부모 테이블과의 Join 시 부모 테이블의 ROW 가 NULL 인 경우가 없음으로 성능 최적화를 위해서 Outer Join 이 아닌 Inner Join 을 사용한다.

### JOIN: Inner vs Outer
> Outer 조인의 경우 쿼리 실행 Cost 가 Inner 보다 많이 들며 Outer 조인의 경우 Explain 으로 Cost 측정이 불가능하다.

## Oracle
### Index 조회
> ```sql
> SELECT * FROM USER_IND_COLUMNS;
> ```

### 제약조건(Constraint) 조회
> ```sql
> SELECT * FROM USER_CONSTRAINTS;
> ```

### Outer Join
> LEFT OUTER JOIN: 왼쪽에 명기된 테이블의 요소는 다 나오고 오른쪽에 명기된 테이블 요소는 왼쪽과 매칭되는 row가 없으면 null로 표기한다.  
> RIGHT OUTER JOIN: 오른쪽에 명기된 테이블의 요소는 다 나오고 왼쪽에 명기된 테이블 요소는 오른쪽과 매칭되는 row가 없으면 null로 표기한다.  
> ```sql
> -- TABLE STUDENT
> --    STD_KEY  | STD_NAME
> --     1       |      A
> --     2       |      B
> --     3       |      C
> --     4       |      D
>
> -- TABLE GRADE
> --    STD_KEY  | SCORE
> --     1       |  10
> --     2       |  20
> --     4       |  40
> 
> -- STUDENT TABLE 기준 LEFT OUTER JOIN
> SELECT 
>   STD.*, GRD.STD_KEY, GRD.SCORE
> FROM STUDENT STD, GRADE GRD
> WHERE STD.STD_KEY = GRD.STD_KEY(+);     -- 왼쪽 테이블 전부에 오른쪽 테이블이 (+)되는 형태로 기억하자.
> 
> -- 결과
> -- ST.STD_KEY | STD_NAME |    GRD.STD_KEY | SCORE 
> --        1       |  A   |        1       | 10
> --        2       |  B   |        2       | 20
> --        4       |  D   |        4       | 40
> --        3       |  C   |        null    | null
> 
> -- STUDENT TABLE 기준 RIGHT OUTER JOIN
> SELECT 
>   STD.*, GRD.STD_KEY, GRD.SCORE 
> FROM STUDENT STD, GRADE GRD
> WHERE STD.STD_KEY(+) = GRD.STD_KEY;     -- 오른쪽 테이블 전부에 왼쪽 테이블이 (+)되는 형태로 기억하자.
> 
> -- 결과
> --   STD.STD_KEY  | NAME |   GRD.STD_KEY  | SCORE 
> --        1       |  A   |        1       | 10
> --        2       |  B   |        2       | 20
> --        4       |  D   |        4       | 40
> ```

## Standard SQL
### Logging
> DB에서는 데이터를 입력하면 로그 파일에 기록한다. 즉, DML 이 발생하면서 많은 양의 로그들이 발생하면서 성능 저하가 일어날 수 있다.  
> Check point 라는 이벤트가 발생하면 로그파일의 데이터를 데이터 파일에 저장한다.  
> Nologging 옵션은 로그파일의 기록을 최소화시켜서 입력 시 성능을 향상시키는 방법이다.  
> Nologging 옵션은 Buffer Cache 라는 메모리 영역을 생략하고 기록한다.  
> ```sql
> ALTER TABLE DEPT_TEST NOLOGGING;
> 
> INSERT /*+APPEND */ INTO DEPT_TEST
> SELECT * FROM DEPT;
> 
> > ALTER TABLE DEPT_TEST LOGGING;
> ```
> 위와 같이 NOLOGGING 옵션 부여와 APPEND 힌트를 사욯아면 INSERT 가 빠르게 실행된다.  
> INSERT 를 실행 후에 LOGGING 옵션을 다시 부여해준다.  
> 
> 단점: 오라클에서 NOLOGGING 작업에 대해서 FLASH BACK 기능을 사용할 수 없다.
 
### EXISTS / IN
> IN 의 괄호 사이에는 특정 값이나, <b>서브쿼리</b>가 올 수 있는 반면  
> EXISTS 의 괄호 사이에는 <b>서브쿼리</b>만 올 수 있다.  
> EXISTS 는 괄호 안의 서브쿼리로 부터 해당 컬럼 값의 존재 유무만 체크하며  
> IN 은 괄호 안의 특정 값이나 서브쿼리의 결과 값이 포함이 되는지를 체크한다.  
> ```sql
> -- TABLE STUDENT
> -- STD_KEY | STD_NAME
> --     1       |      A
> --     2       |      B
> --     3       |      C
> --     4       |      D
>
> -- TABLE GRADE
> -- STD_KEY | SCORE
> --     1       |  10
> --     2       |  20
> --     4       |  40
>
> -- IN 에 특정 값을 명시한다.
> SELECT 
>   STD_KEY, SCORE 
> FROM GRADE 
> WHERE STD_KEY IN (1, 2, 4);
> 
> -- IN 에 서브쿼리(성능의 이점은 없고 그냥 공부용도의 쿼리)
> SELECT
>   STD_KEY, SCORE
> FROM GRADE 
> WHERE STD_KEY IN (
>   SELECT 
>       STD_KEY 
>   FROM STUDENT 
>   WHERE STD_KEY IN (1, 2, 4) 
> );
> 
> -- 서브쿼리를 이용한 EXISTS
> SELECT 
>   STD_KEY, SCORE
> FROM GRADE GRD
> WHERE EXISTS (
>   SELECT 1 
>   FROM STUDENT STD 
>   WHERE STD.STD_KEY = GRD.STD_KEY
> );
> ``` 
> 참조사이트: [[SQL] WHERE 절을 활용하자, EXISTS!](https://runtoyourdream.tistory.com/112)

### Scalar(스칼라) [권장하지 않음.]
> SQL 에서 단일 값을 스칼라 값이라고 한다.   
> 스칼라 서브쿼리는 SELECT 절에서 오는 서브쿼리로 결과 값으로 1행만 반환한다.  
> 스칼라를 권장하지 않는 이유: 가져오는 칼럼이 1개가 넘어가면 확장성이 안좋아쟈 JOIN 을 사용하는게 나아진다 또한 스칼라는 explain 에 cost 가 잡히지 않는다.
> ```sql
> SELECT 
>   EPL.EPL_NAME, 
>   (SELECT DPT_NAME FROM DEPARTMENT DPT WHERE DPT_KEY = EPL.DPT_KEY) DPT_NAME  
> FROM EMPLOYEE EPL; 
> ```

### Outer Join
> LEFT OUTER JOIN: 왼쪽에 명기된 테이블의 요소는 다 나오고 오른쪽에 명기된 테이블 요소는 왼쪽과 매칭되는 row가 없으면 null로 표기한다.  
> RIGHT OUTER JOIN: 오른쪽에 명기된 테이블의 요소는 다 나오고 왼쪽에 명기된 테이블 요소는 오른쪽과 매칭되는 row가 없으면 null로 표기한다.  
> ```sql
> -- TABLE STUDENT
> --    STD_KEY  | STD_NAME
> --     1       |      A
> --     2       |      B
> --     3       |      C
> --     4       |      D
>
> -- TABLE GRADE
> --    STD_KEY  | SCORE
> --     1       |  10
> --     2       |  20
> --     4       |  40
> 
> -- STUDENT TABLE 기준 LEFT OUTER JOIN
> SELECT 
>   STD.*, GRD.STD_KEY, SCORE
> FROM STUDENT STD 
> LEFT OUTER JOIN GRADE GRD ON GRD.STD_KEY = STD.STD_KEY;  -- 일부러 GRD 를 앞에 두었다.
> 
> -- 결과
> --    STD.STD_KEY | STD.STD_NAME | GRD.STD_KEY | SCORE 
> --        1       |       A      |     1       | 10
> --        2       |       B      |     2       | 20
> --        4       |       D      |     4       | 40
> --        3       |       C      |     null    | null
> 
> -- STUDENT TABLE 기준 RIGHT OUTER JOIN
> SELECT 
>   STD.*, GRD.STD_KEY, SCORE
> FROM STUDENT STD
> RIGHT OUTER JOIN GRADE GRD ON GRD.STD_KEY = STD.STD_KEY;  -- 일부러 GRD 를 앞에 두었다.
> 
> -- 결과
> --    STD.STD_KEY | STD.STD_NAME | GRD.STD_KEY | SCORE 
> --        1       |        A     |        1    | 10
> --        2       |        B     |        2    | 20
> --        4       |        D     |        4    | 40
> ```

### INNER JOIN
> 조인하는 두 테이블에서 조인 조건에 부합하는 ROW 만 가져온다.
> ```sql
> -- TABLE STUDENT
> --    STD_KEY  | STD_NAME
> --     1       |      A
> --     2       |      B
> --     3       |      C
> --     4       |      D
>
> -- TABLE GRADE
> --    STD_KEY  | SCORE
> --     1       |  10
> --     2       |  20
> --     4       |  40
>
> -- INNER JOIN
> SELECT 
>   STD.*, GRD.STD_KEY, SCORE
> FROM 
>   STUDENT STD, GRADE GRD
> WHERE 
>   GRD.STD_KEY = STD.STD_KEY;    -- 다음 조건에도 GRD 에 관한 WHERE 조건이 나오도록 GRD를 먼저 써줬다.
> 
> -- 결과
> --    STD.STD_KEY | STD.STD_NAME | GRD.STD_KEY | SCORE
> --        1       |         A    |        1    | 10
> --        2       |         B    |        2    | 20
> --        4       |         D    |        4    | 40
> ```

### ORDER BY
> order by 뒤에 각 칼럼마다 asc 혹은 desc 지정해줘야함.
> ```sql
> SELECT 
>     COL1, COL2, COL3 
> FROM 
>     EXAMPLE 
> ORDER BY 
>     COL1 DESC, 
>     COL2 DESC;
> ```

### COALESCE
> COALESCE(expr1, expr2, expr3...): expr1 이 NULL이면 expr2 를 반환, expr2도 NULL 이면 expr3를 반환...반복
> 예시
> ```sql
> SELECT COALESCE(COL1, ' ') FROM EXAMPLE; -- COL1 이 NULL 이면 ' '(empty string) 반환
> SELECT COALESCE(COL2, COL3, ' ') FROM EXAMPLE; -- COL2 가 NULL 이면 COL3를 반환, COL3도 NULL이면 ' '(empty string) 반환
> ```
> ORACLE 의 경우 NVL(expr1, expr2) 함수를 통해서 expr1 이 NULL 이면 expr2 를 반환하는 함수로 대체할 수 있음

### OVER
> `COUNT(*) OVER()` : 전체행 카운트  
> ```sql
> -- TABLE EXAM 
> -- EXM_NAME | MAJOR | SCORE
> --    홍길동  |    수학    |   99
> --    홍길순  |    수학    |   95
> --    고니   |    영어     |   33
>
> SELECT *, COUNT(*) OVER() FROM EXAM;
> -- 결과)
> -- EXM_NAME | MAJOR | SCORE | COUNT
> --    홍길동  |    수학    |    99     | 3
> --    홍길순  |    수학    |    95     | 3
> --    고니   |    영어     |    33     | 3
> ```
>
> `COUNT(*) OVER(PARTITION BY 컬럼)` : 그룹단위로 나누어 카운트  
> ```sql
> -- TABLE EXAM 
> -- EXM_NAME | MAJOR | SCORE
> --    홍길동  |    수학    |   99
> --    홍길순  |    수학    |   95
> --    고니   |    영어     |   33
>
> SELECT *, COUNT(*) OVER(PARTITION BY MAJOR) FROM EXAM;
> -- 결과)
> -- EXM_NAME | MAJOR | SCORE | COUNT
> --    홍길동  |    수학    |    99     | 2
> --    홍길순  |    수학    |    95     | 2
> --    고니   |    영어     |    33     | 1
> ```

### 서브쿼리를 하는 이유
> JOIN 및 SELECT 한 칼럼의 데이터를 그대로 쓰는 것이 아닌 함수(ex: TO_CHAR(COL1)) 연산이 들어가는 경우 JOIN 혹은 함수 연산을 진행할 ROW의 갯수를 줄이기 위해서 서브쿼리를 이용한다.

### ROW_NUMBER OVER
> ROW_NUMBER() OVER(PARTITION BY col1 ORDER BY col2 ASC/DESC): col1을 기준으로 ROW_NUMBER를 나눠서 매긴다.
> 예를 들어서 MAJOR 가 수학인 애들끼리 1부터 ROW_NUMBER를 주고 MAJOR 가 영어인 애들을 다시 1부터 ROW_NUMBER를 준다.
> 뒤에 ORDER BY는 생략이 가능하며 ORDER BY 에 SCORE DESC 를 넣어서 점수가 높은 애부터 1부터 ROW_NUMBER를 줄 수 있다.(줄세우기)
> ```sql
> -- TABLE EXAM 
> -- EXM_NAME | MAJOR | SCORE
> --    홍길동  |    수학    |   99
> --    홍길순  |    수학    |   95
> --    고니   |    영어     |  33
> --    희동이  |    영어     |  55
>
> -- PARTITION BY 및 ORDER BY 까지 하는 경우
> SELECT *, ROW_NUMBER() OVER (PARTITION BY MAJOR ORDER BY SCORE DESC) FROM EXAM;
> -- 결과)
> -- EXM_NAME | MAJOR | SCORE | ROW_NUMBER
> --    홍길동  |    수학    |    99     |    1
> --    홍길순  |    수학    |    95     |    2
> --    고니   |    영어     |    33    |    2
> --    희동이  |    영어    |    55     |    1
>
> -- ORDER BY 만 하는 경우
> SELECT *, ROW_NUMBER() OVER (ORDER BY SCORE DESC) FROM EXAM;
> -- 결과)
> -- EXM_NAME | MAJOR | SCORE | ROW_NUMBER
> --    홍길동  |    수학    |    99     |   1
> --    홍길순  |    수학    |    95     |   2
> --    희동이  |    영어    |    55     |   3
> --    고니   |    영어     |    33    |   4
> ```

### UNION
> 2개 이상의 SELECT 문의 결과를 합친다. 보통 일반 테이블과 히스토리 테이블과 결과를 합쳐서 보는데 사용한다.  
> `UNION`: 중복 행이 있는 경우 중복을 제거한다.  
> `UNION ALL`: 중복 행이 있어도 중복을 제거하지 않는다.  
> ```sql
> SELECT * FROM EXAM UNION ALL
> SELECT * FROM EXAM_HIS;
> ```

### INTERVAL
> 시간 조작 함수, 특정 시간으로 부터 `30분 전` 혹은 `1시간 후` 와 같이 시간 조건 조작을 위해서 주로 사용한다.  
> 아래 사용 예시는 postgreSQL 기준  
> ```sql
> # 현재 시간으로 부터 30분 전 시간 가져오기
> SELECT NOW() - INTERVAL '30 MINUTE';
>
> # 현재 시간으로 부터 1시간 후 시간 가져오기
> SELECT NOW() + INTERVAL '1 HOUR';
> ```

### InnoDB
> MySQL/MariaDB 에서 사용하는 트랜잭션-세이프 스토리지 엔진  
> 참조사이트: [InnoDB 란 무엇인가?](https://joridari.tistory.com/15)  
