## DFS & BFS

### DFS





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





### BFS

