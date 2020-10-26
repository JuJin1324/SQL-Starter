# SQL-Starter
SQL Query 문법 관련 정리

## Standard SQL
### order by
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

