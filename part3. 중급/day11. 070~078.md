-- 집합 연산자로 데이터의 차이를 출력하기 (MINUS)

```sql
SELECT ename, sal, job, deptno 
  FROM emp 
 WHERE deptno IN (10,20) 
MINUS 
SELECT ename, sal, job, deptno 
  FROM emp 
 WHERE deptno IN (20,30); -- MINUS 연산자도 결과 데이터를 내림차순으로 정렬해서 출력
```
<br>

> - UNION / INTERSECT / MINUS 는 결과 데이터도 정렬하고 중복도 제거
>
> - UNION ALL은 결과 데이터를 정렬하지 않고 중복도 제거하지 않지만 검색 성능을 더 높일 수 있음

<br>

-- 서브 쿼리 사용하기 (단일 행 서브쿼리)
```sql
SELECT ename, sal 
  FROM emp 
 WHERE sal > (SELECT sal FROM emp WHERE ename='JONES');
```

<br>

-- 서브 쿼리 사용하기 (다중 행 서브쿼리)
```sql
SELECT ename, sal 
  FROM emp 
 WHERE sal IN (SELECT sal FROM emp WHERE job='SALESMAN'); 
 -- 서브 쿼리에서 메인 쿼리로 여러 개의 값이 반환 되는 것 = 다중 행 서브쿼리
 -- equal을 사용하면 안됨
```

<br>

-- 서브 쿼리 사용하기 (NOT IN) 
```sql
SELECT ename, sal, job 
  FROM emp 
 WHERE empno NOT IN (SELECT mgr FROM emp WHERE mgr IS NOT NULL);
 -- 서브 쿼리 결과에 NULL 값이 있다면 결과가 출력되지 않음
 -- 각 결과를 하나하나 WHERE의 컬럼과 비교하기 때문
 -- Ex. TRUE AND TRUE AND TRUE AND TRUE AND NULL
```

<br>

-- 서브 쿼리 사용하기 (EXISTS와 NOT EXISTS)
```sql
SELECT * 
  FROM dept d 
 WHERE EXISTS (SELECT * FROM emp e WHERE e.deptno = d.deptno); 
 -- 서브 쿼리 조건에 의하여 양 테이블에 해당 데이터가 존재하는지 검색
 -- 하나라도 존재 확인하면 다음으로 넘어감
 
SELECT * 
  FROM dept d 
 WHERE NOT EXISTS (SELECT * FROM emp e WHERE e.deptno = d.deptno); 
```

<br>

-- 서브 쿼리 사용하기 (HAVING 절의 서브 쿼리)
```sql
SELECT job, sum(sal) 
  FROM emp 
 GROUP BY job
 HAVING sum(sal) > (SELECT sum(sal) FROM emp WHERE job='SALESMAN'); 
 -- 그룹 함수로 검색 조건을 작성할 때는 WHERE 절을 사용할 수 없음
```

<br>
 
### SELECT 문에서 서브 쿼리문을 사용할 수 있는 절

SELECT문의 6가지 절 | 서브 쿼리 사용 여부 | 서브 쿼리 이름
:-------------------:|:--------------------:|------------------
SELECT | 가능 | 스칼라 서브 쿼리
FROM | 가능 | IN LINE VIEW 
WHERE | 가능 | 서브 쿼리
GROUP BY | 불가능 | 
ORDER BY | 가능 | 스칼라 서브 쿼리


<br>

-- 서브 쿼리 사용하기 (FROM절의 서브 쿼리)
```sql
SELECT v.ename, v.sal, v.순위 
  FROM (SELECT ename, sal, rank() over (order by sal desc) 순위 FROM emp) v 
 WHERE v.순위 = 1; -- in line view
 -- WHERE 절에는 분석 함수를 사용할 수 없음
```

<br>

-- 서브 쿼리 사용하기 (SELECT절의 서브 쿼리) 
```sql
SELECT ename
       , sal
       , (SELECT max(sal) FROM emp WHERE job='SALESMAN') as "최대 월급"
       , (SELECT min(sal) FROM emp WHERE job='SALESMAN') as "최소 월급"
  FROM emp
 WHERE job = 'SALESMAN'; 
 -- 스칼라 서브 쿼리는 출력되는 행 수만큼 반복되어 실행
 -- 같은 SQL이 반복되어 4번이나 실행되면서 같은 데이터를 반복해서 출력하므로,
 -- 첫 번째 행의 결과 값들을 메모리에 올려 놓고 다음 행 부터는 메모리의 데이터를 출력
 -- -> 서브 쿼리 캐싱
```

<br>

-- 데이터 입력하기 (INTERSERT) 
```sql
INSERT INTO emp (empno, ename, sal, hiredate, job) VALUES(2812, 'JACK', 3500, TO_DATE('2019/06/05', 'RRRR/MM/DD'), 'ANALYST');
```

<br>

### DML (Data Manipulation Language) 

종류 | 설명
:-------------------:|:--------------------:
INSERT | 데이터 입력
UPDATE | 데이터 수정
DELETE | 데이터 삭제
MERGE | 데이터 입력, 수정, 삭제를 한 번에 수행