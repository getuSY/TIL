## DB :  SQL & ORM

### Database

- TABLE
  - CREATE
  - DROP



### CREATE

- **INSERT** : 테이블에 단일 행 삽입

  ```sqlite
  INSERT INTO 테이블이름 (컬럼1, 컬럼2, ...) VALUES (값1, 값2, ...);
  ```

  ```sqlite
  sqlite > SELECT * FROM 테이블이름;
  ```

  모든 열에 입력할 데이터 값이 있는 경우 컬럼 명시를 하지 않아도 됨.

  ```sqlite
  INSERT INTO <테이블이름> VALUES (모든 값);
  ```

  (SQL Run Query로 전체 실행하지 말고 selected query로 부분 실행..? )

- SQLite는 따로 기본 키 속성의 컬럼을 작성하지 않으면 값이 자동으로 증가하는 PK옵션을 가진 rowid 컬럼을 정의한다.

- NULL : 주소가 필요한 정보라면 주소를 NULL로 두면 안 된다. 

- **NOT NULL** : 스키마를 작성할 때 (컬럼 정보에?) NOT NULL이라는 정보를 입력해준다.

  ```sqlite
  sqlite> DROP TABLE classmates;
  sqlite> CREATE TABLE classmates ()
  ...> id INTEGER PRIMARY KEY,
  ...> name TEXT NOT NULL,
  ...> age INT NOT NULL,
  ...> address TEXT NOT NULL
  ...> );
  ```

  PK 선언 시에는 무조건 "INTEGER" 써야 함!

  다른 정수형 변수에서는 INT라고 쓸 수 있지만 PK는 그 사용이 불가하다.

- 스키마에 ID를 직접 작성한 경우 컬럼 값 입력 시 ID를 입력하지 않으면 에러.

  - ID를 포함한 모든 VALUE를 작성한다.
  - ID를 제외한 모든 컬럼이름을 (명시적으로) 작성하고, 그에 맞는 나머지 값을 작성.

- 여러 정보를 입력할 경우 :  VALUES에 들어갈 값들을 묶어 한 번에 작성!

  ```sqlite
  INSERT INTO <테이블이름> VALUES
  (값1, 값2, 값3,...),
  (값1, 값2, 값3,...),
  (값1, 값2, 값3,...),
  (값1, 값2, 값3,...),
  (값1, 값2, 값3,...);
  ```

- ID 지정을 하지 않은 상태에서 ID까지 같이 보려면

  ```
  SELECT rowid, * FROM <테이블이름>
  ```



### READ - 조회가 가장 중요하다!

- **SELECT** : 테이블에서 데이터를 조회, SQLite에서 가장 복잡, 다양한 절과 함께 사용.

- SELECT와 함께 사용하는 clause

  - **LIMIT** : 쿼리에서 반환되는 행 수를 제한, 특정 행부터 시작해서 조회하기 위한 OFFSET 과 함께 사용하기도 함
  - **WHERE** : (IF문과 비슷하게 생각?) 쿼리에서 반환된 행에 대한 특정 검색 조건을 지정.
  - **SELECT DISTINCT** : 조회 결과에서 중복을 제거. DISTINCT는 SELECT 바로 뒤에 작성한다.

- 특정 테이블에서 두 개 이상 속성 값을 받아오되, 한 개만 조회하고 싶다

  ```sqlite
  SELECT rowid, name FROM <테이블이름> LIMIT 1;
  ```

- 특정 부분에서 원하는 수만큼 데이터 조회하기 (EX) : 숫자1 행부터 숫자2 개만 조회하기

  ```sqlite
  SELECT 컬럼1, 컬럼2, ... FROM <테이블이름> LIMIT 숫자2 OFFSET 숫자1;
  ```

- **OFFEST** : 동일 오브젝트 안에서 오브젝트 처음부터 주어진 요소나 지점까지의 변위차를 나타내는 정수형.(0부터 시작한다...?)

- WHERE 활용

  ```sqlite
  SELECT 컬럼1, 컬럼2,... FROM <테이블이름> WHERE 조건(ex: address="서울");
  ```

- DISTINCT 활용 : 테이블에서 컬럼1을 중복없이 가져오겠다

  ```sqlite
  SELECT DISTINCT 컬럼1 FROM <테이블이름>
  ```



### DELETE

- DELETE : 테이블에서 행을 제거 > 중복 불가능한 값인 rowid를 기준으로 삭제!

  ```sqlite
  DELETE FROM 테이블이름 WHERE 조건;
  ```

  rowid 값이 지워진 이후에는 그 값을 재활용한다.

- AUTOINCREMENT : SQLite가 사용되지 않은 값이나 이전에 삭제된 행의 값을 재사용하는 것을 방지. (장고에서는 PK에 이 속성을 붙여 사용한다, SQLite의 기본 속성은 재사용하는 것!)

  ```sqlite
  CREATE TABLE 테이블이름(
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  ...
  );
  ```



### UPDATE

- UPDATE : 기존 행의 데이터를 수정 > rowid 기준!

  - SET : 테이블의 각 열에 대해 새로운 값을 설정

  ```sqlite
  UPDATE <테이블이름> SET 컬럼1=값1, 컬럼2=값2,... WHERE 조건;
  ```





### CRUD 정리

- INSERT

```sql
INSERT INTO 테이블이름 () VALUES (값1, 값2,...);
```

- SELECT

  ```sqlite
  SELECT * FROM 테이블이름 WHERE 조건;
  ```

- UPDATE

  ```sqlite
  UPDATE 테이블이름 SET 칼럼1=값1, 칼럼1=값2, ... WHERE 조건;
  ```

- DELETE

```sqlite
DELETE FROM 테이블이름 WHERE 조건;
```



### WHERE





### LIKE



