## Stack

### 개념

- 선형 구조 (반대 :  트리)
- LIFO 후입선출 구조





### 구현

- 자료를 선형으로 저장할 곳이 필요함
- top : 마지막 삽입된 원소의 위치
- 연산
  - 삽입 : push
  - 삭제 : pop
  - isEmpty : 공백 확인
  - peek : top 반환





### 재귀 호출

- 자기 자신을 호출하여 순환 수행되는 것
- 재귀 호출이 중복적으로 발생할 때 > 메모이제이션
- Memoization(메모이제이션) :  컴퓨터 프로그램을 실행할 때 이전에 계사한 값을 메모리에 저장해 매번 계산하지 않도록 하여 전체적인 실행 속도를 빠르게 한다 > 동적계획법DP의 핵심



### DFS : 깊이 우선 탐색





### 백트래킹

- 해를 찾는 도중에 막히면 되돌아가서 다시 해를 찾는 방법
- 