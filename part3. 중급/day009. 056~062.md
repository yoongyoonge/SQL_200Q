-- 출력되는 행 제한하기 (ROWNUM)
```sql 
SELECT ROWNUM, empno, ename, job, sal 
  FROM emp
 WHERE ROWNUM <= 5; 
 -- ROWNUM은 PSEUDO COLUMN으로 *로 건색해서는 출력되지 않는 감춰진 컬럼
```

<br>

-- 출력되는 행 제한하기 (Simple top-n queries)
```sql
SELECT empno, ename, job, sal
  FROM emp
 ORDER BY sal DESC FETCH FIRST 4 ROWS ONLY; 
 -- 정렬된 결과로부터 위쪽 또는 아래쪽의 n개 행을 반환하는 쿼리
 
SELECT empno, ename, job, sal 
  FROM emp
 ORDER BY sal desc 
 FETCH FIRST 20 PERCENT ROWS ONLY;
 
SELECT empno, ename, job, sal 
  FROM emp
 ORDER BY sal DESC FETCH FIRST 2 ROWS WITH TIES; 
 -- WITH TIES 옵션을 이용하면 여러 행이 N번째 값과 동일하다면 같이 출력
 
SELECT empno, ename, job, sal 
  FROM emp
 ORDER BY sal DESC OFFSET 9 ROWS; 
 -- OFFSET 옵션을 이용하면 출력이 시작되는 행의 위치를 지정할 수 있음
 
SELECT empno, ename, job, sal 
  FROM emp 
 ORDER BY sal DESC OFFSET 9 ROWS 
 FETCH FIRST 2 ROWS ONLY;
```

<br>

-- 여러 테이블의 데이터를 조인해서 출력하기 (EQUI JOIN) 
```sql 
SELECT ename, loc 
  FROM emp, dept 
 WHERE emp.deptno = dept.deptno;
 
SELECT ename, loc 
  FROM emp, dept;
  
SELECT ename, loc 
  FROM emp, dept
 WHERE emp.deptno = dept.deptno;
 
SELECT ename, loc 
  FROM emp, dept
 WHERE emp.deptno = dept.deptno and emp.job='ANALYST';
 
SELECT ename, loc, job, emp.deptno 
  FROM emp, dept
 WHERE emp.deptno = dept.deptno and emp.job='ANALYST'; 
 -- 컬럼이 두 테이블 모두 있을 때는 명시해 주어야 함

SELECT emp.ename, dept.loc, emp.job 
  FROM emp, dept 
 WHERE emp.deptno = dept.deptno and emp.job='ANALYST'; 
 -- 검색 속도 향상을 위해서는 열 이름 앞에 테이블 명을 붙여주자
 
SELECT e.ename, d.loc, e.job 
  FROM emp e, dept d 
 WHERE e.deptno = d.deptno and e.job='ANALYST'; 
 -- 별칭, 별칭을 지정했으면 별칭으로 다 바꾸어주자
```

<br>

-- 여러 테이블의 데이터를 조인해서 출력하기 (NON EQUI JOIN) 

```sql
SELECT e.ename, e.sal, s.grade 
  FROM emp e, salgrade s 
 WHERE e.sal between s.losal and s.hisal; -- salgrade : 급여 등급 테이블
 
SELECT * FROM salgrade;

SELECT e.ename, e.sal, s.grade 
  FROM emp e, salgrade s 
 WHERE e.sal between s.losal and s.hisal;
```

<br>

-- 여러 테이블의 데이터를 조인해서 출력하기 (OUTER JOIN) 
```sql
SELECT e.ename, d.loc 
  FROM emp e, dept d 
 WHERE e.deptno (+) = d.deptno; 
 -- (+) : outer join 사인, 결과가 덜 나오는 쪽에 붙여주자 
 -- //Ex. e.deptno = (+) d.deptno
```

<br>

-- 여러 테이블의 데이터를 조인해서 출력하기 (SELF JOIN) 
```sql
SELECT e.ename as 사원, e.job as 직업, m.ename as 관리자, m.job as 직업 
  FROM emp e, emp m 
 WHERE e.mgr = m.empno and e.job='SALESMAN'; 
 -- 데이터의 관계가 트리형으로 있는 경우
```

<br>

-- 여러 테이블의 데이터를 조인해서 출력하기 (ON절)
```sql
SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e JOIN dept d 
  ON (e.deptno = d.deptno) 
  WHERE e.job='SALESMAN';
```

<br>

-  오라클 조인
    - equi join 
    - non equi join 
    - outer join 
    - self join

<br>

- ansi/iso sql: 1999 standards
    - on절을 사용한 join 
    - left, right, full outer join
    - using절을 사용한 join
    - natural join, cross join