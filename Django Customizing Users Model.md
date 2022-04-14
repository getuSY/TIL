## Django - Customizing Users Model

### Substituting a custom user model

일부의 경우에서는 내장 user 모델을 사용할 경우 적절하지 않을 수 있다.

Django에서는 user를 참조하는데 `AUTH_USER_MODEL`을 제공해 기본 유저 모델을 재정의할 수 있게 함.

Django는 새 프로젝트 시작 시 커스텀 유저 모델을 설정하는 것을 강력하게 권장한다.

**주의!** User 모델을 재정의할거라면 모든 migration, 혹은 첫 migrate 실행 전에 다 만들어놓고 시작하라!



#### AUTH_USER_MODEL

```python
# settings.py
INSTALLED_APPS = [
    ...
    'django.contrib.auth',
    ...
]
# 기본값
AUTH_USER_MODEL = 'auth.user'   # 기본 값으로는 built-in user model
```

프로젝트를 진행되는 동안에는 변경 불가.(100% 불가는 아니지만 모델 간 복잡한 연결때문에 수정하기 힘들다!)



#### Custom User Model 정의하기

```python
# accounts/models.py
from django.db import models
from django.contrib.auth.models import AbstarctUser

class User(AbstractUser):   # 기본 built-in user model이랑 같다!
    pass
```

그냥 built-in user model과 같은 것 아닌가?

=> 나중에 mid-in project에서 얼마든지 수정이 가능하다!

참고 AbstractUser : https://github.com/django/django/blob/main/django/contrib/auth/models.py

```python
# accounts/settings.py
...
AUTH_USER_MODEL = 'accounts.User'   # 기본 값을 커스텀 유저 모델로 대체한다
```

무조건 필수 과정이라고 생각하고 프로젝트 시작하자마자 대체한다!

원래는 admin에 유저 모델을 등록 안 해도 users가 떴었는데 이렇게 재정의 하면 안 뜬다!

```python
# accounts/admin.py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .model import User

admin.site.register(User, UserAdmin)
```

Django 관리에서 ACCOUNTS 탭에 Users가 생긴다.



만약 프로젝트 중간에 유저 모델을 바꿨다면? => DB 초기화 후 마이그레이션 진행

**초기화 방법**

1. db.sqlite3 파일 삭제
2. migrations 파일 모두 삭제(**주의!** migrations 폴더 말고 숫자 붙은 파일만 삭제!)
3. makemigrations => migrate 진행

이후 원래는 db에 auth_user 테이블이 있었는데 사라지고 accounts_user가 생겨있다.



Custom User Model 모든 과정 참조 : https://docs.djangoproject.com/en/4.0/topics/auth/customizing/#substituting-a-custom-user-model





### Custom User & Built-in auth forms

User 모델을 커스텀하여 바꾼 후 기존의 form들과 안 맞게 된 부분을 바꿔줘야 한다.



#### 회원 가입 시도 에러

기존에 사용한 UserCreationForm, UserChangeForm은 기존 User 모델을 사용한 ModelForm.

`class Meta`에 `model = User`(built-in)로 지정되어 있기 때문에 커스텀 유저 모델로 대체해야 한다.

```python
# accounts/forms.py
from django.contrib.auth.forms import UserCreationForm, UserChangeForm
from django.contrib.auth import get_user_model
# get_user_model() : 현재 장고 프로젝트에 활성화된 User 모델을 참고한다. 
# Django에서 직접적으로 User 모델을 참조하지는 않는다.

class CustomUserChangeForm(UserChangeForm):
    
    class Meta:
        model = get_user_model()
        fields = ('email', 'first_name', 'last_name')
        
        
class CustomUserCreationForm(UserCreationForm):
    
    class Meta(UserCreationForm.Meta):         # Meta도 상속이 가능하다!
        model = get_user_model()
        fields = UserCreationForm.Meta.fields + ('email')
        # UserCreationForm에서는 username, password1, password2를 받는다
```

이후 views.py 수정

```python
# accounts/veiws.py - signup 수정
# 원래라면 update도 해야하지만 이미 CustomUserChangeForm을 만들어 적용해서 안 해도 됨


@require_http_methods(['GET', 'POST'])
def signup(request):
    if request.user.is_authenticated:
        return redirect('articles:index')

    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()
            auth_login(request, user)
            return redirect('articles:index')
    else:
        form = UserCreationForm()
    context = {
        'form': form,
    }
    return render(request, 'accounts/signup.html', context)
```



#### get_user_model()

현재 프로젝트에서 **활성화된 사용자 모델 (active user model)**을 반환한다

django는 User 클래스를 직접 참조하는 대신 `django.contrib.auth.get_user_model()`을 사용할 것을 강조

<= 직접 참조 시 혹시 User 모델이 대체될 경우 문제 발생

 현재 활성화된 모델은 settings.py 의 `AUTH_USER_MODEL`확인.





