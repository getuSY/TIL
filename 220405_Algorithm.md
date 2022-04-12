## 220405_Algorithm

### SWEA 14195 : 미생물 관찰

```python
# 5개 중 1개 맞고 Runtime Error...
import sys
sys.stdin = open("14195_input.txt", 'r')

def sepo(r, c, value):     # s2
    global cnt
    if 0 <= r < N and 0 <= c < M and arr[r][c] == value:
        if (r, c) not in visited:
            visited.append((r, c))
            cnt += 1
            sepo(r - 1, c, value)
            sepo(r + 1, c, value)
            sepo(r, c - 1, value)
            sepo(r, c + 1, value)
            return
    else:
        return

# s1 : 아무 생각 없이 재귀 사용해보려고 형태 짜다가 값이 이상해서 s2로 수정
# def sepo(r, c, value):
#     global cnt
#     d = ((-1, 0), (0, 1), (1, 0), (0, -1))
#     if (r, c) not in visited:
#         visited.append((r, c))
#         cnt += 1
#         for k in range(4):
#             if 0 <= r + d[k][0] < N and 0 <= c + d[k][1] < M and arr[r + d[k][0]][c + d[k][1]] == value:
#                 sepo(r + d[k][0], c + d[k][1], value)
#                 return cnt
#             else:
#                 return
#     else:
#         return

T = int(input())
for tc in range(1, T+1):
    N, M = map(int, input().split())
    arr = ['']*N
    for _ in range(N):
        arr[_] = list(input())
    A = list()
    B = list()
    visited = list()
    for r in range(N):
        for c in range(M):
            if (r, c) not in visited and arr[r][c] != '_':
                cnt = 0
                sepo(r, c, arr[r][c])
                if arr[r][c] == 'A':
                    A.append(cnt)
                elif arr[r][c] == 'B':
                    B.append(cnt)
    num_a, num_b = len(A), len(B)
    result = 0
    if max(A) >= max(B):
        result = max(A)
    else:
        result = max(B)
    print('#{} {} {} {}'.format(tc, num_a, num_b, result))

```

```python
# A형 대비 전 수정
import sys
sys.stdin = open("14195_input.txt", 'r')

def sepo(r, c, value):   # 똑같은 value를 가진 거만 찾아서 값을 센다!
    global cnt
    if 0 <= r < N and 0 <= c < M and arr[r][c] == value:
        if (r, c) not in visited:
            visited.append((r, c))
            cnt += 1
            sepo(r - 1, c, value)      # 없애볼까 했지만 생각해보니 필요함
            sepo(r + 1, c, value)
            sepo(r, c - 1, value)
            sepo(r, c + 1, value)
            print(visited)
            return
    else:
        return

T = int(input())
for tc in range(1, T+1):
    N, M = map(int, input().split())
    arr = ['']*N
    for _ in range(N):
        arr[_] = list(input())
    print(arr)
    A = list()
    B = list()
    visited = list()
    for r in range(N):
        for c in range(M):
            if (r, c) not in visited and arr[r][c] != '_':
                cnt = 0
                sepo(r, c, arr[r][c])
                if arr[r][c] == 'A':
                    A.append(cnt)
                elif arr[r][c] == 'B':
                    B.append(cnt)
    num_a, num_b = len(A), len(B)
    result = max(A) if max(A) >= max(B) else max(B)
    print('#{} {} {} {}'.format(tc, num_a, num_b, result))

```

- 4방향 탐색을 시행하니 이미 방문한 곳을 너무 많이 방문해서 visited가 같은 게 너무 많이 뜬다.
  - 어차피 배열 탐색은 열 방향으로 오른쪽, 행 방향으로 위에서부터 진행되니 sepo 함수의 재탐색은 아래, 좌우만 시행해도 같은 값이 나올 것이라 판단. 시행시킨 결과는 결과값과 같았다. 재귀 한 번 덜 돌려서 시간 및 메모리 축약할 수 있지 않을까?
  - 축약이 불가능한가? visited는 여전히 똑같이 여러 번 뜬다. 시간 조금 줄어든 것 외에는 메모리 사용량이나 visited 출력 횟수는 동일하다.
  - 다시 생각해보니 중간에 뻥 빈 공간이 있을 경우를 고려하여 위쪽 방향의 탐색도 필요하다
- 매번 간단한 식도 for나 if로 풀어서 썼는데 comprehension을 써보았다. 코드 길이 축약 가능.
- 그러나 여전히 Runtime Error가 뜬다. 완전히 새로 풀어봐야 할 것 같다.

```python
# 새로 풀어보기 => BFS 이용
from collections import deque
import sys
sys.stdin = open("14195_input.txt", 'r')

def bfs(r, c, value):
    global bfs_visited
    global arr
    queue = deque()
    queue.append((r, c))
    bfs_visited[r][c] = 1
    arr[r][c] = '_'      # 안 헷갈리게 갔다온 곳은 a, b 대신 _로 바꿔준다
    cnt = 1
    while queue:
        v = queue.popleft()
        arr[v[0]][v[1]] = '_'
        for dr, dc in ((-1, 0), (0, 1), (1, 0), (0, -1)):
            nr, nc = v[0] + dr, v[1] + dc
            if 0 <= nr < N and 0 <= nc < M and arr[nr][nc] == value and bfs_visited[nr][nc] == 0:               # 똑같은 value를 가진 것만 찾아 카운트 올린다!
                queue.append((nr, nc))
                bfs_visited[nr][nc] = 1
                cnt += 1
    return cnt


T = int(input())
for tc in range(1, T+1):
    N, M = map(int, input().split())
    arr = ['']*N
    for _ in range(N):
        arr[_] = list(input())
    A = list()
    B = list()

    # bfs로 풀이 > 여전히 런타임 남...
    bfs_visited = [[0] * M for _ in range(N)]
    for i in range(N):
        for j in range(M):
            if arr[i][j] == 'A' and bfs_visited[i][j] == 0:
                num = bfs(i, j, 'A')
                A.append(num)
            elif arr[i][j] == 'B' and bfs_visited[i][j] == 0:
                num = bfs(i, j, 'B')
                B.append(num)
    num_a, num_b = len(A), len(B)
    result = max(A) if max(A) >= max(B) else max(B)
    print('#{} {} {} {}'.format(tc, num_a, num_b, result))

```

- BFS 방식 기본 문제 풀이 방식 + a
- 케이스 5개 중 하나만 맞고 런타임 에러...
- 재현님 코드와 다른 것 : A, B의 갯수 세는 방식, MAX 값 구하는 방식 (MAX(LIST)의 경우 리스트가 비어 있으면 이 부분에서 문제가 생길 수 있다!), deque?



### SWEA 14190 :  민코씨의 꽃밭

```python
from collections import deque
import sys
sys.stdin = open("14190_input.txt.txt", 'r')

def bloom(r, c):
    lst = [0]*(soil+N+M+1)      # 날짜를 인덱스로 하는 리스트 lst 설정
    queue = deque()
    queue.append((r, c, 0))     # 해당 땅의 위치, 심은 날짜를 큐에 넣어준다
    visited = [[0]*M for _ in range(N)]
    visited[r][c] = 1
    while queue:
        v = queue.popleft()
        print(v)
        cnt = v[2] + 1          # 꽃은 심은 날짜 하루 뒤에 핀다
        lst[cnt] += 1           # 꽃이 피는 날짜에 꽃 개수  + 1
        lst[cnt+arr[v[0]][v[1]]] -= 1   # 꽃 지는 날짜에 꽃 개수 -1

        for dr, dc in [(-1, 0), (0, 1), (1, 0), (0, -1)]:
            nr, nc = v[0] + dr, v[1] + dc
            if 0 <= nr < N and 0 <= nc < M and visited[nr][nc] == 0:
                queue.append((nr, nc, cnt))
                visited[nr][nc] = 1
    return lst

T = int(input())
for tc in range(1, T+1):
    N, M = map(int, input().split())
    arr = [0]*N
    for _ in range(N):
        arr[_] = list(map(int, input().split()))
    soil = max(map(max, arr))
    print(soil)
    print(arr)
    start = tuple(map(int, input().split()))
    lst = bloom(start[0], start[1])
    result = [0]*(soil+N+M+1)
    for i in range(1, len(lst)):       # result 이전 값에 lst 현재 인덱스 값 더하면
        result[i] = result[i-1]+lst[i] # 현재 인덱스 날짜에 꽃 몇 송이 피어있는지 알 수 있음
    print(result)
    ans = max(result)
    day = result.index(ans)            # .index 쓰면 자동으로 제일 큰 수 맨 앞에거 잡아줌
    print('#{} {}일 {}개'.format(tc, day, ans))
```

- 꽃이 피고 지는 날을 표시할 lst를 어느 정도의 크기로 잡아야 하는지 감이 잘 안 옴.
- 테스트 케이스 두개는 답이 맞게 나오는데 8개 중 5개 맞고 런타임 에러 > lst 크기 문제는 아니다

```python
# 다시 풀어보기 => pass!!
from collections import deque
import sys
sys.stdin = open("14190_input.txt.txt", 'r')

def bloom(r, c):
    lst = [0]*(soil+N+M+1)
    queue = deque()
    queue.append((r, c, 0))
    visited = [[0]*M for _ in range(N)]
    visited[r][c] = 1
    while queue:
        v = queue.popleft()
        cnt = v[2] + 1
        lst[cnt] += 1
        lst[cnt+arr[v[0]][v[1]]] -= 1

        for dr, dc in [(-1, 0), (0, 1), (1, 0), (0, -1)]:
            nr, nc = v[0] + dr, v[1] + dc
            if 0 <= nr < N and 0 <= nc < M and visited[nr][nc] == 0 \
            and arr[nr][nc] != 0: # 여기 이 조건 추가해서 맞았음! 땅이 비옥하지 않으면 큐X
                queue.append((nr, nc, cnt))
                visited[nr][nc] = 1
    return lst

T = int(input())
for tc in range(1, T+1):
    N, M = map(int, input().split())
    arr = [0]*N
    for _ in range(N):
        arr[_] = list(map(int, input().split()))
    soil = max(map(max, arr))
    start = tuple(map(int, input().split()))
    lst = bloom(start[0], start[1])
    result = [0]*(soil+N+M+1)
    for i in range(1, len(lst)):
        result[i] = result[i-1]+lst[i]
    ans = max(result)
    day = result.index(ans)
    print('#{} {}일 {}개'.format(tc, day, ans))
```





### SWEA 14193 : 통신병 민코씨

```python
from collections import deque
import sys
sys.stdin = open("14193_input.txt", 'r')

def cable_start(start):
    global stoa
    queue = deque()
    queue.append(start)
    visited = [[0]*X for _ in range(Y)]
    visited[start[0]][start[1]] = 1
    cnt = 0
    while queue:
        s = queue.popleft()
        if s == start:
            cnt = 1
        else:
            cnt = visited[s[0]][s[1]] + 1
        for dy, dx in ((-1, 0), (0, 1), (1, 0), (0, -1)):
            ny, nx = s[0] + dy, s[1] + dx
            if 0 <= ny < Y and 0 <= nx < X and visited[ny][nx] == 0 and arr[ny][nx] != '#':
                queue.append((ny, nx))
                visited[ny][nx] = cnt
    for a in range(len(arrives)):
        if visited[arrives[a][0]][arrives[a][1]] != 0:
            stoa[0][a+1] = visited[arrives[a][0]][arrives[a][1]]
            stoa[a+1][0] = visited[arrives[a][0]][arrives[a][1]]
    return

def atoa(point, idx):
    global stoa
    queue = deque()
    queue.append(point)
    visited = [[0] * X for _ in range(Y)]
    visited[point[0]][point[1]] = 1
    cnt = 0
    while queue:
        s = queue.popleft()
        if s == point:
            cnt = 1
        else:
            cnt = visited[s[0]][s[1]] + 1
        for dy, dx in ((-1, 0), (0, 1), (1, 0), (0, -1)):
            ny, nx = s[0] + dy, s[1] + dx
            if 0 <= ny < Y and 0 <= nx < X and visited[ny][nx] == 0 and arr[ny][nx] != '#':
                queue.append((ny, nx))
                visited[ny][nx] = cnt
    for r in range(len(remain)):
        if visited[remain[r][0]][remain[r][1]] != 0:
            stoa[idx+1][r + idx + 2] = visited[remain[r][0]][remain[r][1]]
            stoa[r + idx + 2][idx+1] = visited[remain[r][0]][remain[r][1]]
    return

T = int(input())
for tc in range(1, T+1):
    X, Y = map(int, input().split())
    arr = [0]*Y
    start = 0
    arrives = list()
    for i in range(Y):
        arr[i] = list(input())
        for j in range(X):
            if arr[i][j] == 'S':
                start = (i, j)
            elif arr[i][j] == 'A':
                arrives.append((i, j))
    # print(X, Y)
    # print(arr)
    # print(start)
    print(arrives)
    stoa = [[0]*(len(arrives)+1) for _ in range((len(arrives)+1))]
    cable_start(start)
    # print(stoa)
    for p in range(len(arrives)-1):
        remain = arrives[p+1:]
        # print(arrives[p], remain)
        atoa(arrives[p], p)
    print(stoa)
    included = [0]*(len(arrives)+1)
    total = 0

    for r in range(1, len(arrives)+1):
        if min(stoa[r]) == stoa[r][0]:
            total += stoa[r][0]
            included[r] = 'ok'
            continue
        else:
            link = stoa[r][0]
            for c in range(1, r+1):  # 두개가 전부 s와 연결되었을 때보다 서로를 연결하는 비용이 더 적을 경우?
                if str(c) in included:
                    total += stoa[r][0]
                if min(stoa[r]) == stoa[r][c]:
                    if stoa[c][0] + stoa[r][c] > stoa[r][0]:
                        total += stoa[r][0]
                        included[r] = 'ok'
                    else:
                        total += stoa[r][c]
                        included[r] = str(c)
                    break

    print(included)
    for inc in included:
        if inc != 0 and inc.isdigit():
            total += stoa[int(inc)][0]

    print(total)

```

- s부터 a까지 거리는 다 구했는데 각각의 가장 짧은 거리를 찾는 게 어렵다...
- MST 문제라고 한다 => 프림, 크루스칼 어떤 방법이든 알고 풀어봐야한다!
- 스터디 후기 : 내가 풀어본 방법이 크루스칼 비슷한 방법인 것 같다...
