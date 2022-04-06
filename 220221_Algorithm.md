# 220221_알고리즘 스터디 _2회차

### 11315 : 오목 판정

```python
import sys
sys.stdin = open("input.txt", 'r')

T = int(input())

for tc in range(1, T+1):
    N = int(input())
    strings = [str()*N]
    for n in range(N):
        strings[n] = input()
    result = 'YES'
        
    # 가로에서 5개 이상 연속되는지 검사하기
    for i in range(N-4):
        for j in range(N-4):
            for k in range(5):
                if strings[i][j+k] == 'o':
                    result = 'YES'
                    continue
                else:
                    break
            if result = 'YES':
                break
                    
                
    # 세로에서 5개 이상
    # 대각선으로 5개 이상 연속되는지 검사하기
    
    
    
    print('#{} {}'.format(tc, result))
```

스도쿠랑 비슷한 문제인 것 같다. 다시 풀어보기...



### 3499 : 퍼펙트 셔플

```python
import sys

sys.stdin = open("3499_input.txt", 'r')

# 같은 과정을 함수로 만들어보려고 했는데 fail이 떠서 보류 중...
def PerfectShuffle(card, N):
    half = round(N/2)
    shuffled = list()

    left = card[0:half]
    right = card[half::]
    print(left)
    print(right)

    for i in range(half):
        shuffled.append(left[i])
        shuffled.append(right[i])

    if N % 2 == 1:
        shuffled.append(left[-1])

    return shuffled


T = int(input())

for tc in range(1, T + 1):
    N = int(input())
    cards = input().split()
    shuffled = list()

    # shuffled = PerfectShuffle(cards, N)  #함수 써볼 때 코드

    half = N//2

    # 반까지는 왼쪽, 반부터 끝까지는 오른쪽으로 분류
    if N % 2 == 0:
        left = cards[0:half]
        right = cards[half::]

        for i in range(half):
            shuffled.append(left[i])
            shuffled.append(right[i])


    elif N % 2 == 1:
        left = cards[0:half+1]
        right = cards[half+1::]

        # 왼쪽이 먼저 들어가고 왼쪽 마지막 하나를 제외하고는 똑같은 과정을 거친다
        for j in range(half):
            shuffled.append(left[j])
            shuffled.append(right[j])

        # 홀수일 경우 하나 더 많이 왼쪽으로 들어가기 때문에 남은 하나를 마지막 셔플로 추가
        shuffled.append(left[-1])

    print('#{}'.format(tc), end=' ')
    for k in range(N):
        if k == N - 1:
            print(shuffled[k])
        else:
            print(shuffled[k], end=' ')

```





### 1860 : 진기의 최고급 붕어빵

```python
import sys
sys.stdin = open("input.txt", 'r')

T = int(input())

for tc in range(1, T+1):
    N, M, K = map(int, input().split())
    arrive = list(map(int, input().split()))
    result = str()
    
    times = 0
    md = 0
    
    # 첫번째 도착 손님 찾기
    first = arrive[0]
    for ar in arrive:
        if ar < first:
            first = ar
            
    # 손님 도착 시간 알기 위해 도착 시간 오름차순으로 정렬하기 > 정렬하고 나면 first는 그냥 0항.
    
    
    # 제일 처음 만들기 전에 손님이 도착하면 실패
    if first < M:
        result = 'Impossible'
    
    i = 0
    # 그 다음 손님이 오기 전에 붕어빵이 만들어지면 성공
    while M >= first and i <= N-1:
        times += 1
        if times % M == 0:
            md += K
        if times == arrive[i]:
            if md >= 1:
                md -= 1
                i += 1
                result = 'Possible'
            else:
                result = 'Impossible'
                break
        
    
    # 출력
    print('#{} {}'.format(tc, result))
    
    
```

