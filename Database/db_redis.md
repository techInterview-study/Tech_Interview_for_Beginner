# REDIS


## 📌 Cache?

### 🖥 개념 설명

- DP알고리즘과 비슷하게 나중에 요청올 결과를 미리 저장해두었다가 빠르게 서비스 해주는 메커니즘을 의미한다.
- 미리 결과를 저장하고 나중에 요청이 오면 해당 요청에 대해서 DB또는 API를 참조하지 않고 캐시에 접근하여 요청을 처리한다.
- cache가 나오게된 배경에는 80퍼센트의 결과는 20퍼센트의 원인으로 인해 발생한다는 파레토 법칙에있다.
    
    `파레토 법칙` : 80%의 결과는 20%의 원인으로 인해 발생한다.
    
    <image src="./img/db_redis_1.png" width = "500px" height="250px" alt = "파레토 img" >
    
    <aside>
    💡 즉, 모든 결과를 캐싱할 필요는 없으며, 우리는 서비스를 할 때 많이 사용되는 `20%`를 캐싱한다면 전체적으로 효율을 극대화 할 수 있다는 말.
    
    </aside>
    

### 🖥 현실적으로 Cache를 접하게 되는 순간

- 서비스를 처음 운영할 때는 WEB-WAS-DB 정도로 작게 인프라를 구축하는데, 사용자가 늘어나면 DB에 무리가 가기 시작한다.

    
- DB는 데이터를 물리 디스크에 직접 쓰기 때문에 서버에 문제가 발생해도 데이터가 손실되지는 않지만, 매 트랜잭션마다 디스크에 접근해야하므로 부하가 많아지면 성능이 떨어진다.
- 그래서 사용자가 늘어나면 DB를 스케일 인 또는 스케일 아웃하는 방식 외에도 캐시 서버를 검토하게 된다.

### 🖥 Cache 사용 구조

<image src="./img/db_redis_2.png" width = "500px" height="250px" alt = "cache img" >

1. 클라이언트로부터 요청을 받는다.
2. Cache와 작업을 한다.
3. 실제 DB와 작업한다.
4. 다시 Cache와 작업한다.

- 클라이언트가 웹 서버에 요청을 보내면, 웹 서버는 데이터를 DB에서 가져오기 전에 캐시에 데이터가 있는지 확인하고, 있다면 바로 클라이언트에게 저장된 데이터를 반환한다. 이를 `Cache Hit`라고 한다.
- 반대로 캐시 서버에 데이터가 없으면 DB에 데이터를 요청하여 원하는 데이터를 조회한 후 그 데이터를 클라이언트에게 제공하는데, 이를 `Cache Miss`라고 한다.
- 위 flow에서 캐시를 어떻게 사용하느냐에 따라서 `look aside cache`와 `write back`으로 나뉜다.
    - `look aside cache`(Lazy Loading)
        - 캐시에 데이터 존재 유무 확인
        - 데이터가 있다면 캐시의 데이터 사용
        - 데이터가 없다면 캐시의 실제 DB 데이터 사용
        - DB에서 가져 온 데이터를 캐시에 저장
        - look aside cache는 캐시를 한 번 접근하여 데이터가 있는지 판단한 후, 있다면 캐시의 데이터를 사용하고 없으면 실제 DB 또는 API를 호출한다. 대부분의 캐시를 사용한 개발이 해당 프로세스를 따른다.
    - `write back`
        - 모든 데이터를 캐시에 저장
        - 캐시의 데이터를 일정 주기마다 DB에 한꺼번에 저장 (배치)
        - DB에 저장한 데이터를 캐시에서 제거
        - write back은 주로, 쓰기 작업이 굉장히 많을때 INSERT 쿼리를 일일이 날리지 않고 한꺼번에 배치 처리를 하기 위해 사용한다. 
        예를 들어 영어 듣기 평가를 온라인으로 진행하는 서비스가 있을 때, 여러 학생이 동시에 제출 버튼을 누르면서 DB에 갑작스럽게 엄청난 쓰기 요청이 몰리게 되면 DB 서버가 죽을 수도 있다. 이때 write back 기반의 캐시를 사용하면 캐시 메모리에 데이터를 저장해 놓고, 이후 DB 디스크에 업데이트 해 주면 안전하게 쓰기 작업을 이행할 수 있다.
        - DB에서 디스크를 접근하는 횟수가 줄어들기 때문에 성능 향상을 기대할 수 있지만, DB에 데이터를 저장하기 전에 캐시 서버가 죽으면 데이터가 유실된다는 문제점이 있다.

## 📌 인메모리?

- 인메모리란 컴퓨터의 주기억장치인 **RAM**에 데이터를 올려서 사용하는 방법을 말한다.
- RAM에 데이터를 저장하게 된다면 메모리 내부에서 처리가 되므로 데이터를 저장하고 조회할때 하드디스크를 오고 가는 과정을 거치지 않아도 됨. → SSD,HDD 같은 저장공간에서 데이터를 가져오는 것보다 RAM에서 데이터를 가져오는 속도가 많게는 수백배 이상 빠르다.
    
    → 때문에 Redis는 속도가 빠르다는 `장점`을 가지고 있다.
    
- 용량으로 인해 데이터 유실이 발생할수 있다는 단점도 존재한다.
- Redis는 인메모리방식이므로 **서버의 메모리 용량을 초과**하는 데이터를 redis에서 처리할 경우, 서버 자체에도 문제가 생기며, ram의 특성인 휘발성(전원이 꺼지면 가지고 있던 데이터가 사라진다.)에 따라 ram에 저장되었던 레디스의 데이터들은 유실될 수 있다. 때문에 메인 데이터베이스로 사용하기에는 무리가 있다.
    
    > 레디스에서도 데이터를 영속적으로 저장하는 방법을 제공하고 있지만 빠르고 간편하게 사용하는 것을 목표로 하는 레디스의 인메모리 방식에는 적합하지 않은 방식이 될 수 있다.
    > 
    

## 📌 Redis? (레디스, Remote Dictionary Server)

### 🖥 **레디스 개념**

<image src="./img/db_redis_3.png" width = "500px" height="250px" alt = "redis img" >

- Redis는 Remote Dictionary Server의 약자로 외부에서 사용 가능한 `Key-Value 쌍의 해시 맵 형태의 서버`라고 생각할 수 있다. 그래서 별도의 쿼리 없이 Key를 통해 빠르게 결과를 가져올 수 있다.
- Redis는 Memcached와 비슷한 `캐시 시스템`으로서 동일한 기능을 제공하면서 **영속성,** 다양한 데이터 구조와 같은 부가적인 기능을 지원한다.
    - Memcached
        
        **Memcached** (멤캐시디, 멤캐시트)는 범용 분산  캐시 시스템이다. 외부 데이터 소스(예: 데이터베이스나 API)의 읽기 횟수를 줄이기 위해 데이터와 객체들을 RAM에 캐시 처리함으로써 동적 데이터베이스 드리븐 웹사이트의 속도를 높이기 위해 종종 사용된다.
        
- DB, Cache, Message Queue, Shared Memory 용도로 사용될 수 있다.
- 오픈소스로서 NoSQL로 분류되기도 하고, Memcached와 같이 인 메모리 솔루션으로 분류되기도 한다.
- 디스크에 데이터를 쓰는 구조가 아니라 메모리에서 데이터를 처리하기 때문에 작업 속도가 상당히 빠르다.

<aside>
💡 고성능 키-값 저장소로서 String, list, hash, set, sorted set 등의 자료 구조를 지원하는 NoSQL

</aside>


### 🖥  Redis의 특징

- List, Set, Sorted Set, Hash 등과 같은 **`Collection`** 을 지원한다.
- **`영속성`** 을 지원하는 인 메모리 데이터 저장소
- Race condition에 빠질 수 있는 것을 방지한다.
    - **`Redis는 Single Thread` →** Atomic 보장 (여기서의 원자성은 동시성을 보장한다는 의미. 여러 요청이 동시에 돌아가지는 않지만 빠르게 처리해 동시성을 보장하려고 노력하고 경쟁상태를 방지)
    - **`경쟁 상태(race condition)`** 란 둘 이상의 입력 또는 조작의 타이밍이나 순서 등이 결과값에 영향을 줄 수 있는 상태
- 읽기 성능 증대를 위한 서버 측 **`리플리케이션`** 을 지원
    
    -리플리케이션: 데이터 저장과 백업하는 방법과 관련이 있는 데이터를 호스트 컴퓨터에서 다른 컴퓨터로 복사하는 것인데 이때 다른 컴퓨터가 반드시 떨어진 지역에 있어야 하는 것은 아니다.
    
- 쓰기 성능 증대를 위한 클라이언트 측 **`샤딩`** 지원
    
    -샤딩:  데이터를 조각내 분산 저장하는 데이터 처리 기법. 조금 더 자세히 설명하자면, 샤딩은 일괄적 관리가 힘든 거대 데이터베이스나 네트워크를 작게 나눠서 저장하고 관리하는 방법이다.
    
- **`트랜잭션`** 지원
    - redis의 트랜잭션을 사용할 경우 명령들을 queue에 모아두고 한번에 처리한다.
    - redis의 트랜잭션은 RDB의 트랜젝션과 다른 점이 존재한다. 일반적인 트랜잭션은 작업이 완전히 실행되거나 rollback되는 **원자**성을 가지고 있지만 redis의 트랜잭션은 `rollback을 지원하지 않는다.`중간에 실패하더라도 실패한 명령을 제외하고 나머지 명령들은 모두 실행된다.
    - rdb의 원자성 --> 오류발생시 트랜잭션이 롤백
    - redis의 원자성 --> 하나의 스레드로 동작하며 중간에 다른 요청이 들어오지 않음
    - 장점: 롤백이 없기 때문에 `속도가 빠름`
    - 단점: 오류가 롤백되지 않음

### 🖥  Redis의 Collection

- 다른 인메모리 DB들과의 가장 큰 차이점은 `레디스의 다양한 자료구조이다`.
    
    문자열 , 해시 , 목록 , 집합 , 범위 쿼리가 있는 정렬된 집합 , 비트맵 , 하이퍼로그 로그 , 지리 공간 인덱스 및 스트림 과 같은 **컬렉션을 제공한다.**
    
    - 다양한 구조를 지원하여 개발 편의성이 좋다.

<image src="./img/db_redis_6.png" width = "500px" height="250px" alt = "collection img" >

### 🖥  Redis의 영속성

- Redis는 영속성을 보장하기 위해 데이터를 디스크에 저장할 수 있다.
- 서버가 내려가더라도 디스크에 저장된 데이터를 읽어서 메모리에 로딩한다. 데이터를 디스크에 저장하는 방식은 크게 두 가지가 있다.

> 데이터 저장 방식
> 
> - `RDB(Snapshotting) 방식`
>     - 순간적으로 메모리에 있는 내용 전체를 디스크에 옮겨 담는 방식
> - `AOF(Append On File) 방식`
>     - Redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태
> 

### 🖥  싱글 스레드

- Redis는 싱글 스레드를 사용하므로 연산을 원자적으로 처리하여 Race Condition이 거의 발생하지 않는다.
- 예를 들어, 친구 리스트의 친구를 추가하는 연산을 시도해 보자. 아래와 같이 정상적인 상황에서는 유저 각각의 트랜잭션이 순서대로 잘 행해지고 있으므로 문제가 없다.

<image src="./img/db_redis_4.png" width = "500px" height="250px" alt = "single thread img" >

- 그러나 동시에 친구 리스트에 B, C를 추가한다고 하면 어떨까?

<image src="./img/db_redis_5.png" width = "500px" height="250px" alt = "single thread img" >

- 두 트랜잭션이 동일한 최종 상태인 A를 자신의 메모리로 읽어 들이고, 그 상태에서 각자 B 또는 C를 추가하게되면 최종 상태가 (A, B) 혹은 (A, C)가 된다. 
(A, B) 혹은 (A, C)라고 한 이유는 컨텍스트 스위칭에 따라 두 트랜잭션 중 누가 먼저 끝날 지 예측할 수 없기 때문이다. 
물론 이러한 Race Condition을 해결하기 위해 격리 수준 등 여러 가지 기법이 있지만, Redis는 싱글 스레드를 사용하므로 하나의 트랜잭션은 하나의 명령만 실행할 수 있으므로 다수의 Race Condition을 해결할 수 있다. 
(모든이 아닌 다수라고 한 이유는 더블 클릭 이슈는 싱글 스레드만으로 해결 못함)
    
    따닥 누르면 write request가 2번 간다-> 이런거 처리 잘못되어있으면 똑같은 데이터 생길 수 있다.
    

## 📌 Redis의 사용처

인메모리의 장단점을 고려했을 때 Redis 가장 적합한 역할은 **캐시 데이터베이스 서버**로 사용하는 것. 인메모리의 단점인 휘발성이 강한 레디스의 특성을 고려하여 비교적으로 유실되어도 무방한 데이터들을 저장하여 사용하도록 한다.

1. 여러 서버에서 같은 데이터를 공유하고 싶을때 사용.
- Redis 자체가 **Atomic**하기때문에 Thread safe 하다.
- Single Thread 이기 때문에 Race Condition 발생 가능성이 낮다.
2. 주로 많이 쓰는 곳
- 인증 토큰등을 저장 (Strings or hash)
- Ranking 보드로 사용 ( Sorted Set )
- 유저 API Limit
- Job QUeue

## 📌 면접 질문

- **1. Redis란?**
    
    Redis는 고성능 키-값 저장소로서 String, list, hash, set, sorted set 등의 자료 구조를 지원하는 NoSQL이다.
    
- **2. Redis는 언제 사용하는가?**
    
    DB, Cache, Message Queue, Shared Memory 용도로 사용될 수 있다. 주로 Cache 서버를 구현할 때 많이 쓴다.
    
- **3. Redis의 특징?**
    - 영속성을 지원하는 인 메모리 데이터 저장소
    - 다양한 자료 구조를 지원함.
    - 싱글 스레드 방식으로 인해 연산을 원자적으로 수행이 가능함.
    - 읽기 성능 증대를 위한 서버 측 리플리케이션을 지원
    - 쓰기 성능 증대를 위한 클라이언트 측 샤딩 지원
    - 다양한 서비스에서 사용되며 검증된 기술
- **4. Redis는 왜 Thread Safe한가?**
    
    싱글 스레드 방식이어서 연산을 하나씩 처리한다.
    
- **5. Redis는 왜 속도가 빠른가?**
    
    Key-Value 방식이므로 쿼리를 날리지 않고 결과를 얻을 수 있다.
    
- **6. Redis의 영속성 보장 방법?**
    
    RDB 또는 AOF 방식을 통해 영속성을 보장한다.
    
    - RDB(Snapshotting) 방식
        - 순간적으로 메모리에 있는 내용 전체를 디스크에 옮겨 담는 방식
    - AOF(Append On File) 방식
        - Redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태
- **7. Redis와 Memcached의 차이는?**
    
    <image src="./img/db_redis_7.png" width = "500px" height="250px" alt = "single thread img" >
    
- **8. Redis를 사용하면 얻는 이점?**
    
    Redis 사용의 장점은
    
    - 빠른 속도를 제공
    - 서버측 잠금을 지원
    - 클라이언트 라이브러리가 많이 있다.
    - 명령 수준 Atomic Operation(tx 작업)이 있다.
- **9. Redis의 한계?**
    - 단일 스레드
    - 일관된 해싱에 대한 클라이언트 지원이 제한되어있음
    - 지속성에 대한 상당한 오버헤드가 있음
    - 널리 배포되지는 않음

## 📌  출처
[Redis란 무엇일까..?](https://velog.io/@hope1213/Redis란-무엇일까)

[[데이터베이스] Redis란?](https://steady-coding.tistory.com/586)

[Redis 인터뷰 질문 및 답변 상위 10개(2024)](https://career.guru99.com/ko/top-10-redis-interview-questions/)