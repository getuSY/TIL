# 220213_알고리즘 스터디

### 1209. [S/W 문제해결 기본] 2일차 - Sum

```python
import sys
sys.stdin = open("input.txt", 'r')


for tc in range(10):
    T = int(input())
    arr = list()

    for i in range(0, 100):
        numbers = list(map(int, input().split()))
        arr.append(numbers)

    row_list = list()
    col_list = list()
    x1 = 0
    x2 = 0
    total = list()
    max_value = 0

    for row in arr:
        row_sum = 0
        for j in range(0, 100):
            row_sum += row[j]

        row_list.append(row_sum)

    arr_col = list(map(list, zip(*arr)))

    for col in arr_col:
        col_sum = 0
        for k in range(0, 100):
            col_sum += col[k]

        col_list.append(col_sum)

    for x in range(0, 100):
        x1 += arr[x][x]
        x2 += arr[x][99-x]

    total.extend(row_list)
    total.extend(col_list)
    total.append(x1)
    total.append(x2)

    for value in total:
        if max_value <= value:
            max_value = value

    print('#{} {}'.format(T, max_value))

```





### 백준 1244번. 스위치 켜고 끄기

```python
# import sys
# sys.stdin = open("input.txt", 'r')

S = int(input())
initial_state = list(map(int, input().split()))

switches = [0]*S
for s in range(S):
    switches[s] = s+1

states = dict(zip(switches, initial_state))

students = int(input())
for student in range(students):
    switch_num = list(states.keys())
    switch_value = list(states.values())
    number = list(map(int, input().split()))

    if number[0] == 1:
        for num in switch_num:
            if num % number[1] == 0:
                states[num] = 1 - states[num]

    elif number[0] == 2:
        iterate = 0
        if S/2 > number[1]:
            iterate = number[1]
        elif round(S/2) == number[1]:
            iterate = round(S/2)
        elif round(S/2) < number[1]:
            iterate = 2*round(S/2) - number[1]

        for i in range(1, iterate):
            if switch_value[number[1] - 1 - i] == switch_value[number[1] - 1 + i]:
                states[number[1] - i] = 1 - states[number[1] - i]
                states[number[1] + i] = 1 - states[number[1] + i]
            else:
                break

        states[number[1]] = 1 - states[number[1]]



result = list(map(str, states.values()))
enter = S/20

if enter < 1:
    print(' '.join(result))
elif enter > 1:
    print(' '.join(result[0:20]))
    if enter > 2:
        print(' '.join(result[20:40]))
        if enter > 3:
            print(' '.join(result[40:60]))
            if enter > 4:
                print(' '.join(result[60:80]))
                print(' '.join(result[80:S]))
            else:
                print(' '.join(result[60:S]))
        else:
            print(' '.join(result[40:S]))
    else:
        print(' '.join(result[20:S]))


```

