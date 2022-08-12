## 19 - 1 저장 서브프로그램

<br/>

- 여러번 사용할 목적으로 이름을 지정하여 오라클에 저장해 두는 PL/SQL 프로그램을 저장 서브프로그램(stored subprogram)이라고 한다.

- 익명 블록과 달리 저장 서브프로그램은 오라클에 저장하여 공유할 수 있으므로 메모리∙성능∙재사용성 등 여러 면에서 장점이 있다.

| | 익명 블록 | 저장 서브프로그램 |
|:-----:|:--------:|:--------:|
| 이름 | 이름 없음 | 이름 지정 |
| 오라클 저장 | 저장할 수 없음 | 저장함 |
| 컴파일 | 실행할 때마다 컴파일 | 저장할 때 한 번 컴파일 |
| 공유 | 공유할 수 없음 | 공유하여 사용 가능 |
|다른 응용 프로그램에서의<br/> 호출 가능 여부 | 호출할 수 없음 | 호출 가능 |

<br/>

- 오라클에서 저장 서브프로그램을 구현할 수 있는데 대표적인 구현 방식은 프로시저∙함수∙패키지∙트리거이다.

| 서브프로그램 | 용도 |
|:-----------:|:------------------:|
| 저장 프로시저<br/> (stored procedure) | 일반적으로 특정 처리 작업 수행을 위한 서브프로그램으로 SQL문에서는 사용할 수 없습니다. |
| 저장 함수<br/> (stored function) | 일반적으로 특정 연산을 거친 결과 값을 반환하는 서브프로그램으로 SQL문에서 사용할 수 있습니다. |
| 패키지<br/> (package) | 저장 서브프로그램을 그룹화하는 데 사용합니다. |
| 트리거<br/> (trigger) | 특정 상황(이벤트)이 발생할 때 자동으로 연달아 수행할 기능을 구현하는 데 사용합니다. |

<br/>

## 19 - 2 프로시저
<br/>

- 저장 프로시저(stored procedure)는 특정 처리 작업을 수행하는 데 사용하는 저장 서브프로그램으로 용도에 따라 파라미터를 사용할 수 있고 사용하지 않을 수도 있다.

<br/>

> 파라미터를 사용하지 않는 프로시저

### 프로시저 생성하기

- 작업 수행에 별다른 입력 데이터가 필요하지 않을 경우에 파라미터를 사용하지 않는 프로시저를 사용한다.

```sql
// 기본 형식
CREATE 1.[OR REPLACE] PROCEDURE 2.프로시저 이름
IS | AS 3.
  선언부
BEGIN
  실행부
EXCEPTION 4.
  예외 처리부
END [프로시저 이름]; 5.
```

| 번호 | 설명 |
|:------:|:------------:|
| 1. | 지정한 프로시저 이름을 가진 프로시저가 이미 존재하는 경우에 현재 작성한 내용으로 대체합니다. 즉 덮어 쓴다는 뜻이며 생략 가능한 옵션입니다. |
| 2. | 저장할 프로시저의 고유 이름을 지정합니다. 같은 스키마 내에서 중복될 수 없습니다. |
| 3. | 선언부를 시작하기 위해 IS 또는 AS 키워드를 사용합니다. 선언부가 존재하지 않더라도 반드시 명시합니다. DECLARE 키워드는 사용하지 않습니다. |
| 4. | 예외 처리부는 생략 가능합니다. |
| 5. | 프로지서 생성의 종료를 뜻하며 프로시저 이름은 생략 가능합니다. |

<br/>

- 프로시저 생성하기
![](https://velog.velcdn.com/images/bebeco/post/96a46de4-d157-4cea-ad95-68579d839a2d/image.png)

- 프로시저 실행하기
```sql
EXECUTE 프로시저 이름;
```



![](https://velog.velcdn.com/images/bebeco/post/6817d682-0b45-4f20-916d-be0bd09f9b67/image.png)

<br/>

### 프로시저 내용 확인하기

- 이미 저장되어 있는 프로시저를 포함하여 서브프로그램의 소스 코드 내용을 확인하려면 USER_SOURCE 데이터 사전에서 조회한다.

| USER_SOURCE의 열 | 설명 |
|:--------:|:--------:|
| NAME | 서브프로그램(생성 객체) 이름 |
| TYPE | 서브프로그램 종류(PROCEDURE, FUNCTION 등) |
| LINE | 서브프로그램에 작성한 줄 번호 |
| TEXT | 서브프로그램에 작성한 소스 코드 |

```sql
// USER_SOURCE를 통해 프로시저 확인하기
SELECT *
FROM USER_SOURCE
WHERE NAME = 'PRO_NOPARAM';
```

![](https://velog.velcdn.com/images/bebeco/post/ac7109d3-7cc9-49c8-bc5d-c9fb793636cb/image.png)

![](https://velog.velcdn.com/images/bebeco/post/bf6306a7-b4f8-4bd9-86eb-57d07b1bc7b1/image.png)

<br/>

### 프로시저 삭제하기

- DROP PROCEDURE 명령어로 프로시저를 삭제할 수 있다.

![](https://velog.velcdn.com/images/bebeco/post/22741944-01f9-4fc2-bf6e-fe5af3a756ab/image.png)

<br/>

> 파라미터를 사용하는 프로시저

- 프로시저를 실행하기 위해 입력 데이터가 필요한 경우에 파라미터를 정의할 수 있다. 파라미터는 여러 개 작성할 수 있으며 다음과 같은 형식으로 사용한다.
                                      
```sql
// 기본 형식
CREATE 1.[OR REPLACE] PROCEDURE 2.프로시저 이름
[(파라미터 이름1 [modes] 자료형 [ := | DEFAULT 기본값], 3.
  파라미터 이름2 [modes] 자료형 [ := | DEFAULT 기본값],
  ...
  파라미터 이름N [modes] 자료형 [ := | DEFAULT 기본값]
)]
IS | AS 4.
   선언부
BEGIN
   실행부
EXCEPTION 5.
   예외 처리부
END [프로시저 이름]; 6.
```

| 번호 | 설명 |
|:----:|:---------------:|
| 1. | 지정한 프로시저 이름을 가진 프로시저가 이미 존재하는 경우에 현재 작성한 내용으로 대체합니다. 즉 덮어 쓴다는 뜻이며 생략 가능한 옵션입니다. |
| 2. | 저장할 프로시저의 고유 이름을 지정합니다. 같은 스키마 내에서 중복될 수 없습니다. |
| 3. | 실행에 필요한 파라미터를 정의합니다. 파라미터는 쉼표(,)로 구분하여 여러 개 지정할 수 있습니다. 기본값과 모드(modes)는 생략 가능합니다. 자료형은 자리수 지정 및 NOT NULL 제약 조건 사용이 불가능합니다. |
| 4. | 선언부를 시작하기 위해 IS 또는 AS 키워드를 사용합니다. 선언부가 존재하지 않더라도 반드시 명시합니다. DECLARE 키워드는 사용하지 않습니다. |
| 5. | 예외 처리부는 생략 가능합니다. |
| 6. | 프로시저 생성의 종료를 뜻하며 프로시저 이름은 생략 가능합니다. |

<br/>

| 파라미터 모드 | 설명 |
|:----:|:--------------:|
| IN | 지정하지 않으면 기본값으로 프로시저를 호출할 때 값을 입력받습니다. |
| OUT | 호출할 때 값을 반환합니다. |
| IN OUT | 호출할 때 값을 입력받은 후 실행 결과 값을 반환합니다. |

### IN 모드 파라미터

- 프로시저 실행에 필요한 값을 직접 입력받는 형식의 파라미터를 지정할 때 IN을 사용한다.
- IN은 기본값이기 때문에 생략 가능하다.


프로시저에 파라미터 지정하기
![](https://velog.velcdn.com/images/bebeco/post/7e0ce48d-c896-4942-a665-32fe52174485/image.png)

파라미터를 입력하여 프로시저 사용하기
![](https://velog.velcdn.com/images/bebeco/post/10419db1-492a-4925-b281-8a4f46ad9170/image.png)

기본값이 지정된 파라미터 입력을 제외하고 프로시저 사용하기
![](https://velog.velcdn.com/images/bebeco/post/7532f0dd-79c5-4171-bbee-e80fa691bd78/image.png)

실행에 필요한 개수보다 적은 파라미터를 입력하여 프로시저 실행하기
![](https://velog.velcdn.com/images/bebeco/post/189858bf-d117-4ff0-bcee-a8ea214ef1ac/image.png)

파라미터 이름을 활용하여 프로시저에 값 입력하기
![](https://velog.velcdn.com/images/bebeco/post/ccb4d66e-12a3-4e8c-b999-cda5806c7d21/image.png)

| 종류 | 설명 |
|:----:|:------------:|
| 위치 지정 | 지정한 파라미터 순서대로 값을 지정하는 방식 |
| 이름 지정 | => 연산자로 파라미터 이름을 명시하여 값을 지정하는 방식 |
| 혼합 지정 | 일부 파라미터는 순서대로 값만 지정하고 일부 파라미터는 => 연산자로 값을 지정하는 방식(11g부터 사용 가능) |

<br/>

### OUT 모드 파라미터

- OUT 모드를 사용한 파라미터는 프로시저 실행 후 호출한 프로그램으로 값을 반환한다.

OUT 모드 파라미터 정의하기
![](https://velog.velcdn.com/images/bebeco/post/b8e4ade1-aa84-4fa3-8e07-45462451d42f/image.png)

OUT 모드 파라미터 사용하기
![](https://velog.velcdn.com/images/bebeco/post/06bea861-80fd-4ba1-91b8-221161d2cd3f/image.png)

<br/>

### IN OUT 모드 파라미터

- IN OUT 모드로 선언한 파라미터는 IN, OUT으로 선언한 파라미터 기능을 동시에 수행한다.

IN OUT 모드 파라미터 정의하기
![](https://velog.velcdn.com/images/bebeco/post/0f0492fa-a735-4b12-8b81-ccdbd549054b/image.png)

IN OUT 모드 파라미터 사용하기
![](https://velog.velcdn.com/images/bebeco/post/be71a779-210d-43aa-b932-7bc7cae1453b/image.png)

<br/>

> 프로시저 오류 정보 확인하기

생성할 때 오류가 발생하는 프로시저 알아보기
![](https://velog.velcdn.com/images/bebeco/post/e75eb70a-c107-430d-ad5b-cbc0b70fad20/image.png)

▶︎ 서브프로그램을 만들 때 발생한 오류는 SHOW ERRORS 명령어와 USER_ERRORS 데이터 사전을 조회하여 확인할 수 있다.

### SHOW ERRORS로 오류 확인

- SHOW ERRORS 명령어는 가장 최근에 생성되거나 변경된 서브프로그램의 오류 정보를 출력한다.

SHOW ERRORS 명령어로 오류 확인하기
![](https://velog.velcdn.com/images/bebeco/post/1ec831d2-cfb2-4737-a784-86a6e98b5324/image.png)

▶︎ SHOW ERRORS 명령어는 줄여서 SHOW ERR로 사용할 수도 있다.

```sql
// 최근에 발생한 프로그램 오류가 아니라 특정 프로그램의 오류 정보를 확인하기
SHOW ERR 프로그램 종류 프로그램 이름;
SHOW ERR PROCEDURE pro_err;
```

### USER_ERRORS로 오류 확인

```sql
// USER_ERRORS로 오류 확인하기
SELECT *
  FROM USER_ERRORS
 WHERE NAME = 'PRO_ERR';
```

![](https://velog.velcdn.com/images/bebeco/post/fdc23673-8c4c-4dad-871f-9d7a60cc225a/image.png)

---

