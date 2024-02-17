# 트랜잭션 이상현상 (Transaction Anomalies)
다중 사용자 데이터베이스 환경에서 여러 트랜잭션 동시에 실행될 때,<br>
서로 다른 트랜잭션 간의 간섭으로 이상현상 발생
## 1. Dirty read (오손 읽기)
> <span style = "color:black; background-color:#fff5b1;"> 아직 커밋되지 않은 데이터를 읽는 현상<br><br>
<img src ="img\db_transaction_anomalies_dirty_read.png">

## 2. Non- repeatable read (반복 읽기 불가능)
> <span style = "color:black; background-color:#fff5b1;"> 한 트랜잭션 내에서 같은 조건의 쿼리를 두 번 실행했을 때, 두 결과가 다른 경우</span><br><br>
isolation 관점에서 '여러 트랜잭션이 동시에 실행되어도, 각각의 트랜잭션은 독립적으로 실행되야한다.'는 점에 위배<br>
다른 트랜잭션이 첫 번째 쿼리와 두 번째 쿼리 사이에 해당 데이터를 수정하고 커밋함으로써 발생
<img src ="img\db_transaction_anomalies_nonrepeatable_read.png">

## 3. Phantom read (유령 읽기)
> <span style = "color:black; background-color:#fff5b1;"> 한 트랜잭션 내에서 같은 쿼리를 두 번 실행했을 때,
첫번째 조회 결과에서 없던 레코드가 생성되거나 있었던 레코드가 없어지는 경우</span><br><br>
<img src ="img\db_transaction_anomalies_phantom_read.png">

---
**Non-repeatable read 와 Phantom read의 차이점?**<br>

 Non-Repeatable Read가 기존 레코드의 수정으로 인해 발생하는 반면, Phantom Read는 새로운 레코드의 추가나 삭제로 인해 발생

---
이외의 이상 현상에는 다음 4가지가 추가적으로 더 존재
- Dirty write
- Lost update
- Read skew
- Write skew
---
 # 격리수준 (Isolation level)
여러 트랜잭션이 동시에 처리될 때, 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있게 허용할지 여부를 결정하는 것 👉 <span style = "color:black; background-color:#fff5b1;">트랜잭션들이 서로 간에 얼마나 독립적으로 실행될 것인지를 정의</span><br><br>
격리 수준을 조정함으로써, 동시성과 데이터 일관성 사이의 균형을 찾을 수 있습니다. 
격리 수준이 낮을수록 동시성이 높아지지만, 데이터 일관성 문제가 발생할 확률이 증가합니다. 반대로, 격리 수준을 높이면 데이터 일관성은 보장되지만, 동시성이 감소할 수 있습니다. 

트랜잭션의 격리 수준은 격리(고립) 수준이 높은 순서대로
- SERIALIZABLE
- REPEATABLE READ
- READ COMMITTED
- READ UNCOMMITED

## 1. SERIALIZABLE
SERIALIZABLE은 가장 엄격한 격리 수준으로, 이름 그대로 트랜잭션을 순차적으로 진행<br><br>
SERIALIZABLE에서 여러 트랜잭션이 동일한 레코드에 동시 접근할 수 없으므로, <span style = "color:black; background-color:#fff5b1;">어떠한 데이터 부정합 문제도 발생하지 않습니다.</span> 하지만 트랜잭션이 순차적으로 처리되어야 하므로 <span style = "color:black; background-color:#fff5b1;">동시 처리 성능이 매우 떨어집니다.</span>
<br><br>
MySQL에서 SELECT FOR SHARE/UPDATE는 대상 레코드에 각각 읽기/쓰기 잠금을 거는 것이다. 하지만 순수한 SELECT 작업은 아무런 레코드 잠금 없이 실행되는데, 잠금 없는 일관된 읽기(Non-locking consistent read)란 순수한 SELECT 문을 통한 잠금 없는 읽기를 의미하는 것이다.<br>

SERIALIZABLE 격리 수준에서는 순수한 SELECT 작업에서도 대상 레코드에 넥스트 키 락을 읽기 잠금(공유락, Shared Lock)으로 건다. 따라서 한 트랜잭션에서 넥스트 키 락이 걸린 레코드를 다른 트랜잭션에서는 절대 추가/수정/삭제할 수 없다. 

---
🔍 **트랜잭션의 동작과정** <Br>
일반적인 RDBMS는 변경 전의 레코드를 UNDO 공간에 백업해둔다. 그러면 변경 전/후 데이터가 모두 존재하므로, 동일한 레코드에 대해 여러 버전의 데이터가 존재한다고 하여 이를 MVCC(Multi-Version Concurrency Control, 다중 버전 동시성 제어)라고 부른다. MVCC를 통해 트랜잭션이 롤백된 경우에 데이터를 복원할 수 있을 뿐만 아니라, 서로 다른 트랜잭션 간에 접근할 수 있는 데이터를 세밀하게 제어할 수 있다. 각각의 트랜잭션은 순차 증가하는 고유한 트랜잭션 번호가 존재하며, 백업 레코드에는 어느 트랜잭션에 의해 백업되었는지 트랜잭션 번호를 함께 저장한다. 그리고 해당 데이터가 불필요해진다고 판단하는 시점에 주기적으로 백그라운드 쓰레드를 통해 삭제한다.<br>

   예를 들면,
```sql
START TRANSACTION;
UPDATE member SET area = "경기" WHERE id = 1;
```
<img src ="img\db_transaction_process.png">

 UPDATE 문이 실행되면 커밋 여부와 관계없이 일단 버퍼풀의 값은 갱신되고 언두 로그에는 변경 전의 데이터가 복사된다. MVCC를 통해 동일한 레코드가 여러 버전으로 관리되고 있고, 이를 통해 커밋 이전의 데이터 읽기, 데이터 롤백 등이 가능한 것이다. 그렇다면 아직 커밋 또는 롤백이 되지 않은 상태에서 다른 사용자가 레코드를 조회하면 어떤 데이터가 조회될까?
진행중인 작업 내역을 보여줄지 또는 백업된 데이터를 보여줄지는 격리 수준(Isolation Level)에 따라 다르다. <br>

🤔 버퍼풀이란? 디스크에 저장된 테이블과 인덱스 정보(엄밀히는 페이지)를 캐시해두는 공간(데이터를 임시 저장하기 위한 메모리 공간)<br>

---
## 2. REPEATABLE READ
<span style = "color:black; background-color:#fff5b1;">트랜잭션이 시작될 때 읽은 데이터는 트랜잭션이 종료될 때까지 동일</span>하게 유지됩니다. <br><br>
REPEATABLE READ는 MVCC를 이용해 한 트랜잭션 내에서 동일한 결과를 보장하지만, <span style = "color:black; background-color:#fff5b1;">새로운 레코드가 추가되는 경우에 부정합</span>이 생길 수 있다. 

<img src ="img\db_isolation_level_repeated_read1.png">
사용자 B의 트랜잭션은(10) 사용자 A의 트랜잭션(12)이 시작하기 전에 이미 시작된 상태다. 이때 REPEATABLE READ는 트랜잭션 번호를 참고하여 자신보다 먼저 실행된 트랜잭션의 데이터만을 조회한다. 만약 테이블에 자신보다 이후에 실행된 트랜잭션의 데이터가 존재한다면 언두 로그를 참고해서 데이터를 조회한다.
따라서 사용자 A의 트랜잭션이 시작되고 커밋까지 되었지만, 해당 트랜잭션(12)는 현재 트랜잭션(10)보다 나중에 실행되었기 때문에 조회 결과로 기존과 동일한 데이터를 얻게 된다. 즉, REPEATABLE READ는 어떤 트랜잭션이 읽은 데이터를 다른 트랜잭션이 수정하더라도 동일한 결과를 반환할 것을 보장해준다.<br><br>


<img src ="img\db_isolation_level_repeated_read2.png">

REPEATABLE READ는 **새로운 레코드의 추가까지는 막지 않는다**고 하였다. 따라서 SELECT로 조회한 경우 트랜잭션이 끝나기 전에 다른 트랜잭션에 의해 추가된 레코드가 발견될 수 있는데, 이를 유령 읽기(Phantom Read)라고 한다. 하지만 MVCC 덕분에 일반적인 조회에서 유령 읽기(Phantom Read)는 발생하지 않는다. 왜냐하면 자신보다 나중에 실행된 트랜잭션이 추가한 레코드는 무시하면 되기 때문이다.<br>

그렇다면 언제 유령 읽기가 발생하는 것일까? 바로 잠금이 사용되는 경우이다.
마찬가지로 사용자B가 먼저 데이터를 조회하는데, 이번에는 SELECT FOR UPDATE를 이용해 쓰기 잠금을 걸었다. 여기서 SELECT … FOR UPDATE 구문은 베타적 잠금(비관적 잠금, 쓰기 잠금)을 거는 것이다. 읽기 잠금을 걸려면 SELECT FOR SHARE 구문을 사용해야 한다. 락은 트랜잭션이 커밋 또는 롤백될 때 해제된다. <br>
이 경우에도 MVCC를 통해 해결될 것 같지만, 두 번째 실행되는 SELECT FOR UPDATE 때문에 그럴 수 없다. 왜냐하면 잠금있는 읽기는 데이터 조회가 언두 로그가 아닌 테이블에서 수행되기 때문이다. 잠금있는 읽기는 테이블에 변경이 일어나지 않도록 테이블에 잠금을 걸고 테이블에서 데이터를 조회한다. <br>
따라서 SELECT FOR UPDATE나 SELECT FOR SHARE로 레코드를 조회하는 경우에는 언두 영역의 데이터가 아니라 테이블의 레코드를 가져오게 되고, 이로 인해 Phantom Read가 발생하는 것이다.<br>

<img src ="img\db_isolation_level_repeated_read3.png">

- SELECT 이후 SELECT: MVCC 때문에 Phantom Read X
- SELECT 이후 SELECT FOR UPDATE: Phantom Read O

## 3. READ COMMITTED
 <span style = "color:black; background-color:#fff5b1;">커밋된 데이터만</span> 읽을 수 있습니다. <br>
 READ COMMITTED는 REPEATABLE READ에서 발생하는 <span style = "color:black; background-color:#fff5b1;">Phantom Read에 더해 Non-Repeatable Read(반복 읽기 불가능) 문제</span>까지 발생한다.

<img src ="img\db_isolation_level_read_commited2.png">

Non-Repeatable Read(반복 읽기 불가능) 문제가 발생할 수 있다.
예를 들어,  사용자 B가 트랜잭션을 시작하고 name = “Minkyu”인 레코드를 조회했다고 하자. 해당 조건을 만족하는 레코드는 아직 존재하지 않으므로 아무 것도 반환되지 않는다.<br>
그러다가 사용자 A가 UPDATE 문을 수행하여 해당 조건을 만족하는 레코드가 생겼다고 하자. 사용자 A의 작업은 커밋까지 완료된 상태이다. 이때 사용자 B가 다시 동일한 조건으로 레코드를 조회하면 어떻게 될까? READ COMMITTED 는 커밋된 데이터는 조회할 수 있도록 허용하므로 결과가 나오게 된다.


## 4. READ UNCOMMITED
다른 트랜잭션이 커밋하지 않은 데이터를 읽을 수 있습니다. <br>
READ UNCOMMITTED에서는 다른 트랜잭션의 작업이 <span style = "color:black; background-color:#fff5b1;">커밋 또는 롤백되지 않아도</span> 즉시 보이게 된다.

<img src ="img\db_isolation_level_read_uncommited.png">

이렇듯 어떤 트랜잭션의 작업이 완료되지 않았는데도, 다른 트랜잭션에서 볼 수 있는 부정합 문제를 <span style = "color:black; background-color:#fff5b1;">Dirty Read(오손 읽기)</span>라고 한다. Dirty Read는 데이터가 조회되었다가 사라지는 현상을 초래하므로 시스템에 상당한 혼란을 주게 된다. 만약 위의 경우에 사용자 A가 커밋이 아닌 롤백을 수행한다면 어떻게 될까?<br>
사용자 B의 트랜잭션은 id = 51인 데이터를 계속 처리하고 있을 텐데, 다시 데이터를 조회하니 결과가 존재하지 않는 상황이 생긴다. 이러한 Dirty Read 상황은 시스템에 상당한 버그를 초래할 것이다.<br>
그래서 READ UNCOMMITTED는 RDBMS 표준에서 인정하지 않을 정도로 정합성에 문제가 많은 격리 수준이다. 따라서 MySQL을 사용한다면 최소한 READ COMMITTED 이상의 격리 수준을 사용해야 한다.

## 트랜잭션의 격리 수준 비교 / 요약
READ UNCOMMITTED는 부정합 문제가 지나치게 발생하고, SERIALIZABLE은 동시성이 상당히 떨어지므로 READ COMMITTED 또는 REPEATABLE READ를 사용하면 된다. 참고로 오라클에서는 READ COMMITTED를 기본으로 사용하며, MySQL에서는 REPEATABLE READ를 기본으로 사용한다. 
<img src ="img\db_isolation_level_comparison.png">