### 19-5 트리거
#### 트리거란?
오라클에서 트리거(trigger)는 데이터베이스 안의 특정 상황이나 동작, 즉 이벤트가 발생할 경우에 자동으로 실행되는 기능을 정의하는 PL/SQL 서브프로그램이다.      

- 트리거 사용시 장점     
1. 데이터 관련 작업을 좀 더 간편하게 수행할 수 있다.    
2. 제약 조건만으로 구현이 어렵거나 불가능한 좀 더 복잡한 데이터 규칙을 정할 수 있어 더 수준 높은 데이터 정의가 가능하다.    
3. 데이터 변경과 관련된 일련의 정보를 기록해 둘 수 있으므로 여러 사용자가 공유하는 데이터 보안성과 안정성 그리고 문제가 발생했을 때 대체 능력을 높일 수 있다.     

> 하지만, 트리거는 특정 작업 또는 이벤트 발생으로 다른 데이터 작업을 추가로 실행하기 때문에     
> 무분별하게 사용하면 데이터베이스 성능을 떨어뜨리는 원인이 될 수 있기에 주의해야 한다.     

- 트리거는 테이블, 뷰, 스키마, 데이터베이스 수준에서 다음과 같은 이벤트에 동작을 지정할 수 있다.     
  - 데이터 조작어(DML) : INSERT, UPDATE, DELETE     
  - 데이터 정의어() : CREATE, ALTER, DROP     
  - 데이터베이스 동작 : SERVERERROR, LOGON, LOGOFF, STARTUP, SHUTDOWN     

- 트리거가 발생할 수 있는 이벤트 종류에 따라 오라클은 트리거를 다음과 같이 구분한다.    

|종류|설명|
|--|--|
|DML 트리거|INSERT, UPDATE, DELETE와 같은 DML 명령어를 기점으로 동작함|
|DDL 트리거|CREATE, ALTER, DROP과 같은 DDL 명령어를 기점으로 동작함|
|INSTEAD OF트리거|뷰(View)에 사용하는 DML 명령어를 기점으로 동작함|
|시스템(system) 트리거|데이터베이스나 스키마 이벤트로 동작함|
|단순(simple) 트리거|다음 각 시점(timing point)에 동작함 <br> 트리거를 작동시킬 문장이 실행되기 전 시점 <br> 트리거를 작동시킬 문장이 실행된 후 시점 <br> 트리거를 작동시킬 문장이 행에 영향을 미치기 전 시점 <br> 트리거를 작동시킬 문장이 행에 영향을 준 후 시점|
|복합(compound) 트리거|단순 트리거의 여러 시점에 동작함|

#### DML 트리거
DML 트리거는 특정 테이블에 DML명령어를 실행했을 때 작동하는 트리거이다. 

```sql
CREATE [OR REPLACE] TRIGGER 트리거 이름
BEFORE | AFTER
INSERT | UPATE | DELETE ON 테이블 이름
REFERENCING OLD as old | New as new
FOR EACH ROW WHEN 조건식
FOLLOWS 트리거 이름2, 트리거 이름3 ...
ENABLE | DISABLE

DECLARE
  선언부
BEGIN
  실행부
EXCEPTION
  예외 처리부
END;
```

#### DML 트리거의 제작 및 사용(BEFORE)

```sql
-- 트리거를 적용할 테이블을 EMP 테이블을 복사하여 생성한다
CREATE TABLE EMP_TRG
    AS SELECT * FROM EMP;
    
    
-- DML 실행 전에 수행할 트리거 생성하기
-- trg_emp_nodml_weekend 트리거는 주말에 EMP_TRG 테이블에 DML어를 사용하면 오류를 일으키고 DML 명령어 실행을 취소한다. 

CREATE [OR REPLACE] TRIGGER trg_emp_nodml_weekend
BEFORE
INSERT OR UPDATE OR DELETE ON EMP_TRG
BEGIN
  IF TO_CHAR(sysdate, 'DY') IN ('토', '일') THEN
    IF INSERTING THEN
      raise_application_error(-20000, '주말 사원정보 추가 불가');
    ELSIF UPDATING THEN
      raise_application_error(-20001, '주말 사원정보 수정 불가');
    ELSIF DELETING THEN
      raise_application_error(-20002, '주말 사원정보 삭제 불가');
    ELSE  
      raise_application_error(-20003, '주말 사원정보 변경 불가');
    END IF;
  END IF;
END;      
/

-- 트리거는 특정 이벤트 발생할 때 자동으로 작동하는 서브프로그램이므로 프로시저나 함수와 같이 EXECUTE 또는 PL/SQL 블록에서 따로 실행하지 못한다.   
```


#### DML 트리거의 제작 및 사용(AFTER)

```sql
-- EMP_TRG_LOG 테이블 생성하기
CREATE TABLE EMP_TRG_LOG(
  TABLENAME VARCHAR2(10), -- DML이 수행된 테이블 이름
  DML_TYPE VARCHAR2(10),  -- DML 명령어의 종류
  EMPNO NUMBER(4),        -- DML 대상이 된 사원 번호
  USER_NAME VARCHAR2(30), -- DML을 수행한 USER 이름
  CHANGE_DATE DATE        -- DML이 수행된 날짜
);

-- DML 실행 후 수행할 트리거 생성하기
CREATE OR REPLACE TRIGGER trg_emp_log
AFTER
INSER OR UPDATE OR DELETE ON EMP_TRG
FOR EACH ROW

BEGIN

  IF INSERTING THEN
    INSERT INTO emp_trg_log
    VALUES ('EMP_TRG', 'INSERT', :new.empno,
            SYS_CONTEXT('USERENV', 'SESSION_USER'), sysdate); -- new.empno는 새로 추가된 empno값을 의미한다. 
            
  ELSIF UPDATING THEN
      INSERT INTO emp_trg_log
      VALUES ('EMP_TRG', 'UPDATE', :old.empno,
              SYS_CONTEXT('USERENV', 'SESSION_USER'), sysdate);
  
  ELSIF DELETING THEN
    INSERT INTO emp_trg_log
    VALUES ('EMP_TRG', 'DELETE', :old.empno,
            SYS_CONTEXT('USERENV', 'SESSION_USER'), sysdate);
              
  END IF;
END;
/
```


#### 트리거 관리
##### 트리거 정보조회
트리거 정보를 확인하려면 USER_TRIGGERS 데이터 사전을 조회한다.    

```sql
SELECT TRIGGER_NAME, TRIGGER_TYPE, TRIGGERING_EVENT, TABLE_NAME, STATUS
  FROM USER_TRIGGERS;
```

#### 트리거 변경
```sql
ALTER TRIGGER 트리거 이름 ENABLE | DISABLE;
```


#### 트리거 삭제
```DROP TRIGGER 트리거 이름;```
