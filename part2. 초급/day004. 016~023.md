-- 첫 번째 컬럼은 대문자, 두 번째 컬럼은 소문자, 세 번째 컬럼은 첫 번째 철자는 대문자/나머지는 소문자
```sql
SELECT UPPER(ename), LOWER(ename), INITCAP(ename) 
  FROM emp;
  
SELECT ename, sal 
  FROM emp
 WHERE LOWER(ename)='scott';
```
<br>
-- 문자 추출 SUBSTR(STR, 시작위치, 추출문자개수)

```sql
SELECT SUBSTR('SMITH', 1, 3)
  FROM DUAL;
```
<br>
-- 문자 길이 출력

```sql
SELECT ename, LENGTH(ename)
  FROM emp;
  
SELECT LENGTH('가나다라마') 
  FROM DUAL;
  
-- LENGTHB는 바이트의 길이를 반환
SELECT LENGTHB('가나다라마') 
  FROM DUAL;
```

<br>
-- 특정 철자의 위치

```sql
SELECT INSTR('SMITH', 'M') 
  FROM DUAL;

-- @의 위치 추출 : 8  
SELECT INSTR('abcdefg@naver.com', '@')
  FROM DUAL;
  
-- 도메인 추출
SELECT SUBSTR('abcdefg@naver.com', INSTR('abcdefg@naver.com', '@')+1) 
  FROM DUAL;
  
SELECT RTRIM(SUBSTR('abcdefg@naver.com', INSTR('abcdefg@naver.com', '@')+1), '.com') 
  FROM DUAL;
```  

<br>
-- 철자 변경

```sql
SELECT ename, REPLACE(sal, 0, '*') 
  FROM emp;
  
SELECT ename, REGEXP_REPLACE(sal, '[0-3]', '*') as SALRAY 
  FROM emp;
```
<br>
-- 예제를 위한 테이블 스크립트

```sql
CREATE TABLE TEST_ENAME 
(ENAME VARCHAR2(10));

INSERT INTO TEST_ENAME VALUES('김인호');
INSERT INTO TEST_ENAME VALUES('안상수');
INSERT INTO TEST_ENAME VALUES('최영희');

COMMIT;


SELECT REPLACE(ename, SUBSTR(ename, 2, 1), '*') as "전광판 이름" 
  FROM test_ename;
```

<br>
-- 특정 철자를 N개 만큼 채우기

```sql
SELECT ename, LPAD(sal, 10, '*') as salary1, RPAD(sal, 10, '*') as salary2 
  FROM emp;

-- 월급 100을 ■로 출력, 시각화
SELECT ename, sal, lpad('■', round(sal/100), '■') as bar_chart 
  FROM emp;
```
<br>
-- 특정 철자 잘라내기

```sql
SELECT 'smith', LTRIM('smith', 's'), RTRIM('smith', 'h'), TRIM('s' from 'smiths') 
  FROM DUAL;
  
INSERT INTO emp(empno, ename, sal, job, deptno) VALUES (8219, 'JACK ', 3000, 'SALSMAN', '30');
COMMIT;

SELECT ename, sal
  FROM emp
 WHERE ename='JACK'; -- 결과 없음
 
SELECT ename, sal 
  FROM emp
 WHERE RTRIM(ename)='JACK';
 
DELETE FROM EMP WHERE TRIM(ENAME)='JACK';
COMMIT;
```
<br>
-- 반올림해서 출력하기

```sql
SELECT '876.567' AS 숫자, ROUND(876.567, 1) 
  FROM DUAL;
```