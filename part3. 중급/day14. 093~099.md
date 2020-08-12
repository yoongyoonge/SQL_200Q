-- 일반 테이블 생성하기 (CREATE TABLE) 
```sql
CREATE TABLE EMP01 (
EMPNO       NUMBER(10),
ENAME       VARCHAR2(10),
SAL         NUMBER(10,2),
HIREDATE    DATE);
```

<br>

### * __테이블 생성 시 사용할 수 있는 주요 데이터 유형__

유형 | 설명
:---:|:---
CHAR | 고정 길이 문자 데이터 유형이며 최대 길이는 2000입니다.
VARCHAR2 | 가변 길이 문자 데이터 유형이며 최대 길이는 4000입니다.
LONG | 가변 길이 문자 데이터 유형이며 최대 2GB의 문자 데이터를 허용합니다.
CLOB | 문자 데이터 유형이며 최대 4GB의 문자 데이터를 허용합니다.
BLOB | 바이너리 데이터 유형이며 최대 4GB의 바이너리 데이터를 허용합니다.
NUMBER | 숫자 데이터 유형이며 십진 숫자의 자리수는 최대 38자리까지 허용 가능하며 소숫점 이하 자리는 -64 ~ 127까지 허용합니다.
DATE | 날짜 데이터 유형이며 기원전 4712년 01월 01일부터 기원후 9999년 12월 31일까지의 날짜를 허용합니다.

<br>

-- 임시 테이블 생성하기 (CREATE TEMPORAY TABLE) 
```sql
CREATE GLOBAL TEMPORARY TABLE EMP37 ( 
EMPNO       NUMBER(10),
ENAME       VARCHAR2(10),
SAL         NUMBER(10))
ON COMMIT DELETE ROWS;
```

<br>

### * __임시 테이블 보관 주기 결정 옵션__    

옵션 | 설명
:---|:---
ON COMMIT DELETE ROWS | 임시 테이블에 데이터를 입력하고 COMMIT 할 때까지만 데이터를 보관합니다.
ON COMMIT PRESERVE ROWS | 임시 테이블에 데이터를 입력하고 세션이 종료될 때까지 데이터를 보관합니다.

<br>

-- 복잡한 쿼리를 단순하게 하기 (VIEW)
```sql
CREATE VIEW EMP_VIEW
AS 
SELECT empno, ename, sal, job, deptno
  FROM emp 
 WHERE job='SLAESMAN';
 
UPDATE EMP_VIEW SET sal=0 WHERE ename='MARTIN'; 
-- 실행하면 EMP 테이블의 데이터가 변경 됨
-- VIEW는 데이터를 가지고 있지 않고 단순히 테이블을 바로 보는 객체

SELECT * FROM emp_view; (사용자 실행) 
--> SELECT empno, ename, sal, job, deptno FROM emp WHERE job='SALESMAN'; (실제 실행)
```

<br>

-- 복잡한 쿼리를 단순하게 하기 (VIEW) 
```sql
CREATE VIEW EMP_VIEW2 
AS 
SELECT deptno, round(avg(sal)) as "평균 월급" 
  FROM emp 
 GROUP BY deptno; 
 -- 뷰 생성 시 함수나 그룹 함수를 작성할 때는 반드시 컬럼 별칭을 사용해야 함
```

<br>

### * __뷰의 종류__    

      | 단순 VIEW | 복합 VIEW
:---: |  :---: | :---:
테이블의 개수 | 1개 | 2개 이상
함수 포함 여부 | 포함 안함 | 포함
데이터 수정 여부 | 수정 가능 | 수정 불가능 할 수 있음   


> 그룹 함수를 쿼리하는 복합 뷰는 수정이 되지 않음, 하지만 쿼리를 단순화 시킬 수 있다는 것에서 큰 장점

<br>

-- 데이터 검색 속도를 높이기 (INDEX)
```sql
CREATE INDEX EMP_SAL ON EMP(SAL);
-- 인덱스(INDEX)는 테이블에서 데이터를 검색할 때 검색 속도를 높이기 위해 사용하는 데이터 베이스 객체(OBJECT)
-- 인덱스는 컬럼값 + ROWID, ROWID는 데이터가 있는 ROW의 물리적 주소 --> 컬럼값은 내림차순으로 정렬되어있음
-- FULL TABLE SCAN은 테이블을 처음부터 끝까지 스캔, 인덱스는 원하는 데이터만 스캔
```

<br>

-- 절대로 중복되지 않는 번호 만들기 (SEQUENE) 

```sql
CREATE SEQUENCE SEQ1 -- 시퀀스 이름
START WITH 1 -- 첫 시작 숫자
INCREMENT BY 1 -- 증가치
MAXVALUE 100 -- 출력될 최대 숫자
NOCYCLE; -- 다시 반복할지



    -- 생성한 시퀀스를 사용하여 사원 테이블에 번호를 입력하는 방법
    CREATE TABLE EMP02 (
        EMPNO   NUMBER(10),
        ENAME   VARCHAR2(10), 
        SAL     NUMBER(10));
        
    INSERT INTO EMP02 VALUES(SEQ1.NEXTVAL, 'JACK', 3500);
    INSERT INTO EMP02 VALUES(SEQ1.NEXTVAL, 'JAMES', 4500);
    -- 시퀀스이름.NEXTVAL : 시퀀스의 다음 번호를 출력 또는 확인
```

<br>

-- 실수로 지운 데이터 복구하기 (FALSHBACK QUERY) 
```sql
SELECT * 
  FROM emp 
  AS OF TIMESTAMP (SYSTIMESTAMP - INTERVAL '5' MINUTE)
  WHERE ename='KING'; -- 5분전 데이터 검색
```

<br>

CF.) 
오늘 현재 시간 확인 | 오늘 현재시간에서 5분 전의 시간 확인
---|---
SELECT SYSTIMESTAMP FROM dual; | SELECT SYSTIMESTAMP - INTERVAL '5' MINUTE FROM dual;
20/08/12 14:00:42.016000000 +09:00 | 20/08/12 13:55:51.900000000 +09:00

* 테이블을 플래쉬백 할 수 있는 골든 타임은 기본이 15분
* UNDO_RETENTION으로 확인할 수 있음
* SELECT name, value FROM V$PARAMETER WHERE name='undo_retention'; -- 900초