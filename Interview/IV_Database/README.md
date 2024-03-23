## 목차

- [1. 🎤 키(Key) 정리]()
- [2. 🎤 ERD와 정규화 과정]()
- [3. 🎤 트랜잭션 개념과 ACID 속성]()
- [4. 🎤 트랜잭션 concurrency control(Serializability, Recoverable)]()
- [5. 🎤 트랜잭션 Isolation level(격리 수준)과 이상 현상]()
- [6. 🎤 SQL - JOIN]()
- [7. 🎤 인덱스(INDEX)]()
- [8. 🎤 스키마(Schema)]()
- [9. 🎤 저장 프로시저(Stored PROCEDURE)]()
- [10. 🎤 DBMS와 NoSQL]()
- [11. 🎤 RDB와 NoSQL에서의 클러스터링/레플리케이션 방식]()
- [12. 🎤 B-Tree & B+Tree]()
- [13. 🎤 DB Locking]()
- [14. 🎤 DB의 Connection Pool]()
- [15. 🎤 Trigger]()
- [16. 🎤 Redis]() <br/><br/>

## <span style="color: #FFA500">**🎤 Key 정리**</span>

## <span style="color: #FFA500">🎤 ERD와 정규화 과정</span>

## <span style="color: #FFA500">**🎤 트랜잭션 개념과 ACID 속성**</span>

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

## <span style="color: #FFA500">**🎤 SQL - JOIN**</span>

## <span style="color: #FFA500">**🎤 인덱스(INDEX)**</span>

## <span style="color: #FFA500">**🎤 저장 프로시저(Stored PROCEDURE)**</span>

## <span style="color: #FFA500">**🎤 DBMS와 NoSQL**</span>

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

## <span style="color: #FFA500">**🎤 DB Locking**</span>

## <span style="color: #FFA500">**🎤 DB의 Connection Pool**</span>

## <span style="color: #FFA500">**🎤 Trigger**</span>

## <span style="color: #FFA500">**🎤 Redis**</span>