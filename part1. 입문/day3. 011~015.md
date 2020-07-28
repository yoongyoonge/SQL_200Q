-- BETWEEN 
```sql
SELECT ename, sal
  FROM emp
 WHERE sal BETWEEN 1000 AND 3000;
 
-- between 하한값 and 상한값 순으로 작성해야한다.
SELECT ename, sal
  FROM emp
 WHERE (sal >= 1000 AND sal <= 3000);
 
SELECT ename, sal
  FROM emp
 WHERE sal NOT BETWEEN 1000 AND 3000;
 
SELECT ename, sal
  FROM emp
 WHERE (sal < 1000 OR sal > 3000);
 
SELECT ename, hiredate
  FROM emp
 WHERE hiredate BETWEEN '1982/01/01' AND '1982/12/31';
```

<br>

-- LIKE
```sql
SELECT ename, sal
  FROM emp
 WHERE ename LIKE 'S%';
 
SELECT ename 
  FROM emp
 WHERE ename LIKE '_M%';
 
SELECT ename 
  FROM emp
 WHERE ename LIKE '%T';
 
SELECT ename 
  FROM emp
 WHERE ename LIKE '%A%';
 
-- IS NULL
SELECT ename, comm
  FROM emp
 WHERE comm is null;
-- null은 알 수 없는 값이기 때문에 =로는 비교할 수 없다.
```

<br>

-- 비교 연산자 IN
```sql
SELECT ename, sal, job 
  FROM emp
 WHERE job in ('SALESMAN', 'ANALYST', 'MANAGER');
 
SELECT ename, sal, job 
  FROM emp
 WHERE (job='SALESMAN' or job='ANALYST' or job='MANAGER');
 
--

SELECT ename, sal, job
  FROM emp
 WHERE job NOT IN ('SALESMAN', 'ANALYST', 'MANAGER');
 
SELECT ename, sal, job
  FROM emp
 WHERE (job!='SALESMAN' or job!='ANALYST' or job!='MANAGER');
```
 
<br> 
 
-- 논리 연산자 AND, OR, NOT
```sql
SELECT ename, sal, job 
  FROM emp
 WHERE job='SALESMAN' AND sal >= 1200;
 
SELECT ename, sal, job
  FROM emp
 WHERE job='ABCDEFG' and sal >= 1200;
```