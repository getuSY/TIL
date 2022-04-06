## 220403_Algorithm

### 백준 15685 : 드래곤 커브

```python
def gen_0(arr, x, y, d):
    arr[y][x] += 1
    if d == 0:
        arr[y][x+1] += 1
    elif d == 1:
        arr[y-1][x] += 1
    elif d == 2:
        arr[y][x-1] += 1
    elif d == 3:
        arr[y+1][x] += 1
    return


def generation(x, y, g, stack):
    global arr
    temp = list()
    if g == 0:
        # tmp = [[0] * 100 for _ in range(100)]
        # if 0 <= x < 100 and 0 <= y < 100:
        arr[y][x] += 1
        for k in range(len(stack)-1, -1, -1):
            if stack[k] % 4 == 0:
                if 0 <= x + 1 < 100:
                    arr[y][x+1] += 1
                    x = x + 1
            elif stack[k] % 4 == 1:
                if 0 <= y - 1 < 100:
                    arr[y - 1][x] += 1
                    y = y - 1
            elif stack[k] % 4 == 2:
                if 0 <= x - 1 < 100:
                    arr[y][x - 1] += 1
                    x = x - 1
            elif stack[k] % 4 == 3:
                if 0 <= y + 1 < 100:
                    arr[y + 1][x] += 1
                    y = y + 1
        return
    else:
        for s in range(len(stack)-1, -1, -1):
            plus = stack[s] + 1
            # if plus == 4: # 이거 안 해도 상관 없음 > plus % 4 로 디렉션 판별 가능할 듯
            #     plus = 0
            temp.append(plus)
        temp.extend(stack)
        stack = temp[:]
        generation(x, y, g-1, stack)


N = int(input())
arr = [[0]*100 for _ in range(100)]
curves = list()
for n in range(N):
    tmp = tuple(map(int, input().split()))
    curves.append(tmp)

for curve in curves:
    x, y, d, g = curve[0], curve[1], curve[2], curve[3]
    if g == 0:
        gen_0(arr, x, y, d)
    else:
        stack = [d]
        generation(x, y, g, stack)
# print(arr)
# 결과 값 구하는 식
# 네 꼭짓점이 모두 드래곤 커브의 일부인 사각형 구하기
cnt = 0
for r in range(99):
    for c in range(99):
        # if 0 <= r + 1 < N and 0 <= c + 1 < N:
        if arr[r][c] != 0 and arr[r+1][c] != 0 and arr[r][c+1] != 0 and arr[r+1][c+1] != 0:
            cnt += 1
print(cnt)
```

- 테스트 케이스는 다 맞는데 제출하면 '틀렸습니다' 뜬다.
- 어디를 고쳐야할지 찾아봐야 할 듯 하다.
- **문제점!!!** 문제에 0 <= x <= 100, 0 <= y <= 100이라고 적혀있었는데 이걸 잘 안 읽어서 범위 설정이 잘못 되었던 거였음!!

```python
# 수정 완료
def gen_0(arr, x, y, d):
    arr[y][x] += 1
    if d == 0:
        arr[y][x+1] += 1
    elif d == 1:
        arr[y-1][x] += 1
    elif d == 2:
        arr[y][x-1] += 1
    elif d == 3:
        arr[y+1][x] += 1
    return


def generation(x, y, g, stack):
    global arr
    temp = list()
    if g == 0:
        arr[y][x] += 1
        for k in range(len(stack)-1, -1, -1):
            if stack[k] % 4 == 0:
                if 0 <= x + 1 <= 100:
                    arr[y][x+1] += 1
                    x = x + 1
            elif stack[k] % 4 == 1:
                if 0 <= y - 1 <= 100:
                    arr[y - 1][x] += 1
                    y = y - 1
            elif stack[k] % 4 == 2:
                if 0 <= x - 1 <= 100:
                    arr[y][x - 1] += 1
                    x = x - 1
            elif stack[k] % 4 == 3:
                if 0 <= y + 1 <= 100:
                    arr[y + 1][x] += 1
                    y = y + 1
        return
    else:
        for s in range(len(stack)-1, -1, -1):
            plus = stack[s] + 1
            temp.append(plus)
        temp.extend(stack)
        stack = temp[:]
        generation(x, y, g-1, stack)


N = int(input())
arr = [[0]*101 for _ in range(101)]
curves = list()
for n in range(N):
    tmp = tuple(map(int, input().split()))
    curves.append(tmp)

for curve in curves:
    x, y, d, g = curve[0], curve[1], curve[2], curve[3]
    if g == 0:
        gen_0(arr, x, y, d)
    else:
        stack = [d]
        generation(x, y, g, stack)
cnt = 0
for r in range(100):
    for c in range(100):
        if arr[r][c] != 0 and arr[r+1][c] != 0 and arr[r][c+1] != 0 and arr[r+1][c+1] != 0:
            cnt += 1
print(cnt)
```

