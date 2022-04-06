## Tree

### 개념

- 트리는 싸이클이 없는 무향 연결 "그래프"이다
- 원소들 간에 1:n 관계를 가지는 자료구조, 계층 관계를 가지는 계층형 자료 구조
- 이진트리 : 모든 노드가 최대 두개까지의 서브트리, 자식을 가질 수 있는 트리
- 포화 이진 트리 : 모든 레벨에 노드가 포화 상태, 높이가 h일 때 최대의 노드 갯수 : (2**(h+1)-1)
- 완전 이진 트리 : 포화 이진 트리의 노드 번호 n번까지 빈 자리가 없는 트리
- 편향 이진 트리 : 높이 h에 대한 최소 개수의 노드를 가지며 한쪽 방향의 자식 노드만 가짐
- 

```python
# 4
# 1 2 1 3 3 4 3 5

def pre_order(v):
    if v:  # 0 정점이 없으므로... 0번은 자식이 없는 경우를 표시
        print(v)  # visit(v)
        pre_order(ch1[v])
        pre_order(ch2[v])


def in_order(v):  # 포화 이진 트리를 따르는 경우 부모 노드 n의 자식은 n*2, n*2+1
    if v:  # 0 정점이 없으므로... 0번은 자식이 없는 경우를 표시
        in_order(ch1[v])
        print(v)  # visit(v)
        in_order(ch2[v])


def post_order(v):
    if v:  # 0 정점이 없으므로... 0번은 자식이 없는 경우를 표시
        post_order(ch1[v])
        post_order(ch2[v])
        print(v)  # visit(v)


E = int(input())  # edge 수

arr = list(map(int, input().split()))

V = E + 1  # 정점수 == 1번부터 V번까지 정점이 있을때 마지막 정보

# 부모번호를 인덱스로 자식번호 저장

ch1 = [0](V+1)
ch2 = [0](V+1)
for i in range(E):
    p, c = arr[i2], arr[i2 + 1]
    if ch1[p] == 0:
        ch1[p] = c
    else:
        ch2[p] = c

pre_order(1)
in_order(1)
post_order(1)

```



### Pre-order

```python
# 부모 번호에 자식 저장
def pre_order(v):
    if v: # 0번 정점이 업으므로 0번은 자
        print(v)  # visit(v)
        pre_order(ch1[v])
        pre_order(ch2[v])
        
# 자식 번호에 부모 저장
def pre_order2(v):
    global last
    if v <= last: # 마지막 정점 번호 이내
        print(v) # visit(v)
        pre_order2(v*2)  # 왼쪽 자식 정점 방문
        pre_order2(v*2+1) # 오른쪽 자식 정점 방문
```





### In-order

```python
# 부모 번호에 자식 저장
def in_order(v):
    if v:
        in_order(ch1[v])
        print(v)
        in_order(ch2[v])
```





### Post-order

```python
```





### 부모 번호를 인덱스에 저장

```python
E = int(input())
arr = list(map(int, input().split()))
V = E + 1

ch1 = [0]*(V+1)
ch2 = [0]*(V+1)
for i in range(E):
    p,c = arr[i*2], arr[i*2+1]
    if ch1[p] == 0:
        ch1[p] = c
    else:
        ch2[p] = c
```





### 자식 번호를 인덱스로 부모 번호에 저장

```python
par = [0]*(V+1)
for i in range(E):
    p, c = arr[i*2], arr[i*2+1]
    par[c] = p
    
# root 찾기
root = 0
for i in range(1, V+1):
    if par[i] == 0:
        root = i
        break
        
# 조상 찾기
c = 5
anc = []
while par[c] != 0:
    anc.append(par[c])
    c = par[c]
print(*anc)
```

루트를 찾거나 조상을 찾을 때 쓸 수 있다.



### 이진 탐색 트리

- 탐색 작업을 효율적으로 하기 위한 자료 구조

- 왼쪽 서브 트리 <  루트  < 오른쪽 서브 트리

- 중위 순회하면 오름차순으로 정렬된 값을 얻는다

- 따질 것 : x == k, x > k : , x < k

  

#### 이진 탐색 트리의 연산

- 삽입 연산
  - 탐색 연산 수행 : 같은 값의 원소가 있으면 삽입 불가, 탐색 실패한 위치가 삽입 위치가 된다
  - 탐색 실패한 위치에 원소를 삽입

- 삭제 연산
  - 삭제할 노드가 리프 노드가 아닌 경우 : 차수가 1인 경우
  - 부모 노드의 차수가 1인 경우 그냥 끌어다 이어준다
  - 부모 노드의 차수가 2인 경우(루트 포함) 왼쪽 서브트리에서 가장 큰 값 혹은 오른쪽 서브트리에서 가장 작은 값을 찾아 넣어준다



### HEAP 

- 완전 이진 트리에 있는 노드 중에서 키 값이 가장 큰 값, 가장 작은 값을 찾기 위해 만든 자료 구조
- 최대 힙 : 키 값이 가장 큰 노드(루트 노드)를 찾기 위한 이진 트리. 부모 노드 키 값 > 자식 노드 키 값
- 최소 힙 :  키 값이 가장 작은 노드(루트)를 찾기 위한 이진 트리. 부모 노드 키 값 < 자식 노드 키 값
- 힙에서는 루트 노드의 원소만을 삭제할 수 있다

```python
# 최대 힙
def enq(n):
    global last
    last += 1
    tree[last] = n
    c = last
    p = c // 2
    while p>= 1 and tree[p] < tree[c]:
        tree[p], tree[c] = tree[c], tree[p]
        c = p
        p = c//2
        
def deq():
    global last
    tmp = tree[1]
    tree[1] = tree[last]
    last -= 1
    # 부모 > 자식 규칙 유지
    p = 1
    c = p * 2
    while c <= last: # 왼쪽 자식이 있으면 
        if c + 1 <= last and tree[c] < tree[c+1]: # 오른쪽 자식에 넣는다
            c += 1
            
        if tree[p] < tree[c]:
            tree[p], tree[c] = tree[c], tree[p]
            p = c
            c = p*2
        else:
            break
        return tmp
    
    
tree = [0]*101
last = 0
enq(3)
enq(2)
enq(4)
enq(7)
enq(5)
enq(1)
print(tree[1])
enq(9)
print(tree[1])
while last > 0:
    print(deq())
    print(tree[1])
```



