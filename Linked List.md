## Linked List

리스트 : 순서를 가진 데이터의 묶음. 시퀀스 자료형. 크기 제한, 타입 제한 없음. 동적 배열로 작성된 순차 리스트.



### 연결 리스트

**리스트의 단점을 보완한 자료 구조**

1. 자료의 논리적인 순서와 메모리 상의 물리적인 순서가 일치하지 않고, 개별적으로 위치하고 있는 원소의 주소를 연결하여 하나의 전체적인 자료 구조를 이훔
2. 링크를 통해 원소에 접근하므로, 순차 리스트에서 물리적인 순서를 맞추기 위한 작업이 필요하지 않음.
3. 자료 구조의 크기를 동적으로 조정할 수 있어 메모리의 효율적인 사용이 가능.
4. 순차 탐색을 해야 함.



**노드** : 연결 리스트에서 하나의 원소에 필요한 데이터를 갖고 있는 자료 단위

- 데이터 필드 : 원소의 값을 저장하는 자료 구조
- 링크 필드 : 다음 노드의 주소를 저장하는 자료 구조

**헤드** : 리스트의 처음 노드를 가리키는 링크 필드만 가지고 있어 데이터가 저장되지 않는다.



### 함수

- addtoFirst() : 연결 리스트의 앞쪽에 원소를 추가
- addtoLast() : 연결 리스트의 뒤쪽에 원소를 추가
- add() : 연결 리스트의 특정 위치에 원소를 추가
- delete() : 연결 리스트의 특정 위치에 있는 원소를 삭제
- get() : 연결 리스트의 특정 위치에 있는 원소를 리턴



### 단순 연결 리스트

노드가 하나의 링크 필드에 의해 다음 노드와 연결되는 구조

헤드가 가장 앞의 노드를 가리키고, 각 노드의 링크 필드가 연속적으로 다음 노드를 가리킴.

최종적으로 None을 가리키는 노드가 리스트의 가장 마지막 노드이다.



#### 단순 연결 리스트 삽입

```python
# 첫 노드 삽입 알고리즘
def addtoFirst(data):    # 첫 노드에 데이터 삽입
	global head
	Head = Node(data, Head)  # 새로운 노드 생성
```

```python
# 가운데 노드 삽입 알고리즘
def add(pre, data):   # pre 다음에 데이터 삽입
    if pre == None:
        print('error')
    else:
        pre.link = Node(data, pre.link)
```

```python
# 마지막 노드 삽입 알고리즘
def addtoLast(data):   # 마지막에 데이터 삽입
    global Head
    if Head == None:   # 빈 리스트일 때
        Head = Node(data, None)
    else:
        p = Head
        while p.link != None:  # 마지막 노드를 찾을 때까지
            p = p.link
        p.link = Node(data, None)
```



#### 단순 연결 리스트 삭제

선행 노드에 대상의 링크 필드를 복사하는 것만으로, 대상을 가리키는 노드가 없기 때문에 삭제한 것과 같은 결과를 볼 수 있다.

```python
# 첫번째 노드 삭제 알고리즘
def deletetoFirst():   # 첫 노드 삭제
    global Head
    if Head == None:
        print('error')
    else:
        Head = Head.link
```

```python
# 노드 삭제 알고리즘
def delete(pre):   # pre 다음 노드 삭제
    if pre == None or pre.link == Node:
        print('error')
    else:
        pre.link = pre.link.link
```



### 이중 연결 리스트

단순 연결 리스트의 경우 한 번 링크를 따라가면 선행 링크로 돌아가기 힘들다.

=> 한 번 대상을 놓치면 다시 head부터 순회해야 함 => 단점 보완을 위한 이중 연결 리스트!

양쪽 방향으로 순회할 수 있도록 노드를 연결, 두 개의 링크 필드와 한 개의 데이터 필드.



#### 이중 연결 리스트 삽입

cur 다음에 new를 삽입하는 과정 : 4개의 링크 연산 필요

1. 새 노드 new를 생성하고 데이터 필드에 값 저장

2. cur의 next를 new의 next에 저장하여 cur의 다음 노드를 삽입할 노드의 다음 노드로 연결

3. new의 위치(메모리 주소)를 cur의 next에 저장하여 삽입할 노드를 cur 노드의 다음 노드로 연결

4. cur의 위치(값, 메모리 주소)를 new의 prev 필드에 저장하여 cur을 new의 이전 노드로 연결

5. new의 위치(메모리 주소)를 삽입하려는 위치 다음 노드의 prev에 저장하여 다음 노드와 연결

   (new의 위치를 다음 노드의 prev에 넣어도 되고, cur의 next 값을 다음 노드의 prev에 넣어도 된다?)



#### 이중 연결 리스트 삭제

cur 노드를 삭제하는 과정

1. cur 다음 노드의 주소(위치, 값)를 이전 노드의 next 저장하여 링크를 연결
2. cur 다음 노드의 prev 필드에 cur 이전 노드의 주소(위치, 값)을 저장하여 링크를 연결
3. cur이 가리키는 노드에 할당된 메모리를 반환



### STACK으로써 활용

push : 리스트 마지막에 노드 삽입

pop : 리스트의 마지막 노드 반환 및 삭제

top : 연결 리스트의 마지막 노드를 가리키는 변수

```python
def push(i):             # 원소 i를 스택 top 위치에 push
    global top
    top = Node(i, top)   # 새 노드 생성
    
def pop():
    global top
    if top == None:      # top이 빈 리스트를 가리키면
        print('error')
    else:
        data = top.data
        top = top.link    # top이 가리키는 노드를 바꾼다
        return data
```



### 우선 순위 큐로써의 활용

연결 리스트를 이용한 우선 순위 큐 구현 가능.

기본 연산 : enQueue, deQueue

순차 리스트를 이용해 자료를 저장하고, 원소를 삽입하는 과정에서 우선순위를 비교하여 적절한 위치에 삽입. 가장 앞에 최고 우선순위의 원소가 위치하게 된다.



**장점 : **삽입, 삭제 연산 이후 원소의 재배치가 필요 없음. 메모리의 효율적 사용 가능.

**단점 : **삽입이나 삭제 연산 시 원소의 배치가 일어나므로 이에 소요되는 시간, 메모리 낭비가 크다.



### Linked List 구현

튜플이나 다른 형태로 구현하는 것이 아니라 class 정의, 혹은 내장 라이브러리를 통해 구현할 수 있을 것 같다.

1. class Node, class LinkedList 만든 후 def 작성

   ```python
   class Node:
       def __init__(self, data, next=None):
           self.data = data
           self.next = None
           
       def __repr__(self):     # data가 그냥 숫자인 경우 이거 안 먹힌다!
           return self.data    # 이 경우 str(self.data) 리턴해주면 보임!
           
   class LinkedList:
       def __init__(self):
           self.head = None
           # ?
           if nodes in not None:
               node = Node(data=nodes.pop(0))
               self.head = node
               for elem in nodes:
                   node.next = Node(data=elem)
                   node = node.next
           
       def __repr__(self):
           node = self.head
           nodes = []
           while node in not None:
               nodes.append(node.data)
               node = node.next
           nodes.append("None")
           return " -> ".join(nodes)
       
       def __iter__(self):       # traverse
           node = self.head
           while node is not None:
               yield node        # yield 사용?? 무슨 뜻
               node = node.next
               
       def add_first(self, node):# 리스트 처음, head로 넣기 => head 바꾸기
           node.next = self.head
           self.head = node
           
       def add_last(self, node):
           if self.head is Node:
               self.head = node
               return
           for current_node in self:
               pass
           current_node.next = node
           
       def add_after(self, target_data, newnode):
           if self.head in None:
               raise Exception("List is empty")
               
           for node in self:
               if node.data == target_data:
                   newnode.next = node.next
                   node.next = newnode
                   return
           raise Exception("Node with data not found")
           
       def add_before(self, target_data, newnode):
           if self.head is None:
               raise Exception("List is empty")
               
           if self.head.data == target_deta:
               return self.add_first(newnode)
           
           prev = self.head
           for node in self:
               if node.data == target_data:
                   prev.next = newnode
                   newnode.next = node
                   return
               prev = node
           raise Exception("Node with data not found")
           
       def remove(self, target_data):
           if self.head is None:
               raise Exception("List is empty")
               
           if self.head.data == target_data:
               self.head = self.head.next
               return
           
           prev = self.head
           for node in self:
               if node.data == target_data:
                   prev.next = node.next
                   return
               prec = node
           raise Exception("Node with data not found")
               
    # ----------------------------------------------------------------
           
       def list_print(self):
           print_node = self.head
           while print_node is not None:
               print(print_node.data)
               print_node = print_node.next
               
       def insert_head(self, data):
           new = Node(data)
           new.next = self.head
           self.head = new
           
       def insert_end(self, data):
           new = Node(data)
           if self.head is None:
               self.head = new
               return
           last = self.head
           while last.next:
               last = last.next
           last.next = new
           
       def insert_between(self, middle, data):  # middle 자리 뒤에 노드 추가
           if middle is None:  # 이건 어디 값이 None이라는 건지 모르겠다
               print('error')
               return
           new = Node(data)
           new.next = middle.next
           middle.next = new
           
       def delete(self, target):
           Head = self.head
           
           if Head is not None:
               if Head.data == target:
                   self.head = Head.next
                   Head = None
                   return
           while Head is not None:
           	if Head.data == tartget:
                   break
            	prev = Head
            	Head = Head.next
               
           if Head == None:
               return
           
           prev.next = Head.next
           Head = None
   
   # ---------------------------------------------------------------------
   
   lst = LinkedList()
   lst.head = Node(value1)
   node2 = Node(value2)
   node3 = Node(value3)
   
   lst.head.next = node2
   node2.next = node3
   ...
   
   list.list_print()
   ```

   

2. llist 라이브러리 사용? => **but** 외부 라이브러리이기 때문에 사용은 지양해야할 것 같다.

   ```bash
   $ pip install llist
   ```

3. LinkedList로 우선순위 큐로의 활용이 가능한 만큼 deque()를 사용하여 구현할 수 있을 것 같다.