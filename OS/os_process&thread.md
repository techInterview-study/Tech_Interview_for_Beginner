# 🌊 Process & Thread

## 목차

- [1.Process](#1-process)
  - [1.1 프로세스(process)란](#1-process)
  - [1.2 프로세스 제어 블록](#2-프로세스-제어-블록-process-control-block--pbc)
  - [1.3 문맥 교환](#3-문맥-교환-context-switching)
  - [1.4 프로세스의 메모리 영역](#4-프로세스의-메모리-영역)
  - [1.5 프로세스 상태](#5-프로세스-상태)
  - [1.2 프로세스 계층 구조](#6-프로세스-계층-구조)
- [2. Thread](#2-thread)
  - [2.1 스레드란](#1-스레드thread란)
- [3. Multiprocess & MultiTread](#3-multiprocess--multitread)
  - [3.1 멀티프로세스](#1-멀티프로세스)
  - [3.2 멀티스레드](#2-멀티-스레드) 
  - [3.3 비교](#3-비교)

# 1. Process
 ## 1. 프로세스(process)란
- 실행중인 프로그램
- 보조기억 장치에 저장된 프로그램을 메모리에 적재하고 실행하는 순간 프로그램은 프로세스가 된다. 
('프로세스를 생성한다' 라고 표현)

<img src = "img\os_process_thread_process.PNG">


- 윈도우에서는 작업관리자의 [프로세스] 탭에서 확인 가능
- 유닉스 체계의 운영체제에선 ps 명령어로 확인가능

- 종류
    - forground process
        : 사용자가 볼 수 있는 공간에서 실행되는 프로세스
    - background process : 보이지 않는 공간에서 실행되는 프로세스
        - Service(윈도우)/daemon(유닉스) : 사용자와 상호작용하지 않는 프로세스


 ## 2. 프로세스 제어 블록 (Process Control Block ) (PBC)
 - 한정된 CPU 자원
 - 정해진 시간만큼 CPU이용하고 타이머 인터럽트가 발생하면 대기
 > 인터럽트 (Interrupt):
CPU가 프로그램을 실행하고 있을 때, 입출력 하드웨어 등의 장치에서 이슈나 예외 상황이 발생하여 처리가 필요할 경우에 CPU에 알려서 처리하는 기술

 >타이머 인터럽트 : 타이머 인터럽트를 발생시키는 칩이 컴퓨터에 별도로 존재. 
이 칩에서 일정 시간마다 타이머 인터럽트를 운영체제에 알려줌

<img src="img\os_process_thread_pbc.PNG">


- 프로세스 제어블록은 프로세스와 관련된 정보를 저장하는 자료 구조
- 해당 프로세스를 식별하기 위해 꼭 필요한 정보들 저장
- 커널 영역에 생성
>
운영체제의 핵심부- 커널

<img src="img\os_process_thread_kernel.PNG">


- PCB에 담기는 정보 
    - Process ID :<br>
         <img src="img\os_process_thread_pid.PNG"><br>

        특정 프로세스를 식별하기 위해 부여하는 고유한 번호 (윈도우 작업관리자에서 확인가능, 두번 실행하면 PID가 다른 두개의 프로세스 생성)
    - 레지스터 값 : 
        > 레지스터란 : CPU가 요청을 처리하는데 필요한 데이터를 일시적으로 저장하는 공간

        프로세스에서는 실행 차례가 돌아오면  이전까지 사용했던 레지스터의 중간값을 복원하여 진행하던 작업들을 이어 실행
    - 프로세스 상태
    - CPU 스케줄링 정보
    - 메모리 관리 정보

        어느 주소에 저장되어 있는지, 페이지 테이블 정보 등
    - 사용한 파일과 입출력 장치 목록

## 3. 문맥 교환 (Context Switching)
- 문맥 : 
    - 중간 정보, 프로세스 재개하기 위해 기억해야 할 정보
    - 프로세스의 PCB에 표현
- 문맥교환 :

    기존 프로세스의 문맥을 PCB에 백업하고, 새로운 프로세스를 실행하기 위해 문맥을 PCB로부터 복구하여 프로세스를 실행하는 것

<img src="img\os_process_thread_context.PNG">


> 문맥교환을 너무 자주 하면 오버헤드가 발생할 수 있다

## 4. 프로세스의 메모리 영역

<img src="img\os_process_thread_memory.PNG">

-  Code segment = text segment
    - 기계어로 이루어진 명령어 저장
    - read-only 공간
-   data segment 
    - 프로그램이 실행되는 동안 유지할 데이터가 저장되는 공간 (ex. 전역변수)
    - 크기가 고정된 영역(정적 할당 영역)
- heap segment 
    - 프로그래머가 직접 할당할 수 있는 저장공간
    - 동적 할당영역 (크기가 변할 수 있는 영역)
    - 메모리 공간을 반환하지 않는 다면 memory leak(메모리 누수)가 발생
- stack segment
    - 데이터를 일시적으로 저장하는 공간
    - 동적 할당 영역

## 5. 프로세스 상태

<img src ="img\os_process_thread_process_state_diagram.PNG">

- running : CPU를 할당 받아 실행 중인 상태

## 6. 프로세스 계층 구조
- 실행 도중 시스템 호출을 통해 다른 프로세스를 생성 가능
- 부모 프로세스: 새 프로세스를 생성한 프로세스
- 자식 프로세스 : 부모 프로세스에 의해 생성된 프로세스

<img src="img\os_process_thread_process_hierarcy.PNG">


- 부모프로세스는 `fork` 라는 시스템 호출을 통해 자신의 복사본을 자식 프로세스로 생성
- 자식프로세스는 `exec` 라는 시스템 호출을 통해 자신의 메모리 공간을 다른 프로그램으로 교체

<img src ="img\os_process_thread_fork_exec.PNG">


# 2. Thread

## 1. 스레드(Thread)란
- 프로세스를 구성하는 실행의 흐름 단위


☝️단일 스레드 프로세스<br>

<img src ="img\os_process_thread_thread1.PNG">


🖐️멀티 스레스 프로세스<br>

<img src ="img\os_process_thread_thread2.PNG">


 - 스레드들은 실행에 필요한 최소한의 정보 (레지스터, 스택)만을 유지한채 프로세스의 자원 공유하며 실행
 > 프로그램 카운터(Program counter, PC)는 마이크로프로세서(중앙 처리 장치) 내부에 있는 레지스터 중의 하나

<img src ="img\os_process_thread_thread3.PNG">


# 3. Multiprocess & MultiTread

 ## 1. 멀티프로세스
 - 여러 프로세스를 동시에 실행하는 것
- 장점
    - 독립된 구조로 안전성이 높은 장점이 있다.

- 단점
    - 독립된 메모리 영역이기 때문에 작업량이 많을수록( Context Switching이 자주 일어나서 주소 공간의 공유가 잦을 경우) 오버헤드가 발생하여 성능저하가 발생 할 수 있다.

## 2. 멀티 스레드 

 - 여러 스레드로 프로세스를 동시에 실행하는 것
 - 장점
    - 시스템 자원소모 감소 (자원의 효율성 증대)

 - 단점
    - 자원을 공유하기에 동기화 문제가 발생
    - 디버깅이 어렵다. (불필요 부분까지 동기화하면, 대기시간으로 인해 성능저하 발생)

<img src ="img\os_process_thread_multithread.PNG">

<img src ="img\os_process_thread_multithread_error.PNG">


## 3. 비교

 🤷‍♂️차이<br>

 <img src ="img\os_process_thread_difference.PNG"><br>

 프로세스는 자원 공유 x , 스레드끼리는 자원 공유
 운영체제가 시스템 자원을 효율적으로 관리하기 위해 스레드를 사용한다.

 🧑‍🏫=> 메모리 구분이 필요할 때는 multi process가 유리하고,
Context switching이 자주 일어나고 데이터 공유가 빈번한 경우, 그리고 자원을 효율적으로 사용해야 되는 경우에는 multi thread를 사용하는 것이 유리하다.

 

|   | 메모리 사용 / CPU 시간 | Context switching | 안전성 |
| :---: | :---: | :---: | :---: |
| multi process | 많은 메모리 공간 / CPU 시간 차지 | 느림 | 높음 |
| multi thread | 적은 메모리 공간 / CPU 시간 차지 | 빠름 | 낮음 |

출처 <br>
https://zhfvkq.tistory.com/12<br>
https://wviewer.kyobobook.co.kr/pdfViewer/ZTQ5NjVkM2EzZTMzOWNjOWUzMzI5NGY5YjI1NDZjNDJhY2Y5NGM3NTRmMGE3MWVlMzBkMTU5NWUxOWFhMWViNw==