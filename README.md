# SQL-Starter
SQL Query 문법 관련 정리

## Standard SQL
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
> SELECT *, ROW_NUMBER() OVER (PARTITION BY MAJOR ORDER BY SCORE DESC) FROM EXAM;
> -- 결과)
> -- NAME | MAJOR | SCORE | ROW_NUMBER
> -- 홍길동 | 수학  | 99     | 1
> -- 홍길순 | 수학  | 95     | 2
> -- 고니  | 영어   | 33    | 2
> -- 희동이 | 영어  | 55     | 1
> ```

