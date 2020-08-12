-- SQL200제_실습스크립트.txt : 실습 준비

```sql
alter session set nls_date_format='RR/MM/DD';

drop table emp;
drop table dept;

CREATE TABLE DEPT
       (DEPTNO number(10),
        DNAME VARCHAR2(14),
        LOC VARCHAR2(13) );

INSERT INTO DEPT VALUES (10, 'ACCOUNTING', 'NEW YORK');
INSERT INTO DEPT VALUES (20, 'RESEARCH',   'DALLAS');
INSERT INTO DEPT VALUES (30, 'SALES',      'CHICAGO');
INSERT INTO DEPT VALUES (40, 'OPERATIONS', 'BOSTON');

CREATE TABLE EMP (
 EMPNO               NUMBER(4) NOT NULL,
 ENAME               VARCHAR2(10),
 JOB                 VARCHAR2(9),
 MGR                 NUMBER(4) ,
 HIREDATE            DATE,
 SAL                 NUMBER(7,2),
 COMM                NUMBER(7,2),
 DEPTNO              NUMBER(2) );

INSERT INTO EMP VALUES (7839,'KING','PRESIDENT',NULL,'81-11-17',5000,NULL,10);
INSERT INTO EMP VALUES (7698,'BLAKE','MANAGER',7839,'81-05-01',2850,NULL,30);
INSERT INTO EMP VALUES (7782,'CLARK','MANAGER',7839,'81-05-09',2450,NULL,10);
INSERT INTO EMP VALUES (7566,'JONES','MANAGER',7839,'81-04-01',2975,NULL,20);
INSERT INTO EMP VALUES (7654,'MARTIN','SALESMAN',7698,'81-09-10',1250,1400,30);
INSERT INTO EMP VALUES (7499,'ALLEN','SALESMAN',7698,'81-02-11',1600,300,30);
INSERT INTO EMP VALUES (7844,'TURNER','SALESMAN',7698,'81-08-21',1500,0,30);
INSERT INTO EMP VALUES (7900,'JAMES','CLERK',7698,'81-12-11',950,NULL,30);
INSERT INTO EMP VALUES (7521,'WARD','SALESMAN',7698,'81-02-23',1250,500,30);
INSERT INTO EMP VALUES (7902,'FORD','ANALYST',7566,'81-12-11',3000,NULL,20);
INSERT INTO EMP VALUES (7369,'SMITH','CLERK',7902,'80-12-11',800,NULL,20);
INSERT INTO EMP VALUES (7788,'SCOTT','ANALYST',7566,'82-12-22',3000,NULL,20);
INSERT INTO EMP VALUES (7876,'ADAMS','CLERK',7788,'83-01-15',1100,NULL,20);
INSERT INTO EMP VALUES (7934,'MILLER','CLERK',7782,'82-01-11',1300,NULL,10);

commit;

drop  table  salgrade;

create table salgrade
( grade   number(10),
  losal   number(10),
  hisal   number(10) );

insert into salgrade  values(1,700,1200);
insert into salgrade  values(2,1201,1400);
insert into salgrade  values(3,1401,2000);
insert into salgrade  values(4,2001,3000);
insert into salgrade  values(5,3001,9999);

commit;
```

--------------------------------------------------------------------------------------------------------------------------------
```sql
select * from emp;
```

-- 사원 테이블에서 사원 번호와 이름과 월급을 출력해보기

```sql
SELECT empno, ename, sal
  FROM emp;
```



-- 사원 테이블을 모든 열들을 전부 출력해보기

```sql
SELECT * 
  FROM emp;
  
SELECT emp, ename, job, mgr, hiredate, sal, comm, deptno 
  FROM emp;

```


-- 마지막에 부서번호 한번 더 출력하기

```sql
SELECT dept.*, deptno 
  FROM dept;
```

-- 컬럼에 별칭 붙이기
```sql
SELECT empno as 사원번호, ename as 사원이름, sal as "Salary"
  FROM emp;
```

-- column alias가 다음의 경우에 해당 된다면 더블 퀘테이션 마크로 감싸야한다.
-- 1. 대소문자를 구분하여 출력할 때
-- 2. 공백문자를 출력할 때
-- 3. 특수문자를 출력할 때 ($, _, #만 가능)
```sql
SELECT empno as "사원 번호", ename as "사원 이름", sal as "Salary"
  FROM emp;
```
  
-- 다음의 경우 별칭이 유용

```sql
SELECT ename, sal * (12 + 3000)
  FROM emp;
  
  
SELECT ename, sal * (12 + 3000) as 월급
  FROM emp
 ORDER BY 월급 DESC;
 ```