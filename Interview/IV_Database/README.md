## 목차

- [1. 🎤 키(Key) 정리](#-key-정리)
- [2. 🎤 ERD와 정규화 과정](#-erd와-정규화-과정)
- [3. 🎤 트랜잭션 개념과 ACID 속성](#-트랜잭션-개념과-acid-속성)
- [4. 🎤 트랜잭션 concurrency control(Serializability, Recoverable)](#-트랜잭션-concurrency-controlserializability-recoverable)
- [5. 🎤 트랜잭션 Isolation level(격리 수준)과 이상 현상](#-트랜잭션-isolation-level격리-수준과-이상-현상)
- [6. 🎤 SQL - JOIN](#-sql---join)
- [7. 🎤 인덱스(INDEX)](#-인덱스index)
- [8. 🎤 스키마(Schema)](#-스키마schema)
- [9. 🎤 저장 프로시저(Stored PROCEDURE)](#-저장-프로시저stored-procedure)
- [10. 🎤 DBMS와 NoSQL](#-dbms와-nosql)
- [11. 🎤 RDB와 NoSQL에서의 클러스터링/레플리케이션 방식](#-rdb와-nosql에서의-클러스터링레플리케이션-방식)
- [12. 🎤 B-Tree & B+Tree](#-b-tree--btree)
- [13. 🎤 DB Locking](#-db-locking)
- [14. 🎤 DB의 Connection Pool](#-db의-connection-pool)
- [15. 🎤 Trigger](#-trigger)
- [16. 🎤 Redis](#-redis) <br/><br/>

## <span style="color: #FFA500">**🎤 Key 정리**</span>

**Q. 릴레이션 스키마 vs 릴레이션 인스턴스 각각 무엇?**

A. 릴레이션 스키마는 테이블의 구조를 의미하고 테이블에서 스키마는 테이블의 첫 행인 헤더에 나타나며, 각 데이터의 특징을 나타내는 속성, 자료타입 등의 정보를 담고있다. 인스턴스는 정의된 스키마에 따라 테이블에 각 행마다 실제로 저장되는 데이터의 집합을 의미한다. <br/><br/>
 
**Q. 차수 vs 카디날리티 각각 무엇?  ( + tuple/attribute에 대한 꼬리질문)**

A. 차수는 속성의 개수를 의미하며, 카디널리티는 튜플 즉, 인스턴스의 수를 의미한다. <br/><br/>

**Q. 슈퍼 키 / 후보 키 / 기본 키의 조건**

A. 

슈퍼키 : 테이블에서 **각 행을 유일하게 식별할 수 있는 하나 또는 그 이상의 속성들의 집합**이다. 릴레이션을 구성하는 모든 튜플에 대해 **유일성은 만족하나 최소성은 만족하지 못함**

후보키 : 테이블에서 **각 행을 유일하게 식별할 수 있는 최소한의 속성들의 집합**이다. **후보 키는 기본 키가 될 수 있는 후보들**이며 **유일성과 최소성을 동시에 만족**해야 함

기본키 : **후보 키 중 특별히 선정된 키**로, **중복된 값을 가질 수 없다.** **각 row(record)를 unique하게 구분하는 column 이므로 테이블 당 하나이다.** **NULL 값을 가질 수 없으며** 튜플에서 기본 키로 설정된 속성엔 **NULL 값이 있어서는 안됨** <br/><br/>

**Q. 외래 키 설정 시 지켜야 하는 조건 3가지 말해보슈** 

A. 

1. 부모 테이블을 삭제하기 위해선 자식 테이블을 먼저 삭제하여야 한다.
2. 참조될 테이블 (A)이 먼저 만들어지고, 참조하는 테이블(B)에 값이 입력되어야 한다.
3. 외래 키는 참조되는 테이블의 기본 키와 **동일한 키 속성을 갖는다.**
4. 참조될 (A) 열의 값은 참조될 (A) 테이블에서 기본 키(PK)로 설정되어 있어야 한다. <br/><br/>

## <span style="color: #FFA500">🎤 ERD와 정규화 과정</span>

**Q. 정규화란?**

A. 하나의 릴레이션에 하나의 의미만 존재하도록 릴레이션을 분해하는 과정이며 데이터의 일관성, 최소한의 데이터 중복, 최대한의 데이터 유연성을 위한 방법.

제1 정규형 : 테이블의 컬럼이 원자값을 갖도록 분해

제2 정규형: 제 1정규형을 만족하고, 기본키가 아닌 속성이 기본키에 완전 함수 종속이도록 분해

(완전 함수 종속이란 기본키의 부분집합이 다른 값을 결정하지 않는 것을 의미)

제3 정규형: 제2 정규형을 만족하고, 이행적 함수 종속을 없애도록 분해.

BCNF: 제3 정규형을 만족하고, 함수 종속성X→Y가 성립할 때 모든 결정자 X가 후보키가 되도록 분해 <br/><br/>

**Q. 정규화의 장단점을 설명해주세요**

A. 장점: 데이터베이스 변경 시 이상현상이 발생하는 문제점 해결. 데이터 베이스 구조 확장 시 정규화된 데이터베이스는 그 구조를 변경하지 않아도 되거나 일부만 변경해도 된다.

단점: 릴레이션 분해로 인해서 릴레이션 간의 연산을 수행할 때 join이 많아지고 이로 인해 성능이 저하될 수 있다. <br/><br/>

**Q. 역정규화와 그 필요성에 대해 말해주세요**

A. 데이터베이스의 비용을 최소화하기 위해 중복을 허용하며, entity를 다시 통합하거나 분할하여 정규화 과정을 통해 도출된 db 구조를 재조정하는 과정이다.

정규화를 거치면 릴레이션간의 연산이 많아지는데 이로인해 성능이 저하될 수 있다. 성능 문제가 있는(읽기 작업이 많이 필요한) DB의 전반적인 성능을 향상시키기 위함이다. <br/><br/>

**Q. ERD란?**

A. 개체-관계 다이어그램으로서, 

현실에 존재하는 사물이나 개체들을 데이터로 변환시켜 해당 개체의 속성과 다른 개체와의 관계를 도식화하여 나타내 주는 다이어그램

erd 다이어그램을 통해 개체의 성질과 데이터의 흐름을 파악하기가 용이해진다.

DB설계 과정을 시각적으로 확인하기가 좋다. <br/><br/>

**Q. Primary key란?**

A. 후보키 중 선택한 메인 키로써, 각 row(record)를 unique하게 구분하는 column

그렇기 때문에 null 값을 가질 수 없으며, 중복된 값을 가질 수 없습니다. table당 1개만 지정해야 한다. <br/><br/>

**Q. 관계형 데이터베이스의 N:M 관계에 대해서 말해보세요**

A. RDB에서 양쪽 entity 모두 서로가 1:N 관계를 가지고 있는 구조이다.

예를 들면 학생 entity와 과목 entity가 있을 때, N:M 관계를 가진다.

특정 학생은 여러 과목을 선택할 수 있으며, 특정 과목 또한 여러 학생들에게 선택될 수 있다. <br/><br/>

## <span style="color: #FFA500">**🎤 트랜잭션 개념과 ACID 속성**</span>

**Q. Transaction의 ACID 속성에 대해서 설명하세요.**

A. 트랜잭션의 ACID 속성은 데이터베이스 관리 시스템(DBMS)에서 안정적으로 트랜잭션을 처리하기 위해 필요한 네 가지 기본 요소

1. **원자성(Atomicity)**: 트랜잭션 내의 모든 연산은 전부 실행되거나 전혀 실행되지 않아야 한다는 것을 의미합니다. 즉, 하나의 트랜잭션 내에서 실행된 연산들은 분리할 수 없는 하나의 단위로 간주됩니다.
2. **일관성(Consistency)**: 트랜잭션이 성공적으로 완료되면, 데이터베이스는 하나의 일관된 상태에서 다른 일관된 상태로 변화해야 합니다. 이는 트랜잭션 수행 전후로 모든 데이터베이스 규칙을 충족시키는 것을 의미합니다.
3. **독립성(Isolation)**: 동시에 여러 트랜잭션이 실행될 때, 각 트랜잭션은 다른 트랜잭션의 연산이 끼어들지 않는 독립적인 실행을 보장받아야 합니다. 즉, 한 트랜잭션이 완료되기 전까지는 다른 트랜잭션에서 그 결과를 볼 수 없습니다.
4. **지속성(Durability)**: 트랜잭션이 성공적으로 완료되면, 그 결과는 영구적으로 데이터베이스에 반영되어야 합니다. 시스템에 장애가 발생하더라도 이러한 정보는 보존되어야 합니다. <br/><br/>

**Q. Transaction 실행 중에 오류가 발생했을 때 RollBack이 이루어지는 과정과 시스템 장애 후 DB 복구 Mechanism에 대해서 설명하세요.** 

A. 실행 중인 트랜잭션에서 오류가 발생하거나 중단되면, 시스템은 해당 트랜잭션에 의해 변경된 모든 데이터를 원래 상태로 되돌리는 RollBack 작업을 수행합니다. 이 과정은 트랜잭션의 원자성을 보장합니다. RollBack은 변경된 데이터를 기록하는 로그 파일을 사용하여 이전 상태로 데이터를 복원합니다. <br/><br/>

**Q. Transaction 중 발생할 수 있는 문제들(deadlock, lost update 등)과 이를 해결하기 위한 기법들에 대해 설명해주세요.** 

A. 트랜잭션 실행 중에는 여러 가지 문제가 발생할 수 있습니다.

1. Deadlock : 두 개 이상의 트랜잭션이 서로 상대방이 보유한 자원의 해제를 무한히 기다리는 상황으로, 어떤 트랜잭션도 진행될 수 없는 상태
    - 해결방법
        - **타임아웃 설정**: 일정 시간이 지나면 트랜잭션을 자동으로 롤백시킵니다.
        - **자원 할당 순서 정의**: 모든 트랜잭션이 자원을 같은 순서로 요청하도록 함으로써 교착 상태를 방지합니다.
        - **우선순위 할당**: 트랜잭션에 우선순위를 부여하여 높은 우선순위의 트랜잭션이 자원을 먼저 얻을 수 있도록 합니다.
        - **자원을 일괄적으로 할당**: 트랜잭션이 실행되기 전 필요한 모든 자원을 한 번에 할당받도록 합니다.
2. **Lost Update**: 동시에 같은 데이터를 수정하려는 두 트랜잭션이 있을 때, 한 트랜잭션의 변경 내용이 다른 트랜잭션에 의해 덮어쓰여지는 현상
    - 해결방법
        - **낙관적 잠금**: 데이터를 실제로 업데이트하기 전에 변경 사항이 발생했는지를 확인합니다. 변경이 발생했다면, 롤백합니다.
        - **비관적 잠금**: 데이터에 접근하기 전에 잠금을 걸어 다른 트랜잭션이 동시에 같은 데이터를 수정하지 못하도록 합니다.
3. **Phantom Read**: 한 트랜잭션 내에서 같은 쿼리를 두 번 실행했을 때, 첫 번째 쿼리 실행 결과와 두 번째 쿼리 실행 결과가 다른 현상
    - 해결방법
        - **Serializable 격리 수준**: 가장 엄격한 격리 수준을 적용하여, 트랜잭션 중에 다른 트랜잭션에 의한 변경을 완전히 차단합니다.
        - **범위 잠금**: 쿼리가 접근하는 데이터 범위에 잠금을 설정하여 다른 트랜잭션이 해당 범위의 데이터를 변경하지 못하게 합니다.
4. **Non-Repeatable Read**: 한 트랜잭션 내에서 동일한 쿼리를 두 번 실행할 때, 두 번째 실행 결과가 첫 번째 실행 결과와 다른 현상
    - 해결방법
        - **비관적 잠금**: 첫 번째 읽기 때 데이터에 잠금을 걸어 다른 트랜잭션이 해당 데이터를 변경할 수 없도록 합니다.
        - **격리 수준 조정**: 격리 수준을 Repeatable Read나 Serializable로 설정하여 같은 데이터에 대한 다른 트랜잭션의 접근을 제한합니다.

## <span style="color: #FFA500">**🎤 트랜잭션 concurrency control(Serializability, Recoverable)**</span>

**Q. conflict serializable (충돌 직렬가능)한 스케줄의 정의**

A. serial schedule과 conflict equivalent한 nonserial schedule을 conflict serializable 이라고 합니다. <br/><br/>

**Q. 충돌 직렬 가능한 스케줄이 아닌 비직렬 스케줄을 사용한다면 어떤 문제가 있을까요?**

A. 비직렬 스케줄의 경우 트랜잭션들이 어떻게 순서가 얽혀 있느냐에 따라서 잘못된 결과가 나올 수 있습니다. <br/><br/>

**Q. 그럼 여기서 conflict equivalent의 의미가 뭔가요?**

A. 두 스케줄이 같은 트랜잭션을 가지면서 모든 conflicting operation의 순서가 동일하면 => 두 스케줄은 conflict equivalent하다.

confilct 란? → 서로 다른 트랜잭션 소속이 같은 데이터에 접근하면서 최소한 하나는 write operation인 경우 => Confilct라고 한다. <br/><br/>

**Q. 여러 transaction이 동시에 system에서 동작할 시 장점**

A. 트랜잭션 처리량이 증가, 트랜잭션 응답 대기 시간 감소 및 시스템 병목 현상 감소 <br/><br/>

## <span style="color: #FFA500">**🎤 트랜잭션 Isolation level(격리 수준)과 이상 현상**</span>

**Q. Isolation Level 이란?**

A. 여러 트랜잭션이 동시에 사용된다면 다음과 같은 이상현상이 발생한다. Dirty read (오손 읽기), Non- repeatable read (반복 읽기 불가능), Phantom read (유령 읽기) 등의 이상현상이 발생하는데 이를 해결하기 위해 트랜잭션들이 서로 간에 얼마나 독립적으로 실행될 것인지를 정의하는 것이 트랜잭션 격리레벨이다. <br/><br/>

**Q. Isolation Level 의 필요성**

A. 격리 수준을 조정함으로써, 동시성과 데이터 일관성 사이의 균형을 찾을 수 있습니다. 격리 수준이 낮을수록 동시성이 높아지지만, 데이터 일관성 문제가 발생할 확률이 증가합니다. 반대로, 격리 수준을 높이면 데이터 일관성은 보장되지만, 동시성이 감소할 수 있습니다. <br/><br/>

**Q. Isolation Level 의 종류**

A. 

- SERIALIZABLE
- REPEATABLE READ
- READ COMMITTED
- READ UNCOMMITED <br/><br/>

## <span style="color: #FFA500">**🎤 SQL - JOIN**</span>

**Q. join이란 무엇인지 간단하게 설명하세요**

A. Join이란 한 데이터베이스 내의 여러 테이블의 레코드를 조합하여 하나의 결과로 표현한 것이다. 관계형 데이터베이스는 정규화 되어있기 때문에 원하는 결과를 도출하기 위해서는 여러 테이블을 조합할 필요가 있다. 이때 Join 연산자를 사용해 관련 있는 컬럼 기준으로 행을 합쳐주는 연산을 실행한다. <br/><br/>

**Q. inner join과 outer join의 차이를 설명하세요**

A. Inner Join, 즉 내부 조인은 가장 흔한 결합 방식이다. 벤 다이어그램으로 봤을 때 교집합에 해당하는 값들을 보여주는 조인 방식이라고 할 수 있다.
Outer Join, 즉 외부 조인은 특정 테이블의 데이터가 모두 필요한 상황에서 활용된다. 벤 다이어그램으로 봤을 때 합집합에 해당하며, 다른 테이블에 값이 없는 내용은 null로 채워진다. Left outer join, Right outer join, Full outer join으로 나누어진다. <br/><br/>

## <span style="color: #FFA500">**🎤 인덱스(INDEX)**</span>

**Q. 인덱스의 적용 기준이 어떻게 되는가?**

A. 

1. 카디널리티가 높은(중복도가 낮은) 컬럼
2. WHERE, JOIN, ORDER BY 절에 자주 사용되는 컬럼
    - 인덱스는 추가 공간이 필요하다.
    - 조건 절이 없다면 인덱스가 사용되지 않는다.
3. INSERT / UPDATE / DELETE가 자주 발생하지 않는 컬럼
4. 규모가 작지 않은 테이블 <br/><br/>

**Q. 인덱스의 종류는 무엇이 있고 각각에 대해 간단하게 설명한다면?**

A.  인덱스의 종류로는 클러스터링 인덱스(clustering index)와 논-클러스터링 인덱스(non-clustering index)가 있다. 클러스터링 인덱스는 실제 데이터와 같은 무리의 인덱스이며 논-클러스터링은 실제 데이터와 다른 무리의 별도의 인덱스인 것이다. <br/><br/>

**Q. 클러스터링 인덱스에 대해 더 자세하게 설명한다면?**

A. 클러스터링 인덱스를 사용하면, 인덱스 검색을 통해 해당 데이터를 바로 찾아갈 수 있으며, 인덱스 키 값에 기반한 범위 검색이나 순차 접근이 매우 효율적이다.

1. 데이터를 인덱스 키 값의 물리적 순서대로 디스크에 저장하기 때문에 실제 데이터 자체가 정렬된다
2. 테이블 당 1개만 존재 가능하다.
3. 리프 페이지가 데이터 페이지이다.
    - 페이지: 데이터가 저장되는 단위()
4. 아래의 제약조건 시 자동 생성된다.
    - primary key(우선순위)
    - unique + not null <br/><br/>

**Q. 논클러스터링 인덱스에 대해 더 자세하게 설명한다면?**

A. 

1. 실제 데이터 페이지는 그대로
2. 별도의 인덱스 페이지 생성 -> 추가공간 필요
3. 여러 개 존재
4. 리프 페이지에 실제 데이터 페이지 주소를 담고 있음
5. unique 제약조건 적용 시 자동 생성
6. 직접 index 생성 시 non-clustering index 생성 <br/><br/>

## <span style="color: #FFA500">**🎤 스키마(Schema)**</span>

**Q. 스키마가 무엇인지**

A. DB내에 어떤 구조로 데이터가 저장되는가를 나타내는 데이터베이스 구조 

더 자세히 말하자면, 개체의 특성을 나타내는 속성(Attribute) 속성들의 집합으로 이루어진 개체(Entity) 개체 사이에 존재하는 관계(Relation)에 대한 정의와 이 것들이 유지해야 할 제약조건을 기술한 것 <br/><br/>

**Q. 스키마 3계층은 어떻게 이루어져 있는지**

A. 사용자 관점에 따라 외부 스키마, 개념 스키마, 내부 스키마로 나뉜다. <br/><br/>

**Q. 스키마 3계층 각각에 대해 설명해보아라 (외부, 개념, 내부) + 꼬리질문 예정**

A. 

**외부 스키마(External Schema) = 사용자 뷰 (view)**

**사용자가 각 개인의 입장에서 필요로 하는 데이터베이스의 논리적인 구조를 정의**

• 하나의 데이터베이스 시스템에는 여러 개의 외부 스키마가 존재한다. <br/><br/>

**개념 스키마(Conceptual) Schema) = 전체적인 뷰 (view)**

**데이터베이스의 전체적인 논리 구조. 모든 응용 프로그램이나 사용자들이 필요로 하는 데이터를 종합한 조직 전체의 데이터베이스.**

- 개체 관의 관계와 제약 조건을 나타내고, 데이터베이스의 접근 권한, 보안 및 무결성 규칙에 관한 명세를 정의
- 데이터베이스 파일에 저장되는 데이터의 형태를 나타내는 것. 단순히 스키마(schema)라고 하면 개념 스키마를 의미 <br/><br/>
 
**내부 스키마(Internal Schema) = 저장 스키마(Storage Schema)**

**물리적 저장장치의 입장에서 본 데이터베이스 구조. 물리적인 저장 장치와 밀접한 계층**

• 실제로 데이터베이스에 저장될 레코드의 물리적 구조를 정의하고, 저장 데이터 항목의 표현 방법. 내부 레코드의 물리적 순서 등을 나타낸다. <br/><br/>

## <span style="color: #FFA500">**🎤 저장 프로시저(Stored PROCEDURE)**</span>

**Q. 저장 프로시저란?**

A. 한마디로 여러 쿼리를 한번에 수행하는 것.

- 일련의 쿼리를 마치 하나의 함수처럼 실행하기 위한 쿼리의 집합이다. 데이터베이스에 대한 일련의 작업을 정리한 절차를 관계형 데이터베이스 관리 시스템에 저장한(지속성) 것으로, **영구저장모듈**(Persistent Storage Module)이라고도 불린다.
- 저장 프로시저와 함수는 각각 다른 컨텍스트에서 사용되지만, `"편리하게 저장하고 가져다 쓰는" 측면`에서는 유사한 개념이다. <br/><br/>

**Q. 장점/단점?**

A. 장점 :

- 네트워크 트래픽이 감소한다.
- 보안성이 향상된다.
- 일반 쿼리보다 성능향상 가능하다.
    
    일반 쿼리 : 실행시마다 컴파일프로시저 <br/>
    프로시저 : 반복 실행시 캐시 메모리를 사용하여 실행 (컴파일 X)
    
- 반복적인 작업을 피할 수 있다.
- 개발 언어에 비의존적이다.

단점:

- 연산 처리 성능이 낮다.
- 디버깅이 어렵다.
- DB확장이 어렵

## <span style="color: #FFA500">**🎤 DBMS와 NoSQL**</span>

**Q. DBMS의 발전 과정은 어떻게 되나요?**

A.  DBMS는 계층형 데이터베이스로 시작해 네트워크, 관계형, 객체 지향 모델을 거쳐, 대규모 분산 처리와 다양한 데이터 유형을 효율적으로 처리할 수 있는 NoSQL 데이터베이스까지 발전했습니다. 이 과정에서 데이터 관리의 유연성, 확장성, 그리고 처리 능력이 지속적으로 향상되었습니다. 각 단계는 시대별로 데이터 관리와 처리의 필요성에 따라 진화해 왔습니다.



## <span style="color: #FFA500">**🎤 RDB와 NoSQL에서의 클러스터링/레플리케이션 방식**</span>

**Q. 클러스터링과 레플리케이션을 활용하는 이유**

A. DB 서버에서 트랜잭션을 수용하지 못한다면 (다수의 클라이언트가 동시에 DB에 접근하거나 대량의 데이터를 처리할 때), DB 스토리지에 저장된 데이터가 손상될 경우가 있다. 클러스터링과 리플리케이션은 이러한 문제점을 해결하기 위한 방식이다. <br/><br/>

**Q. 클러스터링 이란? (Fail Over 시스템과 관련하여 설명해주세요)**

A. 분산 환경을 구성하여 Single point of failure와 같은 문제를 해결할 수 있는 Fail Over 시스템을 구축하기 위해서 사용

**특징**

- 여러 개의 DB를 수평적인 구조로 구축하는 방식
- 동기 방식으로 데이터를 동기화
- 주로 부하의 분산을 목적으로 사용 <br/><br/>

**Q. 레플리케이션 이란?**

A. 다양한 이슈로 데이터 유실이 생길 경우를 대비해서 스토리지까지 복제함으로써 데이터의 유실을 최소화하는 기법 

**특징**

- 여러 개의 DB를 권한에 따라 **수직적인 구조**(Master-Slave)로 구축하는 방식
- **비동기**방식으로 데이터 동기화하기 때문에 일관성있는 데이터 얻지 못 할 수 있음
- Master 서버가 다운되면 복구 및 대처 까다로움 <br/><br/>

## <span style="color: #FFA500">**🎤 B-Tree & B+Tree**</span>

**Q. B-tree와 B+tree에 대해 설명해주세요.**

A. B-트리는 트리 자료구조의 일종으로 이진트리를 확장해 하나의 노드가 가질 수 있는 자식 노드의 최대 숫자가 2보다 큰 트리구조이고, B+Tree는 B-Tree의 변형된 형태로 데이터의 효율적인 삽입, 검색, 삭제를 추구하는 자료구조이다. <br/><br/>

**Q. 보편적으로 배열 대신 B-Tree 자료 구조를 DB 인덱스로 사용하는 이유에 대해 설명해주세요.**

A. 데이터는 기본적으로 second storage에 저장됩니다. 이때, B-Tree의 경우 self-balancing BST에 비해 second storage 접근을 적게 하기 때문에 접근 횟수를 최소화 할 수 있어 사용되고 있습니다. <br/><br/> 

**Q. 보편적으로 해시 테이블 대신 B-Tree 자료 구조를 DB 인덱스로 사용하는 이유에 대해 설명해주세요.**

A. 해시 테이블은 단일 키 검색에 뛰어나지만, 범위 검색이나 정렬된 결과 반환과 같은 작업에는 적합하지 않은 반면에, B-Tree는 범위 검색과 정렬된 순서 유지 등 다양한 쿼리 유형을 처리하는 데 효과적이므로 사용하고 있습니다. <br/><br/>

## <span style="color: #FFA500">**🎤 DB Locking**</span>

**Q. DB Lock에 대해 설명해주세요**

A. Lock이란 트랜잭션 처리의 일관성을 보장하기 위한 방법이다. 크게 공유락과 배타락 두 가지가 있다. <br/>
우선 공유락은 트랜잭션이 읽기를 할 때 사용하는 락이며, 데이터를 읽기만 하기 때문에 같은 공유락끼리는 동시에 접근이 가능하다. 반면 베타락은 데이터를 변경할 때 사용하는 락으로, 트랜잭션이 완료될 때까지 유지되며 배타락이 끝나기 전까지 어떠한 접근도 허용하지 않는다. <br/><br/>

**Q. deadlock 상황에 대해 설명해주세요**

A. Deadlock은 트랜잭션간의 교착상태를 의미한다. 두 개의 트랜잭션 간에 각각의 트랜젝션이 가지고 있는 리소스의 Lock을 획득하려고 할 때 발생한다.
가장 대표적인 데드락 상황으로는 1번 트랜젝션에서 2번 리소스의 배타락을 획득, 2번 트랜젝션에서는 1번 리소스의 배타락을 획득한 상황에서 동시에 상대방의 데이터를 엑세스하려고 하는 경우가 있다. 이때 기존의 Lock이 해제될 때까지 무한정 기다리게 되는 교착상태를 데드락이라고 한다. <br/><br/>

## <span style="color: #FFA500">**🎤 DB의 Connection Pool**</span>

**Q. DB Connection Pool이란?**

A. 클라이언트의 요청에 따라 각 어플리케이션의 스레드에서 데이터베이스에 접근하기 위해서는 Connection이 필요합니다. Connection pool은 이런 Connection을 여러 개 생성해 두어 저장해 놓은 공간(캐시), 또는 이 공간의 Connection을 필요할 때 꺼내 쓰고 반환하는 기법을 말한다. <br/><br/>

**Q. Connection Pool을 사용하는 이유에 대해 설명해보세요.**

A. 

- DB 접속 설정 객체를 미리 만들어 연결하여 메모리 상에 등록해 놓기 때문에 불필요한 작업(커넥션 생성, 삭제)이 사라지므로 클라이언트가 빠르게 DB에 접속이 가능하다.
- DB Connection 수를 제한할 수 있어서 과도한 접속으로 인한 서버 자원 고갈 방지가 가능하다.
- DB 접속 모듈을 공통화하여 DB 서버의 환경이 바뀔 경우 쉬운 유지 보수가 가능하다.
- 연결이 끝난 Connection을 재사용함으로써 새로 객체를 만드는 비용을 줄일 수 있다. <br/><br/>

## <span style="color: #FFA500">**🎤 Trigger**</span>

**Q. 트리거의 주요 용도**

데이터 무결성 보장: 데이터베이스에 부적절한 데이터가 입력되는 것을 방지합니다.

자동화된 작업 수행: 데이터에 변경이 일어났을 때 관련된 작업을 자동으로 수행합니다. 

복잡한 비즈니스 로직 구현: 단순한 데이터 입력이나 수정 뿐 아니라 복잡한 조건에 따르는 작업들을 자동으로 처리합니다. <br/><br/>

**Q. 트리거의 종류**

- **DML 트리거(Data Manipulation Language Trigger)**: INSERT, UPDATE, DELETE 같은 데이터 조작 언어에 의해 실행됩니다.
- **DDL 트리거(Data Definition Language Trigger)**: CREATE, ALTER, DROP 같은 데이터 정의 언어에 의해 실행됩니다.
- **데이터베이스 트리거(Database Trigger)**: 데이터베이스와 관련된 이벤트(EX: LOGON, LOGOFF)에 의해 실행됩니다. <br/><br/>

**Q. 장점/단점**

**장점**

- 데이터의 무결성과 일관성을 유지에 도움을 줍니다.
- 별도의 어플리케이션 로직을 구현하지 않고도 복잡한 연산이나 유효성 검사를 처리할 수 있습니다.
- 중복 코드가 줄어들어 데이터베이스 관리가 용이해집니다.

**단점**

- 트리거가 많아질수록 데이터베이스의 복잡성과 유지 관리의 어려움이 증가.
- 트리거로 인한 자동화 처리가 예기치 않은 결과를 초래할 수 있다. <br/><br/>

## <span style="color: #FFA500">**🎤 Redis**</span>

**Q. redis를 활용도와 연관지어서 설명해주세요.**

A. Redis는 Remote Dictionary Server의 약자로 외부에서 사용 가능한 `Key-Value 쌍의 해시 맵 형태의 서버`라고 생각할 수 있다. 그래서 별도의 쿼리 없이 Key를 통해 빠르게 결과를 가져올 수 있다.

- Redis는 Memcached와 비슷한 `캐시 시스템`으로서 동일한 기능을 제공하면서 **영속성,** 다양한 데이터 구조와 같은 부가적인 기능을 지원한다.
- DB, Cache, Message Queue, Shared Memory 용도로 사용될 수 있다.
- 오픈소스로서 NoSQL로 분류되기도 하고, Memcached와 같이 인 메모리 솔루션으로 분류되기도 한다.
- 디스크에 데이터를 쓰는 구조가 아니라 메모리에서 데이터를 처리하기 때문에 작업 속도가 상당히 빠르다.

사용처

인메모리의 장단점을 고려했을 때 Redis 가장 적합한 역할은 **캐시 데이터베이스 서버**로 사용하는 것. 인메모리의 단점인 휘발성이 강한 레디스의 특성을 고려하여 비교적으로 유실되어도 무방한 데이터들을 저장하여 사용하도록 한다.

1. 여러 서버에서 같은 데이터를 공유하고 싶을때 사용.
- Redis 자체가 **Atomic**하기때문에 Thread safe 하다.
- Single Thread 이기 때문에 Race Condition 발생 가능성이 낮다.
1. 주로 많이 쓰는 곳
- 인증 토큰등을 저장 (Strings or hash)
- Ranking 보드로 사용 ( Sorted Set )
- 유저 API Limit
- Job QUeue <br/><br/>

**Q. redis의 특징에 대해 설명해주세요.** 🌟**꼬리질문 ex) 왜 속도가 빠른지, 영속성 보장 방법 등…**

A. List, Set, Sorted Set, Hash 등과 같은 **`Collection`** 을 지원한다.

- 다른 인메모리 DB들과의 가장 큰 차이점은 `레디스의 다양한 자료구조이다`.
    
    문자열 , 해시 , 목록 , 집합 , 범위 쿼리가 있는 정렬된 집합 , 비트맵 , 하이퍼로그 로그 , 지리 공간 인덱스 및 스트림 과 같은 **컬렉션을 제공한다.**
    
    - 다양한 구조를 지원하여 개발 편의성이 좋다.
- **`영속성`** 을 지원하는 인 메모리 데이터 저장소
    - 레디스의 경우, 영속성을 선택적으로 활성화할 수 있으며, 인메모리 데이터 저장을 기본으로 제공합니다. 이는 레디스가 기본적으로 빠른 속도와 낮은 지연 시간을 제공하면서도 영속성이 필요한 경우 선택적으로 활성화할 수 있음을 의미합니다.
    - Redis는 영속성을 보장하기 위해 데이터를 디스크에 저장할 수 있다.
    - 서버가 내려가더라도 디스크에 저장된 데이터를 읽어서 메모리에 로딩한다. 데이터를 디스크에 저장하는 방식은 크게 두 가지가 있다.
    
    > 데이터 저장 방식
    > 
    > - `RDB(Snapshotting) 방식`
    >     - 순간적으로 메모리에 있는 내용 전체를 디스크에 옮겨 담는 방식
    > - `AOF(Append On File) 방식`
    >     - Redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태
    
- Race condition에 빠질 수 있는 것을 방지한다.
    - **`Redis는 Single Thread` →** Atomic 보장 (여기서의 원자성은 동시성을 보장한다는 의미. 여러 요청이 동시에 돌아가지는 않지만 빠르게 처리해 동시성을 보장하려고 노력하고 경쟁상태를 방지)
    - **`경쟁 상태(race condition)`** 란 둘 이상의 입력 또는 조작의 타이밍이나 순서 등이 결과값에 영향을 줄 수 있는 상태
- 읽기 성능 증대를 위한 서버 측 **`리플리케이션`** 을 지원
    - 리플리케이션: 데이터 저장과 백업하는 방법과 관련이 있는 데이터를 호스트 컴퓨터에서 다른 컴퓨터로 복사하는 것인데 이때 다른 컴퓨터가 반드시 떨어진 지역에 있어야 하는 것은 아니다.
- 쓰기 성능 증대를 위한 클라이언트 측 **`샤딩`** 지원
    - 샤딩: 데이터를 조각내 분산 저장하는 데이터 처리 기법. 조금 더 자세히 설명하자면, 샤딩은 일괄적 관리가 힘든 거대 데이터베이스나 네트워크를 작게 나눠서 저장하고 관리하는 방법이다.
- **`트랜잭션`** 지원
    - redis의 트랜잭션을 사용할 경우 명령들을 queue에 모아두고 한번에 처리한다.
    - redis의 트랜잭션은 RDB의 트랜젝션과 다른 점이 존재한다. 일반적인 트랜잭션은 작업이 완전히 실행되거나 rollback되는 **원자**성을 가지고 있지만 redis의 트랜잭션은 `rollback을 지원하지 않는다.`중간에 실패하더라도 실패한 명령을 제외하고 나머지 명령들은 모두 실행된다.
    - rdb의 원자성 --> 오류발생시 트랜잭션이 롤백
    - redis의 원자성 --> 하나의 스레드로 동작하며 중간에 다른 요청이 들어오지 않음
    - 장점: 롤백이 없기 때문에 `속도가 빠름`
    - 단점: 오류가 롤백되지 않음 <br/><br/>

**Q. redis의 이점과 한계점?**

A. **Redis를 사용하면 얻는 이점?**

- 빠른 속도를 제공
- 서버측 잠금을 지원
- 클라이언트 라이브러리가 많이 있다.
- 명령 수준 Atomic Operation(tx 작업)이 있다.

**Redis의 한계?**

- 단일 스레드
- 일관된 해싱에 대한 클라이언트 지원이 제한되어있음
- 지속성에 대한 상당한 오버헤드가 있음
- 널리 배포되지는 않음