## Graph

### 개념

- 그래프는 아이템 간의 연결 관계를 표현
- 그래프는 정점의 집합과 이들을 연결하는 간선의 집합으로 구성된 자료 구조
- V: 정점의 개수, E : 그래프에 포함된 간선의 개수
  - V개의 정점을 가지는 그래프는 최대 V*(V-1)/2개의 간선을 가질 수 있다
- N:N 관계 표기에 용이



### 유형

- 무향 그래프(Undirected Graph)
- 유향 그래프(Directed Graph)
- 가중치 그래프(Weighted Graph)
- 사이클 없는 방향 그래프(DAG, Directed Acyclic Graph)
- 완전 그래프 : 정점에서 가능한 모든 간선을 가진 그래프
- 부분 그래프 : 원래 그래프에서 일부 정점이나 간선을 제거한 그래프



### 인접(Adjacency)

- 두 개의 정점에 간선이 있어 연결되어 있으면 인접되어 있다고 한다
- 완전 그래프에 속한 임의의 두 정점은 모두 인접해있다



### 경로

- 경로란 간선을 순서대로 나열한 것
- 단순 경로 : 경로 중 한 정점을 최대 한 번만 지나는 것
- 사이클: 시작한 정점에서 끝나는 경로



### 그래프 표현

- 인접 행렬 : V X V 크기의 2차원 배열을 이용해 간선을 저장, 배열의 배열(포인터 배열)

  - 두 정점을 연결하는 간선의 유무를 행렬로 표현

    ```python
    # adj
    V, E = map(int, input().split())      # 마지막 정점 번호, 간선 수
    arr = list(map(int, input().split()))
    adjM = [[0]*(V+1) for _ in range(V+1)]
    adjL = [[] for _ in range(V+1)]
    
    for i in range(E):   # 무방향 그래프 인접 행렬
        n1, n2 = arr[i*2], arr[i*2+1]
        adjM[n1][n2] = 1
        adjM[n2][n1] = 1 # 방향성의 경우 이 줄 없음
        
    for j in range(E):  # 인접 리스트로 저장
        n1, n2 = arr[i*2], arr[i*2+1]
        adjL[n1].append(n2)
        adjL[n2].append(n1)   # 무향의 경우에만
    ```

  - 인접 관계를 파악하는 데는 불편하다

  - 배열의 크기가 크다

  - 단점을 보완하기 위해 인접 리스트 활용

- 인접 리스트 : 각 정점마다 해당 정점으로 나가는 간선의 정보를 저장

- 간선의 배열 : 간선의 시작과 끝을 배열에 연속적으로 저장



### DFS 

- 가장 최근에 지나온 곳의 정보를 꺼낼 수 있어야 한다 : 스택, 재귀

- DFS 알고리즘 : 재귀 -> 딱 한 번만 방문하는 경우 / 경로의 갯수 못 센다

  ```python
  DFS_Recursive(G, v):
      visited(v) <- true   // v 방문 설정
      for each all w in adjacency(G, v):
          if not visited[w]:
              DFS_Recursive(G, w)
  ```

- 가능한 모든 경로를 다 찾아야 한다! : 본인 정점으로 다시 돌아와서 뱅글뱅글 도는 것은 막아야한다! => 하지만 다른 정점을 거쳐서 다른 경로로 돌아오는 건 허용해줘야 한다.

  ```python
  # 위 마지막 줄에
  visited[w] <- false
  ```

  

- DFS 반복 구조

  ```python
  STACK s
  visited[]
  DFS[v]:
      push(s, v)
      # visited[v] = True
      while not isEmpty(s):
          v = stack.pop(s)
          if not visited[v]:
              visit[v]
              for each w in adjacency(v):
                  if not visited[w]:
                      push(s, w)
                      # visited[v] = True
                      
  ```
  
- DFS 재귀

```python
def dfs1(v, V):
    visited[v] = 1
    print(v, end = ' ')
    for w in range(V+1):   # i에 인접한 모든 노드 j에 대해
        if adjM[V][w] and visited[w] == 0:
            dfs1(w, V)
            
def dfs2(v, V):    # 인접 리스트 활용
    visited[v] = 1
    print(v, end = ' ')
    for w in adjL[v]:
        if visited[w] == 0:
            dfs2(w, V)
        
```



### BFS

```python
BFS(G, v)
	queue = [0]*V
    시작점 V를 큐에 삽입
    점 V를 방문한 것으로 표시
    while queue:
    	t <- 큐의 첫 번째 원소 반환
        for t와 연결된 모든 선에 대해
        	u <- t의 이웃점
            u가 방문되지 않은 곳이라면
            if visited[u] == 0:
            u를 큐에 넣고, 방문한 것으로 표기
```



### 서로소 집합

- 서로소 집합, 상호 배타 집합들은 서로 중복 포함된 원소가 없는 집합, 교집합이 없다.

- 집합에 속한 하나의 특정 멤버를 통해 각 집합들의 구분한다 > 대표자(represetative)

- 상호 배타 집합을 표현하는 방법 : 연결 리스트, 트리

- 상호 배타 집합 연산

  - Make-set(x) : x를 대표 원소로 하는 집합을 만든다
  - Find-set(x) : x가 속한 집합의 대표 원소가 무엇인지 찾는다
  - Union(x, y) : x를 대표 원소로 하는 집합과 y를 대표로 하는 집합을 합치고, 대표 원소를 x로 만든다.

- 트리 형태 : 자식 인덱스에 부모 번호를 저장하면 표현 가능

  - 하나의 집합을 하나의 트리로 표현
  - 자식 노드가 부모 노드를 가리키며 루트 노드가 대표자

  - Make-set(a~f) 예시

    ```python
    rep = [0, 1, 2, 3, 4, 5, 6] # 인덱스와 동일한 값으로 초기화된 리스트 설정
    ```

- Make-set(x) : 유일한 멤버를 포함하는 새로운 집합을 생성하는 연산

  ```python
  Make-set(x):
      p[x] <- x
  ```

- Find-Set(x) : x를 포함하는 집합을 찾는 연산

  ```python
  Find-Set(x):
      if x == p[x] : reeturn x
      else: return Find-Set(p[x])
  ```

  ```python
  # 반복
  Find-Set(x):
      while x != p[x]:
          x = p[x]
      return x
  ```

- Union(x,y) : x와 y를 포함하는 두 집합을 통합하는 연산

  ```python
  Union(x, y):
      p[Find-Set(y)] <- Find-Set(x)
  ```

  - 문제점 : 
  - 연산 효율을 높이기 위해 랭크 활용!



### 최소 신장 트리(MST)

- 그래프에서의 최소 비용 문제 
  - 모든 정점을 연결하는 간선들의 가중치의 합이 최소가 되는 트리
  - 두 정점 사이의 최소 비용 경로 찾기
- 신장 트리
  - N개의 정점으로 이루어진 무방향 그래프에서 N개의 정점과 N-1개의 간선으로 이루어진 트리
- 최소 신장 트리
  - 무방향 가중치 그래프에서 신장 트리를 구성하는 간선들의 가중치의 합이 최소인 신장 트리



#### Prim 알고리즘 1:45:00

- 하나의 정점에서 연결된 간선 중에 하나씩 선택하면서 MST를 만들어 ㄴ가ㅏㅁ
  - 임의 정점을 하나 선택해 시작
  - 선택한 정점과 인접하는 