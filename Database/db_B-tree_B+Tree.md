# 📌 B-Tree
B 트리는 트리 자료구조의 일종으로 이진트리를 확장해 하나의 노드가 가질 수 있는 자식 노드의 최대 숫자가 2보다 큰 트리구조이다.<br>
1. 노드에는 2개 이상의 데이터(key)가 들어갈 수 있으며, 항상 오름차순으로 정렬된 상태로 저장된다.
2. 내부 노드는 ceil(M/2) ~ M개의 자식을 가질 수 있다. 최대 M개의 자식을 가질 수 있는 B 트리를 M차 B트리라고 한다. <br>
<참고> ceil()함수는 올림 함수를 뜻한다. 예를 들면 ceil(3/2) = 2이다.
3. 특정 노드의 데이터(key)가 K개라면, 자식 노드의 개수는 K+1개여야 한다.
4. 특정 노드의 왼쪽 서브 트리는 특정 노드의 key보다 작은 값들로, 오른쪽 서브 트리는 큰 값들로 구성된다.
5. 노드 내에 데이터는 ceil(M/2) - 1개부터 최대 M-1개까지 포함될 수 있다.
6. 모든 리프 노드들이 같은 레벨에 존재한다.

<img src="./img/db_B_tree_and_B+tree_1.png" alt="db_B_tree_and_B+tree_1 img">

## 📍 B-Tree 탐색 과정
B-Tree는 루트 노드에서 탐색을 시작하여 하향식으로 탐색을 진행한다. 찾고자 하는 값이 K라면 다음과 같은 과정을 거친다.

1. 루트 노드에서 탐색을 시작한다.
2. K를 찾았다면 탐색을 종료한다.
3. K와 노드의 key값을 비교해 앎자은 자식 노드로 내려한다.
4. 해당 과정을 리프 노드에 도달할 때까지 반복한다.
5. 리프 노드에서도 K를 찾지 못한다면 트리에 값이 존재하지 않는 것이다.

## 📍 B-Tree 삽입 과정
B-Tree에 데이터를 삽입하는 과정은 탐색과는 다르게 상향식으로 진행된다. B-에서의 데이터 삽입은 항상 리프 노드에서 시작된다.

1. 트리가 비어있다면 루트 노드를 할당하고 K를 삽입한다.
2. 트리가 비어있지 않다면, 데이터를 넣을 적절한 리프 노드를 탐색한다.
3. 리프 노드에 데이터를 넣고 리프 노드가 적절한 상태에 있다면 종료한다.
4. 리프 노드가 부적절한 상태에 있다면 분리한다.

## 📍 B-Tree 삭제 과정
B-Tree에서 데이터를 삭제하는 과정이 일어나더라도 M차 트리는 다음과 같은 조건을 만족해야 한다.
1. 내부 노드는 M/2 ~ M개의 자식을 가질 수 있다.
2. 각 노드는 floor(M/2)-1 ~ M-1개의 데이터(key)를 가질 수 있다.
3. 노드의 key가 K개 라면 자식 노드의 개수는 K+1 개여야 한다.

이제 데이터 삭제에 관해서 보자.
- 삭제도 항상 리프노드에서 발생한다.
- 삭제 후 **최소 key 수**보다 적어졌다면 재조정한다.<br>

    1. key수가 여유있는 형제의 지원을 받는다.<br>
    1-1. 동생(왼쪽 형제)이 여유가 있다면<br>
    ➡️ 동생의 가장 큰 key를 부모 노드의 나와 동생 사이에 둔다.<br>
    ➡️ 원래 그 자리에 있던 key는 나의 가장 왼쪽에 둔다.<br>
    1-2. 그게 아니라 형(오른쪽 형제)이 여유가 있으면
    ➡️ 형의 가장 작은 key를 부모 노드의 나와 형 사이에 둔다.
    ➡️ 원래 그 자리에 있던 key는 나의 가장 오른쪽에 둔다.

    2. 1번이 불가능하다면 부모의 지원을 받고 형제와 합친다. 
    2-1. 동생이 있으면 동생과 나 사이의 key를 부모로부터 받는다.<br>
    ➡️ 그 key와 나의 key를 차례대로 동생에게 합친다.<br>
    ➡️ 나의 노드를 삭제한다.<br>
    2-2. 동생이 없으면 형과 나 사이의 key를 부모로부터 받는다.<br>
    ➡️ 그 key와 형의 key를 차례대로 나에게 합친다.<br>
    ➡️ 형의 노드를 삭제한다<br>

    3. 2번 후 부모에 문제가 있다면 거기서 다시 재조정한다.
    3-1. 부모가 root 노드가 아니라면
    ➡️ 그 위치에서부터 다시 1번부터 재조정을 시작한다.
    3-2. 부모가 root 노드이면서 비어있다면
    ➡️ 부모 노드를 삭제한다.
    ➡️ 직전에 합쳐진 노드가 root 노드가 된다.

<img src="./img/db_B_tree_and_B+tree_2.png" alt="db_B_tree_and_B+tree_2 img">
<img src="./img/db_B_tree_and_B+tree_3.png" alt="db_B_tree_and_B+tree_3 img">
<img src="./img/db_B_tree_and_B+tree_4.png" alt="db_B_tree_and_B+tree_4 img">
<img src="./img/db_B_tree_and_B+tree_5.png" alt="db_B_tree_and_B+tree_5 img">
<img src="./img/db_B_tree_and_B+tree_6.png" alt="db_B_tree_and_B+tree_6 img">
<img src="./img/db_B_tree_and_B+tree_7.png" alt="db_B_tree_and_B+tree_7 img">
<img src="./img/db_B_tree_and_B+tree_8.png" alt="db_B_tree_and_B+tree_8 img">
<img src="./img/db_B_tree_and_B+tree_9.png" alt="db_B_tree_and_B+tree_9 img">
<img src="./img/db_B_tree_and_B+tree_10.png" alt="db_B_tree_and_B+tree_10 img">
<img src="./img/db_B_tree_and_B+tree_11.png" alt="db_B_tree_and_B+tree_11 img">
<img src="./img/db_B_tree_and_B+tree_12.png" alt="db_B_tree_and_B+tree_12 img">
<img src="./img/db_B_tree_and_B+tree_13.png" alt="db_B_tree_and_B+tree_13 img">

- B tree의 속성을 만족하고 합칠 때는 왼쪽으로 합치기 때문에 위와 같이 된다.

<img src="./img/db_B_tree_and_B+tree_14.png" alt="db_B_tree_and_B+tree_14 img">
<img src="./img/db_B_tree_and_B+tree_15.png" alt="db_B_tree_and_B+tree_15 img">

- 원래 형제에게 먼저 도움을 청하는데 여유가 없으므로 부모의 도움을 받는다.

<img src="./img/db_B_tree_and_B+tree_16.png" alt="db_B_tree_and_B+tree_16 img">
<img src="./img/db_B_tree_and_B+tree_17.png" alt="db_B_tree_and_B+tree_17 img">

## 📍 Internal 노드 데이터 삭제
- 리프 노드에서 삭제하고 필요하면 재조정
- Internal 노드에 있는 데이터를 삭제하려면 리프 노드에 있는 데이터와 위치를 바꾼 후 삭제한다.
- 삭제 후 최소 key 수보다 적어졌다면 재조정
- 형제 지원 ➡️ 부모 지원 받고 형제 합침 ➡️ 부모 도움 후 부모도 문제 시 재차 조정

<img src="./img/db_B_tree_and_B+tree_18.png" alt="db_B_tree_and_B+tree_18 img">
<img src="./img/db_B_tree_and_B+tree_19.png" alt="db_B_tree_and_B+tree_19 img">
<img src="./img/db_B_tree_and_B+tree_20.png" alt="db_B_tree_and_B+tree_20 img">



## 📍 B-Tree가 DB 인덱스로 사용되는 이유
<img src="./img/db_B_tree_and_B+tree_21.png" alt="db_B_tree_and_B+tree_21 img">

### 이렇게 보듯 시간복잡도는 같지만 왜 B-Tree 게열을 사용할까?

<img src="./img/db_B_tree_and_B+tree_22.png" alt="db_B_tree_and_B+tree_22 img">

<img src="./img/db_B_tree_and_B+tree_23.png" alt="db_B_tree_and_B+tree_23 img"> <br>

# 📌 B+ Tree
- B+ Tree는 B-Tree의 변형된 형태로 데이터의 효율적인 삽입, 검색, 삭제를 추구하는 자료구조이다.
- B-Tree와 달리 삽입, 삭제 연산이 단말 노드에서만 이루어지며 단말 노드끼리 연결리스트로 연결되어 있다.
- 단말 노드가 순차집합 연결되어 있기 때문에 순차적인 탐색에 유리하다.

## 📍 데이터 삽입
1. 단말 노드가 가득 찼을 경우 ➡️ 중간값을 부모노드로 올리고 분할한다.
<img src="./img/db_B_tree_and_B+tree_24.png" alt="db_B_tree_and_B+tree_24 img"><br>
<img src="./img/db_B_tree_and_B+tree_25.png" alt="db_B_tree_and_B+tree_25 img"><br>
<img src="./img/db_B_tree_and_B+tree_26.png" alt="db_B_tree_and_B+tree_26 img"><br>
✅ 4를 넣었더니 노드가 꽉차서 [2][3,4]로 분열한 뒤 부모노드로 중간값 3이 올라간 모습이다.
2. 내부 노드가 가득 찼을 경우
<img src="./img/db_B_tree_and_B+tree_27.png" alt="db_B_tree_and_B+tree_27 img"><br>
✅ 5를 넣고나니 노드가 꽉 찼다.
<img src="./img/db_B_tree_and_B+tree_28.png" alt="db_B_tree_and_B+tree_28 img"><br>
✅ [3][4,5] 노드로 분열한 뒤 중간값인 4를 부모노드로 올린다.
<img src="./img/db_B_tree_and_B+tree_29.png" alt="db_B_tree_and_B+tree_29 img"><br>
✅ 부모노드도 꽉차서 중간값인 3을 부모로 올리고 [2][4]로 분열한다.<br>
✅ 이 때 중요한 것이 index set은 분열할 때 [3,4]로 되지 않는다는 것이다.

## 📍 데이터 삭제
- B+Tree에서 데이터 삭제는 단말노드에서만 일어나기 때문에 내부노드는 신경 쓸 필요가 없다.

### Case 1. 삭제한 노드가 underflow가 아닐 때 ➡️ 부모노드만 수정해주면 된다.
<img src="./img/db_B_tree_and_B+tree_30.png" alt="db_B_tree_and_B+tree_30 img"><br>
<img src="./img/db_B_tree_and_B+tree_31.png" alt="db_B_tree_and_B+tree_31 img"><br>
### Case 2. 삭제한 노드가 underflow일 때 ➡️ 형제노드에게 값을 빌린 후 부모노드 수정
<img src="./img/db_B_tree_and_B+tree_32.png" alt="db_B_tree_and_B+tree_32 img"><br>
<img src="./img/db_B_tree_and_B+tree_33.png" alt="db_B_tree_and_B+tree_33 img"><br>
### Case 3. 형제 노드도 underflow라면?
<img src="./img/db_B_tree_and_B+tree_34.png" alt="db_B_tree_and_B+tree_34 img"><br>
### 4. 데이터를 삭제한다
<img src="./img/db_B_tree_and_B+tree_35.png" alt="db_B_tree_and_B+tree_35 img"><br>
- 삭제하였지만 형제노드도 underflow 상태라 병합을 진행한다.

<img src="./img/db_B_tree_and_B+tree_36.png" alt="db_B_tree_and_B+tree_36 img"><br>

- 병합 후 부모노드를 수정한다.

# 📌 출처
- https://m.blog.naver.com/shekwl24/222245938621
- https://www.youtube.com/watch?v=bqkcoSm_rCs
- https://code-lab1.tistory.com/217