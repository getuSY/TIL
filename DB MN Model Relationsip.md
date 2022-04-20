## DB - M:N

### 1:N의 한계

하나의 외래키에 두 개의 데이터를 넣을 수 없다.

다른 외래키에 연결하면 새로운 객체를 생성해야함.

해결책 => 중개 모델, 중개 테이블 => 기존 테이블에서 외래키가 사라진다.



### 중개 모델

환자 모델, 의사 모델, 예약 모델이 있다고 할 경우. => 다대다 관계

의사와 환자 입장에서는 예약을 **역참조**.

다대다 관계에서는 테이블이 따로 만들어지고, 기존 테이블에는 외래키가 존재하지 않는다.



#### ManyToManyField

하나의 필수 위치 인자(모델) 필요.

ManyToManyField 작성 시 중개 모델 삭제. 필드 작성 위치는 의사, 환자 모델 아무데나 상관 없음.

<app name>_<modelname>_<ManyToManyField name> 별도의 중개 테이블이 따로 생김!

ManyToManyField를 작성한 테이블에는 컬럼이 사라짐. (1:N과의 차이)

의사 모델에 ManyToManyField를 작성하든, 환자 모델에 ManyToManyField를 작성하든 상관 없다.

만약 patient 모델에 doctors 필드를 ManyToManyField 작성했을 때, 물리적으로 변화 없음.

별도의 테이블에 patient_id과 doctors_id가 생김. <= 1:N과의 차이



**BUT** 참조 시에는 차이가 있다.

ManyToManyField를 작성한 모델에서는 다른 값을 참조로 가져올 수 있지만, ManyToManyField를 작성하지 않은 값(M:N 관계의 다른 측)은 물리적으로 작성된 바가 없으므로 manager를 활용한 역참조를 하게 된다.



#### QuerySet API :  다대다 관계에서 새롭게 사용된다

```shell
doctor1 = Doctor.objects.create(name='justin')
patient1 = Patient.objects.create(name='tony')
patient2 = Patient.objects.create(name='harry')
patient1.doctors.add(doctor1)
doctor1.patient_set.all()
doctor1.patient_set.add(patient2)
doctor1.patient_set.all()
patient2.doctors.all()
```

- add()
- remove()
- 헷갈리는 부분 : comment에서는 comment.article.<sth>이면 comment가 있는 article의 필드값을 불러올 수 있었는데, 이 경우에는 patient1.doctors.all()을 하면 patient1과 연관된 doctor1의 모든 것을 불러오는 것이 아니라 patient1과 연관된 모든 doctor를 불러오는 것 같다. <= 기존 1:N과 비슷한 문법에 쓰임이 다른 부분을 주의



이렇게 헷갈리는 부분을 완화하고 1:N과 M:N을 구분하기 위해서 related_name 사용!

마찬가지로 M:N에서는 단수 소문자로 외래키명을 작성하는 1:N과 달리 복수 소문자로 필드명 작성!



#### related_name

관계 필드를 가지지 않은 target model이 관계 필드를 가지고 있는 source model을 참조할 때 사용할 manager의 이름 설정

역참조 시에 사용하는 manager 이름! ForeignKey의 related_name과 동일하게 설정한다.

related_name을 설정하면 과거에 사용하던 _set 기본 manager는 쓸 수 없다!



#### ManyToManyField를 썼을 때의 단점 

객체(ex.사람간의 정보)만 들어감. 

진료를 몇 시에, 무슨 병명으로 방문하는지 모름. 예약 자체는 가능하지만 왜 예약을 하는지 추가 정보가 없음.

외래키 두개로만 만들어지기 때문(기본 정보), 중개 테이블에 추가 정보를 작성해야할 때에는 직접 작성을 해야한다.



### ManyToManyField

다대다 관계 설정 시 사용하는 모델 필드

하나의 피수 위치인자(모델 클래스) 필요

모델 필드의 RealtedManager를 사용하여 관련 개체를 추가, 제거, 혹은 만들 수 있다.

add(), remove(), create(), clear(), ...



#### related manager

1:N에서는 tartget 모델 인스턴스에서만 사용 가능, M:N 관계에서는 두 객체에서 모두 사용 가능

- add() : 지정된 객체를 관련 객체 집합에 추가, 이미 존재하는 관계가 복제되지 않는다.
- remove() : 관련 객체 집합에서 지정된 모델 객체를 제거, 내부적으로 QuerySet.delete() 사용.



#### through

중개 테이블을 직접 작성하는 경우 이 옵션을 통해 중개 테이블을 나타내는 장고 모델 지정 가능.

중개 테이블에 추가 데이터를 사용하는 다대다 관계와 연결하는 경우에 사용.

```python
from django.db import models


class Doctor(models.Model):
    name = models.TextField()

    def __str__(self):
        return f'{self.pk}번 의사 {self.name}'


class Patient(models.Model):
    doctors = models.ManyToManyField(Doctor, through='Reservation')
    name = models.TextField()

    def __str__(self):
        return f'{self.pk}번 환자 {self.name}'


class Reservation(models.Model):
    doctor = models.ForeignKey(Doctor, on_delete=models.CASCADE)
    patient = models.ForeignKey(Patient, on_delete=models.CASCADE)
    symptom = models.TextField()
    reserved_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f'{self.doctor.pk}번 의사의 {self.patient.pk}번 환자'
```

```shell
# 예약 만들기
doctor1 = Doctor.objects.create(name='justin')
patient1 = Patient.objects.create(name='tony')
patient2 = Patient.objects.create(name='harry')

reservation1 = Reservation(doctor=doctor1, patient=patient1, symptom='headache')
reservation1.save()
doctor1.patient_set.all()
patient1.doctors.all()

patient2.doctors.add(doctor1, through_defaults={'symptom': 'flu'})
doctor1.patient_set.all()
patient2.doctors.all()

doctor1.patient_set.remove(patient1)
patient2.doctors.remove(doctor1)
```

필드 추가의 경우 through 옵션을 통해 추가 정보 기록 가능



#### 중개 테이블 필드 생성 규칙

1. target model, source model이 다른 경우

   - `id`
   - `<containing modle>_id`
   - `<other model>_id`

2. ManyToManyField가 동일한 모델을 가리키는 경우

   - `id`
   - `from_<model>_id`
   - `to_<model>_id`

   

### 실습 - LIKE

 User - Article 관계

like는 어디에 구현하든 상관 없지만 User 모델은 변동사항이 별로 없으니 Article쪽에 하자.

```python
class Article(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    like_users = models.ManyToManyField(settings.AUTH_USER_MODEL)
    title = models.CharField(max_length=10)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title
```

- 이 경우 `articles.Article.like_users`와 `article.Article.users`의 역참조가 충돌한다.
  - User:Article 1:N에서 참조는 `article.user.all()`, 역참조는 `user.article_set.all()`
  - User:Article M:N에서 참조는 `article.like_users.all()`, 역참조는 `user.article_set.all()`
  - 역참조 시 겹친다.
  - 1:N이든 M:N이든 related_name을 설정해야 한다!

```python
# related_name 추가!
class Article(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    like_users = models.ManyToManyField(settings.AUTH_USER_MODEL, related_name='like_articles')
    title = models.CharField(max_length=10)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title
```

ulrs.py 작성

```python
urlpatters = [
    ...,
    path('<int:article_pk>/likes/', views.likes, name='likes'),
]
```

views.py 작성

```python
def likes(reqeust, article_pk):
    article = get_object_or_404(Article, pk=article_pk)
    # 이미 좋아요를 누른 적 있으면 좋아요 취소
    if request.user in article.like_users.all():
        article.like_users.remove(request.user)
    else:  # 유저가 좋아요를 누른 적 없다면 좋아요
        article.like_users.add(request.user)
    return redirect('articles:index')
```

templates 수정

form에서 like_users도 보이지 않게 설정해준다!

```html
<!--articles/index.html-->
{% extends 'base.html' %}

{% block content %}
  <h1>Articles</h1>
  {% if request.user.is_authenticated %}
    <a href="{% url 'articles:create' %}">CREATE</a>
  {% else %}
    <a href="{% url 'accounts:login' %}">[새 글을 작성하려면 로그인 하세요]</a>
  {% endif %}
  <hr>
  {% for article in articles %}
    <p>작성자: {{ article.user }}</p>
    <p>글 번호: {{ article.pk }}</p>  
    <p>글 제목: {{ article.title }}</p>
    <p>글 내용: {{ article.content }}</p>
    <div>
      <form action="{% url 'articles:likes' article.pk %}" method="POST">  
        {% csrf_token %}
        {% if user in article.like_users.all %}
          <input type="submit" value="좋아요 취소">
        {% else %}
          <input type="submit" value="좋아요">
        {% endif %}
      </form>
    </div>
    <a href="{% url 'articles:detail' article.pk %}">DETAIL</a>
    <hr>
  {% endfor %}
{% endblock content %}
```

views.py 추가!

```python
def likes(reqeust, article_pk):
    article = get_object_or_404(Article, pk=article_pk)
    if article.like_users.filter(pk=request.user.pk).exists():   # exist 무엇?
        article.like_users.remove(request.user)
    else:
        article.like_users.add(request.user)
    return redirect('articles:index')
```

- exists() : 쿼리셋에 결과가 포함되어 있으면 True, 아니면 False. 규모가 큰 쿼리셋에서 특정 개체 존재 여부와 관련된 검색에 유용.(in보다 유용!) 고유한 필드(pk)가 있는 모델이 쿼리셋의 구성원인지 여부를 찾는 가장 효율적인 방법.



### Profile Page

follow 버튼은 보통 프로필 페이지에 존재한다.

프로필 페이지를 위한 urls.py 작성

```python
# accounts/urls.py
urlpatterns = [
    ...,
    path('<username>/', views.profile, name='profile'),
]
```

- 주의사항 : 문자열을 variable routing으로 쓸 일이 별로 없어서 헷갈릴 수 있다. 

  개발 순서에 따라 이걸 맨 위로 올려버리면 큰 일이 발생한다!!

  - 이 뒤에 <int>를 포함한 url이 나오지 않는 이상 맨 처음 있는 <문자열>의 view함수에 걸려버린다. 
  - ex) `login/`은 로그인 view 함수에 걸려야 하는데 username이 login인 유저라고 판단하여 `<username>/`에 걸려 username view 함수에 걸릴 수 있다.
  - 결론적으로, variable routing이 문자열로 처리되는 url함수는 맨 마지막으로 해야 한다.

view 함수 작성

```python
# accounts/views.py
def profile(request, username):
    # User = get_user_model() 이렇게 하고 아래에 User 써도 됨
    person = get_object_or_404(get_user_model(),username=username)
    context = {
        'person':person,
    }
    return render(request, 'accounts/profile.html', context)
```

프로필 템플릿 페이지 작성

```html
<body>
  <div class="container">
    {% if request.user.is_authenticated %}
      <h3>Hello, {{ user }}</h3>
      <a href="{% url 'accounts:profile' request.user.username %}">내 프로필</a>      <!-- 내 프로필 바로가기 버튼 추가! -->
      <form action="{% url 'accounts:logout' %}" method="POST">
        {% csrf_token %}
        <input type="submit" value="Logout">
      </form>
      <a href="{% url 'accounts:update' %}">회원정보수정</a>
      <form action="{% url 'accounts:delete' %}" method="POST">
        {% csrf_token %}
        <input type="submit" value="회원탈퇴">
      </form>
    {% else %}
      <a href="{% url 'accounts:login' %}">Login</a>
      <a href="{% url 'accounts:signup' %}">Signup</a>
    {% endif %}
    <hr>
    {% block content %}
    {% endblock content %}
  </div>
```



```html
{% extends 'base.html' %}

{% block content %}
<h1>{{ person.username }}님의 프로필</h1>

<hr>

{% comment %} 이 사람이 작성한 게시글 목록 {% endcomment %}
<h2>{{ person.username }}이 작성한 게시글</h2>
{% for article in person.article_set.all %}
  <p>{{ article.title }}</p>
{% endfor %}

<hr>

{% comment %} 이 사람이 작성한 댓글 목록 {% endcomment %}
<h2>{{ person.username }}이 작성한 댓글</h2>
{% for comment in person.comment_set.all %}
  <p>{{ comment.content }}</p>
{% endfor %}

<hr>

{% comment %} 이 사람이 좋아요를 누른 게시글 목록 {% endcomment %}
<h2>{{ person.username }}이 좋아요를 누른 게시글</h2>
{% for article in person.like_articles.all %}
  <p>{{ article.title }}</p>
{% endfor %}

<a href="{% url 'articles:index' %}">[back]</a>

{% endblock content %}
```



### 실습 - Follow

```python
# accounts/models.py
class User(AbstractUser):        # self는 참조하는 모델명이므로 항상 문자열로 받자!
    followings = models.ManyToManyField('self', symmetrical=True, related_name='followers')
```

- symmetrical : ManyToManyField가 동일한 모델(self)을 가리키는 정의에서 사용

  `symmetrical=True`일 경우 장고는 person_set 매니저를 추가하지 않는다. => **대칭!!**

  source 모델의 인스턴스가 target 모델의 인스턴스를 참조하면, target 모델의 인스턴스고 source 모델 인스턴스를 자동으로 참조하도록 한다.

  (ex) 데이터베이스에서 person1이 person2를 follow할 경우 자동으로 person2가 person1을 follow하는 것으로 표시한다.(서로 같이 참조하게 된다)

  만약 대칭 기능을 원하지 않으면 `symmetrical=False`로 설정!

- 테이블이 특이하게 생성된다!

  ```sql
  accounts_user_followings
  id : integer
  from_user_id : integer
  to_user_id : integer
  ```

url 작성

```python
# accounts/urls.py
urlpatterns = [
    ...,
    path('<int:user_pk>/follow/', views.follow, name='follow'),
]
```

view 작성(like 함수랑 거의 똑같다!!)

```python
def follow(request, user_pk):  # 이해하기 쉬우려면 person=you, request.user=me
    person = get_object_or_404(get_user_model(), pk=user_pk)
    # 나를 팔로우할 수 없기 때문에 user 특정해줘야 한다
    user = request.user
    if user != person:
        # 만약 내가 이 사람의 팔로워 목록에 내가 있다면
        if request.user in person.followers.all():
            person.followers.remove(request.user)
        # 팔로워 목록에 없다면
        else:
            person.followers.add(request.user)
    return redirect('accounts:profile', person.username)
```

follow 요청해보기 => 버튼 만들기

```html
<!-- accounts/profile.html -->
{% extends 'base.html' %}

{% block content %}
<h1>{{ person.username }}님의 프로필</h1>
<div>
  팔로워 : {{ person.followers.all|length }} / 팔로우 : {{ person.followings.all|length }}
</div>
<div>
  {% if user != person %}                <!-- 나를 팔로우할 수는 없다! -->
    <form action="{% url 'accounts:follow' person.pk %}" method="POST">
      {% csrf_token %}
      {% if user in person.followers.all() %}
        <input type="submit" value="언팔로우">
      {% else %}
        <input type="submit" value="팔로우">
      {% endif %}    <!-- 나와 다른 사람일 경우에만 팔로우 및 취소 버튼 보이기 -->
    </form>
  {% endif %}
  </div>
<hr>
{% comment %} 이 사람이 작성한 게시글 목록 {% endcomment %}
<h2>{{ person.username }}이 작성한 게시글</h2>
{% for article in person.article_set.all %}
  <p>{{ article.title }}</p>
{% endfor %}
<hr>
{% comment %} 이 사람이 작성한 댓글 목록 {% endcomment %}
<h2>{{ person.username }}이 작성한 댓글</h2>
{% for comment in person.comment_set.all %}
  <p>{{ comment.content }}</p>
{% endfor %}
<hr>
{% comment %} 이 사람이 좋아요를 누른 게시글 목록 {% endcomment %}
<h2>{{ person.username }}이 좋아요를 누른 게시글</h2>
{% for article in person.like_articles.all %}
  <p>{{ article.title }}</p>
{% endfor %}
<a href="{% url 'articles:index' %}">[back]</a>
{% endblock content %}
```

- 이 경우 from_user_id가 팔로잉을 요청하는 사람(팔로워), to_user_id가 팔로잉받는 사람!

- 겹치는 부분이 많은 경우 좀 더 깔끔하게 만들기 위한 방법

  겹치는 부분을 with 태그로 감싼다. **but** 최적화와는 아무런 관련이 없다!

  ```html
  <!-- accounts/profile.html -->
  {% extends 'base.html' %}
  
  {% block content %}
  <h1>{{ person.username }}님의 프로필</h1>
  <!-- with : 중복되는 부분들을 with 태그 안에서 변수화 -->
  {% with followers=person.followers.all followings=person.followings.all %}
    <div>
      팔로워 : {{ followers|length }} / 팔로우 : {{ followings|length }}
    </div>
  
    <div>
      {% if user != person %}
        <form action="{% url 'accounts:follow' person.pk %}" method="POST">
          {% csrf_token %}
          {% if user in followers %}
            <input type="submit" value="언팔로우">
          {% else %}
            <input type="submit" value="팔로우">
          {% endif %}
        </form>
      {% endif %}
    </div>
  {% endwith %}
  
  <hr>
  ```

  필요한 부분만 구역화하여 with 태그로 묶는 게 관리 측면에서 편할 수 있다.



### Summary

```python
# articles/views.py
@require_POST
def likes(request, article_pk):
    if request.user.is_authenticated:
        article = get_object_or_404(Article, pk=article_pk)
        if article.like_users.filter(pk=request.user).exists():
            article.like_users.remove(request.user)
        else:
            article.like_users.add(request.user)
        return redirect('articles:index')
    return redirect('articles:login')
```

```python
# accounts/views.py

def follow(request, user_pk):
    if request.user.is_authenticated:
        you = get_object_or_404(get_user_model(), pk=user_pk)
        me(user) = request.user
        if you != me:
            if you.followers.filter(pk=me.pk).exists():
                you.followers.remove(me)
            else:
                you.followers.add(me)
        return redirect('accounts:profile', you.username)
    return redirect('accounts:login')
```

article의 index.html에 작성자의 프로필로 이동할 수 있도록 링크 걸어주기

```html
<!--articles/index.html-->
{% extends 'base.html' %}

{% block content %}
  <h1>Articles</h1>
  {% if request.user.is_authenticated %}
    <a href="{% url 'articles:create' %}">CREATE</a>
  {% else %}
    <a href="{% url 'accounts:login' %}">[새 글을 작성하려면 로그인 하세요]</a>
  {% endif %}
  <hr>
  {% for article in articles %} <!--게시글을 작성한 user.username으로 가야한다-->
    <p>작성자:  <a href="{% url 'accounts:profile' article.user.username %}">{{ article.user }}</a></p>
    <p>글 번호: {{ article.pk }}</p>  
    <p>글 제목: {{ article.title }}</p>
    <p>글 내용: {{ article.content }}</p>
    <div>
      <form action="{% url 'articles:likes' article.pk %}" method="POST">  
        {% csrf_token %}
        {% if user in article.like_users.all %}
          <input type="submit" value="좋아요 취소">
        {% else %}
          <input type="submit" value="좋아요">
        {% endif %}
      </form>
    </div>
    <a href="{% url 'articles:detail' article.pk %}">DETAIL</a>
    <hr>
  {% endfor %}
{% endblock content %}
```

프로필에 메인페이지로, 뒤로 가기 버튼 추가해준다.(확인을 위해)



### with Tag

django 공식 문서 참조 : https://docs.djangoproject.com/en/4.0/ref/templates/builtins/#with

자주 등장하는 복잡한 변수를 간단한 이름으로 저장해 사용할 수 있다.

여러 개의 변수는 공백을 포함하여 사용할 수 있다.

as로도 사용할 수 있지만 변수로 할당하는 게 보다 깔끔해서 with도 괜찮다.
