-- 숫자를 버리고 출력하기
```sql
SELECT '876.567' AS 숫자, TRUNC(876.567, 1) 
  FROM DUAL;
```
<br>
-- 나눈 나머지 값 출력하기

```sql
SELECT MOD(10, 3) 
  FROM DUAL;

SELECT empno, MOD(empno, 2) 
  FROM emp; -- 사원 번호가 홀수이면 1, 짝수이면 0을 출력
  
SELECT empno, ename 
  FROM emp
 WHERE MOD(empno, 2) = 0;
 
SELECT FLOOR(10/3) 
  FROM DUAL; -- 몫 출력
```
<br>  
-- 날짜간 개월 수 출력

```sql
SELECT ename, MONTHS_BETWEEN(sysdate, hiredate) 
  FROM emp; -- MONTHS_BETWEEN(최신날짜, 예전날짜)
  
SELECT TO_DATE('2019-06-01', 'RRRR-MM-DD') - TO_DATE('2018-10-01', 'RRRR-MM-DD') 
  FROM DUAL;

SELECT ROUND((TO_DATE('2019-06-01', 'RRRR-MM-DD') - TO_DATE('2018-10-01', 'RRRR-MM-DD')) / 7) AS "총 주수"
  FROM DUAL; -- 주는 계산 가능, 달은 각 월 별 일자가 다르니 함수(MONTHS_BETWEEN)를 사용하자.
```
<br>
-- 개월 수 더한 날짜 출력하기

```sql
SELECT ADD_MONTHS(TO_DATE('2019-05-01', 'RRRR-MM-DD'), 100) 
  FROM DUAL;
  
SELECT TO_DATE('2019-05-01', 'RRRR-MM-DD') + 100 
  FROM DUAL;
  
SELECT TO_DATE('2019-05-01', 'RRRR-MM-DD') + INTERVAL '100' MONTH 
  FROM DUAL;
  
SELECT TO_DATE('2019-05-01', 'RRRR-MM-DD') + INTERVAL '1-3' YEAR(1) TO MONTH 
  FROM DUAL;
  
SELECT TO_DATE('2019-05-01', 'RRRR-MM-DD') + INTERVAL '3' YEAR 
  FROM DUAL;
  
SELECT TO_DATE('2019-05-01', 'RRRR-MM-DD') + TO_YMINTERVAL('03-05') AS 날짜 
  FROM DUAL;
```
<br>
-- 특정 날짜 뒤에 오는 요일 날짜 출력하기

```sql
SELECT '2019/05/22' AS 날짜, NEXT_DAY('2019/05/22', '월요일') 
  FROM DUAL;
  
SELECT SYSDATE AS "오늘 날짜" 
  FROM DUAL;
  
SELECT NEXT_DAY(SYSDATE, '화요일') AS "다음 날짜" 
  FROM DUAL;
  
SELECT NEXT_DAY(ADD_MONTHS('2019/05/22', 100), '화요일') AS "다음 날짜"
  FROM DUAL;
```
<br>
-- 특정 날짜가 있는 달의 마지막 날짜 출력하기

```sql
SELECT '2019/05/22' AS 날짜, LAST_DAY('2019/05/22') AS "마지막 날짜" 
  FROM DUAL;
  
SELECT LAST_DAY(SYSDATE) - SYSDATE AS "남은 날짜" 
  FROM DUAL;
  
SELECT ename, hiredate, LAST_DAY(hiredate) 
  FROM emp
 WHERE ename='KING';
``` 
<br>
-- 문자형으로 데이터 유형 변환하기

```sql
SELECT ename, TO_CHAR(hiredate, 'DAY') AS 요일, TO_CHAR(sal, '999,999') AS 월급
  FROM emp
 WHERE ename='SCOTT'; -- 숫자형 -> 문자형, 날짜형 -> 문자형 변환 시 TO_CHAR 사용 
 -- TO_CHAR(sal, '999,999') : 천 단위 표시
 
SELECT hiredate
       , TO_CHAR(hiredate, 'RRRR') AS 연도
       , TO_CHAR(hiredate, 'MM') AS 달
       , TO_CHAR(hiredate, 'DD') AS 일
       , TO_CHAR(hiredate, 'DAY') AS 요일
  FROM emp
 WHERE ename='KING';
 
SELECT ename, hiredate
  FROM emp
 WHERE TO_CHAR(hiredate, 'RRRR') = '1981';
 
SELECT ename as 이름
       ,EXTRACT(year from hiredate) as 연도
       ,EXTRACT(month from hiredate) as 달
       ,EXTRACT(day from hiredate) as 요일
  FROM emp;
  
SELECT ename as 이름, TO_CHAR(sal*200, '999,999,999') as 월급
  FROM emp; -- 천 단위와 백만 단위를 표시
  
SELECT ename as 이름, TO_CHAR(sal*200, 'L999,999,999') as 월급
  FROM emp; -- L을 사용하면 화폐 단위를 붙여 출력할 수 있음
``` 
<br>
-- 날짜형으로 데이터 유형 변환하기

```sql
SELECT ename, hiredate
  FROM emp 
  WHERE hiredate = TO_DATE('81/11/17', 'RR/MM/DD');
  
SELECT * 
  FROM SYS.nls_session_parameters 
 WHERE parameter = 'NLS_DATE_FORMAT';
 
SELECT ename, hiredate
  FROM emp
 WHERE hiredate = '81/11/17';
 
ALTER SESSION SET NLS_DATE_FORMAT='DD/MM/RR';

SELECT ename, hiredate 
  FROM emp 
 WHERE hiredate = '17/11/81';
 
SELECT ename, hiredate 
  FROM emp
 WHERE hiredate = TO_DATE('81/11/17', 'RR/MM/DD');
 
ALTER SESSION SET NLS_DATE_FORMAT = 'RR/MM/DD';
```