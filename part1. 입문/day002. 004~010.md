-- 컬럼 두개를 이어서 출력하기
```sql
SELECT ename || sal
  FROM emp;
  
SELECT ename || '의 월급은 ' || sal || '입니다 ' as 월급정보
  FROM emp;
  
SELECT ename || '의 직업은 ' || job || '입니다 ' as 직업정보
  FROM emp;
```

<br>

-- 중복된 데이터를 제거해서 출력하기  
-- unique와 distinct는 완전히 같은 예약어이다.  
-- unique가 더 오래 되었지만 표준은 distinct 이고, unique는 어떤 DBMS에서는 인식되지 않을 수 있다.

```sql
SELECT DISTINCT job 
  FROM emp;

SELECT UNIQUE job 
  FROM emp;
```

<br>

-- 정렬해서 출력하기, 오름차순 정렬
```sql
SELECT ename, sal
  FROM emp
 ORDER BY sal ASC;
```

<br>  

> __쿼리의 실행 순서 : FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY__

<br>

-- ORDER BY 절은 맨 마지막에 실행되기 때문에 SELECT 절에 사용한 별칭을 ORDER BY 절에 사용할 수 있다.
```sql
SELECT ename, sal as 월급
  FROM emp
 ORDER BY 월급 ASC;
 
SELECT ename, deptno, sal
  FROM emp 
 ORDER BY deptno ASC, sal DESC;
```

-- ORDER BY 절에 작성한 숫자는 SELECT 절 컬럼의 순서
```sql
SELECT ename, deptno, sal
  FROM emp
 ORDER BY 2 ASC, 3 DESC;
```

-- 조건 검색
```sql
SELECT ename, sal, job 
  FROM emp 
 WHERE sal = 3000;
 
SELECT ename AS 이름, sal as 월급 
  FROM emp
 WHERE sal >= 3000;
```

-- 다음의 구문은 실행 순서로 인해 에러가 발생함. 위의 실행 순서를 참고
```sql
SELECT ename as 이름, sal as 월급
  FROM emp
 WHERE 월급 >= 3000;
```
 
-- 조건 검색 2
```sql
SELECT ename, sal, job, hiredate, deptno
  FROM emp
 WHERE ename='SCOTT';
```

-- 날짜 검색 시에는 접속한 session의 날짜 형식에 맞춰 작성해야 한다.
```sql
SELECT ename, hiredate
  FROM emp
 WHERE hiredate = '81/11/17';
```
<br>  

> __접속한 session의 날짜 형식 조회하는 방법__

```sql
SELECT * 
  FROM sys.nls_session_parameters 
 WHERE PARAMETER = 'NLS_DATE_FORMAT';
```

<br>

-- RR과 YY는 다르다.  

```sql
ALTER SESSION SET NLS_DATE_FORMAT='YY/MM/DD';

SELECT ename, sal
  FROM emp
 WHERE hiredate='81/11/17'; -- 검색되지 않음
 
-- 81년을 2081년으로 인식하기 때문
-- RR : 1981, YY : 2081
-- RR은 현재 세기를 기준으로 이전 세기로 인식, YY는 현재 세기의 연도로 인식

ALTER SESSION SET NLS_DATE_FORMAT='RR/MM/DD'; -- 복구
```

-- 산술
```sql
SELECT ename, sal*12 as 연봉 
  FROM emp 
 WHERE sal*12 >= 3600;
 
SELECT ename, sal, comm, sal + comm
  FROM emp
 WHERE deptno = 10;
-- null은 데이터가 할당되지 않은 상태 또는 알 수 없는 값

-- NVL을 사용하자.
SELECT sal + NVL(comm, 0) 
  FROM emp
 WHERE ename='KING';
```

-- 비교 연산자
```sql
SELECT ename, sal, job, deptno
  FROM emp
 WHERE sal <= 1200;
```