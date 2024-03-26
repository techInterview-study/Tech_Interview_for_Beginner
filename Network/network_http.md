# HTTP
### ✏️ HTTP란?
- Hyper Text Transfer Protocol
    > 웹 서버와 사용자의 인터넷 브라우저 사이에 문서를 전송하기 위해 사용되는 통신 규약

### ✏️ HTTP의 발전 과정
1. HTTP/0.9 - 웹의 시작
2. HTTP/1.0 - 표준화
3. HTTP/1.1 - 성능 최적화
4. HTTP/2 - 고속화와 보안 강화
5. HTTP/3 - 최신 프로토콜

### ✏️ HTTP 발전 과정 상세
#### 1. HTTP/0.9
> HTTP/0.9는 인터넷의 초기 단계에서 사용된 매우 기본적인 프로토콜로서, 웹의 기본 구조를 마련하는 데 중요한 역할을 했음

- 최초의 HTTP 버전
- Method : GET만 가능
- HTTP Header 부재
    > 메타데이터나 상태 코드 전송 불가  
    HTML 문서만 전송 가능
- 응답 구조 : 요청에 대한 응답으로 HTML 문서만 반환

-> HTTP/0.9는 웹 통신의 가장 기본적인 형태를 제공했으며, 이후 웹의 발전에 기초가 되는 중요한 역할을 수행

---

#### 2. HTTP/1.0
> HTTP/1.0은 웹 통신 기술의 중요한 발전 단계로, HTTP/0.9의 제한적 기능을 대폭 확장

- Method : GET, POST, HEAD 지원
- HTTP Header 지원
    > Server <-> Client 간의 메타데이터 전송을 가능하게 하여, 통신의 유연성과 확장성 제공
- 상태 코드 도입
    > Response에 상태 코드를 포함시켜 Request의 성공 여부와 오류 유형을 명확하게 구분
- Caching Mechanism 도입
    > Caching을 통해 Web Page 로딩 속도를 개선하고, 서버 부하를 줄일 수 있는 기초 마련

-> HTTP/1.0은 웹 통신의 기능성과 효율성을 크게 향상시켜, 복잡하고 다양한 웹 애플리케이션의 개발을 지원

---

#### 3. HTTP/1.1
> HTTP/1.1은 웹 통신의 효율성과 신뢰성을 대폭 개선한 중요한 프로토콜 업데이트

- Keep-Alive(연결 재사용)
    > TCP 연결을 여러 요청에 걸쳐 재사용하여, 페이지 로딩 속도와 서버 통신 효율 향상

    - HTTP/1.0에서는 기본적으로 한 연결당 하나의 요청을 처리, 요청에 따른 응답이 수신되면 TCP 종료
    - TCP 가상 회선 방식은 신뢰성을 보장하지만 속도가 느리다는 단점
    - Keep Alive Header, 연결을 유지하는 시간이 길어지면 서버에 부하가 생기기 때문에 연결을 유지하는 시간을 제한

        <img src="./img/network_http_1.png" height = "300px" alt = "network_http_1 img">
        <br></br>

- Pipelining(파이프라이닝)
    > 여러 요청을 연속적으로 보내고 순차적으로 응답을 받아, 통신 지연 시간 단축
    - AS IS : 여러 요청을 보낼 때 한 요청과 응답을 끝내고 다음 요청을 전송
    - TO BE : 첫번째 요청에 대한 응답이 완료되기 이전에 다음 요청 전송

        <img src="./img/network_http_2.png" height = "300px" alt = "network_http_2 img">
        <br></br>

- Cache-Control
    > 웹 콘텐츠의 재사용을 통해 서버 부하와 네트워크 대역폭 소비를 줄이는 방법 제공
- 청크 전송 인코딩
    > 대용량 데이터를 청크로 나눠 전송하여, 전송 효율성 증가
- Host Header
    > 하나의 서버(IP)에서 여러 도메인을 운영할 수 있게 하여 가상 호스팅 지원

-> HTTP/1.1은 웹 성능의 큰 도약을 이끌었으며, 현대 웹 서비스의 기반 기술

---

#### 4. HTTP/2
> HTTP/2는 웹 성능 최적화를 위해 설계된 프로토콜

- Binary Protocol(이진 프로토콜)
    > Data를 Binary 형식으로 전송하여 파싱과 오류 검출 절차 효율화
    - HTTP/1.1은 Text 기반 Protocol로 작성, 읽기는 편하지만 Data가 커지는 단점 존재
    - HTTP/2는 Data를 Binary로 변환 후 전송하여 파싱이 더 빠르고, 오류 발생 가능성을 낮춤

        <img src="./img/network_http_3.png" height = "300px" alt = "network_http_3 img">
        <br></br>

- Multiplexing(응답 다중화)
    > 단일 TCP 연결을 통해 여러 요청과 응답을 동시에 처리, 웹 페이지 로딩 시간 감소
    - HTTP /1.1은 하나의 TCP 연결 내 하나의 요청을 처리할 수 있었고, 응답이 순차적으로 치리되어야 해서 HOLB 같은 문제점 발생
        - HOLB(Head Of Line Blocking)
            > Network에서 Packet 대기열이 존재할 때 앞 선(Head) Packet이 지연될 때 발생하는 성능 저하 현상
    - HTTP/2는 하나의 TCP 연결에 여러 개의 요청을 처리할 수 있도록 Stream, Message, Frame 단위로 세분화 하여 Multiplexing 진행
        - Stream : 구성된 연결 내에서 전달되는 바이트의 양방향 흐름, TCP 연결에서 여러 개 Stream 존재 가능
        - Message : 논리적 요청 또는 응답 메시지에 매핑되는 프레임의 전체 시퀀스
        - Frame: HTTP/2에서 통신의 최소 단위. 각 최소 단위에는 하나의 프레임 헤더가 포함된다. 이 프레임 헤더는 최소한으로 프레임이 속하는 스트림을 식별(HEADERS Type Frame, DATA Type Frame이 존재)

            <img src="./img/network_http_4.png" height = "300px" alt = "network_http_4 img">
            <br></br>

            <img src="./img/network_http_5.png" height = "150px" alt = "network_http_5 img">
            <br></br>

- Server Push
    > Server가 Client의 요청을 기다리지 않고 필요한 리소스를 미리 보내는 기능

- Header Compression
    > Header Data의 중복을 제거하여 전송량을 줄이고, 통신 효율성 증대

-> HTTP/2는 웹 페이지의 로드 시간을 줄이고, 네트워크 효율성을 개선하는 등 웹 사용 경험을 크게 향상시키는 데 기여

---

#### 5. HTTP/3
> HTTP/3는 웹 통신의 새로운 패러다임을 제시하는 프로토콜, QUIC 프로토콜 위에서 구현되어 기존 성능 문제 해결

- QUIC Protocol
    > TCP 대신 QUIC를 사용하여 연결 설정 시간 감소 및 Packet 손실 시 빠른 회복 가능
    - TCP 문제점
        - 연결 지향형 프로토콜로서, 신뢰성 지향
        - 데이터 손실 발생 시 재전송 수행 -> 순차적으로 처리하기 때문에 재전송 시 병목현상 발생
        - 흐름제어
            - 데이터를 송신하는 곳과 수신하는 곳의 데이터 처리 속도를 조절하여 수신자의 버퍼 오버플로우를 방지하는 것
            - 송신하는 곳에서 감당이 안되게 많은 데이터를 빠르게 보내 수신하는 곳에서 문제가 일어나는 것을 막는다
        - 혼잡제어
            - 네트워크 내의 패킷 수가 넘치게 증가하지 않도록 방지하는 것
            - 정보의 소통량이 과다하면 패킷을 조금만 전송하여 혼잡 붕괴 현상이 일어나는 것을 막는다 (속도를 제어한다. 천천히 속도를 올린다.)

    - QUIC
        - UDP를 기반으로 하여, 구글에서 2013년에 TCP의 신뢰성 보장을 위해 제공되는 기능을 UDP에서 직접 구현해서 성능을 개선한 프로토콜이다
        - UDP 자체는 신뢰성을 보장하지 않지만, 추가적인 정의를 통해 신뢰성을 보장받을 수 있다
            >  UDP + 패킷 재전송, 혼잡 제어, 흐름 제어 기능 등 (직접 구현) = QUIC

            <img src="./img/network_http_6.png" height = "300px" alt = "network_http_6 img">
            <br></br>

            - 최초 연결(1-RTT): 연결에 필요한 정보를 한 번에 보냄
            - 다음 연결(0-RTT): 한 번 성공한 연결을 캐싱하고 다음 연결 때 캐싱된 정보를 사용함
            - 연결 내 스트림이 완전히 독립적으로 동작 -> 하나가 실패해도 다른 스트림에 영향X
            - IP 기반이 아니라 연결 별 고유 UUID(Connection ID)를 이용해 각 연결들을 식별한다
                - 그래서 TCP 기반에선 Wi-Fi에서 셀룰러 환경으로 이동하면 IP주소가 변경되어 연결을 재수립해야하지만
                - QUIC에서는 ID 기반이기 때문에 연결이 그대로 유지된다

-> HTTP/3는 웹의 속도와 안정성, 보안을 크게 향상 시켜 웹 통신 미래 도모

---
### 📢 질문 예상 List
1. TCP / UDP 설명
2. HTTP Protocol의 주요 버전들은 어떤 개선사항을 가지고 있나요?
3. HTTP/3 이전 버전과의 주요한 차이가 무엇인지 설명해주세요.

---
### 📌 Reference
- https://github.com/devSquad-study/2023-CS-Study