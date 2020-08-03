-- 실습을 위한 테이블 생성
```sql
drop  table order2;

create table order2
( ename  varchar2(10),
  bicycle  number(10),
  camera   number(10),
  notebook  number(10) );

insert  into  order2  values('SMITH', 2,3,1);
insert  into  order2  values('ALLEN',1,2,3 );
insert  into  order2  values('KING',3,2,2 );

commit;
```

<br>

-- ROW를 COLUMN으로 출력하기
```sql 
SELECT * 
  FROM order2 
  UNPIVOT (건수 for 아이템 in (BICYCLE, CAMERA, NOTEBOOK));
-- 건수는 가로로 저장되어 있는 세로로 unpivot 시킬 출력 열 이름

SELECT * 
  FROM order2 
  UNPIVOT (건수 for 아이템 in (BICYCLE as 'B', CAMERA as 'C', NOTEBOOK as 'N'));
-- order2 테이블의 데이터에 NULL이 포함되어 있었다면 unpivot 결과에서 출력되지 않음

UPDATE ORDER2 SET NOTEBOOK=NULL WHERE ENAME='SMITH';

-- NULL 값인 행도 포함시킬 때
SELECT * 
  FROM order2 
  UNPIVOT INCLUDE NULLS (건수 for 아이템 in (BICYCLE as 'B', CAMERA as 'C', NOTEBOOK as 'N'));
```

<br>

-- 데이터 분석 함수로 누적 데이터 출력하기 (SUM OVER)
```sql 
SELECT empno, ename, sal, SUM(sal) OVER (ORDER BY empno ROWS 
                                         BETWEEN UNBOUNDED PRECEDING 
                                         AND CURRENT ROW) 누적치
  FROM emp
 WHERE job IN ('ANALYST', 'MANAGER'); -- UNBOUNDED PRECEDING : 맨 첫 번째 행, UNBOUNDED FOLLOWING : 맨 마지막 행, CURRENT ROW : 현재 행
```


<br>

-- 데이터 분석 함수로 비율 출력하기 (RATIO_TO_REPORT)
```sql 
SELECT empno, ename, sal, RATIO_TO_REPORT(sal) OVER () AS 비율 
  FROM emp 
 WHERE deptno = 20;
 
SELECT empno, ename, sal, RATIO_TO_REPORT(sal) OVER () AS 비율, 
                          sal/SUM(sal) OVER () AS "비교 비율"
  FROM emp 
 WHERE deptno = 20;
```

<br>

-- 데이터 분석 함수로 집계 결과 출력하기 (ROLLUP) 

```sql 
SELECT job, sum(sal) 
  FROM emp 
 GROUP BY ROLLUP(job); -- rollup은 전체 토탈 집계를 할 수 있는 함수 + job 컬럼의 데이터도 오름차순으로 정렬되어 출력

SELECT deptno, job, sum(sal) 
  FROM emp 
 GROUP BY ROLLUP(deptno, job); -- 부서 번호별, 직업별 토탈 월급이 집계되고, 전체 토탈 월급도 출력됨
```

<br>

-- 데이터 분석 함수로 집계 결과 출력하기 (CUBE) 
```sql 
SELECT job, sum(sal) 
  FROM emp 
 GROUP BY CUBE(job); -- CUBE를 추가했을 때 맨 위쪽에 전체 토탈 월급이 추가가 되었고 부서 번호도 오름차순으로 정렬이 되어 출력
 
SELECT deptno, job, sum(sal) 
  FROM emp 
 GROUP BY CUBE(deptno, job); -- rollup과 비교했을 때 job에 대한 집계 한 가지가 더 출력됨
```

<br>

-- 데이터 분석 함수로 집계 결과 출력하기 (GROUPING SETS)
```sql
SELECT deptno, job, sum(sal) 
  FROM emp 
 GROUP BY GROUPING SETS((deptno), (job), ()); -- 집계 하고 싶은 컬럼명을 기술하면, 기술한대로 결과 출력 / ()는 전체 집계
```

<br>

-- 데이터 분석 함수로 출력 결과 넘버링 하기 (ROW_NUMBER) 
```sql
SELECT empno, ename, sal, RANK() OVER (ORDER BY sal DESC) RANK,
                          DENSE_RANK() OVER (ORDER BY sal DESC) DENSE_RANK, 
                          ROW_NUMBER() OVER (ORDER BY sal DESC) 번호
  FROM emp 
 WHERE deptno = 20; 
 -- row_number 함수는 출력되는 결과에 번호를 순서대로 부여, over 다음 괄호 안에 반드시 order by절을 기술해야 함.
```
<br>

> `기술하지 않으면 다음의 에러 발생`

```sql
 SELECT empno, ename, sal, ROW_NUMBER() OVER () 번호
   FROM emp 
 WHERE deptno = 20; --ORA-30485: 윈도우 지정에 ORDER BY 표현식이 없습니다
 
SELECT deptno, ename, sal, ROW_NUMBER() OVER (PARTITION BY deptno ORDER BY sal DESC) 번호 
  FROM emp 
 WHERE deptno IN (10, 20);
```