## Django - Authentication System

### 장고의 인증 시스템

`django contrib module`로 제공

필수 구성은 settings.py에 이미 포함되어 있다

```python
# settings.py
INSTALLED_APPS = [
    ...,
    'django.contrib.auth',
    'django.contrib.contenttypes',
    ...
]
```

- `django.contrib.auth` : 인증 프레임워크의 핵심과 기본 모델을 포함
- `django.contrib.contenttypes` : 사용자가 생성한 모델과 권한을 연결
- 장고의 인증 시스템은 인증과 권한 부여를 함께 제공하며 이 기능이 어느 정도 결합되어 통칭 "인증 시스템"
- Authentication (인증) : 신원 확인, 사용자가 누구인지 확인
- Authorization (권한) : 권한 부여, 사용자가 할 수 있는 작업 결정.



### 두번째 앱 생성하기

```bash
$ python manage.py startapp accounts
```

이름이 꼭 "accounts"일 필요는 없지만 장고 내부적으로 accounts라는 이름으로 쓰이고 있기 때문에 관례적으로, 일반적으로 user 관련하여 진행할 때에는 accounts로 지정하는 것을 권장!

앱 생성 > 앱 등록 > projects/urls.py url 연결 > accounts/urls.py 생성



### 쿠키와 세션

로그인/로그아웃 전에 꼭 알아야할 것!



#### HTTP : Hyper Text Transfer Protocol

데이터를 가져올 수 있도록 만들어주는 일종의 규약

웹에서 이루어지는 모든 데이터 교환의 기초

**클라이언트와 서버간의 규약**

1. 비연결지향 특징 (connectionless) : 서버는 요청에 응답을 보낸 후 연결을 끊는다. 우리가 웹을 보고 있어도 연결되어 있지 않다.
2. 무상태 특징 (stateless) : 연결을 끊는 순간 클라이언트와 서버 간의 통신이 끝나기 때문에 상태 정보(ex. 로그인 상태)가 유지되지 않음.

=> 클라이언트와 서버의 지속적인 관계를 유지하기 위해 쿠키과 세션이 반드시 필요한 것! 비연결 지향과 무상태를 해소해주기 위해서!



#### 쿠키(Cookie)

서버가 사용자의 웹 브라우저에 전송하는 작은 데이터 조각

사용자가 웹사이트를 방문할 경우 웹사이트 서버를 통해 사용자의 컴퓨터에 배치시키는 작은 기록 정보 파일(쿠키를 로컬에 KEY-VALUE의 데이터 형식으로 저장. 이렇게 저장해두었다가 동일한 서버 재요청시 저장된 쿠키를 함께 전송)

HTTP는 상태가 존재하지 않기 때문에 상태를 만들기 위해서 쿠키가 필요한 것 <= 상태 유지 정보를 요청과 쿠키를 함께 전송

HTTP  쿠키는 상태가 있는 세션을 만들어줌 <= 상태가 있는 것처럼 보여준다!

상태가 없는 특징의 HTTP에서 상태 정보를 기억하여 로그인 상태 등을 유지할 수 있음.

웹페이지에 접속하면 요청한 웹 페이지를 받으면 쿠키를 저장, 클라이언트가 같은 서버 재요청시 요청과 함께 쿠키도 전송. 로그인 이외 여러 가지 기능에 사용되고 있음.

**쿠키 사용 목적**

1. 세션 관리 (상태를 유지시켜주는 세션)(Session Management) : 로그인, 아이디 자동 완성, 공지 안 보기, 팝업 체크, 장바구니 등 정보 관리
2. 개인화(Personalization)
3. 트래킹(Tracking)



**<예시 : 장바구니 시스템 확인 >**

IDE > networks > header > 응답 부분 같은 쿠키가 계속 등장(set-Cookie)

cookies > key-value 형태로 저장 > request cookies > 수많은 쿠키 확인

networtk 문서 > cookies > 받은 걸 다시 보내고 있는 걸 확인할 수 있음

IDE > application tab > storage > cookies > 브라우저에서 받은 쿠키가 저장되어 있다

상품 추천 정보도 쿠키에 저장되어 트래킹되는 것

storage에 저장된 쿠키 정보를 삭제함으로써 장바구니를 비울 수도 있음 <= 장바구니도 쿠키 정보로 이루어짐



#### 세션(Session)

쿠키가 좀 더 큰 범주의 정보

사이트와 특정 브라우저 사이의 상태를 유지시키는 것

클라이언트가 서버에 접속하면 서버가 특정 세션 id를 발급하고 클라이언트는 발급받은 세션 id를 쿠키에 저장

id는 세션을 구별하기 위해 필요, 쿠키는 id만 저장. id에 대한 값은 서버가 가지고 있다.

로그아웃 => 로그인 상태를 유지하고 있는 세션을 삭제하는 과정

**쿠키 수명**

1. 세션 쿠키 (Session Cookies) : 세션이 종료되면 삭제됨. 세션 복원(session restoring)으로 오래 지속 가능.
2. Persistent Cookies(Permanent cookies) : Expires 속성에 지정된 날짜, MAX-Age 속성된 지정된 기간이 지나면 삭제



#### Session in Django

미들웨어를 통해 구현됨. settings.py에 작성되어 있다.

```python
# sessions.py
MIDDLEWARE = [
    ...
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    ...
]
```

database_backed sessions 저장 방식을 기본 값으로 사용 <= 설정을 통해 방식 변경 가능

특정 세션 id를 포함하는 쿠키를 사용해 각각의 브라우저와 사이트가 연결된 세션을 알아냄 : django_session

모든 것을 세션으로 사용하려고 하면 사용자가 많을 시 서버에 부하가 걸릴 수 있다.



### 로그인

로그인은 세션을 create하는 로직과 같다

장고에는 built-in form이 있어 세션의 메커니즘 생각을 할 필요 없음



**AuthenticaionForm**

사용자 로그인을 위한 폼이며 request를 첫 번째 인자로 취한다



**login 함수**

authentication 함수를 통과했을 때 login() 함수가 필요하다. HttpsRequest 객체와 User 객체 필요.

실행되었을 때 장고 세션 프레임워크를 활용해 세션에 user의 id를 저장.

참고 - How to log a user in : https://docs.djangoproject.com/en/4.0/topics/auth/default/#how-to-log-a-user-in-1



#### Login CRUD

```python
# accounts/urls.py
from django.urls import path
from . import views

app_name = 'accounts'
urlpatterns = [
    path('login/', views.login, name='login'),
```

```django
<!-- accounts/login.html -->
{% extends 'base.html' %}

{% block content %}
<h1>로그인</h1><hr>
<form action="{% url 'accounts:login' %}" method='POST'>
    {% csrf_token %}
    {{ form.as_p }}
    <input type='submit'>
</form>
<a href="">back</a>
{% endblock %}
```

```python
# accounts/views.py
from django.shortcuts import render
from django.contrib.auth.forms import AuthenticationForm

def login(request):
    if request.method == 'POST':
        pass
    else:
        form = AuthenticationForm()
    context = {
        'form':form,
    }
    return render(request, 'accounts/login.html', context)
```

- 장고 built-in forms : https://docs.djangoproject.com/en/4.0/topics/auth/default/

```python
# accounts/views.py
from django.shortcuts import render
from django.contrib.auth.forms import AuthenticationForm
from django.contrib.auth import authenticate, login as auth_login   # login 함수
from django.views.decorators.http import require_http_methods

def login(request):
    if request.method == 'POST':
        form = AuthenticaionForm(request, request.POST)    # ModelForm과 인자가 다르다
        if form.is_valid():                                # Form의 상속을 받는다
            auth_login(request, form.get_user())   # view함수 이름과 빌트인 함수 이름 동일X
            return redirect('articles:index')
    else:                                                  
        form = AuthenticationForm()
    context = {
        'form':form,
    }
    return render(request, 'accounts/login.html', context)
```

- 로그인을 하는 건 DB에 저장이 되는 게 아니라 session을 만드는 것! => ModelForm을 쓸 필요가 없다!
- form이냐 ModelForm이냐를 구분하는 것이 중요하다!
- `AuthenticaionForm(request, request.POST)` : 사전 인증 절차를 진행해주는 것, 실제 세션을 만들어주는 것은 아니다! 
- `AuthenticationForm`은 첫 번째 인자로 request를 받도록 설계되어 있고, request.POST를 두번째 인자로 받는 것은 form의 인자를 상속받기 때문!
- `auto_login(request, form.get_user())` : 인증된 form으로부터 정보를 받아온다. user_cache는 인증 전에는 `request=None`이지만 인증 검사를 통과한 이후 할당받음. 
  - is_valid() 내에서 내부적으로 동작하는 메소드 clean이 호출되면서 인증을 통과하게 되면 `user_cache`라는 객체로 채워지게 된다!
- `auth_login(request, form.get_user()) ` 이전까지는 인증된 사용자 객체를 만들기 위한 과정이다.

```bash
$ python manage.py createsuperuser                          # 로그인 위한 관리자 계정 생성
```

- 실제 정보는 django의 DB(django_session table)에 있다.(클라이언트에는 key 값만!) 

- 세션과 일반 쿠키의 다른 점 : 일반적인 쿠키는 key-value 모두 브라우저가 가지고 있다!

- 이대로도 웹 페이지에서는 로그인 및 유저 정보가 뜬다! 왜?

  - context processors : 장고 템플릿이 렌더링 될 때 자동으로 호출 가능한 컨텍스트 데이터 목록

  - Users : User 인스턴스를 템플릿 `{{ user }}`에 반환. 로그인 안 하면 AnonymousUser 인스턴스로!

    https://docs.djangoproject.com/en/4.0/ref/contrib/auth/

    https://docs.djangoproject.com/en/4.0/ref/contrib/auth/#anonymoususer-object

    비로그인 사용자를 위한 별도의 클래스가 있음을 인지!

  - 다양한 context processors가 있음



### 로그아웃

세션을 삭제하는 과정



**logout 함수**

HttpRequest 객체를 인제로 받고 반환 값 없음. 로그인하지 않은 경우 오류 발생 X.

현재 요청에 대한 세션 데이터를 서버 DB에서도 지우고 클라이언트의 쿠키에서도 세션 ID를 삭제. 

=> 다른 사람이 동일한 웹 브라우저를 사용해 로그인 및 이전 사용자의 세션 데이터에 액세스하는 것을 방지.



#### Logout CRUD

```django
<!--accounts/base.html-->
<body>
    <div class="container">
        <h3>
            Hello, {{ user }}
        </h3>
        <a href="{% url 'account:login '%}">Login</a>
        <form action="" method="POST">
            {% csrf_token%}
            <input type="submit" value="Logout">
        </form>
        {% block content %}
        {% endblock %}
    </div>
</body>
```

```python
# accounts/urls.py
from django.urls import path
from . import views

app_name = 'accounts'
urlpatterns = [
    path('login/', views.login, name='login'),
    path('logout/', view.logout, name='logout'),
]
```

```python
# accounts/views.py
from django.shortcuts import render
from django.contrib.auth.forms import AuthenticationForm
from django.contrib.auth import authenticate, login as auth_login   # login 함수
from django.contrib.auth import logout as auth_logout               # logout 함수
from django.views.decorators.http import require_POST, require_http_methods
from django.views.decorators.http import require_http_methods

@require_http_methods(['GET', 'POST'])
def login(request):
    if request.method == 'POST':
        form = AuthenticaionForm(request, request.POST)    # ModelForm과 인자가 다르다
        if form.is_valid():                                # Form의 상속을 받는다
            auth_login(request, form.get_user())   # view함수 이름과 빌트인 함수 이름 동일X
            return redirect('articles:index')
    else:                                                  
        form = AuthenticationForm()
    context = {
        'form':form,
    }
    return render(request, 'accounts/login.html', context)

@require_POST
def logout(request):
    auth_logout(request)
    return redirect('articles:index')
```

- 이 상태로 두게 되면, 기본 페이지에서 로그인과 로그아웃 버튼이 둘 다 뜨게 된다!
- 로그인했을 때만 로그아웃 버튼이 보이고, 로그인했을 때는 로그아웃만 보여야 한다! => 이를 위한 사용자 접근 제한이 필요!



### 로그인 사용자에 대한 접근 제한

액세스 제한 방법 2가지

1. `is_authenticated` attribute

   - user model의 속성 중 하나, 항상 True인 읽기 속성. **BUT** anonymous에 대해서는 항상 False!

   - 사용자가 인증되었는지의 여부만을 확인할 수 있는 방법. 오직 **T/F!**

   - 장고에서의 권한과는 관련이 없으며 사용자가 활성화 상태나 유효한 세션을 가지고 있는지 확인 안 함.

   - 단순히 로그인 된 유저인지, 로그인하지 않은 유저인지만 확인한다.

     ```django
     <!--accounts/base.html-->
     <body>
         <div class="container">
             {% if request.user.is_authenticated %} <!--이 request 호출은?-->
                 <h3>Hello, {{ user }}</h3>
                 <form action="" method="POST">
                     {% csrf_token%}
                     <input type="submit" value="Logout">
                 </form>
             {% else %}
             	<a href="{% url 'account:login '%}">Login</a>
             {% endif%}
             {% block content %}
             {% endblock %}
         </div>
     </body>
     ```

     context_processors 덕분에 어디서든 request를 쓸 수 있다! 자주 쓰는 것을 미리 올려놨기 때문에!

   - 원래라면 로그인을 했다면 로그인 페이지로 들어갈 수 없어야 한다. 하지만 이 방법의 경우 단순히 login, logout 버튼의 출력만 바꾼 것이기 때문에 url 요청을 통해 로그인 페이지로 들어갈 수 있다.

   - 로그인되어 있다면 view 함수를 호출하면 안 된다!

     ```python
     @require_http_methods(['GET', 'POST'])
     def login(request):
         if request.user.is_authenticated:       # 로그인 한 상태라면 강제로 되돌리기
             return redirect('articles:index')
         
         if request.method == 'POST':
             form = AuthenticaionForm(request, request.POST)  
             if form.is_valid():                            
                 auth_login(request, form.get_user()) 
                 return redirect('articles:index')
         else:                                                  
             form = AuthenticationForm()
         context = {
             'form':form,
         }
         return render(request, 'accounts/login.html', context)
     
     
     @require_POST
     def logout(request):       # 로그인 된 사용자만 로그아웃 함수를 사용할 수 있게 한다
         if request.user.is_authenticated:
         	auth_logout(request)
         return redirect('articles:index')
     ```

   - 게시글 작성에 활용

     ```django
     <!-- accounts/index.html -->
     {% extends 'base.html' %}
     
     {% block content %}
         <h1>Articles</h1><hr>
         {% if request.user.is_authenticated %}
     		<a href={% url 'articles:index' %}>CREATE</a>
     	{% else%}
     		<a href={% url 'articles:index' %}>[새 글을 작성하려면 로그인하세요]</a>
         {% endif %}
         <a href="">back</a>
     {% endblock %}
     ```

     이 상태로만 두면 결국 view함수는 막혀있지 않기 때문에 url 요청을 통해 작성 가능

2. `login_required` decorator

   - 사용자가 로그인되어 있지 않으면 settings.LOGIN_URL에 설정된 문자열 기반 절대 경로로 redirect

     LOGIN_URL 기본 값 `'accounts/login/'` <= 두 번째 앱 이름이 accounts인 이유!

   - 사용자가 로그인되어 있으면 정상적으로 view 함수 실행

     ```python
     from django.contrib.auth.decorators import login_required
     
     @login_required       # C, U, D는 로그인 정보가 필요하다!
     @require_http_methods(['GET', 'POST'])   # 데코레이터 여러개 가능! 위에서부터 읽는다
     def create(request):
         return
     
     @login_required
     @require_http_methods(['GET', 'POST'])
     def update(request, pk):
         return
         
     @login_required
     @require_POST
     def delete(request, pk):
         return
     ```

     로그인되어 있지 않으면 해당 기능 수행 및 url 요청 시 로그인 페이지로 돌려보낸다!

     이 때 주소가 특이하다! => `/accounts/login/?next=/articles/create/`

     로그인 후 이전에 조회하고자 했던 페이지로 보내주기 위함 <= 데코레이터가 next 파라미터로 붙여줌
     
     => login을 진행하면 그냥 login만 된다 => next 파라미터를 쓰려면?
     
     form action의 url을 지워줘야 한다. <= 이 값이 없으면 현재 url로 보내주기 때문에
     
     ```python
     @require_http_methods(['GET', 'POST'])
     def login(request):
         if request.user.is_authenticated:      
             return redirect('articles:index')
         
         if request.method == 'POST':
             form = AuthenticaionForm(request, request.POST)  
             if form.is_valid():                            
                 auth_login(request, form.get_user()) 
                 return redirect(request.GET.get('next') or 'articles:index')
         else:                                                  
             form = AuthenticationForm()
         context = {
             'form':form,
         }
         return render(request, 'accounts/login.html', context)
     ```
     
     ```django
     <!-- accounts/login.html -->
     {% extends 'base.html' %}
     
     {% block content %}
     <h1>로그인</h1><hr>
     <form action="" method='POST'> <!--액션을 비워줘야 next 작동!-->
         {% csrf_token %}
         {{ form.as_p }}
         <input type='submit'>
     </form>
     <a href="">back</a>
     {% endblock %}
     ```
     
   - delete시 문제 발생 : `required_POST`가 작성된 함수에 `login_required`를 함께 사용하는 경우
   
     ```python
     @login_required      # login required는 이후 get 방식으로 바꿔준다
     @require_POST        # 두 데코레이터의 궁합이 안 맞다
     def delete(request, pk):
         article = get_object_or_404(Article, pk=pk)
         article.delete()
         return redirect('articles:index')
     ```
   
     비로그인 시 로그인 후 삭제 요청 > login view 함수에서 next 파라미터에 의해 redirect > 
   
     redirect 요청은 POST 방식이 불가능하기 때문에 GET 방식으로 요청됨. > @required_POST 405 Error
   
     ```python
     # @login_required 둘 중 하나를 밑으로 내려준다!     
     @require_POST   
     def delete(request, pk):
         if request.user.is_authenticated:
             article = get_object_or_404(Article, pk=pk)
             article.delete()
         return redirect('articles:index')
     ```





### 회원가입

built-in ModelForm 사용



#### UserCreationForm

주어진 username과 password로 권한이 없는 새 user를 생성하는 MdoelForm. 3개의 필드를 가짐

1. username (from the user model) 
2. password1
3. password2



#### 회원가입 구현

```python
# accounts/urls.py
from django.urls import path
from . import views

app_name = 'accounts'
urlpatterns = [
    path('login/', views.login, name='login'),
    path('logout/', views.logout, name='logout'),
    path('signup/', views.signup, name='signup'),
]
```

```python
# accounts/views.py
# signup 구조 먼저 짜기
def signup(request):
    if request.method == 'POST':
        pass
    else:
        pass
    return render(request, 'accounts/signup.html')
```

```django
<!--accounts/signup.html-->
{% extends 'base.html' %}

{% block content %}
<h1>회원가입</h1><hr>
<form action="{% url 'accounts:signup' %}" method='POST'>
    {% csrf_token %}
    {{ form.as_p }}
    <input type='submit'>
</form>
<a href="{% url 'articles:index' %}">back</a>
{% endblock %}
```

```python
# accounts/views.py
from django.shortcuts import render, redirect
from django.contrib.auth.forms import AuthenticationForm, UserCreationForm  # 같은 위치
from django.contrib.auth import authenticate, login as auth_login  
from django.contrib.auth import logout as auth_logout               
from django.views.decorators.http import require_POST, require_http_methods
from django.views.decorators.http import require_http_methods

def signup(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('articles:index')
    else:
        form = UserCreationForm()   # else 먼저 작성!
    context = {
        'form':form,
    }
    return render(request, 'accounts/signup.html', context)
```

현재 built-in form을 사용하고 있기 때문에 forms.py 안 만들고 있음!!

**BUT** 이대로 두면 로그인 상태에서도 회원 가입이 가능하다. 회원가입 후 로그인이 바로 되지 않는다.

```django
<!--accounts/base.html   홈페이지에 회원가입 버튼 만들어주기 -->
<body>
    <div class="container">
        {% if request.user.is_authenticated %}
            <h3>Hello, {{ user }}</h3>
            <form action="" method="POST">
                {% csrf_token%}
                <input type="submit" value="Logout">
            </form>
        {% else %}
        	<a href="{% url 'account:login '%}">Login</a>
        	<a href="{% url 'accounts:signup' %}">Signup</a>
        {% endif%}
        {% block content %}
        {% endblock %}
    </div>
</body>
```

signup view 함수 보완!

```python
# 회원 가입  +  로그인 바로 되게 구현

@require_http_methods(['GET', 'POST']) 
def signup(request):
    if request.method == 'POST':   # 나중에 로그인 시에는 회원가입이 안 보이게 할 수 있는 부분
        # if request.user.is_authenticated:      
        # return redirect('articles:index')
    
        form = UserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()     # save에 대한 return으로 user 객체 생성됨
            auth_login(request, user)     # 로그인을 시켜주는 함수
            return redirect('articles:index')
    else:
        form = UserCreationForm()
    context = {
        'form':form,
    }
    return render(request, 'accounts/signup.html', context)
```

- articles의 create 과정과 굉장히 흡사하다! <= CRUD는 반복의 과정과 같다



### 회원 탈퇴

#### 회원 탈퇴 구현

```python
# accounts/urls.py

...
+ path('delete/', views.delete, name='delete')
```

```python
# accoutns/views.py

@required_POST   # 포스트와 login_required가 겹치기 때문에 데코레이터 하나는 내려줌
def delete(request):
    if request.user.is_authenticated:   # 로그인 시에만 보이게 하기
    	request.user.delete()
    return redirect('articles:index')
```

```django
<!--accounts/base.html   홈페이지에 탈퇴 버튼 만들어주기 -->

<body>
    <div class="container">
        {% if request.user.is_authenticated %}
            <h3>Hello, {{ user }}</h3>
            <form action="" method="POST">
                {% csrf_token%}
                <input type="submit" value="Logout">
            </form>
       		<form action="{% url 'accounts:delete' %}" method="POST">
                {% csrf_token %}
                <input type="submit" value="회원 탈퇴">
        	</form>   <!--일단은 바로 삭제->
        {% else %}
        	<a href="{% url 'account:login '%}">Login</a>
        	<a href="{% url 'accounts:signup' %}">Signup</a>
        {% endif%}
        {% block content %}
        {% endblock %}
    </div>
</body>
```

탈퇴했다고 해서 세션이 지워지는 것은 아니다! <= 세션은 로그아웃을 해야 지워지는 부분이기 때문에!

필수는 아니다 => 어차피 로그인을 하면 새 세션 값을 받기 때문에.

그러나 세션까지 다 지우고 싶다면?

```python
# accounts/views.py  delete 함수 추가

@required_POST 
def delete(request):
    if request.user.is_authenticated: 
    	request.user.delete()
        auth_logout(request)            # 유저 삭제 후 로그아웃 함수 호출함으로써 세션 삭제
    return redirect('articles:index')
```

- **주의! :** `request.user.delete()` 와 `auth_logout(request)` 순서 바뀌면 안됨.

  반드시 회원 탈퇴 후 로그아웃을 해줘야한다.





### 회원정보 수정

#### UserChangeForm

사용자의 정보 및 권한을 수정하기 위해 admin 인터페이스에서 사용되는 ModelForm

articles는 등록을 해야하지만 User는 미리 등록이 되어 있다.

```python
# accounts/urls.py 추가
+
path('update/', views.update, name='update'),
...
```

```python
def update(request):
    if request.method == 'POST':
        pass
    else:
        pass
    return render(request, 'accounts/update.html')
```

```django
<!--accounts/update.html-->
{% extends 'base.html' %}

{% block content %}
<h1>회원정보 수정</h1><hr>
<form action="{% url 'accounts:update' %}" method='POST'>
    {% csrf_token %}
    {{ form.as_p }}
    <input type='submit'>
</form>
<a href="{% url 'articles:index' %}">back</a>
{% endblock %}
```

```python
from django.contrib.auth.forms import AuthenticationForm, UserCreationForm, UserChangeForm # 추가!
def update(request):
    if request.method == 'POST':
        pass
    else:
        form = UserChangeForm(instance=request.user)
    context = {
        'form':form,
    }
    return render(request, 'accounts/update.html', context)
```

- 이대로 하면 회원정보 수정 란에서 최상위 사용자 권한도 줄 수 있다.

- 일반 사용자가 봐서는 안 되는 필드들이 포함되어 있다.

  => 이 form을 그대로 상속하여 사용해서는 안 된다! 

```python
# accounts/forms.py 생성
from django.contrib.auth.forms import UserChangeForm
from django.contrib.auth import get_user_model
# 프로젝트에서 사용하는 user class를 return해주는 함수!

class CustomUserChangeForm(UserChangeForm): # 필드가 다 보이니까 필드만 재정의할 것!
    
    class Meta:
        model = get_user_model()  # user 모르니까 확인!
        fields = ('email', 'first_name', 'last_name')
```

- User Fields 참조 : https://docs.djangoproject.com/en/4.0/ref/contrib/auth/

```python
from django.contrib.auth.forms import AuthenticationForm, UserCreationForm # 빼!
from .forms import CustomUserChangeForm

def update(request):
    if request.method == 'POST':
        form = CustomUserChangeForm(request.POST, instance=request.user)
        if form.is_valid():
            form.save()
            return redirect('articles:index')
    else:
        form = CustomUserChangeForm(instance=request.user)  # 커스텀 폼으로 바꾸기!
    context = {
        'form':form,
    }
    return render(request, 'accounts/update.html', context)
```

```django
<!--accounts/base.html   회원정보 수정 버튼 만들기 -->
<body>
    <div class="container">
        {% if request.user.is_authenticated %}
            <h3>Hello, {{ user }}</h3>
            <form action="" method="POST">
                {% csrf_token%}
                <input type="submit" value="Logout">
            </form>
        	<a href="{% url 'accounts:update' %}">회원정보 수정</a>        <!--추가!-->
       		<form action="{% url 'accounts:delete' %}" method="POST">
                {% csrf_token %}
                <input type="submit" value="회원 탈퇴">
        	</form>   <!--일단은 바로 삭제->
        {% else %}
        	<a href="{% url 'account:login '%}">Login</a>
        	<a href="{% url 'accounts:signup' %}">Signup</a>
        {% endif%}
        {% block content %}
        {% endblock %}
    </div>
</body>
```

- 이대로하면 필드는 설정한대로 나오지만 ***비밀번호 관련 사항**은 설정하지 않아도 나타나게 된다!
- 비밀번호 관련으로 장고가 기본적으로 걸어두는 url은 /accounts/password/로 리다이렉트한다.
- 장고에는 비밀번호 수정이 회원정보 수정에 포함되어 있지 않다! => 따로 PasswordChangeForm 있음!





### 비밀번호 변경

#### PasswordChangeForm

사용자가 비밀번호를 변경할 수 있도록 하는 form. 이전 비밀번호를 입력하여 변경 가능하게 함. 

이전 비밀번호를 입력하지 않고 비밀번호를 설정할 수 있는 SetPasswordForm을 상속받는 서브클래스.



#### 비밀번호 변경 구현

```python
# accounts/urls.py
+
path('password/', views.change_password, name="change_password"),
...
```

```python
# accounts/view.py

def change_password(request):
    if request.method == 'POST':
        pass
    else:
        pass
    return render(request, 'accounts/change_password.html')
```

```django
<!--accounts/change_password.html 생성 -->
{% extends 'base.html' %}

{% block content %}
<h1>비밀번호 변경</h1><hr>
<form action="{% url 'accounts:change_password' %}" method='POST'>
    {% csrf_token %}
    {{ form.as_p }}
    <input type='submit'>
</form>
<a href="{% url 'articles:index' %}">back</a>
{% endblock %}
```

```python
# accounts/view.py
from django.contrib.auth.forms import AuthenticationForm, UserCreationForm, PasswordChangeForm  # <= 추가!
from django.contrib.auth.decorator import login_required

@login_required
@require_http_methods(['GET', 'POST'])
def change_password(request):
    if request.method == 'POST':
        form = PasswordChangeForm(request.user, request.POST) # 부모가 form
        if form.is_valid():
            form.save()
            return redirect('articles:index')
    else:
        form = PasswordChangeForm(request.user)  # 첫번째 인자 주의!
    context = {
        'form': form,
    }
    return render(request, 'accounts/change_password.html', context)
```

- 주의 없이 이대로 돌리면 missing 1 required positional argument : 'user'가 나온다!

- passwordChangeForm은 첫번째 인자로 request.user를 받는다! => 부모 class 참조 modelform 아님!

  save() 시 반환값으로 user를 받아온다.

- 비밀번호가 바뀌면 장고의 session_key가 바뀐다!

  **but** 브라우저의 key는 바뀌지 않아서 로그아웃되어버린다.



#### 암호 변경 시 세션 무효화 방지

**update_session_auth_hash(request, user)**

비밀번호가 변경되면 기존 세션과의 회원 인증 정보가 일치하지 않게 되어 로그인 상태를 유지할 수 없다

암호가 변경되어도 로그아웃되지 않도록 새로운 password hash로 session을 업데이트함

```python
from django.contrib.auth import update_session_auth_hash

def change_password(request):
    if request.method == 'POST':
        form = PasswordChangeForm(request.user, request.POST)
        if form.is_valid():
            user = form.save()   # 반환값이 있기 때문에 유저 객체를 받아올 수 있다
            update_session_auth_hash(request, user)  # 비밀번호가 바뀐 이후 수행해야함
            return redirect('articles:index')
    else:
        form = PasswordChangeForm(request.user)
    context = {
        'form': form,
    }
    return render(request, 'accounts/change_password.html', context)
```

- 비밀번호가 바뀌면 해당되는 session key가 바뀐다 => 브라우저의 session도 함께 바꾸어준다.



##### * 비밀번호가 보이지 않습니다 제거

UseerChangeForm에 password가 정의되어 있다!

```python
# accounts/forms.py 생성

from django.contrib.auth.forms import UserChangeForm
from django.contrib.auth import get_user_model

class CustomUserChangeForm(UserChangeForm):
    password = Node   # 안내 문구 없앨 수 있다! but 비밀번호 변경하는 링크를 따로 만들어야 함!
    
    class Meta:
        model = get_user_model()
        fields = ('email', 'first_name', 'last_name')
```



