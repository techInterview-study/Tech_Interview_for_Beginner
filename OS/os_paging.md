# Paging

## 사전학습

### 연속 메모리 할당
![연속 메모리 할당](img\os_paging_0.PNG)
- 프로세스 이후에 프로세스의 크기만큼 연속적인 메모리 주소를 할당 받아 배치

### 외부 단편화
![외부 단편화](img\os_paging_externalfragmentation.PNG)
- 프로세스를 할당하기 어려울 만큼의 작은 메모리 공간들로 인해 메모리가 낭비되는 현상

## 1. 페이징을 통한 가상 메모리 관리

### 1.1 페이징이란

![페이징 정의](img\os_paging_definition.PNG)
- 각기 다른 크기의 프로세스가 메모리에 연속적으로 할당되었기 때문에 외부 단편화의 문제 발생

![페이징 해결 방법](img\os_paging_definition2.PNG)
- 메모리와 프로세스를 일정한 단위로 자르고, 메모리에 불연속적으로 할당할 수 있다면 외부 단편화가 생기지 않는다.

> 페이징은 `프로세스의 논리 주소 공간`을 `페이지`라는 일정한 단위로 자르고, `메모리 물리 주소 공간`을 `프레임`이라는 페이지와 동일한 크기의 일정한 단위로 자른 뒤 페이지를 프레임에 할당하는 가상메모리 관리 기법.

![Swap-in, Swap-out](img\os_paging_swapinout.PNG)
- 페이징 시스템에서의 swap-out은 page out, swap-in은 page in이라고 부르기도 한다.

>스와핑이란 ? 
현재 사용되지 않는 프로세스가 있을 경우 해당 프로세스의 메모리를 임시로 보조 기억장치로 옮겨두는것. 

![프로세스 실행](img\os_paging_swapinout2.PNG)
- 이는 프로세스를 실행하기 위해 프로세스 전체가 메모리에 적재될 필요가 없다는 뜻.

### 1.2 페이지 테이블

프로세스가 메모리에 불연속적으로 배치되어 있다면, CPU는 이를 순차적으로 실행할 수 없다.

> 이를 해결하기 위해 페이징 시스템은 프로세스가 (실제 메모리 내의 주소인) 물리주소에 불연속적으로 배치되더라도 (CPU가 바라보는 주소인) 논리 주소에는 연속적으로 배치되도록 `Page Table`을 이용

![페이지 테이블 예시1](img\os_paging_pagetable1.PNG)
![페이지 테이블 예시2](img\os_paging_pagetable2.PNG)

- CPU 내의 페이지 테이블 베이스 레지스터(`PTBR`, Page Table Base Register)는 각 프로세스의 페이지 테이블이 적재된 주소를 가리킨다.
> 예를 들어, 프로세스 A 가 실행될 때 PTBR 는 프로세스 A의 페이지 테이블을 가리키고, CPU는 프로세스 A의 페이지 테이블을 통해 프로세스 A의 페이지가 적재된 프레임을 알 수 있다. 

![PTBR 예시1](img\os_paging_ptbr.PNG)
![PTBR 예시2](img\os_paging_ptbr2.PNG)
- 이렇게 페이지 테이블을 메모리에 두면 메모리 접근 시간이 2배로 늘어난다. 메모리에 있는 페이지 테이블을 보기 위해 한 번, 그렇게 알게 된 프레임에 접근하기 위해 한 번 이렇게 두 번의 메모리 접근이 필요하다.
- 이 문제를 해결하기 위해 CPU곁에 `TLB`(Translation Lookaside Buffer)라는 페이지 테이블의 캐시 메모리를 둔다.
-  = TLB는 페이지 테이블의 캐시 메모리 역할을 수행하기 위해 페이지 테이블의 일부를 저장

![TLB 설명](img\os_paging_tlb.PNG)
![TLB Hit and Miss](img\os_paging_tlbhitmiss.PNG)
- CPU가 발생한 논리 주소에 대한 페이지 번호가 TLB에 있을 경우 이를 `TLB hit`라고 한다. 이 경우에는 페이지가 적재된 프레임을 알기 위해 메모리에 접근할 필요가 없다. 메모리 접근을 한 번만 하면 된다. 하지만 페이지 번호가 TLB에 없을 경우 이를 `TLB miss`라고 한다.

### 1.3 내부 단편화
페이징은 외부 단편화 문제를 해결할 수 있지만, 내부 단편화 라는 문제를 야기한다. 
- 프로세스의 논리 주소 공간을 페이지라는 일정한 크기 단위로 자른다. 하지만 모든 프로세스가 페이지 크기에 딱 맞게 잘리지 않는다.
- 페이지 크기가 10KB인데 프로세스의 크기가 108KB라면 마지막 페이지는 2KB 만큼의 크기가 남는다. 
- 이러한 메모리 낭비를 `내부 단편화` 라고 한다.
![Internal fragment](img\os_paging_internal.PNG)
- 내부 단편화는 하나의 페이지 크기보다 작은 크기로 발생. 그렇기에 하나의 페이지 크기가 작다면 발생하는 내부 단편화의 크기는 작아진다. 하지만 페이지 테이블의 크기가 커져 테이블이 차지하는 공간이 낭비된다. 
- 따라서 내부단편화를 적당히 방지하면서 너무 크지 않은 페이지 테이블이 만들어지도록 페이지의 크기를 조정해야한다. 

### 1.4 페이징에서의 주소 변환
![os_paging_address.PNG](img\os_paging_address.PNG)
하나의 페이지는 여러 주소를 포괄하고 있다. 따라서 특정 주소를 접근하려면 두 가지 정보가 필요하다:
1. 어떤 페이지에 접근하고 싶은지
2. 접근하려는 주소가 그 페이지로부터 얼마나 떨어져 있는지

그렇기 때문에 페이징 시스템에서는 모든 논리 주소가 기본적으로 page number와 offset(변위) 로 이루어져 있다. 
![os_paging_address_trans.png](img\os_paging_address_trans.png)

### 1.5 페이지 테이블 엔트리 PTE (Page Table Entry)
페이지 테이블의 각 엔트리 , 페이지 테이블의 각각의 행들은 페에지 테이블 엔트리라고 한다. 
![os_paging_pagetableentry](img\os_paging_pagetableentry.PNG)
- Present bit : 현재 해당 페이지에 접근 가능한지 여부 (현재 페이지가 메모리에 적재되어 있는지 아니면 보조기억장치에 있는지)
    - Present bit가 0인 메모리에 적재되어 있지 않은 페이지로 접근하려고 하면 Page fault라는 예외가 발생.
- 보호 비트 (protection bit): 페이지 보호 기능을 위해 존재하는 비트. Read (r), Write (w), eXecute (x)의 조합으로 읽기, 쓰기, 실행 권한을 나타냄
- 참조 비트 (reference bit): CPU가 이 페이지에 접근한 적이 있는지 여부. 적재 이후 한번도 읽거나 쓴 적이 없는 페이지는 0으로 유지.
- 수정비트 (modified bit)(dirty bit): 해당 페이지에 데이터를 쓴 적이 있는지 없는지 수정 여부. 1이면 변경된 적이 있는 페이지
![os_paging_pte.PNG](img\os_paging_pte.PNG)

### 1.6 페이징 작동방식

![os_paging_last.png](img\os_paging_last.png)
1. Virtual Address와 Page Table Ptr 값을 더해 Page Table에 접근한다.
- PTBR(Page Table Base Register)에는 Page Table의 주소가 담겨 있다.
2. Present bit를 확인한다.
- Present bit 가 1이면(물리 메모리에 필요한 데이터가 적재되어 있음)
    - 계속해서 진행한다.
- Present bit가 0이면(물리 메모리에 필요한 데이터가 적재되어 있지 않음)
    - Page Fault 발생, 해결 후 진행한다.
3. 해당하는 Frame Number를 찾고 가상 주소를 실제 주소로 변환한다.
- Page의 크기와 Frame의 크기가 동일하기 때문에 Offset을 그대로 이용할 수 있다.
4. Main Memory의 Frame에 접근하여 작업을 진행한다.

### 출처
혼자 공부하는 컴퓨터 구조 + 운영체제 (강민철 지음, 한빛미디어)
https://rntlqvnf.github.io/lecture%20notes/os-at-1/#paging