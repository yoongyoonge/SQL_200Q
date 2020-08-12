-- 데이터 수정하기 (UPDATE) 
```sql
UPDATE emp SET sal = 3200 WHERE ename='SCOTT';

UPDATE emp SET sal = (SELECT sal FROM emp WHERE ename='KING') WHERE ename='SCOTT';
    -- UPDATE, SET, WHERE 서브 쿼리 가능
```

<br>

-- 데이터 삭제하기 (DELETE, TRUNCATE, DROP) 
```sql
DELETE FROM emp WHERE ename='SCOTT';
```
<br>

### 삭제 명령어별 범위
| | DELETE | TRUNCATE | DROP 
:----:|:----:|:----:|:----:
데이터 | 삭제 | 삭제 | 삭제
저장 공간 | 남김 | 삭제 | 삭제
저장 구조 | 남김 | 남김 | 삭제
취소 여부 | 가능 | 불가능 | 불가능
플래쉬백 여부 | 가능 | 불가능 | 가능

<br>
<br>

cf)       

* 플래시 백
    * 오라클 9i부터 지원되는 기능
    * 과거 일정시점의 데이터를 쿼리해 볼수 있는 기능
    * 커밋을 완료해도 이전의 데이터를 볼 수 있음
    * 일정시점은 관리자가 정한 기간 [init.ora -> undo_retention(초) 시간동안]
    * 오라클 default 시간은 10800(3시간)

<br>

>  DELETE는 DML, TRUNCATE/DROP은 DDL   
>  DDL문은 수행되면서 암시적인 COMMIT이 발생  

<br>

-- 데이터 저장 및 취소하기 (COMMIT, ROLLBACK) 
```sql
INSERT INTO emp (empno, ename, sal, deptno) VALUES (1122, 'JACK', 3000, 20);

COMMIT;

UPDATE emp SET sal = 4000 WHERE ename='SCOTT';

ROLLBACK;
  -- COMMIT 명령어는 COMMIT 이전에 수행했던 DML 작업들을 DB에 영구히 반영하는 TCL(Transaction Control Language)
  -- ROLLBACK 명령어는 마지막 COMMIT 명령어를 수행한 이후 모든 변경사항(DML) 취소하는 TCL
  -- SAVEPOINT 명령어는 특정 시점까지의 변경을 취소
```

<br>

-- 데이터 입력, 수정, 삭제 한번에 하기 (MERGE)
```sql
MERGE INTO emp e 
USING dept d 
ON (e.deptno = d.deptno) 
WHEN MATCHED THEN -- MERGE UPDATE절
UPDATE SET e.loc = d.loc 
WHEN NOT MATCHED THEN -- MERGE INSERT절
INSERT (e.ename, e.deptno, e.loc) VALUES (1111, d.deptno, d.loc);
```
* MERGE INTO 문법
```sql
/* 
MERGE INTO (TARGET_TABLE OR TARGET_VIEW)
USING (SOURCE_TABLE)
ON (JOIN_CONDITION)
WHEN MATCHED THEN -- 조인 조건이 일치 되는 경우 UPDATE
UPDATE SET (CO11=VAL1, COL2=VAL2, ...) -- * JOIN_CONDITION에 사용한 컬럼은 UPDATE가 불가능하다!
WHEN NOT MATCHED THEN -- 일치 되지 않는 경우 INSERT
INSERT (COL1, COL2, ...) VALUES (VAL1, VAL2, ...)
*/
-- ORABLE 9i 버전 이상부터 사용가능 하지만, 9i는 불완전
-- 10g 부터 delete 구문을 포함한 merge into 구문 사용가능
-- trigger 발생되지 않음
```

* MERGE INTO 구문의 예

```sql
MERGE INTO SALES_MST SM
     USING (SELECT COMPANY_CODE, ITEM_CODE
              FROM SALES_MST_INF
             WHERE COMPANY_CODE = '1000' AND ITEM_CODE = '5845') S
            
        ON (SM.COMPANY_CODE = S.COMPANY_CODE AND SM.ITEM_CODE = S.ITEM_CODE)
       
WHEN MATCHED THEN
   UPDATE SET SM.APPLY_DATE = S.APPLY_DATE, SM.DEPARTMENT_CODE = S.DEPARTMENT_CODE
  
WHEN NOT MATCHED THEN
   INSERT  (SM.COMPANY_CODE, SM.ITEM_CODE, SM.EXPORTER_NAME, SM.CUSTOMER_NAME)
   VALUES (S.COMPANY_CODE, S.ITEM_CODE, S.EXPORTER_NAME, S.CUSTOMER_NAME);
```

<br>

-- 락 (LOCK) 이해하기

순서 | SCOTT으로 접속한 터미널창 1 | SCOTT으로 접속한 터미널창 2
:---:|:---|:---
1|UPDATE emp <br>SET sal = 3000 <br>WHERE ename='JONES'; <br> 1행이 업데이트되었습니다.| |
2| |UPDATE emp <br>SET sal = 9000 <br>WHERE ename='JONES' <br>             <- 멈춤
3| COMMIT; <br>커밋이 완료되었습니다. | |
4| | 1행이 업데이트 되었습니다|

<br> 

* update문을 수행하면 대상 row를 lock 함

<br>

-- SELECT FOR UPDATE절 이해하기

순서 | SCOTT으로 접속한 터미널창 1 | SCOTT으로 접속한 터미널창 2
:---:|:---|:---
1|SELECT ename, sal, deptno <br>FROM emp <br>WHERE ename='JONES' <br>FOR UPDATE;| |
2| |UPDATE emp <br>SET sal = 9000 <br>WHERE ename='JONES' <br>             <- 멈춤
3| COMMIT; <br>커밋이 완료되었습니다. | |
4| | 1행이 업데이트 되었습니다|

<br>

* SELECT ... FOR UPDATE문은 검색하는 행에 LOCK을 거는 SQL문

<br>

-- 서브 쿼리를 사용하여 데이터 입력하기
```sql
INSERT INTO emp2(empno, ename, sal, deptno) 
SELECT empno, ename, sal, deptno
FROM emp 
WHERE deptno = 10;
```

* 예제를 위한 emp2 테이블 생성
```sql
CREATE TABLE emp2 
AS 
SELECT * 
FROM emp 
WHERE 1=2;
```