# SQL-Starter
SQL Query 문법 관련 정리

## DBMS
### Key 와 Index
> 대부분의 DB 에서는 Key(PK/UK/FK) 생성 시에 모두 자동으로 Index 를 생성해준다. 하지만 Index 가 자동으로 생성되면 시스템에서 자동으로 붙여준 이름으로 생성이 된다.
> Index 를 이용한 쿼리 및 Index 관리 측면에서 Index 이름을 지정해서 생성한 다음에 Table 의 PK 혹은 UK 로 사용하는 것이 바람직하다.

### 테이블 관계
> 예시 테이블
> ```sql
> CREATE TABLE DEPARTMENT(
> DPT_ID INT AUTO_INCREMENT PRIMARY KEY,
> DPT_NAME VARCHAR(20) NOT NULL,
> DPT_CODE CHAR(13) NOT NULL UNIQUE KEY
> );
> 
> CREATE TABLE EMPLOYEE (
> EMP_ID INT AUTO_INCREMENT PRIMARY KEY,
> EMP_NAME VARCHAR(20) NOT NULL,
> EMP_CODE CHAR(13) NOT NULL UNIQUE KEY,
> DPT_ID INT,
> FOREIGN KEY (DPT_ID) REFERENCES DEPARTMENT(DPT_ID)
> );
> ```
> 위의 테이블은 다대일 관계 테이블이다: 다(EMPLOYEE) 대 일(DEPARTMENT)  
> 일(DEPARTMENT)인 테이블이 부모 테이블이며 다(EMPLOYEE)인 테이블이 자식 테이블이다.  
> 외래키를 가진 테이블(EMPLOYEE)이 자식 테이블이고, 참조되는 테이블(DEPARTMENT)이 부모 테이블이다.  
> ERD 에서 까치발을 가지고 있는 테이블이 다(자식) 테이블 / '|' 을 가지고 있는 테이블이 일(부모) 테이블이다.  

### JOIN: Inner vs Outer
> Outer 조인의 경우 쿼리 실행 Cost 가 Inner 보다 많이 들며 Outer 조인의 경우 Explain 으로 Cost 측정이 불가능하다.

## Oracle
### Outer Join
> LEFT OUTER JOIN: 왼쪽에 명기된 테이블의 요소는 다 나오고 오른쪽에 명기된 테이블 요소는 왼쪽과 매칭되는 row가 없으면 null로 표기한다.  
> RIGHT OUTER JOIN: 오른쪽에 명기된 테이블의 요소는 다 나오고 왼쪽에 명기된 테이블 요소는 오른쪽과 매칭되는 row가 없으면 null로 표기한다.  
> ```sql
> -- TABLE STUDENT
> -- STUDENT_KEY | STUDENT_NAME
> --     1       |      A
> --     2       |      B
> --     3       |      C
> --     4       |      D
>
> -- TABLE GRADE
> -- STUDENT_KEY | SCORE
> --     1       |  10
> --     2       |  20
> --     4       |  40
> 
> -- STUDENT TABLE 기준 LEFT OUTER JOIN
> SELECT *
> FROM STUDENT ST, GRADE GD
> WHERE ST.STUDENT_KEY = GD.STUDENT_KEY(+);     -- 왼쪽 테이블 전부에 오른쪽 테이블이 (+)되는 형태로 기억하자.
> 
> -- 결과
> -- ST.STUDENT_KEY | NAME | GD.STUDENT_KEY | SCORE 
> --        1       |  A   |        1       |  10
> --        2       |  B   |        2       | 20
> --        4       |  D   |        4       | 40
> --        3       |  C   |        null    | null
> 
> -- STUDENT TABLE 기준 RIGHT OUTER JOIN
> SELECT * 
> FROM STUDENT ST, GRADE GD
> WHERE ST.STUDENT_KEY(+) = GD.STUDENT_KEY;     -- 오른쪽 테이블 전부에 왼쪽 테이블이 (+)되는 형태로 기억하자.
> 
> -- 결과
> -- ST.STUDENT_KEY | NAME | GD.STUDENT_KEY | SCORE 
> --        1       |  A   |        1       |  10
> --        2       |  B   |        2       | 20
> --        4       |  D   |        4       | 40
> ```

## Standard SQL
### Scalar(스칼라) [권장하지 않음.]
> SQL 에서 단일 값을 스칼라 값이라고 한다.  
> 스칼라 서브쿼리는 SELECT 절에서 오는 서브쿼리로 결과 값으로 1행만 반환한다.
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
> -- STUDENT_KEY | STUDENT_NAME
> --     1       |      A
> --     2       |      B
> --     3       |      C
> --     4       |      D
>
> -- TABLE GRADE
> -- STUDENT_KEY | SCORE
> --     1       |  10
> --     2       |  20
> --     4       |  40
> 
> -- STUDENT TABLE 기준 LEFT OUTER JOIN
> SELECT *
> FROM STUDENT ST 
> LEFT OUTER JOIN GRADE GD ON GD.STUDENT_KEY = ST.STUDENT_KEY;  -- 일부러 GD 를 앞에 두었다.
> 
> -- 결과
> -- ST.STUDENT_KEY | NAME | GD.STUDENT_KEY | SCORE 
> --        1       |  A   |        1       |  10
> --        2       |  B   |        2       | 20
> --        4       |  D   |        4       | 40
> --        3       |  C   |        null    | null
> 
> -- STUDENT TABLE 기준 RIGHT OUTER JOIN
> SELECT * 
> FROM STUDENT ST
> RIGHT OUTER JOIN GRADE GD ON GD.STUDENT_KEY = ST.STUDENT_KEY;  -- 일부러 GD 를 앞에 두었다.
> 
> -- 결과
> -- ST.STUDENT_KEY | NAME | GD.STUDENT_KEY | SCORE 
> --        1       |  A   |        1       |  10
> --        2       |  B   |        2       | 20
> --        4       |  D   |        4       | 40
> ```

### INNER JOIN
> 조인하는 두 테이블에서 조인 조건에 부합하는 ROW 만 가져온다.
> ```sql
> -- TABLE STUDENT
> -- STUDENT_KEY | STUDENT_NAME
> --     1       |      A
> --     2       |      B
> --     3       |      C
> --     4       |      D
>
> -- TABLE GRADE
> -- STUDENT_KEY | SCORE
> --     1       |  10
> --     2       |  20
> --     4       |  40
>
> -- INNER JOIN
> SELECT 
>   * 
> FROM STUDENT ST, GRADE GD
> WHERE 
>   GD.STUDENT_KEY = ST.STUDENT_KEY;    -- 다음 조건에도 GD 에 관한 WHERE 조건이 나오도록 GD를 먼저 써줬다.
> 
> -- 결과
> -- ST.STUDENT_KEY | NAME | GD.STUDENT_KEY | SCORE 
> --        1       |  A   |        1       |  10
> --        2       |  B   |        2       | 20
> --        4       |  D   |        4       | 40
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
> -- NAME | MAJOR | SCORE
> -- 홍길동 | 수학  | 99
> -- 홍길순 | 수학  | 95
> -- 고니  | 영어   | 33
>
> SELECT *, COUNT(*) OVER() FROM EXAM;
> -- 결과)
> -- NAME | MAJOR | SCORE | COUNT
> -- 홍길동 | 수학  | 99   | 3
> -- 홍길순 | 수학  | 95   | 3
> -- 고니  | 영어   | 33   | 3
> ```
>
> `COUNT(*) OVER(PARTITION BY 컬럼)` : 그룹단위로 나누어 카운트  
> ```sql
> -- TABLE EXAM 
> -- NAME | MAJOR | SCORE
> -- 홍길동 | 수학  | 99
> -- 홍길순 | 수학  | 95
> -- 고니  | 영어   | 33
>
> SELECT *, COUNT(*) OVER(PARTITION BY MAJOR) FROM EXAM;
> -- 결과)
> -- NAME | MAJOR | SCORE | COUNT
> -- 홍길동 | 수학  | 99   | 2
> -- 홍길순 | 수학  | 95   | 2
> -- 고니  | 영어   | 33   | 1
> ```

### 서브쿼리를 하는 이유
> JOIN 및 SELECT 한 칼럼의 데이터를 그대로 쓰는 것이 아닌 함수(ex: TO_CHAR(COL1)) 연산이 들어가는 경우 JOIN 혹은 함수 연산을 진행할 ROW의 갯수를 줄이기 위해서 서브쿼리를 이용한다.

### ROW_NUMBER OVER
> ROW_NUMBER() OVER(PARTITION BY col1 ORDER BY col2 ASC/DESC): col1을 기준으로 ROW_NUMBER를 나눠서 매긴다.
> 예를 들어서 MAJOR 가 수학인 애들끼리 1부터 ROW_NUMBER를 주고 MAJOR 가 영어인 애들을 다시 1부터 ROW_NUMBER를 준다.
> 뒤에 ORDER BY는 생략이 가능하며 ORDER BY 에 SCORE DESC 를 넣어서 점수가 높은 애부터 1부터 ROW_NUMBER를 줄 수 있다.(줄세우기)
> ```sql
> -- TABLE EXAM 
> -- NAME | MAJOR | SCORE
> -- 홍길동 | 수학  | 99
> -- 홍길순 | 수학  | 95
> -- 고니  | 영어   | 33
> -- 희동이 | 영어  | 55
>
> -- PARTITION BY 및 ORDER BY 까지 하는 경우
> SELECT *, ROW_NUMBER() OVER (PARTITION BY MAJOR ORDER BY SCORE DESC) FROM EXAM;
> -- 결과)
> -- NAME | MAJOR | SCORE | ROW_NUMBER
> -- 홍길동 | 수학  | 99     | 1
> -- 홍길순 | 수학  | 95     | 2
> -- 고니  | 영어   | 33    | 2
> -- 희동이 | 영어  | 55     | 1
>
> -- ORDER BY 만 하는 경우
> SELECT *, ROW_NUMBER() OVER (ORDER BY SCORE DESC) FROM EXAM;
> -- 결과)
> -- NAME | MAJOR | SCORE | ROW_NUMBER
> -- 홍길동 | 수학  | 99     | 1
> -- 홍길순 | 수학  | 95     | 2
> -- 희동이 | 영어  | 55     | 3
> -- 고니  | 영어   | 33    | 4
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
