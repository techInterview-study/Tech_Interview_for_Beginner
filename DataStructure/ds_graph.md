# `Graph`

## 📌 차례
- 개념
- 종류
- 특징
- 구현 방법
- 탐색 방법
- 면접 예상 질문
<br>

## 🖥️ 개념

### ⚙️**그래프의 정의**
- 그래프는 `정점(Vertex)`와 `간선(Edge)`들의 유한 집합이라고 할 수 있다. 수학적으로는 `G=(V,E)`로 표현한다.
- 정점은 노드라고도 불리며 간선은 링크라고도 불린다.
- 트리나 연결리스트의 개념을 확장시킨 것으로 볼 수 있다.<br>
*즉, 연결되어있는 객체 간의 관계를 표현할 수 있는 자료 구조*
- 경로 길이: 경로를 구성하는 데 사용된 간선의 수
- 단순 경로: 경로 중에서 반복되는 정점이 없는 경우

<image src="./img/ds_graph_1.png" width = "400px" height="180px" alt = "graph img" >
<br>
<br>
<image src="./img/ds_graph_4map.png" width = "200px" height="200px" alt = "map img">

ex) 지도, 지하철 노선도의 최단 경로, 전기 회로의 소자들, 도로, 선수 과목 등..
###### 지하철 노선도는 여러개의 역들이 어떻게 연결되어 있는지를 보여주며 이렇게 그래프로 표현된 노선도를 이용하여 최단거리를 찾는 문제도 쉽게 해결할 수 있다.(길찾기에서 최단 경로를 잘 해줄 수 있는 이유)

#### <a href="https://ko.wikipedia.org/wiki/%EA%B7%B8%EB%9E%98%ED%94%84_(%EC%9E%90%EB%A3%8C_%EA%B5%AC%EC%A1%B0)" >위키백과 정의 바로가기</a>


## 🖥️ 종류
### 1️. 무방향 그래프
<image src="./img/ds_graph_3.png" width = "400px" height="180px" alt = "undirected graph img">

    - 무방향 그래프의 간선은 간선을 통해서 양방향으로 갈 수 있음을 나타낸다.

    - 정점 A와 정점 B를 연결하는 간선은 (A,B)와 같이 쌍으로 나타낸다.
    
    - 연결 그래프(Connected Graph): 모든 정점쌍에 대해 항상 경로가 존재하는 경우를 말한다. (Tree)

    - 비연결 그래프(Disconnected Graph): 특정 정점쌍 사이에 경로가 존재하지 않는 경우를 말한다.
### 2. 방향 그래프
<image src="./img/ds_graph_2.png" width = "400px" height="180px" alt = "directed graph img">

    - 간선에 방향이 존재하는 그래프를 말한다.
    
    - 방향 그래프 노드1과 노드2를 연결하는 간선 (1,2), (2,1)은 서로 다른 간선이며 위 그림에서는 (1,2)만 존재한다.

❗완전 그래프: 그래프에 속해있는 모든 정점이 서로 연결되어있는 그래프를 말한다.


## 🖥️ 특징
### 1. 가중치
>- 연결의 강도, 연결의 중요성을 `우선순위`를 매기는 방법이다.
>- 두 정점간의 연결 유무 뿐만 아니라 연결의 중요도까지 설정한다면 보다 복잡한 관계까지 표현가능하다.
        
### 2. 사이클 (Cycle)
>- 단순 경로의 시작 정점과 종료 정점이 동일하다면 Cycle이라 한다.

### 3. 차수 (Degree)

>- Undirected Graph에서 각 정점에 인접한 정점의 수를 말한다.
    > 위의 무방향 그래프 예시 그림에서 정점 B의 차수는 3이다.<br>
   ❗ `인접정점 (adjacent vertex)`란 간선에 의해 직접 연결된 정점을 말한다.
>- Directed Graph에서는 간선에 방향성이 존재하므로 차수가 두 개로 나뉜다.
>- 각 정점에서 나가는 간선의 개수를 진출 차수`Outdegree`라 하며, 들어오는 간선의 개수를 진입 차수`Indegree`라 한다. 

## 🖥️ 그래프 구현 방법
### 1. 인접 행렬 (adjacent matrix)
>- 그래프의 연결 관계를 이차원 배열로 나타내는 방식이다. 인접 행렬을 adj[][]라고 한다면 adj[i][j]에 대해서 다음과 같이 정의할 수 있다.
>- 해당하는 위치의 값을 통해 정점 간의 연결 관계를 파악할 수 있다.
>- `adj[i][j] : 노드 i에서 노드 j로 가는 간선이 있으면 1, 아니면 0`<br>
❗만약 가중치가 있는 그래프라면 1대신 가중치의 값을 직접 넣어주는 방식으로 구현할 수 있다.
>> 방향 그래프<br>
>> <image src="./img/ds_graph_5.png" width = "400px" height="180px" alt = "directed graph img">
<br><br>
>>무방향 그래프<br>
>><image src="./img/ds_graph_6.png" width = "400px" height="180px" alt = "undirected graph img"><br>
>>❗대각 성분을 기준으로 대칭이다.
>
>😊 장점 😊: 구현이 쉽다, 노드i와 노드j가 연결되어 있는지 확인하고 싶을 때 배열을 바로 확인하면 되므로 시간 복잡도가 O(1)이다.
>
>😒 단점 😒: 전체 노드의 개수를 V개 간선을 E개라고 한다면. 노드 i에 연결된 모든 노드들에 방문하고 싶으면 adj[i][1]~adj[i][V]를 모두 확인해야 한다. 노드의 개수에 비해 간선의 개수가 훨씬 적은 그래프라면 비효율적이다.
<br>ex) V = 100000000, E = 2일 경우 2개를 찾기 위해 100000000을 모두 확인
>> 🌟 구현 🌟<br>
>> 무향그래프<br>
>>  노드의 개수, 간선의 개수, 각 간선의 양끝 노드가 순차적으로 주어진다.<br>
>> 4<br>
>> 5<br>
>> 1 2<br>
>> 1 3<br>
>> 1 4<br>
>> 2 3<br>
>> 3 4<br>
>>```cpp
>>#include <iostream> 
>>using namespace std;
>>int node, edge;
>>int adj[100][100];
>>int main()
>>{
>>     cin >> node >> edge;
>>
>>     for(int i = 0; i < edge; i++)
>>    {
>>         int a, b; cin >> a >> b;
>>
>>         adj[a][b] = 1;
>>        //무향 그래프이기 때문에
>>        //노드 a에서 노드 b로 가는 간선이 존재한다면
>>        //노드 b에서 노드 a로 가는 간선도 존재한다고 생각할 수 있음
>>        adj[b][a] = 1;    }}
>>```

### 2. 인접 리스트 (adjacent list)
>- 정점에 연결되어 있는 다른 정점들을 리스트로 표현한 것이다.
>- 그래프의 연결 관계를 vector의 배열로 나타내는 방식이다.<br>
>- `adj[i] : 노드 i에 연결된 노드들을 원소로 갖는 vector`<br>
❗만약 가중치가 있다면, pair<int, int> adj[]를 통해 구현할 수 있다. 앞에는 노드번호 뒤에는 간선의 가중치를 저장하는 방식이다.
><image src="./img/ds_graph_9.png" width = "400px" height="180px" alt = "weight graph img">
>> 방향 그래프<br>
>> <image src="./img/ds_graph_7.png" width = "400px" height="180px" alt = "directed graph img">
>>- 그림은 편의상 링크드리스트와 같은 형태로 나타낸 것 이다. 
>>- 접근은 이차원 배열과 같은 방식을 이용하면 된다.
>>- 노드의 순서는 의미가 없다.
>>
>>무방향 그래프<br>
>><image src="./img/ds_graph_8.png" width = "400px" height="180px" alt = "undirected graph img">
>>- Edge수의 2배수의 배열을 갖게된다.
>
>😊 장점 😊: 인접 행렬과 달리, 실제로 연결된 노드들에 대한 정보만 저장하기 때문에 모든 벡터들의 원소의 개수의 합이 간선의 개수와 같다.<br>
즉, 간선의 개수에 비례하는 메모리만 차지한다.
>
>😒 단점 😒: 노드i와 노드j가 연결되어 있는지 알고 싶다면 adj[i]의 벡터를 전체를 돌며, j를 성분으로 갖는지 확인해야한다. 인접 행렬의 경우에는 adj[i][j]만 확인하면 됐다.
>> 🌟 구현 🌟<br>
>> 무향그래프<br>
>>```cpp
>>#include <iostream> 
>>using namespace std; 
>>
>>int node, edge;
>>vector<int> adj[100]; 
>>
>>int main()
>>{    cin >> node >> edge;
>>     for(int i = 0; i < edge; i++)
>>    {        int a, b; cin >> a >> b;
>>             adj[a].push_back(b);
>>             adj[b].push_back(a);    }     
>>     //노드 1과 연결된 모든 노드들의 번호를 출력하고 싶은 경우    
>>     for(int i = 0; i < adj[1].size(); i++)        
>>         cout << adj[1][i] << '\n';}
>>```


## 🖥️ 그래프 탐색 방법
### 1. 깊이 우선 탐색 (Depth First Search: `DFS`)
>- 넓게(wide) 탐색하기 전에 깊게(deep) 탐색하는 것이다.
>- 그래프 상에 존재하는 임의의 한 정점으로부터 연결되어있는 한 정점으로만 나아간다.
>- 모든 노드를 방문하고자 하는 경우에 이 방법을 선택한다.
>- 너비 우선 탐색보다 조금 더 간단하다.
### 2. 너비 우선 탐색 (Breadth First Search: `BFS`)
>- 깊게(deep) 탐색하기 전에 넓게(wide) 탐색하는 것이다. 
>- 그래프 상에 존재하는 임의의 한 정점으로부터 연결되어있는 모든 정점으로 나아간다.
>- 두 노드 사이의 최단 경로 혹은 임의의 경로를 찾고 싶을 때 이 방법을 선택한다. 



## 🖥️ 면접 예상 질문
- 그래프와 트리의 차이점에 대해서 설명해주세요.
- 그래프를 정의한다면? 그래프에 대해서 설명해주세요.
<br>
<br>
<br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br>
출처:<br>
- <a href="https://gmlwjd9405.github.io/2018/08/13/data-structure-graph.html">[자료구조]그래프란?</a><br>
- <a href="
https://m.blog.naver.com/PostView.naver?blogId=sunkwang0307&logNo=221556947570&proxyReferer=https:%2F%2Fm.search.naver.com%2Fsearch.naver%3Fquery%3D%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%20%EA%B7%B8%EB%9E%98%ED%94%84%EB%9E%80&where=m&sm=mob_hty.idx&qdt=1">자료구조 그래프 개요[c언어 프로그래밍]</a><br>
- <a href= "https://sarah950716.tistory.com/12">인접 행렬과 인접 리스트</a>