## 19 저장 서브프로그램

### 19-1 저장 서브프로그램
#### 저장 서브프로그램이란
여러번 사용할 목적으로 이름을 지정하여 오라클에 저장해 두는 PL/SQL 프로그램을 '저장 서브프로그램'이라고 한다.     

- 익명 블록과 저장 서브프로그램의 비교   

  
||익명 블록|저장 서브프로그램|
|---|----|----|
|이름|이름 없음|이름 지정|
|오라클 저장|저장할 수 없음|저장함|
|컴파일|실행할 때마다 컴파일|저장할 때 한 번 컴파일|
|공유|공유할 수 없음|공유하여 사용 가능|
|다른 응용 프로그램에서의 <br> 호출 가능 여부|호출할 수 없음|호출 가능|

> 오라클에서는 용도에 따라 여러가지 방식으로 저장 서브프로그램을 구현할 수 있는데,      
> 대표적인 구현 방식으로는 프로시저, 함수, 패키지, 트리거가 있다.       

|서브 프로그램|용도|
|----|------|
|저장 프로시저|일반적으로 특정 처리 작업 수행을 위한 서브프로그램으로 **SQL문에서는 사용할 수 없다.**|
|저장 함수|일반적으로 특정 연산을 거친 결과값을 반환하는 서브프로그램으로 **SQL문에서 사용할 수 있다.**|
|패키지|저장 서브프로그램을 그룹화하는 데 사용한다. |
|트리거|특정 상황(이벤트)이 발생할 때 자동으로 연달아 수행할 기능을 구현하는데 사용한다. |


### 19-2 프로시저
- 저장 프로시저는 특정 처리 작업을 수행하는데 사용하는 저장 서브프로그램이다.     
- 용도에 따라 파라미터를 사용할 수 있고, 사용하지 않을 수도 있다.     

#### 파라미터를 사용하지 않는 프로시저
##### 프로시저 생성하기
작업 수행에 별다른 입력 데이터가 필요하지 않을 경우에 파라미터를 사용하지 않는다.    

```sql
CREATE [OR REPLACE] PROCEDURE 프로시저 이름
IS | AS
  선언부    
BEGIN
  실행부
EXCEPTION
  예외 처리부
END [프로시저 이름];
```

```sql
-- 프로시저 생성하기
CREATE OR REPLACE PROCEDURE pro_noparam
IS 
  V_EMPNO NUMBER(4) := 7788;
  V_ENAME VARCHAR2(10);
BEGIN
  V_ENAME := 'SCOTT';
  DBMS_OUTPUT.PUT_LINE('V_EMPNO : ' || V_EMPNO);
  DBMS_OUTPUT.PUT_LINE('V_ENAME : ' || V_ENAME);
END;
/
```

##### 프로시저 실행하기
```sql
-- SQL*PLUS로 프로시저 실행하기
EXECUTE pro_noparam;

-- PL/SQL 블록에서 프로시저 실행하기
BEGIN
  pro_noparam
END;
/
```

##### 프로시저 내용 확인하기
이미 저장되어 있는 프로시저를 포함하여 서브프로그램의 소스 코드 내용을 확인하려면 USER_SOURCE 데이터 사전에서 조회한다.    

|USER_SOURCE의 열|설명|
|---|---|
|NAME|서브프로그램(생성 객체) 이름|
|TYPE|서브프로그램 종류(PROCEDURE, FUNCTION 등)|
|LINE|서브프로그램에 작성한 줄 번호|
|TEXT|서브프로그램에 작성한 소스 코드|

##### 프로시저 삭제하기

```sql
DROP PROCEDURE PRO_NOPARAM;
```

> 이미 저장되어 있는 프로시저의 소스 코드를 변경할 때에는 CREATE RO REPLACE PROCEDURE 명령어로 프로시저를 **재생성**한다.     
> ALTER PROCEDURE는 프로시저의 소스 코드 내용을 재컴파일하는 명령어이므로 작성한 코드 내용을 변경하지 않는다는 점을 유의하자.     


#### 파라미터를 사용하는 프로시저
프로시저를 사용하기 위해 입력 데이터가 필요한 경우에 파라미터를 정의할 수 있다. 
```sql
CREATE [OR REPLACE] PROCEDURE 프로시저 이름

[(파라미터 이름1 [modes] 자료형 [ := | DEFAULT 기본값],
  파라미터 이름2 [modes] 자료형 [ := | DEFAULT 기본값],
  ...
  파라미터 이름N [modes] 자료형 [ := | DEFAULT 기본값]
)]
IS | AS
  선언부    
BEGIN
  실행부
EXCEPTION
  예외 처리부
END [프로시저 이름];
```

- 파라미터를 지정할 때 사용하는 모드는 IN, OUT, IN OUT 세 가지가 있다.    

|파라미터 모드|설명|
|--|--|
|IN|지정하지 않으면 기본값으로 프로시저를 호출할 때 값을 입력받는다.|
|OUT|호출할 때 값을 반환한다.|
|IN OUT|호출할 때 값을 입력받은 후 실행 결과 값을 반환한다.|

##### IN 모드 파라미터
프로시저 실행에 필요한 값을 직접 입력받는 형식의 파라미터를 지정할 때 IN을 사용한다. (IN 은 기본값이라 **생략 가능**)      

```sql
CREATE OR REPLACE PROCEDURE pro_param_in
(
  param1 IN NUMBER,
  param2 NUMBER,
  param3 NUMBER := 3,
  param1  NUMBER DEFAULT 4
)
IS 

BEGIN
  DBMS_OUTPUT.PUT_LINE('param1 : ' || param1);
  DBMS_OUTPUT.PUT_LINE('param2 : ' || param2);
  DBMS_OUTPUT.PUT_LINE('param3 : ' || param3);
  DBMS_OUTPUT.PUT_LINE('param4 : ' || param4);
END;
/

-- 파라미터를 입력하여 프로시저 사용하기
EXECUTE pro_param_in(1,2,9,8);

-- 기본값이 지정된 파라미터 입력을 제외하고 프로시저 사용하기
EXECUTE pro_param_in(1,2);

-- 파라미터 이름을 활ㅇㅇ하여 프로시저에 값 입력하기
EXECUTE pro_param_in(param1 => 10, param2 => 20);

```
- 파라미터에 값을 지정할 때는 다음 세 가지 지정 방식을 사용할 수 있다.     

|종류|설명|
|--|--|
|위치 지정|지정한 파라미터 순서대로 값을 지정하는 방식|
|이름 지정| => 연산자로 파라미터 이름을 명시하여 값을 지정하는 방식|
|혼합 지정|일부 파라미터는 순서대로 값만 지정하고, 일부 파라미터는 => 연산자로 값을 지정하는 방식(11g부터 사용 가능 )|


> 프로시저 호출시, 기본값이 지정되지 않은 파라미터 수보다 적은 수의 값을 지정하면 프로시저 실행은 실패하게 된다.   


##### OUT 모드 파라미터  
OUT 모드를 사용한 파라미터는 프로시저 실행 후 호출한 프로그램으로 값을 반환한다.       
   
```sql
-- OUT 모드 파라미터 정의하기
CREATE OR REPLACE PROCEDURE pro_param_out
(
  in_empno IN EMP.EMPNO%TYPE,
  out_ename OUT EMP.ENAME%TYPE,
  out_sal OUT EMP.SAL%TYPE
)
IS

BEGIN
  SELECT ENAME, SAL INTO out_ename, out_sal
    FROM EMP
   WHERE EMPNO = in_empno;
END pro_param_out;
/


-- OUT 모드 파라미터 사용하기
DECLARE
  v_ename EMP.ENAME%TYPE;
  v_sal EMP.SAL%TYPE;
BEGIN
  pro_param_out(7788, v_ename, v_sal);
  DBMS_OUTPUT.PUT_LINE('ENAME : ' || v_ename);
  DBMS_OUTPUT.PUT_LINE('ENAME : ' || v_sal);
END;
/
```

##### IN OUT 모드 파라미터
값을 입력받을 때와 프로시저 수행 후 결과값을 반환할 때 사용한다.   

```sql
-- IN OUT 모드 파라미터 정의하기
CREATE OR REPLACE PROCEDURE pro_param_inout
(
  inout_no IN OUT NUMBER
)
IS

BEGIN
  inout_no := inout_no * 2;
END pro_param_inout;
/

-- IN OUT 모드 파라미터 사용하기
DECLARE
  no NUMBER;
BEGIN
  no := 5;
  pro_param_inout(no);
  DBMS_OUTPUT.PUT_LINE('no : ' || no);
END;
/
```

#### 프로시저 오류 정보 확인하기
서브프로그램을 만들 때 발생한 오류는 SHOW ERRORS 명령어와 USER_ERRORS 데이터 사전을 조회하여 확인이 가능하다.



