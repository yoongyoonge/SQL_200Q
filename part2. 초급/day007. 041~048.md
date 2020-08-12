-- 데이터 분석 함수로 순위 출력
```sql
SELECT ename, job, sal, RANK() over (ORDER BY sal DESC) 순위
  FROM emp 
  WHERE job in ('ANALYST','MANAGER');

SELECT ename, sal, job, RANK() over (PARTITION BY job
                                     ORDER BY sal DESC) as 순위
  FROM emp;
```

<br>

-- 데이터 분석 함수로 순위 출력
```sql
SELECT ename, job, sal, RANK() over (ORDER BY sal DESC) AS RANK,
                        DENSE_RANK() over (ORDER BY sal DESC) AS DENSE_RANK
  FROM emp 
  WHERE job in ('ANALYST','MANAGER');

SELECT job, ename, sal, DENSE_RANK() OVER (PARTITION BY job
                                           ORDER BY sal DESC) 순위
  FROM emp
  WHERE hiredate BETWEEN to_date('1981/01/01','RRRR/MM/DD') 
                     AND to_date('1981/12/31','RRRR/MM/DD');

SELECT DENSE_RANK(2975) within group (ORDER BY sal DESC) 순위
  FROM emp; -- within group : 어느 그룹에서 순위가 어떻게 되는지

SELECT DENSE_RANK('81/11/17') within group (ORDER BY hiredate ASC) 순위
  FROM emp;
```

<br>

-- 데이터 분석 함수로 등급 출력
```sql
SELECT ename, job, sal, 
        NTILE(4) over (order by sal desc nulls last) 등급 
  FROM emp
  WHERE job in ('ANALYST','MANAGER','CLERK');
-- NTILE 함수는 등급을 나눔, nulls last는 null을 맨 아래에 출력하겠다는 의미
```

<br>

-- 데이터 분석 함수로 순위의 비율 출력
```sql
SELECT ename, sal, RANK() over (order by sal desc) as RANK ,
                   DENSE_RANK() over (order by sal desc) as DENSE_RANK,
                   CUME_DIST()  over (order by sal desc) as CUM_DIST
  FROM emp;
  -- cume_dist는 등수의 비율을 출력, 같은 등수가 여러 명 있으면 마지막 등수로 계산 (2등이 2명이면 3으로 비율을 계산)

SELECT job, ename, sal, RANK() over ( partition by job
                                      order by sal desc) as RANK ,
                        CUME_DIST() over ( partition by job
                                           order by sal desc) as CUM_DIST
  FROM emp;
```

<br>

-- 데이터 분석 함수로 데이터를 가로로 출력
```sql
SELECT deptno, LISTAGG(ename,',') within group (order by ename) as EMPLOYEE
  FROM emp
  GROUP BY deptno;
-- listagg 함수는 데이터를 가로로 출력하는 함수, 구분자 설정 가능
-- group by 절은 필수로 기술해야 함

SELECT job, LISTAGG(ename,',') within group (ORDER BY ename asc) as employee
  FROM emp
  GROUP BY job;

SELECT job, 
       LISTAGG(ename||'('||sal||')',',') within group (ORDER BY ename asc) as employee
  FROM emp
  GROUP BY job;
```

<br>

-- 데이터 분석 함수로 바로 전 행과 다음 행 출력
```sql
SELECT empno, ename, sal, 
        LAG(sal, 1) over (order by sal asc) "전 행",
        LEAD(sal, 1) over (order by sal asc) "다음 행"
  FROM emp
 WHERE job IN ('ANALYST', 'MANAGER');

SELECT empno, ename, hiredate, 
        LAG(hiredate, 1) over (order by hiredate asc) "전 행",
        LEAD(hiredate, 1) over (order by hiredate asc) "다음 행"
  FROM emp 
 WHERE job IN ('ANALYST', 'MANAGER');

SELECT deptno, empno, ename, hiredate, 
        LAG(hiredate, 1) over (partition by deptno order by hiredate asc) "전 행",
        LEAD(hiredate, 1) over (partition by deptno order by hiredate asc) "다음 행"
  FROM emp;
```

<br>

-- column을 row로 출력
```sql
SELECT SUM(DECODE(deptno, 10, sal)) as "10", 
       SUM(DECODE(deptno, 20, sal)) as "20",
       SUM(DECODE(deptno, 30, sal)) as "30"
  FROM emp;

SELECT deptno, DECODE(deptno, 10, sal) as "10"
  FROM emp;
  
SELECT SUM(DECODE(deptno, 10, sal)) as "10" 
  FROM emp;
  
SELECT SUM(DECODE(job, 'ANALYST', sal)) as "ANALYST",
       SUM(DECODE(job, 'CLERK', sal)) as "CLERK",
       SUM(DECODE(job, 'MANAGER', sal)) as "MANAGER",
       SUM(DECODE(job, 'SALESMAN', sal)) as "SALESMAN"
  FROM emp; -- 직업이 뭔지 알 때 작성할 수 있는 쿼리, 모를때는 PL/SQL을 사용해야 함

SELECT SUM(DECODE(job, 'ANALYST', sal)) as "ANALYST",
       SUM(DECODE(job, 'CLERK', sal)) as "CLERK",
       SUM(DECODE(job, 'MANAGER', sal)) as "MANAGER",
       SUM(DECODE(job, 'SALESMAN', sal)) as "SALESMAN"
  FROM emp
 GROUP BY deptno; -- 월급 분포를 볼 수 있음
```

<br>

-- COLUMN을 ROW로 출력하기
```sql
SELECT * 
  FROM (SELECT deptno, sal from emp)
  PIVOT (sum(sal) for deptno IN (10, 20, 30));
  
SELECT * 
  FROM (SELECT job, sal from emp) 
  PIVOT (sum(sal) for job IN ('ANALYST', 'CLERK', 'MANAGER', 'SALESMAN'));
  
SELECT * 
  FROM (SELECT job, sal from emp) 
  PIVOT (sum(sal) for job IN ('ANALYST' as "ANALYST", 'CLERK' as "CLERK", 'MANAGER' as "MANAGER", 'SALESMAN' as "SALESMAN"));
  -- as 로 새로 명명하면 싱글 쿼테이션 없이 출력할 수 있게 됨
```