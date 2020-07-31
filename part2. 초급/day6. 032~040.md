-- 암시적 형 변환 이해하기
SELECT ename, sal
  FROM emp
 WHERE sal = '3000'; -- 오라클은 문자형과 숫자형 두 개를 비교할 때는 문자형을 숫자형으로 암시적 형 변환 한다.
 
CREATE TABLE EMP32 ( 
  ENAME VARCHAR2(10),
  SAL VARCHAR2(10)
  );
  
SELECT ename, sal
  FROM emp32
 WHERE sal = '3000'; -- sal이 문자형
 
SELECT ename, sal
  FROM emp32
 WHERE sal = 3000; -- 오라클은 다음과 같이 변환하여 실행한다. TO_NUMBER(SAL) = 3000

-- SET AUTOT ON 명령어는 SQL을 실행할 때 출력되는결과와 실행 계획을 한 번에 보여 달라는 SQLPLUS 명령어
SET AUTOT ON 

SELECT ename, sal
  FROM emp32
 WHERE sal = 3000;
 
 
-- NULL 값 대신 다른 데이터 출력하기
SELECT ename, comm, NVL(comm, 0) 
  FROM emp;
  
SELECT ename, sal, comm, sal+comm 
  FROM emp
 WHERE job IN ('SALESMAN', 'ANALYST');
 
SELECT ename, sal, comm, NVL(comm, 0), sal+NVL(comm, 0) 
  FROM emp
 WHERE job IN ('SALESMAN', 'ANALYST');
 
 
SELECT ename, sal, comm, NVL2(comm, sal+comm, sal) 
  FROM emp
 WHERE job IN ('SALESMAN', 'ANALYST');
 
 
-- IF문을 SQL로 구현하기
SELECT ename, deptno, DECODE(deptno, 10, 300, 20, 400, 0) as 보너스 
  FROM emp; -- 부서번호가 10번이면 300, 20번이면 400, 나머지는 0
  
SELECT empno, mod(empno, 2), DECODE(mod(empno, 2), 0, '짝수', 1, '홀수') as 보너스
  FROM emp;
  
  
SELECT ename, job, DECODE(job, 'SALESMAN', 5000, 2000) as 보너스
  FROM emp;
  
  
SELECT ename, job, sal, CASE WHEN sal >= 3000 THEN 500
                             WHEN sal >= 2000 THEN 300
                             WHEN sal >= 1000 THEN 200
                             ELSE 0 END AS BONUS
  FROM emp
 WHERE job IN ('SALESMAN', 'ANALYST')
; -- DECODE는 등호(=)만 비교 가능하지만, CASE는 비교와 부등호 모두 가능

SELECT ename, job, comm, CASE WHEN comm is null THEN 500
                         ELSE 0 END BONUS
  FROM emp
 WHERE job IN('SLAESMAN', 'ANALYST');
 
SELECT ename, job, CASE WHEN job IN ('SALESMAN', 'ANALYST') THEN 500 
                        WHEN job IN ('CLERK', 'MANAGER') THEN 400
                   ELSE 0 END AS 보너스
  FROM emp;

-- 최대값
SELECT MAX(sal) 
  FROM emp;
  
SELECT MAX(sal) 
  FROM emp 
 WHERE job = 'SALESMAN';

SELECT job, MAX(sal) 
  FROM emp
 WHERE job = 'SALESMAN';
 
SELECT job, MAX(sal) 
  FROM emp 
 WHERE job = 'SALESMAN'
 GROUP BY job;
 
SELECT deptno, MAX(sal) 
  FROM emp 
 GROUP BY deptno;

 
-- 최소값 출력하기
SELECT MIN(sal) 
  FROM emp 
  WHERE job = 'SALESMAN';

SELECT job, MIN(sal) 최소값
  FROM  emp 
  GROUP BY job 
  ORDER BY 최소값 DESC; -- ORDER BY는 절 마지막에 작성하고 실행도 마지막에 됨
  
SELECT MIN(sal) 
  FROM emp 
  WHERE 1 = 2; -- 조건이 거짓이지만 실행됨, 그룹함수는 조건이 거짓이어도 결과를 항상 출력 
  
SELECT job, MIN(sal) 
  FROM emp
 WHERE job != 'SALESMAN' 
 GROUP BY job 
 ORDER BY MIN(sal) DESC;
 
 
-- 평균값 
SELECT AVG(comm) 
  FROM emp;
  
SELECT ROUND(AVG(NVL(comm, 0))) 
  FROM emp;
  
-- 토탈값

SELECT deptno, SUM(sal) 
  FROM emp
 GROUP BY deptno;
 
SELECT job, SUM(sal) 
  FROM emp
 GROUP BY job 
 ORDER BY sum(sal) DESC;
 
SELECT job, SUM(sal) 
  FROM emp
 WHERE sum(sal) >= 4000
 GROUP BY job; -- 에러 발생 그룹 함수로 조건 줄때는 HAVING 절 사용
 
SELECT job, SUM(sal) 
  FROM emp 
  GROUP BY job 
  HAVING sum(sal) >= 4000;
  
SELECT job, SUM(sal) 
  FROM emp 
 WHERE job != 'SALESMAN' 
 GROUP BY job 
 HAVING sum(sal) >= 4000;
 
SELECT job AS 직업, SUM(sal) 
  FROM emp 
 WHERE job != 'SALESMAN' 
 GROUP BY 직업 
 HAVING sum(sal) >= 4000; -- 에러발생, 실행 숭서와 관련
 
 
-- 건수 출력
SELECT COUNT(empno) 
  FROM emp;
  
SELECT COUNT(comm) 
  FROM emp;