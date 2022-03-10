## Django : Model

### 1. Model

장고에서 필요한 모든 걸 모델 안에 담아두고 찾아간다.

- 단일한 데이터에 대한 정보를 가짐



### 2. Database (DB)

- 크게 보면 데이터 베이스 안에 모델이 있는 것.

- 데이터베이스(DB)

- 쿼리 : 쿼리를 활용해 내가 원하는 데이터를 가져올 수 있다.

  데이터를 조회하기 위한 명령여 >  보통 q라고 쓰지만 각각 다르게 쓰는 경우도 있다.

- 기본 구조

  - 스키마 : 데이터 뼈대, 데이터베이스에서 자료의 구조, 표현 방법, 관계 등을 정의한 구조.
  - table 
  - PK(기본키) : 기본으로 정해진다. > 따로 바꿀 수는 없는가?



### 3. ORM

```git
$ python -m venv venv
$ source venv/Scripts/activate
$ django-admin startproject <project>
$ python manage.py startapp <articles>
> settings.py 앱 등록
> articels/models.py
> class 만들어주기
$ python manage.py makemigrations
$ python manage.py migrate
$ python manage.py sqlmigrate <앱 이름> <table번호> (+BEGIN)??
$ python manage.py showmigrations > 마이그레이션 됐는지 확인
> 모델 새로 만들거나, 업데이트한 사항이 있다...? models.py에 추가 작성
$ python manage.py makemigrations
You are trying to add the field 'created_at' with 'auto_now_add=True' to article without a default; the database needs something to populate existing rows.

 1) Provide a one-off default now (will be set on all existing rows) 
 2) Quit, and let me add a default in models.py
Select an option: (1 : default 값으로 설정)
> 이후 sqlmigrate~show 반복
```

```
auto_now_add : 생성 일자
auto_now : 최종 수정 일자
```



### 4. DB API

- class name. manager. queryset API

```
Article.object.all()
```

- more powerful interactive shell을 위한 라이브러리 설치

```
$ pip install ipython
$ pip install django-extensions
> 이후 settings.py 가서 installed app에 django_extensions 추가
```

- shell_plus 하고 객체 속성 지정해준 다음엔 꼭 .save() 해야 반영된다!



### 5. CRUD

**CREATE**

- 새 객체 생성하는 방법 세 가지 : 클래스, 속성 지정, 속성을 디폴트로 지정, 한번에 생성

```
shell_plus
article = Article()
article.<속성> = ''
or 
article = Article(속성 = '',속성='')
article.pk > 기본키 갯수?
Article.objects.create(attribues='', attribute='')
```

- <class name>.<objects>.create(attribues='', attribute='')는 save() 과정이 필요 없다!
- 덮어쓰기 안 하는 방법은 무엇인가...?
- models.py에 __str__ 지정하면 self.title 등으로 키값 대신 보여준다



**READ**

- all() 
- get() : 딕셔너리 .get과 다르다. 없으면 에러!
- filter() : 딕셔너리 .get()과 비슷하다. 조건이 맞으면 여러 개 가져올 수도 있고 없을 경우 에러 나지 않는다. 그냥 아무것도 없다는 표시가 뜸.



**UPDATE**

- .속성 = '' 으로 변경 후 .save() 잊지말것!
- delete()
- 변수 할당 없이 직접적으로 삭제는 불가능한가? 여러 개 삭제는...?



### 6. Admin

관리자 superuser

- admin.site.register() > 모델에 등록한 테이블을 올려준다
- .ModelAdmin.actions : https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#modeladmin-options



### 7. 실습

- templates 만들고 base.html 만든 후 settings에 등록
- 프로젝트 내 urls.py에 path 등록 후 앱 내 urls.py 만들기
- 앱 내 urls.py 만들기
- views def 함수 정의 후 그에 맞는 앱 내(app/templates/app/--.html) html 생성
- path >  namespace는 무엇이고 왜 정의하는가? > 별명?



**READ 구현**

- views > from .models import <model 내 class>
- views 내 함수 내 변수 및 context 정의
- 각 views 함수 작성 및 그에 따른 html 작성
- 장고에서 하이퍼링크를 사용할 때에는 namespace를 사용하자.
  - {% url  "app name:namespace"% }
- 이후 추가하고 싶다면 urls path 설정 >  views 설정 > html 설정



**CREATE 구현**

request가 중요해지는 것!

- request.GET() : 일단 묻고 따짐 없이 조건 없이 그냥 가져올 때?
  - .GET() : 데이터 전체?
  - .get() :  받아온 데이터에서 찾기?
- 웹에서 다른 .html 페이지로 입력, 보낼 때 a 태그 등의 액션값을 잘 봐야 한다.
- views에서는 



**만드는 순서**

models > urls > views > form



### 8. HTTP method

- GET : 데이터를 가져올 때만 사용. DB에 영향을 주지 않는다.
- POST : DB 접근이 가능하다. 보안이 취약하기 때문에 POST를 사용할 때에는 CSRF를 사용한다 > 요청 위조 방지를 위해

- csrf_token : 사용 안 하면 에러 나올 때 403 forbidden error. 장고 미들웨어.



### 9. Django shortcut function - redirect()

- render 대신 사용
- redirect('<app name>:<namespace>') : 데이터만 돌려주면 되기 때문에 request 필요 없다.



### 10. Detail 만들기

- 



### 11. DELTE

- GET 방식으로 하면 그냥 URL에 DELETE/만 해도 삭제해버린다.
- URL 접근만으로 삭제하는 걸 막으려면 POST 방식일때만 삭제하게 한다.
- form 액션에 delete 액션을 하게 했다면 button 태그로 해야한다? a태그로도 되나..?
  - a태그 타입은 input으로 만들어야 액션을 한다!
  - 하지만 a는 하이퍼링크이기 때문에 기본적으로는 button태그, input 태그의 submit type으로 해야 정상적으로 작동을 한다!



### 12. EDIT

- 보통은 pk값으로 접근한다.
- path(<str:username>/<int:pk>/edit/)
- 수정하고 난 후 변경 사항은 저장을 해줄 곳으로 보내야 한다.



### 13. UPDATE

- path에서 연결된 인자가 두개 이상이라면 form 액션에서 순서대로 쓰면된다.
- title로 보낼 것인지 pk를 보낼 것인지 잘 확인해야 한다!
