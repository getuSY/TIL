## DFS & BFS

### DFS

**깊이 우선 탐색**

스택 자료 구조 혹은 재귀 함수 이용

1. 탐색 시작 노드를 스택에 삽입, 방문 처리

2. 스택의 최상단 노드에 방문하지 않은 인접 노드가 하나라도 있으면, 그 노드를 스택에 넣고 방문 처리. 

   방문하지 않은 인접 노드가 없으면 스택에서 최상단 노드를 꺼낸다.

3. 이 과정을 더 이상 수행할 수 없을 때까지 반복



#### DFS 구현

```python
def dfs(graph, v, visited):
    visited[v] = True
    for i in graph[v]:
        if not visited:
            dfs(graph, i, visited)
            
graph = []
visited = [0]*9
dfs(graph, 1, visited)
```



#### 백트래킹

```python
def checknode(v):
    if promising(v):
        if 
```



#### 부분집합 구하기

```python
bit = [0, 0, 0, 0]
for i in range(2):
    bit[0] = i
    for j in range(2):
        bit[1] = j
        for k in range(2):
            bit[2] = l
            for l in range(2):
                bit[3] = l
                print(bit)
```



### powerset을 구하는 백트래킹

```python
def backtrack(a, k, input):
    global MAXCANDIDATES
    c = [0] * MAXCANDIDATES
    
    if k == input:
        process_solution(a, k)
    else:
        k += 1
        ncandidates = construct_candidates(a, k, input, c)
        for i in range(ncandidates):
            a[k] = c[i]
            backtrack(a, k, input)
          
def construct_candidates(a, k, input, c):
    c[0] = True
    c[1] = False
    return 2

MAXCANDIDATES = 2
NMAX = 4
a = [0] * NMAX
backtrack(a, 0, 3)
```



### 단순 순열 생성

```python
```



### BFS

**너비 우선 탐색** : 가까운 노드부터 우선 탐색

큐 자료 구조 활용하기

1. 탐색 시작 노드를 큐에 삽입, 방문 처리
2. 큐에서 노드를 꺼낸 후 해당 노드 인접 노드 중에서 방문하지 않은 노드를 모두 큐에 삽입 후 방문 처리
3. 위 과정을 더 이상 수행할 수 없을 때까지 수행

리스트를 활용하여 큐를 구현할 수도 있지만 시간 복잡도 측면에서 너무 비효율적이 되므로 파이썬에서는 `from collections import deque`를 활용하는 편이 낫다.

```python
# deque 사용한 큐 구현
from collextions import deque

queue = deque()

queue.append(5)     # 리스트 append와 동일하게 작용
queue.append(2)
queue.append(7)
queue.popleft()     # 시간 복잡도 : 상수 시간, O(N)
queue.append(4)
queue.popleft()
```



#### BFS 구현

```python
# deque 없이 구현
def BFS(G, v):
    visited = [0]*(n+1)
    queue = []
    queue.append(v)
    while queue:
        t = queue.pop(0)
        if not visited[t]:
            visited[t] = True
            visit(t)
        for i in G(t):
            if not visited[i]:
                queue.append(i)
```



```python
from collections import deque

def bfs(graph, start, visited):
    queue = deque([start])
    visited[start] = True
    while queue:
        v = queue.popleft()
        for i in graph[v]:
            if not visited[i]:
                queue.append(i)
                visited[i] = True
                
graph = []
visited = [0]*9
bfs(graph, 1, visited)
```

