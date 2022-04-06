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

def sepo(r, c, value):
    global cnt
    if 0 <= r < N and 0 <= c < M and arr[r][c] == value:
        if (r, c) not in visited:
            visited.append((r, c))
            cnt += 1
            # sepo(r - 1, c, value)
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
# 새로 풀어보기

```

- BFS 방식 기본 문제 풀이 방식 + a
