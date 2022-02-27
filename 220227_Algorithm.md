## 220227_ 알고리즘 스터디3

### 백준_2116 : 주사위 쌓기

```python
N = int(input())
# N개의 주사위들을 딕셔너리로 구현하여 주사위들을 모아둔 리스트에 저장
dices = [0]*N
for n in range(N):
    dice = dict()
    tmp = list(map(int, input().split()))
    dice[tmp[0]] = tmp[-1]
    dice[tmp[1]] = tmp[3]
    dice[tmp[2]] = tmp[4]
    dice[tmp[3]] = tmp[1]
    dice[tmp[4]] = tmp[2]
    dice[tmp[5]] = tmp[0]
    dices[n] = dice
# 주사위를 줄지어 세웠을 때 꼭대기 값에 따라 옆면 합의 최댓값을을 저장할 리스트 생성
total_list = list()

for i in range(1, 7):
    total = 0
    for j in range(N):
        # 맨 첫 주사위의 경우 맨 윗면을 1~6 사이 아무렇게나 설정한다
        if j == 0:
            top = i
            # 만약 윗면이 6이거나 6의 반대면이 아니라면 6이 최대값이므로 total에 더함
            if top != 6 and dices[j].get(i) != 6:
                total += 6
            # 만약 6과 5가 마주보고 있고, 둘 중 하나가 윗면이라면 최대값은 4이므로 4를 더함
            elif i == 5 and dices[j].get(i) == 6 or top == 6 and dices[j].get(i) == 5:
                total += 4
            # 이 경우가 아니라면 최대값은 5이므로 5를 더함
            else:
                total += 5
        # 두번째 주사위부터는 그 아래 주사위의 윗면에 따라 현재 주사위의 윗면이 정해진다
        elif j > 0:
            # 아래 주사위의 윗면의 반대면을 현재 주사위의 윗면으로 설정. 아래 동일
            top = dices[j].get(top)
            if top != 6 and dices[j].get(top) != 6:
                total += 6
            elif top == 5 and dices[j].get(top) == 6 or top == 6 and dices[j].get(top) == 5:
                total += 4
            else:
                total += 5
    # 각 옆면 합의 최댓값들을 리스트에 추가
    total_list.append(total)
# 그 중 최대값을 출력
print(max(total_list))

```





### 백준_2304 : 창고 다각형

```python
# 입력 예시

# 7
# 2 4
# 11 4
# 15 8
# 4 6
# 5 3
# 8 10
# 13 6

N = int(input())
# 기둥들을 리스트로 받아온다
sticks = [0]*N
for _ in range(N):
    sticks[_] = list(map(int, input().split()))
# 기둥 리스트를 정렬하여 맨 앞 기둥의 왼쪽면을 left로, 마지막 기둥의 왼쪽면을 right로 함
sticks.sort()
left = sticks[0][0]
right = sticks[-1][0]
# 가장 높이가 높은 기둥 중 첫번째를 찾아 그 높이를 highest, 인덱스를 idx, 기둥의 위치를 location에 저장
highest = 0
idx = 0
location = 0
for x in range(N):
    if highest < sticks[x][1]:
        highest = sticks[x][1]
        idx = x
        location = sticks[x][0]
# 창고 다각형의 넓이를 저장할 변수 설정
result = 0
# 만약 가장 왼쪽 기둥이 가장 높다면, 창고 다각형은 아래로 작아지는 계단 구조일 것
if highest == sticks[0][1]:
    stack = [0] * N
    top = -1
    for i in sticks[::-1]:
        if top == -1:
            top += 1
            stack[top] = i
        elif stack[top][1] < i[1]:
            top += 1
            stack[top] = i
    for j in range(top):
        result += stack[j][1]*(stack[j][0] - stack[j+1][0])
    result += highest*(stack[-1][0] - left + 1)
    print(result)
# 만약 가장 높은 기둥이 어느 순서 중간에 존재한다면 왼쪽과 오른쪽을 분리해서 구한다
elif left < location < right:
    stack_left = [0]*N
    stack_right = [0]*N
    top_l = -1
    top_r = -1
    # 가장 높은 첫번째 기둥 전까지를 left side, 그 이후부터 끝까지를 right side로 슬라이싱
    left_side = sticks[:idx+1]
    right_side = sticks[N-1:idx-1:-1]
    # 점점 커지는 계단 형식이므로 높이가 높아질수록 스택에 쌓는다
    # 면적 계산은 다름 스택에 있는 기둥 왼쪽면에서 본인의 왼쪽면을 뺀 길이와 높이를 곱해 더함
    for a in left_side:
        if top_l == -1:
            top_l += 1
            stack_left[top_l] = a
        elif stack_left[top_l][1] < a[1]:
            top_l += 1
            stack_left[top_l] = a
    for x in range(top_l):
        result += stack_left[x][1] * (stack_left[x + 1][0] - stack_left[x][0])
	# 가장 끝 기둥부터 가장 높은 첫번째 기둥까지 점점 낮아지는 계단 구조
    # 끝에서부터 스택을 쌓기 시작해 높이가 높아지면 스택에 추가한다
    for b in right_side:
        if top_r == -1:
            top_r += 1
            stack_right[top_r] = b
        elif stack_right[top_r][1] < b[1]:
            top_r += 1
            stack_right[top_r] = b
    # left side와 동일한 면적 계산
    for y in range(top_r):
        result += stack_right[y][1] * (stack_right[y][0] - stack_right[y + 1][0])
	# 만약 가장 높은 높이의 기둥이 여러개 있다면 right side에 다 포함되어 있으므로
    # 스택 가장 마지막 기둥의 왼쪽면에서 가장 첫번째로 높은 기둥의 왼쪽면을 빼고 기둥 너비 1을 더함
    result += highest * (stack_right[top_r][0] - location + 1)
    print(result)
# 만약 가장 오른쪽 기둥이 가장 높다면, 창고 다각형은 오른쪽으로 커지는 계단 구조일 것
elif highest == sticks[-1][1]:
    stack = [0] * N
    top = -1
    for i in sticks:
        if top == -1:
            top += 1
            stack[top] = i
        elif stack[top][1] < i[1]:
            top += 1
            stack[top] = i
    for j in range(top):
        result += stack[j][1] * (stack[j + 1][0] - stack[j][0])
    result += highest * (right + 1 - stack[-1][0])
    print(result)

```





### 백준_2309 : 일곱 난쟁이

```python
tall = [0]*9
for _ in range(9):
    tall[_] = int(input())

# stack으로 풀 수 있을 것 같다. 비트 연산이나 부분 집합으로도 풀 수 있을 것 같다.
# 총합이 구해졌으니까 그 차이로 풀어보자.
total = sum(tall)
difference = total - 100
# 9명 중 2명만 빼면 되니까 100을 초과하는 나이의 두명만 세서 뺀다
diff = [0, 0]
for i in range(8):
    if tall[i] + max(tall) >= difference:
        diff[0] = tall[i]
        for j in range(i+1, 9):
            diff[1] = tall[j]
            if sum(diff) == difference:
                break
    if sum(diff) == difference:
        break
# 난쟁이 키 중에서 총합을 넘게 하는 키의 두명을 뺀 뒤 정렬해서 출력한다
tall.remove(diff[0])
tall.remove(diff[1])
tall.sort()

for k in range(7):
    print(tall[k])
```





### 백준 2477 : 참외밭

```python
K = int(input())
lines = [0]*6
for i in range(6):
    lines[i] = list(map(int, input().split()))
# 가로 방향으로 진행하는 1, 2를 모아 동서 방향만의 리스트에 저장
ew = list()
# 세로 방향으로 진행하는 3, 4를 모아 남북 방향의 리스트에 저장
ns = list()
for j in lines:
    if j[0] == 1 or j[0] == 2:
        ew.append(j[1])
    elif j[0] == 3 or j[0] == 4:
        ns.append(j[1])
ew.sort()
ns.sort()
# 정렬 후 동서 리스트에서 가장 큰 값이 큰 사각형의 너비가 되고, 남북 리스트 최대값이 높이가 된다
width = ew[-1]
height = ns[-1]
# 인덱스 에러를 방지하고 앞뒤 비교를 위해 리스트에 가장 끝값과 첫 값을 각각 반대 위치에 추가해준다
tmp = lines[0]
lines.insert(0, lines[-1])
lines.append(tmp)
# part1, part2는 큰 사각형에서 뺄 사각형의 너비와 높이가 될 것
part1 = part2 = 0
# 어떤 길이 진행 앞뒤로 같은 방향으로 길이를 재게 된다면, 그 부분이 작은 사각형 부분이 된다
for k in range(1, 7):
    if lines[k-1][0] == lines[k+1][0]:
        part1 = lines[k][1]
        for l in range(k+1, 7):
            if lines[l - 1][0] == lines[l + 1][0]:
                part2 = lines[l][1]
        break
# 참외밭의 넓이는 큰 사각형에서 작은 사각형을 빼는 방식으로 계산
area = width*height - part1*part2
# 참외밭의 넓이에 단위 면적당 수확할 수 있는 참외의 갯수를 곱함
result = area*K
print(result)
```

