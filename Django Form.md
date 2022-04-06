## Django Form

#### Django Form : 

사용자가 입력한 데이터를 검증하는 작업 "유효성 검증", 이 과정을 코드로 모두 구현하는 것은 어렵기에 반복 코드를 줄여 이 작업을 쉽게 해준다.

- form, input 등을 반복적으로 입력하는 걸 줄여주기 위함!

Form은 django 유효성 검사 도구 중 하나로 외부의 악의적 공격 및 데이터 손상에 대한 중요한 방어 수단

- 렌더링을 위한 데이터 준비 및 재구성
- 데이터에 대한 HTML forms 생성
- 클라이언트로부터 받은 데이터 수신 및 처리

**Form Class 작성 필요!** - 핵심

- field, field 배치, 디스플레이 widget, label, 초기값, 유효하지 않은 field에 관한 에러 메시지 결정



### Form 선언

앱 내 forms.py를 만든다!

```python
from django import forms

class ArticleForm(forms.Form):
    title = forms.CharField(max_ldngth=10)
    content = forms.CharField()            # 왜 forms에는 textfield가 없나?
```

- forms.TextField가 없는 이유? (model.TextField)
  - 제공하지 않아서 쓸 수가 없는 건가?
- forms.CharField는 max_length가 필수가 아니다!  = > model에선 필수였음



```python
# views.py
def new(request):
    form = ArticleForm
    context = {
        'form' = form,
    }
    return render(request, 'articles/new',context)
```

```html
<!--new.html -->
{{ form }}  <!-- 변수 호출 form -->
```

- IDE로 찍어보면 input, label, name 등까지 다 만들어져 있다.



#### Form rendering options

<label>, <input> 쌍의 출력 옵션!  <= 없으면 input은 inline 속성이므로 옆으로 한 줄 출력되!

1. as_p() : 각 필드가 p태그로 감싸져서 렌더링 됨
2. as_ul() : 각 필드가 li 태그로 감싸져서 렌더링 됨.  <ul> 태그는 직접 작성해야 함!
3. as_tabel() : 각 필드가 tr 태그 행으로 감싸져서 렌더링 됨. <table> 태그는 직접 작성!





#### HTML input 요소 표현 방법

input의 속성에는 여러 가지가 있다. <type>이를 다른 방식으로 바꿔야 한다. 

1. FormField : FormField (CharField, 등등)을 바꾼다
2. Widgets : 웹 페이지의 HTML input 요소 렌더링. FormField 내에서 사용.

```python
from django import forms

class ArticleForm(forms.Form):
    REGION_A = 'sl'
    REGION_B = 'dj'
    REGION_C = 'gj'
    REGION_CHOICES = [
        (REGION_A, '서울'),
        (REGION_B, '부산'),
        (REGION_C, '광주'),
    ]
    title = forms.CharField(max_ldngth=10)
    content = forms.CharField(widget=Forms.Textarea)
    region = forms.ChoiceField(widget=Forms.Select) 
    # choice필드는 select가 기본! 인자는 튜플로 받아야한다!
```

- input 속성이 textarea로 바뀜. (Forms.Password 하면 input type = password가 된다)
- FormField로 먼저 작성을 하고, FormField로 작성할 수 없는 경우에는 Widgets으로 작성해야한다.
- Widgets 목록 : https://docs.djangoproject.com/en/4.0/ref/forms/widgets/#widgets-handling-input-of-text
- Form Fields와 혼동되어서는 안됨. 단순히 input element의 처리만 한다.
- 유효성 검사는 여전히 Form Fields가 한다!
- REGION_ 을 왜 저렇게 작성 했는가? Django Coding Style 문서 참조 : https://docs.djangoproject.com/en/dev/internals/contributing/writing-code/coding-style/





### ModelForm

Django Form을 사용하다 보면 사용자로부터 input을 받기 위해 Model에 정의한 필드를 Form에 재정의하는 행위가 중복됨.(models.py, forms.py)



#### ModelForm 선언

일반 Form Class와 같은 방식으로 생성, view에서 사용 가능

```python
# forms.py
from django import forms
from .models import Article

class ArticleForm(forms.ModelForm):
    
    class Meta:
        model = Article
        fields = '__all__' # 모든 필드를 출력 가능, 아니라면 ['title', 'content',...]
        # exclude = ('title',)

```

- forms 라이브러리에서 파생된 ModelForm 클래스를 상속한다
- 정의한 클래스 안에 **Meta 클래스**에서 어떤 모델을 기반으로 Form을 작성할 것인지를 저장
  - 모델의 정보를 작성하는 곳, ModelForm을 사용할 경우 사용할 모델이 있어야 하는데 Meta가 이를 구성 : 해당 모델에 정의한 필드 정보를 폼에 적용하기 위한 것!
  - Meta Date : 데이터에 관한 데이터
  - widgets 설정도 따로 안해줘도 된다! <= Model에서 다 설정을 했기 때문에!

- 그렇다면 ModelForm이 항상 나은가?

  - 사용자로부터 input을 받을 때 항상 데이터를 저장하지는 않는다. 

    (예시) 로그인 > 그냥 인증 절차만 거치므로 데이터베이스와 연관이 없다 <= Form 사용!

  - 상황에 따라 사용자로부터 받은 데이터가 단순히 데이터로만 사용이 되는지, 데이터베이스의 구조와 동일하게 저장이 되는가 판단해 From, ModelForm 선택

- exclude = : 리스트, 튜플로 모두 받을 수 있음. 출력에서 제외하는 것. 지정한 것만 빼고 받아온다.
  - **주의!** fields와 exclude는 동시에 사용할 수 없다! 모두 쓰는 것보다 제외하는 걸 쓰는 게 빠를 때 사용!
- Form과 DB는 밀접한 연관(Form에서 받아 저장하기 때문에). DB와 비슷한 구조를 사용한다면 ModelForm을 사용하는 것이 낫다! ModelForm을 사용한다면 이를 통해 DB의 구조도 유추해볼 수 있다! <= DB에 맞춰서 설계가 되어 있기 때문에!



#### * ModelForm이 해주는 것

1. 모델로 만들어진 테이블 필드 속성에 맞는 html element를 만들어준다.
2. 이를 통해 받은 데이터를 view 함수에서 유효성 검사를 할 수 있도록 해준다.





### view 수정

```python
def create(request):
    form = ArticleForm(request.POST) # request.POST를 통해 한번에 받아온다!
    # 유효성 검사
    if form.is_valid():
        # save 후 생성된 객체가 리턴됨 => article 객체 지정. 없으면 redirect article 지정 안됨!
        article = form.save()   # DB에 저장하기 위해선 필수 작업! 
        return redirect('articles:detail', article.pk)
    return redirect('article:new')
```

- 이전 모델 사용 시 view에서 굉장히 많은 변수를 받아와야 했다.

- Form Fields : 유효성 검증에 대해 별도의 복잡한 코드를 작성하지 않아도 된다는 게 장점!

- is_valid() method : 데이터 유효성 검사 실행, 데이터가 유효한지 여부를 bool로 반환

  - 유효성 검사 : 데이터가 특정 조건에 충족하는지 확인하는 작업. 데이터베이스 각 필드 조건에 맞지 않는 데이터가 서버로 전송되거나 저장되지 않게 하기 위한 것. 

- save() method : 기존 model save 와는 다름. Form에 바인딩된 데이터에서 객체를 만들고 저장. 

  - 유효성 검사를 통과하지 못한 경우 form.errors를 써 에러 확인할 수 있음

  - ModelForm의 하위 클래스는 기존 모델 인스턴스를 키워드 인자 instance로 받아들일 수 있다.

    ```python
    form  = ArticleForm(request.POST)
    # create
    new_article = f.save()
    # update
    article = Article.objects.get(pk=1)
    # instance를 통해 기존 객체가 있는가 없는가를 확인하고 받아옴, save가 판단하여 저장, 수정한다.
    form = ArticleForm(request.POST, instance=article)
    form.save()
    ```



#### VIEW 대격변

##### CREATE

기존에는 create 작업을 위해 view 내에서 new, create 두 개가 필요했다.

new <- 조회를 위한 GET 메소드 사용

create <- 실제 DB 작성 및 저장을 위해 POST 메소드 사용

그렇다면 이 두 가지를 합쳐서 하나의 URL, VIEW로 만들고, 메소드만 따로 분리할 수 있지 않을까?

```python
# 기존 new와 create
def new(request):
    form = ArticleForm()
    context = {
        'form': form,
    }
    return render(request, 'articles/new.html', context)

def create(request):   # 위와 동일
    form = ArticleForm(request.POST)
    '''
    위 create 함수와 동일
    '''
    return redirect('articles:new')
```

```python
# new와 create를 합친다!
def create(request):
    
    if request.method == 'POST':  # CREATE
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save() 
            return redirect('articles:detail', article.pk)
    else:    # new
        form = ArticleForm()
        # 유효성 검사를 통과하지 못했을 때를 위해 context, return indent 빼주기!
    context = {
        'form': form,
    }
    return render(request, 'articles/new.html', context)
```

- 위에서 그대로 붙이면 module 'articles.views' 에 new 뷰함수가 사라져 에러가 난다!

  ```python
  # 기존 views.py에서
  path('new/', views.new, name='new')   # <= 삭제!!
  path('create/', views.create, name='create')   # GET/POST 구분하여 사용할 것
  ```

  new의 흔적을 지워야 한다.

  - 템플릿 new 이름 바꾸기
  - context와 return의 indent 주의! (is_valid() 통과하지 못했을 시를 고려!)
  - urls.py 및 views.py 수정
  - 



##### UPDATE

new, create과 비슷하게 하나로 합친 후 바꿀 수 있다!

```python
# 일단 pass로 채워가며 큰 틀부터, else부터 짠다!
def update(reqeuest, pk):
    if request.method == 'POST':
        pass
    else:
        pass
    return 
```

```python
def update(reqeuest, pk):
    article = Article.objects.get(pk=pk)
    if request.method == 'POST':    # edit
        form = ArticleForm(requset.POST, instance=article)
        # instance=article 없으면 create가 된다!
        if form.is_valid():
            article = form.save()
            return redirect('articles:upate', article.pk)
    else:     # update
        form = ArticleForm()
    context = {
        'form': form, 
        'article':article,
    }
    return render(request,'articles/update.html', context)  # 구 edit.html
```

- edit.html 이름 바꾸기, views.py, urls.py 수정하기! 



##### DELETE

바뀌는 것이 별로 없다!



##### * forms.py의 위치

Form Class는 forms.py 뿐만 아니라 어느 위치에 두어도 상관은 없지만 app 폴더/forms.py에 작성하는 것이 일반적인 구조.





### Form & ModelForm

- Form: 어떤 모델에 저장해야 하는지 알 수 없으므로 유효성 검사 이후 데이터 딕셔너리에서 데이터를 가져온 후 .save() 호출. 모델에 연관되지 않은 데이터를 받을 때 사용.
- ModelForm : 장고가 해당 모델에서 양식에 필요한 데이터의 대부분을 이미 정의했기 때문에 어떤 레코드를 만들어야 할지 알고 있으므로 바로 .save() 호출 가능.





### Widgets 활용

Form Class에서는 따로 쓸 수 있었다.

ModelForm에서는 **widgets= {}** 변수 지정하여 작성할 수 있음. **BUT** 권장하지 않는다.

```python
```







### 수동으로 바꾸기

```python
# 다른 방식으로 form 출력하기
```

- working with forms documentaion 참고 : https://docs.djangoproject.com/en/4.0/topics/forms/





### * Bootstrap 함께 활용

- 위젯에 직접 클래스를 넣는 경우(base.html에 cdn이 이미 들어있는 경우, 아니면 넣어야함)

  ```python
  widgets = forms.Textarea(
  	att
  )
  ```

- django-bootstrap v5 : form class에 부트스트랩을 적용시켜주는 라이브러리

  ```bash
  $ pip install django-bootstrap-v5
  $ pip freeze > requirements.txt
  ```

  ```python
  # settings.py
  INSTALLED_APPS = [
      'bootstrap5'           # <- 추가!
  ]
  ```

  ```django
  <!--bootstrap5를 쓰기 위해서는 맨 위에 load 지정해야함! -->
  {% load bootstrap5 %}
  
  {% csrf_token %}
  {% bootstrap_form form %}   <!-- load 했기 때문에 쓸 수 있는 form 앞 태그 -->
  ```

  - 단점 : 자율성이 제한됨. custom 어려워진다.
  - https://django-bootstrap-v5.readthedocs.io/en/latest/installation.html





##### 번외 : NoReverseMatch at /~~/

이 에러는 해당되는 템플릿의 url만 보면 된다! 해당 페이지의 url만!
