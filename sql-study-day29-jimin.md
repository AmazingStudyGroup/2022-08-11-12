sql-study-day29-jimin
# 19강-저장 서브프로그램
## 19-1 저장 서브프로그램
### - 저장 서브 프로그램이란?
- 익명 블록(anonymouse block):
  - 작성한 내용을 단 한 번 실행하는데 사용하는 앞에서 다룬 PL/SQL 블록
  - 이름이 정해져 있지 앟은 PL/SQL 블록
  - 익명 블록은 오라클에 저장되지 않기 때문에 한 번 실행된 뒤에 다시 실행하려면 PL/SQL 블록을 다시 작성하여 실행해야 한다.
  - 매번 이렇게 내용을 작성하는 것은 불편하기 떄문에 실행할 내용을 따로 파일(.sql)에 저장하여 실행하기도 하지만 오라클에 저장되는 것은 아니다.
- 그런데, PL/SQL로 만든 프로그램을 주기적으로 또는 필요할 때마다 여러 번 사용해야 하는 상황이 빈번히 발생한다.
- 이러한 경우, PL/SQL 프로그램을 오라클에 저장해 두면 필요할 때마다 수행할 내용을 작성하지 않고 실행할 수 있으므로 여러모로 편리하다.
- 저장 서브 프로그램(stored subprogram): 
  - 여러번 사용할 목적으로 이름을 지정하여 오라클에 저장해두는 PL/SQL 프로그램
  - 익명 블록과 달리 저장 서브 프로그램은 오라클에 저장하여 공유할 수 있으므로 메모리, 성능, 재사용성등의 여러 면에서 장점이 있다.
- 익명 블록과 저장 서브프로그램의 차이점

||익명 블록|저장 서브프로그램|
|-------|--------|---------------------------|
|이름|이름 없음|이름 지정|
|오라클 저장|저장할 수 없음|저장함|
|컴파일|실행할 때마다 컴파일|저장할 때 한 번 컴파일|
|공유|공유할 수 없음|공유하여 사용 가능|
|다른 응용 프로그램에서의 호출 가능 여부|호출할 수 없음|호출 가능|

- 오라클에서 용도에 따라 저장 서브 프로그램 구현하는 방식 세가지
→ 프로시저, 함수, 패키지, 트리거

|서브프로그램|용도|
|----------|-------------------------|
|저장 프로시저(stored procedure)|일반적으로 특정 처리 작업 수행을 위한 서브프로그램으로 SQL문에서는 사용할 수 없다.|
|저장 함수(stored function)|일반적으로 특정 연산을 거친 결과 값을 반환하는 서브프로그램으로 SQL문에서 사용할 수 있다.|
|패키지(Package)|저장 서브프로그램을 그룹화하는데 사용한다.|
|트리거(trigger)|특정 상황(이벤트)이 발생할 때 자동으로 연달아 수행할 기능을 구현하는 데 사용한다.|

## 19-2 프로시저
- 저장 프로시저(stored procedure): 특정 처리 작업을 수행하는데 사용하는 저장 서브프로그램
  - 용도에 따라 파라미터를 사용할 수도 있고, 사용하지 않을 수도 있다.
### - 파라미터를 사용하지 않는 프로시저
#### -- 프로시저 생성하기
- 작업 수행에 별다른 입력 데이터가 필요하지 않을 경우, 파라미터를 사용하지 않는 프로시저를 사용한다.
- CREATE [OR REPLACE] PROCEDURE를 사용해 만들 수 있다.
- 익명 블록과 마찬가지로 프로시저도 선언부, 실행부, 예외 처리부로 이루어져 있다.
- 프로시저의 기본 형식
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
- 프로시저 생성하기
```sql

```
#### -- SQL*PLUS로 프로시저 실행하기
- SQL*PLUS에서 실행할 때 다음과 같이 EXECUTE 명령어를 사용한다.
```sql
EXECUTE 프로시저 이름;
```
- 생성한 프로시저 실행하기
```sql
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
#### -- PL/SQL 블록에서 프로시저 실행하기
- 특정 PL/SQL블록에서 이미 만들어진 프로시저를 실행한다면, 밑에와 같이 실행부(BEGIN)에 실행할 프로시저 이름을 지정한다.
```sql
SET SERVEROUTPUT ON;

EXECUTE pro_noparam;
```
- 익명 블록에서 프로시저 실행하기
```sql
BEGIN
   pro_noparam;
END;
/
```
#### -- 프로시저 내용 확인하기
- 이미 저장되어 있는 프로시저를 포함하여 서브프로그램의 소스코드 내용을 확인하려면 USER_SOURCE 데이터 사전에서 조회한다.

|USER_SOURCE의 열|설명|
|-------------------------|---------------------------------------------|
|NAME|서브프로그램(생성 객체) 이름|
|TYPE|서브프로그램 종류(PROCEDURE, FUNCTINO 등)|
|LINE|서브프로그램에 작성한 줄 번호|
|TEXT|서브프로그램에 작성한 소스 코드|

- USER_SOURCE를 통해 프로시저 확인하기(토드)
```sql
SELECT *
  FROM USER_SOURCE
 WHERE NAME = 'PRO_NOPARAM';
```
- USER_SOURCE를 통해 프로시저 확인하기(SQL*PLUS)
```sql
SELECT TEXT
  FROM USER_SOURCE
 WHERE NAME = 'PRO_NOPARAM';
```
#### -- 프로시저 삭제하기
- DROP PROCEDURE 명령어로 프로시저를 삭제할 수 있다.
- 프로시저 삭제하기
```sql
DROP PROCEDURE PRO_NOPARAM;
```
- **이미 저장되어 있는 프로시저의 소스 코드를 변경**할 때
→ **CREATE OR REPLACE PROCEDURE** 명령어로 프로시저를 재생성한다.
- ALTER PROCEDURE는 프로시저의 소스 코드 내용을 **재컴파일하는** 명령어이므로 작성한 코드 내용을 **변경하지 않는다**.

### - 파라미터를 사용하는 프로시저
- 프로시저를 실행하기 위해 **입력 데이터가 필요한 경우**, 파라미터를 정의할 수 있다.
- 파라미터는 여러 개 작성할 수 있다.
- 파라미터 작성 기본 형식
```sql
CREATE [OR REPLACE] PROCEDURE 프로시저 이름
[(파라미터 이름1 [modes] 자료형 [ := | DEFUALT 기본값],
  파라미터 이름2 [modes] 자료형 [ := | DEFUALT 기본값],
  ...
  파라미터 이름N [modes] 자료형 [ := | DEFUALT 기본값],
)]
IS | AS
	선언부
BEGIN
	실행부
EXCEPTION
	예외 처리부
END [프로시저 이름];
```
- 파라미터를 지정할 때 사용하는 모드 → IN, OUT, IN OUT

|파라미터 모드|설명|
|--------------------|----------------------------------------|
|IN|지정하지 않으면 기본 값으로 프로시저를 호출할 떄 값을 입력 받는다.(입력, void)|
|OUT|호출할 떄 값을 반환한다.(입력X, return)|
|IN OUT|호출할 때 값을 입력받은 후 실행 결과 값을 반환한다.(입력, return)|

#### -- IN 모드 파라미터
- 프로시저 실행에 필요한 값을 직접 입력 받는 형식의 파라미터를 지정할 때 IN을 사용한다.
- IN은 기본값이기 때문에 생략이 가능하다.
- 프로시저에 파라미터 지정하기
```sql
CREATE OR REPLACE PROCEDURE pro_param_in
(
   param1 IN NUMBER,
   param2 NUMBER,
   param3 NUMBER := 3,
   param4 NUMBER DEFAULT 4
)
IS

BEGIN
   DBMS_OUTPUT.PUT_LINE('param1 : ' || param1);
   DBMS_OUTPUT.PUT_LINE('param2 : ' || param2);
   DBMS_OUTPUT.PUT_LINE('param3 : ' || param3);
   DBMS_OUTPUT.PUT_LINE('param4 : ' || param4);
END;
/
```
- 파라미터를 입력하여 프로시저 사용하기
```sql
EXECUTE pro_param_in(1,2,9,8);
```
- 기본 값이 지정되어 있는 상태라면 호출할 때 값을 지정하지 않아도 실행이 가능하다.
- 기본 값이 지정된 파라미터 입력을 제외하고 프로시저 사용하기
```sql
EXECUTE pro_param_in(1, 2);
```
- 프로시저를 호출 할 때 기본값이 지정되지 않은 파라미터 수보다 적은 수의 값을 지정하면 프로시저 실행은 실패하게 되어 유의해야한다.
- 기본값이 지정된 파라미터와 지정되지 않은 파라미터 순서가 섞여있다면 기본값이 지정되지 않은 파라미터까지 값을 입력해주어야 한다.
- 즉, 밑에와 같이 파라미터가 지정되어 있다면 프로시저를 실행할 때 최소한 세 개 값을 입력해 주어야 한다.
```sql
(
  param1 IN NUMBER,
  param2 NUMBER := 3,
  param3 NUMBER,
  param4 NUMBER DEFAULT 4
)
```
- 하지만, 파라미터 종류나 개수가 많아지면 이러한 방식은 다소 불편할 수 있다.
- 따라서 **=>** 를 사용하여 파라미터 이름에 직접 값을 대입하는 방식도 사용한다.
- 파라미터 이름을 활용하여 프로시저에 값 입력하기
```sql
EXECUTE pro_param_in(param1 => 10, param2 => 20);
```
- 파라미터에 값을 지정할 때의 세가지 지정 방식

|종류|설명|
|-------------------|---------------------------------------------------|
|위치 지정|지정한 파라미터 순서대로 값을 지정하는 방식|
|이름 지정|=> 연산자로 파라미터 이름을 명시하여 값을 지정하는 방식|
|혼합 지정|일부 파라미터는 순서대로 값만 지정하고 일부 파라미터는 => 연산자로 값을 지정하는 방식|

#### -- OUT 모드 파라미터
- OUT 모드를 사용한 파라미터는 프로시저 실행 후 호출한 프로그램으로 값을 반환한다.
- OUT 모드 파라미터 정의하기
  - 변수 두 개를 선언하여 pro_param_out 프로시저의 반환값을 대입하고 있다.
```sql
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
```
- OUT 모드 파라미터 사용하기
```sql
DECLARE
   v_ename EMP.ENAME%TYPE;
   v_sal EMP.SAL%TYPE;
BEGIN
   pro_param_out(7788, v_ename, v_sal);
   DBMS_OUTPUT.PUT_LINE('ENAME : ' || v_ename);
   DBMS_OUTPUT.PUT_LINE('SAL : ' || v_sal);
END;
/
```
#### -- IN OUT 모드 파라미터
- IN OUT 모드로 선언한 파라미터는 IN, OUT으로 선언한 파라미터 기능을 동시에 수행한다.
- 즉, 갑슬 입력받을 때와 프로시저 수행 후 결과 값을 반환할 때 사용한다.
- IN OUT 모드 파라미터 정의하기
```sql
CREATE OR REPLACE PROCEDURE pro_param_inout
(
   inout_no IN OUT NUMBER
)
IS

BEGIN
   inout_no := inout_no * 2;
END pro_param_inout;
/
```
- IN OUT 모드 파라미터 사용하기
```sql
DECLARE
   no NUMBER;
BEGIN
   no := 5;
   pro_param_inout(no);
   DBMS_OUTPUT.PUT_LINE('no : ' || no);
END;
/
```
### - 프로시저 오류 정보 확인하기
- 프로시저를 생성할 때 발생하는 오류를 확인하는 방법
→ 이 방법은 다른 서브프로그램의 오류에도 똑같이 적용할 수 있다.
- 생성할 때 오류가 발생하는 프로시저 알아보기
```sql
CREATE OR REPLACE PROCEDURE pro_err
IS
   err_no NUMBER;
BEGIN
   err_no = 100;
   DBMS_OUTPUT.PUT_LINE('err_no : ' || err_no);
END pro_err;
/
```
#### -- SHOW ERRORS 명령어로 오류 확인하기
```sql
SHOW ERRORS;
```
- SHOW ERRORS 명령어는 줄여서 SHOW ERR로 사용할 수도 있다.
- 만약 최근에 발생한 프로그램의 오류가 아니라 특정 프로그램의 오류 정보를 확인하고 싶다면 다음과 같이 프로그램 종류과 이름을 추가로 지정하면 된다. → 프로시저로 생성한 경우 PROCEDURE를 붙여준다.
```sql
SHOW ERR 프로그램 종류 프로그램 이름;
SHOW ERR PROCEDURE pro_err;
```
#### -- USER_ERRORS로 오류 확인
- 토드 같은 응용 프로그램을 사용한다면, USER_ERRORS 데이터 사전을 조회하여 오류 정보를 확인할 수 있다.
```sql
SELECT *
  FROM USER_ERRORS
 WHERE NAME = 'PRO_ERR';
```
---
## 19-3 함수
- 오라클 함수는 크게 내장 함수(built-in function)와 사용자 정의 함수(user defined function)로 분류할 수 있다.
- 프로시저와 함수의 차이점

|특징|프로시저|함수|
|--------------|---------------------------|-------------------------|
|실행|EXECUTE 명령어 또른 다른 PL/SQL 서브 프로그램 내에서 호출하여 실행|변수를 사용한 EXECUTE 명령어 또는 다른 PL/SQL 서브프로그램에서 호출하여 실행하거나 SQL무네서 직접 실행 가능|
|파라미터 지정|필요에 따라 지정하지 않을 수도 있고, <br>여러 개를 지정할 수도 있으며 IN, OUT, IN OUT 세 가지 모드를 사용할 수 있다.|프로시저와  같게 지정하지 않을 수도 있고 여러 개 지정할 수도 있지만 IN 모드(또는 생략)만 사용|
|값의 반환|실행 후의 값의 반환이 없을 수도 있고, <br>OUT, IN OUT 모드의 파라미터 수에 따라 여러 개 값을 반환할 수 있다.|반드시 하나의 값을 반환해야 하며, <br>값의 반환은 프로시저와 달리 OUT, IN OUT 모드의 파라미터를 사용하는 것이 아니라 RETURN절과 RETURN문을 통해 반환|
- 함수는 프로시저와 달리 SQL문에서도 사용할 수 있다는 특징이 있다.
- 함수는 RETURN절과 RETURN문을 통해 **반드시 하나의 값**을 반환해야 한다.

### - 함수 생성하기
- 함수 생성은 프로시저와 마찬가지로 CREATE [OR REPLACE] 명령어와 FUNCTION 키워드를 명시하여 생성한다.
- 실행부의 RETURN문이 실행되면 함수 실행은 즉시 종료된다.
- 함수 생성 기본 형식
```sql
CREATE [OR REPLACE] FUNCTION 함수 이름
[(파라미터 이름1 [IN] 자료형1,
  파라미터 이름2 [IN] 자료형2,
  ...
  파라미터 이름N [IN] 자료형N
)]
RETURN 자료형
IS | AS
  선언부
BEGIN
  실행부
  RETURN (반환값);
ECXEPTION
  예외 처리부
END [함수 이름];
```
#### -- 한 발 더 나가기! - 함수의 파라미터에 OUT, IN OUT 모드 사용
- 함수에도 OUT, IN OUT 모드의 파라미터를 지정할 수 있다. 하지만 함수에 OUT, IN OUT모드의 파라미터가 지정되어 있으면 SQL문에서는 사용할 수 없는 함수가 된다.
⇒ 즉, 프로시저와 다를 바 없는 함수가 되는 것이다.
- 이러한 이유와 부작용 때문에 오라클에서도 함수 파라미터에 OUT, IN OUT 모드를 사용하지 말라고 권장한다.
- SQL문에 사용할 함수는 반환 값의 자료형을 SQL문에서 사용할 수 없는 자료형으로 지정할 수 없으며 트랜잭션을 제어하는 명령어(TCL, DDL) 또는 DML 명령어도 사용할 수 없다.
- 함수 생성하기
```sql
CREATE OR REPLACE FUNCTION func_aftertax(
   sal IN NUMBER
)
RETURN NUMBER
IS
   tax NUMBER := 0.05;
BEGIN
   RETURN (ROUND(sal - (sal * tax)));
END func_aftertax;
/
```
### - 함수 실행하기
- 생성된 함수는 익명 블록 또는 프로시저 같은 저장 서브 프로그램, SQL문에서 사용할 수 있다.
- PL/SQL로 함수를 실행할 때는, 함수 반환 값을 대입받을 변수를 필요로 한다.
#### -- PL/SQL로 함수 실행하기
- 함수는 실행 후 하나의 값을 반환하므로 PL/SQL로 구현한 프로그램 안에 반환 값을 받기 위한 변수를 선언하여 사용한다.
- PL/SQL에서 함수 사용하기
```sql
DECLARE
   aftertax NUMBER;
BEGIN
   aftertax := func_aftertax(3000);
   DBMS_OUTPUT.PUT_LINE('after-tax income : ' || aftertax);
END;
/
```
#### -- SQL문에서 함수 실행하기
- SQL문에서 제작한 함수를 사용하는 방식은 기존 오라클의 내장 함수와 같다.
- DUAL 테이블에 다음과 같이 값을 직접 입력하여 사용할 수 있다.
- SQL에서 함수사용하기
```sql
SELECT func_aftertax(3000)
  FROM DUAL;
```
- 함수에 테이블 데이터 사용하기
```sql
SELECT EMPNO, ENAME, SAL, func_aftertax(SAL) AS AFTERTAX
  FROM EMP;
```
### - 함수 삭제하기
- DROP FUNCTION 명령어를 사용하여 함수를 삭제한다.
- 함수 삭제하기
```sql
DROP FUNCTION func_aftertax;
```
## 19-4 패키지
- 패키지(package)는 업무나 기능 면에서 연관성이 높은 프로시저, 함수 등 여러 개의 PL/SQL 서브 프로그램을 하나의 논리 그룹으로 묶어 통합, 관리하는 데 사용하는 객체를 뜻한다.
  - 프로시저나 함수 등은 각각 개별 기능을 수행하기 위해 제작 후 따로 저장한다.
- 패키지를 사용하여 서브프로그램을 그룹화했을 때의 장점

|장점|설명|
|---------|---------------------------------------------------------------|
|모듈성|서브프로그램을 포함한 여러 PL/SQL 구성 요소를 모듈화 할 수 있다.<br>모듈성은 잘 묶어둔다는 뜻으로, 프로그램의 이해를 쉽게 하고 패키지 사이의 상호 작용을 더 간편하고 명료하게 해주는 역할을 한다. <br>즉, PL/SQL로 제작한 프로그램의 사용 및 관리에 큰 도움을 준다.|
|쉬운 응용 프로그램 설계|패키지에 포함할 서브 프로그램은 완벽하게 완성되지 않아도 정의가 가능하다.<br>이 때문에 전체 소스 코드를 다 작성하기 전에 미리 패키지에 저장할 서브프로그램을 지정할 수 있으므로 설계가 수월해진다.|
|정보 은닉|제작 방식에 따라 패키지에 포함하는 서브 프로그램의 외부 노출 여부 또는 접근 여부를 지정할 수 있다.<br>즉, 서브프로그램을 사용할 때 보안을 강화할 수 있다.|
|기능성 향상|패키지 내부에서는 서브프로그램 외에 변수, 커서, 예외 등도 각 세션이 유지되는 동안 선언해서 공용(public)으로 사용할 수 있다.<br>예를 들어 특정 커서 데이터는 세션이 종료되기 전까지 보존되므로 여러 서브프로그램에서 사용할 수 있다.|
|성능 향상|패키지를 사용할 때 패키지에 포함된 모든 서브프로그램이 메모리에 한 번에 로딩되는데,<br> 메모리에 로딩된 후의 호출은 디스크 I/O를 일으키지 않으므로 성능이 향상된다.|
⇒ 패키지는 PL/SQL 서브 프로그램의 제작, 사용, 관리, 보안, 성능 등에 좋은 영향을 끼친다.
### - 패키지 구조와 생성
- 패키지는 프로시저, 함수와 달리 보통 두 부분으로 나누어 제작한다.
1. 명세(specification)
2. 본문(body)
#### -- 패키지 명세
- 패키지 명세는 패키지에 포함할 변수, 상수, 예외, 커서 그리고 PL/SQL 서브프로그래을 선언하는 용도로 작성한다.
- 패키지 명세에 선언한 여러 객체는 패키지 내부뿐만 아니라 외부에서도 참조할 수 있다.
- 패키지 명세의 기본형식
```sql
CREATE [OR REPLACE] PACKAGE 패키지 이름
IS | AS
	서브프로그램을 포함한 다양한 객체 선언
END [패키지 이름];
```
- 다음 패키지 명세는 변수 한 개, 함수 한 개, 프로시저 두 개를 선언한다.
- 패키지 선언하기
```sql
CREATE OR REPLACE PACKAGE pkg_example
IS
   spec_no NUMBER := 10;
   FUNCTION func_aftertax(sal NUMBER) RETURN NUMBER;
   PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE);
   PROCEDURE pro_dept(in_deptno IN DEPT.DEPTNO%TYPE);
END;
/
```
- 이미 생성되어 있는 패키지 명세의 코드를 확인하거나 선언한 서브프로그램을 확인하려면 UER_SOURCE 데이터 사전을 조회하거나 DESC 명령어를 활용할 수 있다.
- 패키지 명세 확인하기(USER_SOURCE 데이터 사전으로 조회)
```sql
SELECT TEXT
  FROM USER_SOURCE
 WHERE TYPE = 'PACKAGE'
   AND NAME = 'PKG_EXAMPLE';
```
- 패키지 명세 확인하기(DESC 명령어로 조회)
```sql
DESC pkg_example;
```
#### -- 패키지 본문
- 패키지 본문에는 패키지 명세에서 선언한 서브프로그램 코드를 작성한다.
- 패키지 명세에 선언하지 않은 객체나 서브프로그램을 정의하는 것도 가능하다.
  - 패키지 본문에만 존재하는 프로그램은 패키지 내부에서만 사용할 수 있다.
- 패키지 본문 이름은 패키지 명세 이름과 같게 지정해야 한다.
- 패키지 본문 기본 형식
```sql
CREATE [OR REPLACE] PACKAGE BODY 패키지 이름
IS | AS
	패키지 명세에서 선언한 서브프로그램을 포함한 여러 객체를 정의
    경우에 따라 패키지 명세에 존재하지 않는 객체 및 서브프로그램도 정의 가능
END [패키지 이름];
```
- 패키지 본문 생성하기
```sql
CREATE OR REPLACE PACKAGE BODY pkg_example
IS
   body_no NUMBER := 10;

   FUNCTION func_aftertax(sal NUMBER) RETURN NUMBER
      IS
         tax NUMBER := 0.05;
      BEGIN
         RETURN (ROUND(sal - (sal * tax)));
   END func_aftertax;

   PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE)
      IS
         out_ename EMP.ENAME%TYPE;
         out_sal EMP.SAL%TYPE;
      BEGIN
         SELECT ENAME, SAL INTO out_ename, out_sal
           FROM EMP
          WHERE EMPNO = in_empno;

         DBMS_OUTPUT.PUT_LINE('ENAME : ' || out_ename);
         DBMS_OUTPUT.PUT_LINE('SAL : ' || out_sal);
   END pro_emp;

PROCEDURE pro_dept(in_deptno IN DEPT.DEPTNO%TYPE)
   IS
      out_dname DEPT.DNAME%TYPE;
      out_loc DEPT.LOC%TYPE;
   BEGIN
      SELECT DNAME, LOC INTO out_dname, out_loc
        FROM DEPT
       WHERE DEPTNO = in_deptno;

      DBMS_OUTPUT.PUT_LINE('DNAME : ' || out_dname);
      DBMS_OUTPUT.PUT_LINE('LOC : ' || out_loc);
   END pro_dept;
END;
/
```
#### -- 서브프로그램 오버로드
- 기본적으로 서브프로그램 이름은 중복될 수 없다.
→ 그러나, 몇몇 경우에 중복이 가능하다.
- 서브프로그램 오버로드(subprogram overload): 
  - 같은 패키지에서 사용하는 파라미터의 개수, 자료형, 순서가 다를 경우에 한해서만 이름이 같은 서브프로그램을 정의하는 것
  - 서브프로그램 오버로드는 보통 같은 기능을 수행하는 여러 서브프로그램이 입력 데이터를 각각 다르게 정의할 때 사용한다.
- 서브프로그램 종류가 같아야 오버로드가 가능하다.
⇒ 즉, 특정 프로시저를 오버로드할 때 반드시 이름이 같은 프로시저로 정의해야 한다.
- 프로시저와 이름이 같은 함수를 정의할 수는 없다.
- 서브프로그램 오버로드 기본 형식
```sql
CREATE [OR REPLACE] PACKAGE 패키지 이름
IS | AS
	서브프로그램 종류 서브프로그램 이름(파라미터 정의);
    서브프로그램 종류 서브프로그램 이름(개수나 자료형, 순서가 다른 파라미터 정의);
END [패키지 이름];
```
- 프로시저 오버로드하기
```sql
CREATE OR REPLACE PACKAGE pkg_overload
IS
   PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE);
   PROCEDURE pro_emp(in_ename IN EMP.ENAME%TYPE);
END;
/
```
- 패키지 본문에서 오버로드된 프로시저 작성하기
```sql
CREATE OR REPLACE PACKAGE BODY pkg_overload
IS
   PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE)
      IS
         out_ename EMP.ENAME%TYPE;
         out_sal EMP.SAL%TYPE;
      BEGIN
         SELECT ENAME, SAL INTO out_ename, out_sal
           FROM EMP
          WHERE EMPNO = in_empno;

         DBMS_OUTPUT.PUT_LINE('ENAME : ' || out_ename);
         DBMS_OUTPUT.PUT_LINE('SAL : ' || out_sal);
      END pro_emp;

   PROCEDURE pro_emp(in_ename IN EMP.ENAME%TYPE)
      IS
         out_ename EMP.ENAME%TYPE;
         out_sal EMP.SAL%TYPE;
      BEGIN
         SELECT ENAME, SAL INTO out_ename, out_sal
           FROM EMP
          WHERE ENAME = in_ename;

         DBMS_OUTPUT.PUT_LINE('ENAME : ' || out_ename);
         DBMS_OUTPUT.PUT_LINE('SAL : ' || out_sal);
      END pro_emp;

END;
/
```
### - 패키지 사용하기
- 패키지를 통해 그룹화된 변수, 상수, 예외, 커서 그리고 PL/SQL 서브프로그램은 패키지 이름과 마침표(.)와 사용할 객체 이름으로 사용할 수 있다.
- 패키지에 포함된 서브프로그램 실행하기
```sql
BEGIN
   DBMS_OUTPUT.PUT_LINE('--pkg_example.func_aftertax(3000)--');
   DBMS_OUTPUT.PUT_LINE('after-tax:' || pkg_example.func_aftertax(3000));

   DBMS_OUTPUT.PUT_LINE('--pkg_example.pro_emp(7788)--');
   pkg_example.pro_emp(7788);

   DBMS_OUTPUT.PUT_LINE('--pkg_example.pro_dept(10)--' );
   pkg_example.pro_dept(10);

   DBMS_OUTPUT.PUT_LINE('--pkg_overload.pro_emp(7788)--' );
   pkg_overload.pro_emp(7788);

   DBMS_OUTPUT.PUT_LINE('--pkg_overload.pro_emp(''SCOTT'')--' );
   pkg_overload.pro_emp('SCOTT');
END;
/
```
### - 패키지 삭제하기
- 두 가지 방식을 사용하여 패키지를 삭제할 수 있다.
```sql
패키지 명세와 본문을 한 번에 삭제하기
DROP PACKAGE 패키지 이름;
```
```sql
패키지의 본문만을 삭제
DROP PACKAGE BODY 패키지 이름;
```
- 패키지 명세와 본문을 한 번에 삭제하거나 패키지 본문만 삭제할 수도 있다.
- 하지만, 패키지에 포함된 서브프로그램을 따로 삭제하는 것은 불가능하다.
  - CREATE OR REPLACE문을 활용하여 패키지 안의 객체 또는 서브프로그램을 수정 및 삭제할 수 있다.
---
## 19-5 트리거
### - 트리거란?
>오라클에서 트리거는 데이터베이스 안의 특정 상황이나 동작, 즉 이벤트가 발생할 경우에 자동으로 실행되는 기능을 정의하는 PL/SQL 서브 프로그램이다.

- 예;
  - 어떤 테이블의 데이터를 특정 사용자가 변경하려 할 때 해당 데이터나 사용자 기록을 확인한다든지, 상황에 따라 데이터를 변경하지 못하게 막는 것이 가능하다.
  - 오라클 데이터베이스다 가동하거나 종료할 때 데이터베이스 관리자 등 관련 업무자에게 메일을 보내는 기능도 구현할 수 있다.
- 트리거를 통한 연관 데이터 작업을 잘 정의해 두면 다음과 같은 장점이 있다.
1. 데이터와 연관된 여러 작업을 수행하기 위해 여러 PL/SQL문 또는 서브프로그램을 일일이 실행해야 하는 번거로움을 줄일 수 있다. 
⇒ 즉, 데이터 관련 작업을 좀 더 간편하게 수행할 수 있다.
2. 제약 조건(constraints)만으로 구현이 어렵거나 불가능한 좀 더 복잡한 규칙을 정할 수 있어 더 높으 수준의 데이터 정의가 가능하다.
3. 데이터 변경과 관련되 일련의 정보를 기록해둘 수 있으므로 여러 사용자가 공유하는 데이터 보안성과 안정성 그리고 문제가 발생했을 때 대처 능력을 높일 수 있다.

- 하지만 트리거는 특정 작업 또는 이벤트 발생으로 다른 데이터 작업을 추가로 실행하기 때문에 무분별하게 사용허면 데이터베이스 성능을 떨어뜨리는 원인이 되므로 주의가 필요하다.
- 트리거는 테이블, 뷰, 스키마, 데이터베이스 수준에서 다음과 같은 이벤트에 동작을 지정할 수 있다.
  - 데이터 조작어(DML): INSERT, UPDATE, DELETE
  - 데이터 정의어(DDL): CREATE, ALTER, DROP
  - 데이터베이스 동작: SERVERERROR, LOGON, LOGOFF, STARTUP, SHUTDOWN
- 트리거가 발생할 수 있는 이벤트 종류에 따라 오라클을 트리거를 다음과 같이 구분한다.

|종류|설명|
|---------|------------------------------|
|DML 트리거|INSERT, UPDATE, DELETE와 같은 DML 명령어를 기점으로 동작함|
|DDL 트리거|CREATE, ALTER, DROP과 같은 DDL 명령어를 기점으로 동작함|
|INSTEAD OF 트리거|뷰(View)에 사용하는 DML 명령어를 기점으로 동작함|
|시스템(system) 트리거|데이터베이스나 스키마 이벤트로 동작함|
|단순(simple) 트리거|다음 각 시점(timing point)에 동작함<br>- 트리거를 작동시킬 문장이 실행되기 전 시점<br>- 트리거를 작동시킬 문장이 실행된 후 시점<br>- 트리거를 작동시킬 문장이 행에 영향을 미치기 전 시점<br>- 트리거를 작동시킬 문장이 행에 영향을 준 후 시점|
|복합(compund) 트리거|단순 트리거의 여러 시점에 동작함|
### - DML 트리거
#### -- DML 트리거 형식
- DML 트리거는 특정 테이블에 DML 명령어를 실행했을 때 작동하는 트리거이다.
- DML 트리거의 기본 형식
```sql
CREATED [OR REPLACE] TRUGGER 트리거 이름
BEFORE | AFTER
INSERT | UPDATE | DELETE ON 테이블 이름
REFERENCING OLD as old | New as new
FOR EACH ROW WHEN 조건식
FOLLOWS 트리거 이름2, 트리거 이름3, ...
ENABLE | DISABLE

DECLARE
	선언부
BEGIN
	실행부
EXCEPTION
	예외 처리부
END;
```
- `REFERENCING OLD as old | New as new`:
DML로 변경되는 행의 변경 전 값과 변경 후 값을 참조하는데 사용한다.(생략 가능)
- `FOR EACH ROW WHEN 조건식`:
트리거를 실행하는 DML 문장에 한 번만 실행할지 DML 문장에 의해 영향 받는 행별로 실행할지를 지정한다.
생략하면 트리거는 DML 명령어가 실행할 때 한 번만 실행한다.
생략하지 않고 사용할 경우, DML 명령어에 영향받는 행별로 트리거를 작동하되 WHEN키워드를 함께 사용하면 DML 명령어에 영향받는 행 중 트리거를 작동시킬 행을 조건식으로 지정할 수 있다.

### - DML 트리거의 제작 및 사용(BEFORE)
- 트리거를 적용할 테이블을 EMP 테이블을 복사하여 생성한다.
- EMP_TRG 테이블 생성하기
```sql
CREATE TABLE EMP_TRG
    AS SELECT * FROM EMP;
```
- trg_emp_nodml_weekend 트리거 생성: 주말에 EMP_TGR 테이블에 DML 명령어를 사용하면 오류를 일으키고, DML 명령어 실행을 취소한다.
- DML 실행 전에 수행할 트리거 생성하기
```sql
CREATE OR REPLACE TRIGGER trg_emp_nodml_weekend
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
```
- 트리거는 특정 이벤트가 발생할 때 자동으로 작동하는 서브프로그램이므로,
프로시저나 함수와 같이 EXECUTE 또는 PL/SQL 블록에서 따로 실행하지 못한다.
- 평일 날짜로 EMP_TRG 테이블 UPDATE하기
```sql
UPDATE emp_trg SET sal = 3500 WHERE empno = 7788;
```
- 주말 날짜에 EMP_TRG 테아블 UPDATE하기
  - 주말에 DML 명령어가 실행되면 오류를 발생시켜, 명령어가 취소되게 된다.
```sql
UPDATE emp_trg SET sal = 3500 WHERE empno = 7788;
```
### DML 트리거의 제작 및 사용(AFTER)
- DML 명령어가 실행된 후 작동하는 AFTER 트리거 제작하기
- 앞에서 생성한 EMP_TRG 테이블에 DML 명령어가 실행되었을 때 테이블에 수행된 DML 명령어의 종류, DML을 실행시킨 사용자, DML 명령어가 수행된 날짜와 시간을 저장할 EMP_TRG_LOG 테이블 생성하기
```sql
CREATE TABLE EMP_TRG_LOG(
   TABLENAME VARCHAR2(10), -- DML이 수행된 테이블 이름
   DML_TYPE VARCHAR2(10),  -- DML 명령어의 종류
   EMPNO NUMBER(4),        -- DML 대상이 된 사원 번호
   USER_NAME VARCHAR2(30), -- DML을 수행한 USER 이름
   CHANGE_DATE DATE        -- DML이 수행된 날짜
);
```
- DML 실행 후 수행할 트리거 생성하기
```sql
CREATE OR REPLACE TRIGGER trg_emp_log
AFTER
INSERT OR UPDATE OR DELETE ON EMP_TRG
FOR EACH ROW

BEGIN

   IF INSERTING THEN
      INSERT INTO emp_trg_log
      VALUES ('EMP_TRG', 'INSERT', :new.empno,
               SYS_CONTEXT('USERENV', 'SESSION_USER'), sysdate);

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
- EMP_TRG 테이블에 INSERT 실행하기
```sql
INSERT INTO EMP_TRG
VALUES(9999, 'TestEmp', 'CLERK', 7788,
       TO_DATE('2018-03-03', 'YYYY-MM-DD'), 1200, null, 20);
```
- EMP_TRG 테이블에 INSERT 실행하기(COMMIT하기)
```sql
COMMIT;
```
- EMP_TRG 테이블의 INSERT 확인하기
```sql
SELECT *
  FROM EMP_TRG;
```
- EMP_TRG_LOG 테이블의 INSERT 기록 확인하기
```sql
SELECT *
  FROM EMP_TRG_LOG;
```
- EMP_TRG 테이블에 UPDATE 실행하기
```sql
UPDATE EMP_TRG
   SET SAL = 1300
 WHERE MGR = 7788;
```
- EMP_TRG 테이블에 UPDATE 실행하기(COMMIT하기)
  - 두 개 행이 DML문에 영향을 받았으므로 트리거에 지정한 FOR EACH ROW 옵션으로 트리거는 두 번 실행한다.
```sql
COMMIT;
```
### - 트리거 관리
#### -- 트리거 정보 조회
- 트리거 정보를 확인하려면 USER_TRGGERS 데이터 사전을 조회한다.
- USER_TRIGGERS로 트리거 정보 조회하기
```sql
SELECT TRIGGER_NAME, TRIGGER_TYPE, TRIGGERING_EVENT, TABLE_NAME, STATUS
  FROM USER_TRIGGERS;
```
### - 트리거 변경
- ALTER TRIGGER 명령어로 트리거 상태를 변경할 수 있다.
- 특정 트리거를 활성화 또는 비활성화하려면 ALTER TRIGGER 명령어에 ENABLE 또는 DISABLE 옵션을 지정한다.
```sql
ALTER TRIGGER 트리거 이름 ENABLE | DISABLE
```
- 특정 테이블과 관련된 모든 트리거의 상태를 활성화하거나 비활성화하는 것도 가능하다.
  - 이 경우는 ALTER TABLE 명령어를 사용한다.
```sql
특정 테이블과 관련된 모든 트리거의 상태 활성화
ALTER TABLE 테이블 이름 ENABLE ALL TRIGGERS;

특정 테이블과 관련된 모든 트리거의 상태 비활성화
ALTER TABLE 테이블 이름 DISABLE ALL TRIGGERS;
```
### - 트리거 삭제
- DROP문을 사용하여 트리거를 삭제할 수 있다.
```sql
DROP TRIGGER 트리거 이름;
```
---
## 잊기 전에 한 번 더!
Q1.
```sql
--①
CREATE OR REPLACE PROCEDURE pro_dept_in
(
   inout_deptno IN OUT DEPT.DEPTNO%TYPE,
   out_dname OUT DEPT.DNAME%TYPE,
   out_loc OUT DEPT.LOC%TYPE
)
IS
BEGIN
   SELECT DEPTNO, DNAME, LOC INTO inout_deptno, out_dname, out_loc
     FROM DEPT
    WHERE DEPTNO = inout_deptno;
END pro_dept_in;
/

--②
DECLARE
   v_deptno DEPT.DEPTNO%TYPE;
   v_dname DEPT.DNAME%TYPE;
   v_loc DEPT.LOC%TYPE;
BEGIN
   v_deptno := 10;
   pro_dept_in(v_deptno, v_dname, v_loc);
   DBMS_OUTPUT.PUT_LINE('부서번호 : ' || v_deptno);
   DBMS_OUTPUT.PUT_LINE('부서명 : ' || v_dname);
   DBMS_OUTPUT.PUT_LINE('지역 : ' || v_loc);
END;
/
```

Q2.
```sql
CREATE OR REPLACE FUNCTION func_date_kor(
   in_date IN DATE
)
RETURN VARCHAR2
IS   
BEGIN
   RETURN (TO_CHAR(in_date, 'YYYY"년"MM"월"DD"일"'));
END func_date_kor;
/
```

Q3.
```sql
--①
CREATE TABLE DEPT_TRG
    AS SELECT * FROM DEPT;

--②
CREATE TABLE DEPT_TRG_LOG(
   TABLENAME   VARCHAR2(10), -- DML이 수행된 테이블 이름
   DML_TYPE    VARCHAR2(10), -- DML 명령어의 종류
   DEPTNO      NUMBER(2),    -- DML 대상이 된 부서번호
   USER_NAME   VARCHAR2(30), -- DML을 수행한 USER 이름
   CHANGE_DATE DATE          -- DML 이 수행된 날짜
);

--③
CREATE OR REPLACE TRIGGER trg_dept_log
AFTER
INSERT OR UPDATE OR DELETE ON DEPT_TRG
FOR EACH ROW
BEGIN
   IF INSERTING THEN
     INSERT INTO DEPT_TRG_LOG
     VALUES ('DEPT_TRG', 'INSERT', :new.deptno,
             SYS_CONTEXT('USERENV', 'SESSION_USER'), sysdate);

   ELSIF UPDATING THEN
     INSERT INTO DEPT_TRG_LOG
     VALUES ('DEPT_TRG', 'UPDATE', :old.deptno,
             SYS_CONTEXT('USERENV', 'SESSION_USER'), sysdate);

   ELSIF DELETING THEN
     INSERT INTO DEPT_TRG_LOG
     VALUES ('DEPT_TRG', 'DELETE', :old.deptno,
             SYS_CONTEXT('USERENV', 'SESSION_USER'), sysdate);
   END IF;
END;
/
```