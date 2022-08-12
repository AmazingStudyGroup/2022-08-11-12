### 19-3 함수
오라클 함수는 크게 내장함수와 사용자 정의 함수로 분류할 수 있다. 앞서 내장함수를 살펴봤다면, 이번에는 PL/SQL을 사용하여 함수를 직접 정의하는 방법을 알아보자.     

- 프로시저와 함수의 차이점    
함수는 프로시저와 달리, SQL문에서도 사용할 수 있다. RETURN절과 RETURN문을 통해 반드시 하나의 값을 반환해야 한다는 것도 기억하자.      

#### 함수 생성하기
- CREATE [OR REPLACE] 명령어와 FUNCTION 키워드를 명시하여 생성한다.    
- 반환값의 자료형과 실행부에서 반환할 값을 RETURN절 및 RETURN문으로 명시해야 한다.   
- 실행부의 RETURN문이 실행되면 함수 실행은 즉시 종료된다.     

```sql
CREATE [OR REPLACE] FUNCTION 함수이름
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
EXCEPTION
  예외 처리부
END [함수 이름];
```

```sql
-- 급여 값을 입력받아 세금을 제한 실수령액을 계산하는 함수
CREATE [OR REPLACE] FUNCTION func_aftertax(
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

#### 함수 실행하기
- 생성된 함수는 익명 블록 또는 프로시저 같은 저장 서브프로그램, SQL문에서 사용할 수 있다.     
- PL/SQL로 실행할 때는 함수 반환 값을 대입받을 변수가 필요하다.    

```sql
-- PL/SQL에서 함수 사용하기

DECLARE
  aftertax := func_aftertax(3000);
  DBMS_OUTPUT.PUT_LINE('after-tax income : ' || aftertax);
END;
/

-- SQL문에서 함수 실행하기
SELECT func_aftertax(3000)
  FROM DUAL;
```

#### 함수 삭제하기
`DROP FUNCTION func_aftertax`

### 19-4 패키지
패키지는 업무나 기능 면에서 연관성이 높은 프로시저, 함수 등 여러개의 PL/SQL 서브프로그램을 하나의 논리 그룹으로 묶어 통합, 관리하는데 사용하는 객체이다.    


#### 패키지 구조와 생성
패키지는 보통 두 부분으로 나누어 제작한다 => 1. 명세  2. 본문     

##### 패키지 명세
- 패키지에 포함할 변수, 상수, 예외, 커서, PL/SQL 서브프로그램을 선언하는 용도로 작성한다.     
- 패키지 명세에 선언한 여러 객체는 패키지 내부뿐만 아니라 외부에서도 참조할 수 있다.    

```sql
CREATE [OR REPLACE] PACKAGE 패키지 이름
IS | AS
  서브프로그램을 포함한 다양한 객체 선언
END [패키지 이름];
```

```sql
-- 패키지 생성하기
CREATE [OR REPLACE] PACKAGE pkg_example
IS
  spec_no NUMBER := 10;
  FUNCTION func_aftertax(sal NUMBER) RETURN NUMBER;
  PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE);
  PROCEDURE pro_dept(in_deptno IN DEPT.DEPTNO%TYPE);
END;
/
```

> 이미 생성되어 있는 패키지 명세의 코드를 확인하거나 선언한 서브프로그램을 확인하려면 USER_SOURCE 데이터 사전을 조회하거나 DESC 명령어를 활용할 수 있다.    

```sql
-- 패키지 명세 확인하기(USER_SOURCE 데이터 사전으로 조회)
SELECT TEXT
  FORM USER_SOURCE
 WHERE TYPE = 'PACKAGE'
   AND NAME = 'PKG_EXAMPLE';

-- 패키지 명세 확인하기(DESC 명령어로 조회)
DESC pkg_example;
```

##### 패키지 본문
- 패키지의 본문에는 패키지 명세에서 선언한 서브프로그램 코드를 작성한다.   
- 패키지 명세에 선언하지 않은 객체나 서브프로그램을 정의하는 것도 가능하다.      
- 이 때, 패키지 본문에만 존재하는 프로그램은 **패키지 내부에서만** 사용할 수 있다.    

```sql
CREATE [OR REPLACE] PACKAGE BODY 패키지 이름
IS | AS
  패키지 명세에서 선언한 서브프로그램을 포함한 여러 객체를 정의
  경우에 따라 패키지 명세에 존재하지 않는 객체 및 서브프로그램도 정의 가능  
END [패키지 이름];
```

```sql
-- 패키지 본문 생성하기 
CREATE [OR REPLACE] PACKAGE BODY pck_example
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

##### 서브프로그램 오버로드
- 기본적으로 서브프로그램 이름은 중복될 수 없다.    
- 하지만, 같은 패키지에서 사용하는 파라미터의 개수, 자료형, 순서가 다를 경우에 한해서만 이름이 같은 서브프로그램을 정의할 수 있다. => 서브프로그램 오버로드       
- 서브프로그램 오버로드는 보통 같은 기능을 수행하는 여러 서브프로그램이 입력 데이터를 각각 다르게 정의할 때 사용한다.     
- 특정 프로시저를 오버로드할 때 반드시 이름이 같은 프로시저로 정의해야 한다.     

```sql
CREATE [OR REPLACE] PACKAGE 패키지 이름
IS | AS
  서브프로그램 종류 서브프로그램 이름(파라미터 정의);
  서브프로그램 종류 서브프로그램 이름(개수나 자료형, 순서가 다른 파라미터 정의);
END [패키지 이름];
```


```sql
-- 사원 번호 또는 사원 이름을 입력받아 사원 이름과 급여를 출력하기 위해 pro_emp 프로시저를 오버로드하는 패키지와 패키지 본문을 생성한다.  

-- 프로시저 오버로드하기
CREATE OR REPLACE PACKAGE pkg_overload
IS
  RPOCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE);
  RPOCEDURE pro_emp(in_ename IN EMP.ENAME%TYPE);
END;
/


-- 패키지 본문에서 오버로드된 프로시저 작성하기

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
      out_name EMP.ENAME%TYPE;
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

```sql
-- 패키지 본문 생성하기 
CREATE [OR REPLACE] PACKAGE BODY pck_example
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

#### 패키지 사용하기
패키지를 통해 그룹화된 변수, 상수, 예외, 커서 그리고 PL/SQL 서브프로그램은 패키지 이름과 마침표(.)와 사용할 객체 이름으로 사용할 수 있다.     

`패키지 이름.객체 이름`    

#### 패키지 삭제하기

```sql
-- 패키지 명세와 본문을 한 번에 삭제하기
DROP PACKAGE 패키지 이름;

-- 패키지의 본문만을 삭제
DROP PACKAGE BODY 패키지 이름;
```

> 패키지에 포함된 서브프로그램을 따로 삭제하는 것은 불가능하다.     
> (CREATE RO REPLACE문을 활용하여 패키지 안의 객체 또는 서브프로그램을 수정 및 삭제할 수 있다)
