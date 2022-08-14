## 19 - 5 트리거
<br/>

> 트리거란?

- 오라클에서 트리거(trigger)는 데이터베이스 안의 특정 상황이나 동작, 즉 이벤트가 발생할 경우에 자동으로 실행되는 기능을 정의하는 PL/SQL 서브프로그램이다.

- 트리거를 통해 연관 데이터 작업을 잘 정의해 두면 좋은 장점
  1. 데이터와 연관된 여러 작업을 수행하기 위해 여러 PL/SQL문 또는 서브프로그램을 일일이 실행해야 하는 번거로움을 줄일 수 있습니다. 즉 데이터 관련 작업을 좀 더 간편하게 수행할 수 있습니다.

  2. 제약 조건(constraints)만으로 구현이 어렵거나 불가능한 좀 더 복잡한 데이터 규칙을 정할 수 있어 더 수준 높은 데이터 정의가 가능합니다.
  
  3. 데이터 변경과 관련된 일련의 정보를 기록해 둘 수 있으므로 여러 사용자가 공유하는 데이터 보안성과 안정성 그리고 문제가 발생했을 때 대처 능력을 높일 수 있습니다.
  
- 하지만 트리거는 특정 작업 또는 이벤트 발생으로 다른 데이터 작업을 추가로 실행하기 때문에 무분별하게 사용하면 데이터베이스 성능을 떨어뜨리는 원인이 되므로 주의가 필요하다.

- 트리거는 테이블∙뷰∙스키마∙데이터베이스 수준에서 이벤트 동작을 지정할 수 있다.

  1. 데이터 조작어(DML) : INSERT, UPDATE, DELETE
  2. 데이터 정의어(DDL) : CREATE, ALTER, DROP
  3. 데이터베이스 동작 : SERVERERROR, LOGON, LOGOFF, STARTUP, SHUTDOWN
  
- 트리거가 발생할 수 있는 이벤트 종류에 따라 오라클은 다음과 같이 구분한다.

| 종류 | 설명 |
|:-------:|:-----------------:|
| DML트리거 | INSERT, UPDATE, DELETE와 같은 DML 명령어를 기점으로 동작함 |
| DDL트리거 | CREATE, ALTER, DROP과 같은 DDL 명령어를 기점으로 동작함 |
| INSTEAD OF 트리거 | 뷰(View)에 사용하는 DML 명령어를 기점으로 동작함 |
| 시스템(system) 트리거 | 데이터베이스나 스키마 이벤트로 동작함 |
| 단순(simple) 트리거 | 다음 각 시점(timing point)에 동작함<br/> ∙ 트리거를 작동시킬 문장이 실행되기 전 시점<br/> ∙ 트리거를 작동시킬 문장이 실행된 후 시점<br/> ∙ 트리거를 작동시킬 문장이 행에 영향을 미치기 전 시점<br/> ∙ 트리거를 작동시킬 문장이 행에 영향을 준 후 시점 |
| 복합(compound) 트리거 | 단순 트리거의 여러 시점에 동작함 |

<br/>

> DML 트리거

- DML 트리거 형식
    - DML 트리거는 특정 테이블에 DML 명령어는 실행했을 때 작동하는 트리거이다.

```sql
// 기본 형식
CREATE [OR REPLACE] TRIGGER 트리거 이름 1.
BEFORE | AFTER 2.
INSERT | UPDATE | DELETE ON 테이블 이름 3.
REFERENCING OLD as old | New as new 4.
FOR EACH ROW WHEN 조건식 5.
FOLLOWS 트리거 이름2, 트리거 이름3 ... 6.
ENABLE | DISABLE 7.

DECLARE
  선언부
BEGIN
  실행부
EXCEPTION
  예외 처리부
END;
```

| 번호 | 설명 |
|:----:|:--------------:|
| 1. | 트리거 이름을 명시하고 트리거를 생성합니다. 트리거 내용을 갱신하려면 OR REPLACE 키워드를 함께 명시합니다. |
| 2. | 트리거가 작동할 타이밍을 지정합니다. BEFORE는 DML 명령어가 실행되기 전 시점, AFTER는 DML 명령어가 실행된 후 시점에 트리거가 작동합니다. |
| 3. | 지정한 테이블에 트리거가 작동할 DML 명령어를 작성합니다. 여러 종류의 DML 명령어를 지정할 경우에는 OR로 구분합니다. |
| 4. | DML로 변경되는 행의 변경 전 값과 변경 후 값을 참조하는 데 사용합니다(생략 가능). |
| 5. | 트리거를 실행하는 DML 문장에 한 번만 실행할지 DML 문장에 의해 영향받는 행별로 실행할지를 지정합니다. 생략하면 트리거는 DML 명령어가 실행할 때 한 번만 실행합니다. 생략하지 않고 사용할 경우, DML 명령어에 영향받는 행별로 트리거를 작동하되 WHEN 키워드를 함께 사용하면 DML 명령어에 영향받는 행 중 트리거를 작동시킬 행을 조건식으로 지정할 수 있습니다. |
| 6. | 오라클 11g부터 사용 가능한 키워드로서 여러 관련 트리거의 실행 순서를 지정합니다(생략 가능).
| 7. | 오라클 11g부터 사용 가능한 키워드로서 트리거의 활성화∙비활성화를 지정합니다(생략 가능). |

<br/>

> 트리거 관리

### 트리거 정보 조회

- 트리거 정보를 확인하려면 USER_TRIGGERS 데이터 사전을 조회한다.

```sql
// USER_TRIGGERS로 트리거 정보 조회하기
SELECT TRIGGER_NAME, TRIGGER_TYPE, TRIGGERING_EVENT, TABLE_NAME, STATUS
  FROM USER_TRIGGERS;
```

![](https://velog.velcdn.com/images/bebeco/post/2cb7cd47-ee61-468c-9c2d-86ee9a5a08b2/image.png)

<br/>

> 트리거 변경

- ALTER TRIGGER 명령어로 트리거 상태를 변경할 수 있다.
- 특정 트리거를 활성화 또는 비활성화하려면 ALTER TRIGGER 명령어에 ENABLE 또는 DISABLE 옵션을 지정한다.

```sql
ALTER TRIGGER 트리거 이름 ENABLE | DISABLE;
```

- 특정 테이블과 관련된 모든 트리거의 상태를 활성화하거나 비활성화하기

```sql
특정 테이블과 관련된 모든 트리거의 상태 활성화
ALTER TABLE 테이블 이름 ENABLE ALL TRIGGERS;

특정 테이블과 관련된 모든 트리거의 상태 비활성화
ALTER TABLE 테이블 이름 DISABLE ALL TRIGGERS;
```

<br/>

> 트리거 삭제

```sql
DROP TRIGGER 트리거 이름;
```

---

