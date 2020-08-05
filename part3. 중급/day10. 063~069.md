-- 여러 테이블의 데이터를 조인해서 출력하기 (USING절)
```sql
SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e join dept d 
 USING (deptno) 
 WHERE e.job='SALESMAN'; 
 -- USING 다음엔 사용할 컬럼만, 별칭을 사용하면 에러! 
 -- 괄호를 사용하여 컬럼 명시, 사용하지 않으면 에러!
 -- 여러 개의 테이블을 조인하려면 조인하는 테이블 명 다음에 USING절 사용하기
```
<br>

-- 여러 테이블의 데이터를 조인해서 출력하기 (NATURAL JOIN)
```sql
SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e natural join dept d
 WHERE e.job='SALESMAN'; -- 오라클이 알아서 동일한 컬럼을 찾아 조인을 수행
 -- 조인의 연결고리가 되는 컬럼은 별칭 없이 기술해야 함 
 -- (NATURAL 조인에 사용된 열은 식별자를 가질 수 없음)
 

SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e NATURAL JOIN dept d 
 WHERE e.job='SALESMAN' AND deptno = 30;
```

<br>

-- 여러 테이블의 데이터를 조인해서 출력하기 (left/right outer join)
```sql
SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e RIGHT OUTER JOIN dept d 
    ON (e.deptno = d.deptno); -- 1999 ANSI/SQL OUTER JOIN 


SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e, dept d 
 WHERE e.deptno (+) = d.deptno; -- ORABLE OUTER JOIN


--------------------------------------------------------------------------------------------------------------------------
INSERT INTO emp(empno, ename, sal, job, deptno) VALUES(8282, 'JACK', 3000, 'ANALYST', 50);
--------------------------------------------------------------------------------------------------------------------------


SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e LEFT OUTER JOIN dept d 
    ON (e.deptno = d.deptno); -- 1999 ANSI/ISO OUTER JOIN
    
SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e, dept d 
 WHERE e.deptno = d.deptno (+); -- ORACLE OUTER JOIN
```

<br>

-- 여러 테이블의 데이터를 조인해서 출력하기 (FULL OUTER JOIN) 
```sql
SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e FULL OUTER JOIN dept d 
    ON (e.deptno = d.deptno); 

    -- 위와 아래의 쿼리 결과는 같음, ORACLE 문법 (+)으로는 FULL OUTER를 할 수 없음

SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e LEFT OUTER JOIN dept d 
    ON (e.deptno = d.deptno)
UNION 
SELECT e.ename as 이름, e.job as 직업, e.sal as 월급, d.loc as "부서 위치" 
  FROM emp e RIGHT OUTER JOIN dept d 
    ON (e.deptno = d.deptno); 
```    

<br>

-- 집합 연산자로 데이터를 위아래로 연결하기 (UNION ALL) 
```sql
SELECT deptno, sum(sal) 
  FROM emp 
  GROUP BY deptno 
UNION ALL 
SELECT TO_NUMBER(NULL) AS deptno, sum(sal) 
  FROM emp; 
  -- 부서 번호별 토탈 월급과 토탈 월급
  -- TO_NUMBER 함수 사용 이유 : 위쪽 쿼리 DEPTNO의 데이터 유형이 숫자이기 때문에 맞춰줌
  -- UNION ALL은 중복을 제거하지 않고 그대로 출력
```

<br>

<code>

* 집합 연산자 작성 시 주의사항
    * UNION ALL 위쪽 쿼리와 아래쪽 쿼리 컬럼의 개수가 동일해야 함
    * UNION ALL 위쪽 쿼리와 아래쪽 쿼리 컬럼의 데이터 타입이 동일해야 함
    * 결과로 출력되는 컬럼명은 위쪽 쿼리의 컬럼명으로 출력
    * ORDER BY절은 제일 아래쪽 쿼리에만 작성할 수 있음
    
</code>

<br>

-- 집합 연산자로 데이터를 위아래로 연결하기 (UNION) 
```sql
SELECT deptno, sum(sal) 
  FROM emp 
  GROUP BY deptno 
UNION  
SELECT NULL AS deptno, sum(sal) 
  FROM emp;
```

<br>

<code>

- UNION 연산자가 UNION ALL과 다른 점
    - 중복된 데이터를 하나의 고유한 값으로 출력
    - 첫 번째 컬럼의 데이터를 기준으로 내림차순으로 정렬하여 출력

</code>

<br>

-- 집합 연산자로 데이터의 교집합을 출력하기 (INTERSECT) 
```sql
SELECT ename, sal, job, deptno 
  FROM emp 
  WHERE deptno IN (10, 20) 
INTERSECT
SELECT ename, sal, job, deptno 
  FROM emp
  WHERE deptno IN (20, 30);
  -- INTERSECT 연산자도 UNION 연산자처럼 중복된 데이터를 제거하고 결과 데이터를 내림차순으로 정렬해서 출력
```