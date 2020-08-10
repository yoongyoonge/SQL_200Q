-- 서브 쿼리를 사용하여 데이터 수정하기
```sql
UPDATE emp SET sal = (SELECT sal FROM emp WHERE ename='ALLEN') WHERE job='SALESMAN';

UPDATE emp SET (sal, comm) = (SELECT sal, comm FROM emp WHERE ename='ALLEN') WHERE job='SALESMAN';
```

<br>

-- 서브 쿼리를 사용하여 데이터 삭제하기
```sql
DELETE FROM emp WHERE sal > (SELECT sal FROM emp WHERE ename='SCOTT');

DELETE FROM emp m WHERE sal > (SELECT avg(sal) FROM emp s WHERE s.deptno = m.deptno);
    -- 사원의 월급이 자기가 속한 부서 번호의 평균 월급보다 크면 삭제
```

<br>

-- 서브 쿼리를 사용하여 데이터 합치기
```sql
ALTER TABLE dept ADD sumsal number(10);


MERGE INTO dept d 
USING (SELECT deptno, sum(sal) sumsal 
       FROM emp 
       GROUP BY deptno) v 
ON (d.deptno = v.deptno) 
WHEN MATCHED THEN 
UPDATE SET d.sumsal = v.sumsal;
```

<br>

-- 계층형 질의문으로 서열을 주고 데이터 출력하기
```sql
SELECT rpad(' ', level+3) || ename as employee, level, sal, job 
  FROM emp 
 START WITH ename='KING' 
 CONNECT BY prior empno = mgr;
```

<br>
 
*  __계층형 질의문 용어__

 용어 | 설명
 :-----:|:----
 노드(node) | 표시된 항목
 레벨(level) | 트리(tree) 구조에서 각각의 계층
 루트(root) | 트리(tree) 구조에서 최상위에 있는 노드
 부모(parent) | 트리(tree) 구조에서 상위에 있는 노드
 자식(child) | 트리(tree) 구조에서 하위에 있는 노드
 
<br>

<code>

### point 1.

 - 계층형 질의문의 키워드인 CONNECT BY와 START WITH절을 사용하면 PSEUDO COLUMN인 LEVEL을 출력할 수 있음   
 - RPAD로 이름 앞에 공백을 level 수의 3배가 되도록 추가 -> 시각화   
 - START WITH 절로 루트 노드의 데이터를 지정   
 - CONNECT BY 절은 부모 노드와 자식 노드의 관계를 지정   

<br>

### point 2.

 - 해당 SQL문은 순환 구조 SQL 문이라고 함   
 - 순환 구조 SQL에서는 WHERE가 처리 범위를 줄여주는 선행 조건이 아님   
 - START WITH 조건에서 선행 처리를 담당, WHERE절은 단순한 필터로 작용   
 - 따라서, 이러한 구조의 SQL에서는 START WITH절에 사용되는 컬럼에 인덱스를 생성해야 함   
 - 해당 쿼리 내의 WHERE 절 혹은 START WITH의 해당 컬럼의 비교 값에서 INLINE VIEW를 사용해서 범위를 줄여줘야 함   
 
 <br>

 ### point 3.

 - PRIOR 절은 반대편에 기술해도 되고 SELECT 절에 기술해도 됨   
 - 최초 시작한 노드의 PRIOR절에 기술되는 컬럼(A) 값을 읽어 PRIOR절 내 =의 반대편 컬럼(B)에 상수를 제공하겠다는 의미   
 - 컬럼의 상/하 위의 계층정보를 설정하는 키워드  

</code>

 
<br>

 -- 계층형 질의문으로 서열을 주고 데이터 출력하기

```sql
 SELECT RPAD(' ', level+3) || ename as employee, level, sal, job 
   FROM emp 
   START WITH ename='KING' 
   CONNECT BY prior empno = mgr AND ename != 'BLAKE';
   
   -- BLAKE만 제외하고 출력 : WHERE ename != 'BLAKE'
   
   -- BLAKE와 팀원 모두 제외하고 출력 : CONNECT BY ename != 'BLAKE' 
   -- -> 부모 노드와 자식 노드의 관계를 맺을 때 BLAKE의 사원 번호를 MGR 번호로 하고 있는 사원들이 모두 출력되지 않음
```

<br>

-- 계층형 질의문으로 서열을 주고 데이터 출력하기
```sql
SELECT RPAD(' ', LEVEL+3) || ename as employee, level, sal, job 
  FROM emp 
  START WITH ename='KING'
  CONNECT BY PRIOR empno = mgr
  ORDER SIBLINGS BY sal desc; -- SIBLINGS을 사용하여 정렬하면 계층형 질의문의 서열 순서를 깨뜨리지 않음
```

<br>

-- 계층형 질의문으로 서열을 주고 데이터 출력하기
```sql
SELECT ename, SYS_CONNECT_BY_PATH(ename, '/') as path
  FROM emp 
 START WITH ename='KING'
 CONNECT BY prior empno = mgr;
 
 SELECT ename, LTRIM(SYS_CONNECT_BY_PATH(ename, '/'), '/') as path
  FROM emp 
 START WITH ename='KING'
 CONNECT BY prior empno = mgr;
 ```