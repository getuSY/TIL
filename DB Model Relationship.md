## DB - Model Relationship

### Foreign Key

외래키, 외부키.

관계형 DB에서 한 테이블의 필드 중 다른 테이블의 행을 식별할 수 있는 키.

참조하는 테이블의 속성-필드에 해당하고 참조되는 테이블의 기본 키를 가리킴.

1:N의 관계에서 N쪽이 FK 필드(참조되는 쪽의 PK값을 사용)를 가지게 된다.



**특징**

- 부모 테이블의 유일한 값을 참고 (참조 무결성)
- 반드시 부모 테이블의 기본키일 필요는 없지만 유일한 값이어야 함



#### ForeignKey field

2개의 위치 인자가 반드시 필요

1. 참조하는 model class
2. on_delete 옵션

migrate 작업 시 필드 이름에 _id를 추가하여 데이터베이스 열 이름을 만듦



```python
# articles/models.py

class Comment(models.Model):    # 보통 참조되는 모델명의 단수형 소문자로 쓴다
    article = models.ForeignKey(Article, on_delete=models.CASCADE)  # 2개 인자 필요!
    content = models.CharField(max_length=20)
    created_at = 
    updated_at = 
    
    def __str__(self):
        return self.content
```



**on_delete**

- 외래키가 참조하는 객체가 사라졌을 때 외래키를 가진 객체를 어떻게 처리할 것인지를 정의
- 예시 : 게시글이 사라졌을 때 그 게시글에 달린 댓글은?
- 데이터 무결성(데이터의 정확성과 일관성을 유지하고 보증)을 위해 중요한 옵션
- on_delete 옵션
  - CASCADE : 부모 객체가 삭제됐을 때 이를 참조하는 객체도 참조
  - PROTECT, SET_NULL, SET_DEFAULT, SET(), DO_NOTHING, RESTRICT
  - soft deletion을 위해 다른 옵션을 사용하게 될 수 있다



외래키는 모델이 만들어질 때 가장 마지막에 만들어진다!(**모델명(정의한 필드 이름)_id** 로 생성)

모델명을 소문자 단수로 쓰는 이유 : 어떤 모델을 참조하는지 알 수 있고 관계 모델을 알 수 있다(N:M과 비교)



#### 댓글 생성하기

```bash
$ python manage.py shell_plus
```

```shell
comment = Comment()
```

comment에 외래키 속성을 넣어줄 때

만약 정확하게 테이블 명을 넣어준 경우에는 : comment`.article_id` = `article.pk` 딱 찍어서 넣어줘야 함

모델에서 정의한 필드명으로 넣어준 경우에는 :  comment`.article` = `article` 객체를 통째로 넣어줘도 된다

두 번째 방법을 권장!

```shell
# 참조 대상 article이 있고 설정을 마쳤을 때, 외래키를 통해 한 번에 참조하는 객체를 찾을 수 있다
comment.article = <Article:title>

# 객체를 찾을 수 있기 때문에 바로 속성을 사용해 참조 객체의 내용을 알 수도 있다 => 객체쪽 접근
comment.article.pk = 1
comment.article.content = 'content'

# 속성으로 접근할 수도 있다
comment.article_id = 1

# 안되는 것! 
comment.article_pk
```



댓글 확인하기 위해 admin.site에 등록한다

```python
# articles/admin.py
class ArticleAdmin(admin.ModelAdmin):
    list_display = ()

admin.site.register(Article, ArticleAdmin)
admin.site.register(Comment)
```



#### 1:N 관계 related manager

comment에서 `comment.article`로 객체를 찾는 것 : 참조

article에서 comment를 찾는 것 : 역참조, 그러나 comment와는 달리 참조 가능하게 하는 외래키 등이 없다!

이 때 사용하는 manager : article`.comment_set`



**역참조**

- 1에서 N을 참조하는 것
- <참조당하는 모델명>.<(N쪽의 모델명)_set>
- article.comment는 사용 X! <= 게시글에 댓글이 없을 수도 있고, 실제 Article에는 comment와 관계 작성 X

**참조**

- N에서 1을 찾는 것
- 댓글의 경우 어떤 경우든 참조하고 있는 게시글이 있으므로 외래키를 통해 접근 가능 => ex)comment.article



```shell
article = Article.objects.get(pk=1)
dir(article)   # 사용할 수 있는 모든 method가 나온다
article.comment_set.all()    # 게시글에 쓰여진 댓글 모두 보여준다
comments = article.comment_set.all()  # comments 변수에 할당
for comment in comments:              # template에서 이와 같이 활용할 수 있다
	print(comment.content)
# ^ 결과
# first comment
# second comment
```



**related_name : 역참조 시 사용할 이름('model_set' manager)을 변경 가능 **

```python
# articles/models.py
class Comment(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE, related_name='comments')
```

변경 이후부터는 article.comment_set은 더 이상 사용 불가능.

1:N 관계에서는 related_name을 변경하는 것을 권장하지 않음.

=> N:M등의 관계에서는 어쩔 수 없이 이름을 바꿔야하기 때문에 혼동 가능성이 있다.

=> article`.comment_set`의 경우에 1:N 관계임을 쉽게 파악할 수 있으나 이름을 바꾸면 그렇지 않음





### Comment CREATE

```python
# articles/forms.py
class ArticleForm(models.ModelForm):
    
    class Meta:
        model = Article
        fields = '__all__'
        
        
class CommentForm(forms.ModellForm):
    
    class Meta:
        model = Comment
        fields = '__all__'
```

```python
# articles/views.py
# 코멘트는 디테일 페이지에서 보여야 한다
... + from .models import Article, Comment
from .forms import ArticleForm, CommentForm


@require_safe
def detail(request, pk):
	article = get_object_or_404(Article, pk=pk)
    comment_form = CommentForm()
    context = {
        'article': article,
        'comment_form':comment_form,
    }
    return render(request, 'articles/detail.html', context)
```

```django
<!--articles/detail.html-->
{% extends 'base.html' %}

{% block content %}
  <h1>DETAIL</h1>
  <h3>{{ article.pk }}번째 글</h3>
  <hr>
  <p>제목 : {{ article.title }}</p>
  <p>내용 : {{ article.content }}</p>
  <p>작성 시각 : {{ article.created_at }}</p>
  <p>수정 시각 : {{ article.updated_at }}</p>
  <hr>
  <a href="{% url 'articles:update' article.pk %}">수정</a>
  <form action="{% url 'articles:delete' article.pk %}" method="POST">
    {% csrf_token %}
    <input type="submit" value="삭제">
  </form>
  <a href="{% url 'articles:index' %}">back</a>
  <hr>
  <form action="" method="POST">
  	{% csrf_token %}
    {{ comment_form }}
    <input type="submit">
  </form>
{% endblock content %}

```

- 이대로 진행하면 Comment 모델의 필드를 모두 받아와 게시글 선택과 댓글 내용을 쓸 칸 두개가 출력됨

  => forms.py fields 내용을 수정해야함! 사용자에게 ForeignKey를 직접 선택하게 하거나 노출하면 안 됨!

  ```python
  # articles/forms.py
  class CommentForm(forms.ModellForm):
      
      class Meta:
          model = Comment
          fields = ('content',)
  ```



#### 댓글 작성

```python
# articles/urls.py
app_name = 'articles'
urlpatterns = [
    ...,
    path('<int:pk>/comments/', views.comment_create, name='comment_create')
]   # 댓글을 어떤 게시글에 쓸지가 필요하기 때문에 <int:pk> 필요
```

```django
<!--articles/detail.html-->
...
<form action="{% url 'articles:comment_create' %}" method="POST">
  	{% csrf_token %}
    {{ comment_form }}
    <input type="submit">
  </form>
```

```python
# articles/views.py
def comment_create(request, pk):
    article = Article.objects.get(pk=pk)    # 그에 맞는 article.pk 디테일 페이지로 가야하니까
    comment_form = CommentForm(request.POST)# CommentForm에 content밖에 없다!
    if comment_form.is_valid():
        comment_form.save()
    return redirect('articles:detail', article.pk)
```

- 댓글 출력은 기존 디테일에서 출력되고 있음 => POST 방식 이외 분기 처리가 필요 없다
- 이대로 하면 article_id 값이 없다고 뜬다!
- `.save()`메소드가 자동으로 제공하는 속성 `.save(commit=False)` 사용
- `.save()`가 리턴하는 값은 comment 객체.

```python
# articles/views.py
def comment_create(request, pk):
    article = Article.objects.get(pk=pk)
    comment_form = CommentForm(request.POST)
    if comment_form.is_valid():
        comment = comment_form.save(commit=False)
        comment.article = article
        comment.save()                                         # 또 save를 한다?
    return redirect('articles:detail', article.pk)
```

- 또 save를 할 수 있는 이유  `.save(commit=False)`  : commit의 기본값은 True

  => 실제 DB에 저장이 되는 것 : commit

  =>  `.save(commit=False)` DB에 저장은 안 하고 객체만 만들어준다. 더 값을 추가할 수 있음! 나중에 SAVE.

  저장하기 전에 객체에 관한 사용자 지정 처리를 수해할 때 유용하게 사용할 수 있다.

  **"Create, but don't save the new instance"**

  참고 : https://github.com/django/django/blob/main/django/forms/models.py





### Comment READ

```python
# articles/views.py

@require_safe
def detail(request, pk):
	article = get_object_or_404(Article, pk=pk)
    comment_form = CommentForm()
    commnets = article.comment_set.all()   # 해당 article의 모든 댓글 조회(역참조)
    context = {
        'article': article,
        'comment_form':comment_form,
        'comments':comments,
    }
    return render(request, 'articles/detail.html', context)
```

```django
<!--articles/detail.html-->
{% extends 'base.html' %}

{% block content %}
  <h1>DETAIL</h1>
  <h3>{{ article.pk }}번째 글</h3>
  <hr>
  <p>제목 : {{ article.title }}</p>
  <p>내용 : {{ article.content }}</p>
  <p>작성 시각 : {{ article.created_at }}</p>
  <p>수정 시각 : {{ article.updated_at }}</p>
  <hr>
  <a href="{% url 'articles:update' article.pk %}">수정</a>
  <form action="{% url 'articles:delete' article.pk %}" method="POST">
    {% csrf_token %}
    <input type="submit" value="삭제">
  </form>
  <a href="{% url 'articles:index' %}">back</a>
  <hr>
  <h4>댓글 목록</h4>
  <ul>
      {% for commen in comments %}
        <li>{{ commen.content }}</li>
      {% endfor %}
  </ul>
  <form action="" method="POST">
  	{% csrf_token %}
    {{ comment_form }}
    <input type="submit">
  </form>
{% endblock content %}
```





### Comment DELETE

```python
# articles/urls.py

app_name = 'articles'
urlpatterns = [
    ...,
    path('<int:pk>/comments/', views.comment_create, name='comment_create'), #create
    path('<int:comment_pk>/comments/delete/', views.comment_delete, name='comment_delelte'),
]
```

```python
# articles/views.py

def comment_delete(request, article.pk, comment_pk):
    comment = Comment.objects.get(pk=comment_pk)
    # article = comment.article.pk  이렇게 받으면 redirect 두번째 인자는 article
    comment.delete()
    return redirect('articles:detail', article.pk)
```

- redirect 시 article.pk의 detail 페이지로 가기 위한 두 가지 방법
  - `article = comment.article.pk`
  - Variable Routing으로 받는 방법 `comment_delete(request, article_pk, comment_pk)` **채택!**

```python
# articles/urls.py 변경
# urls 구조와 일관성을 위해서 variable routing으로 받는다

urlpatterns = [
    ...,
    path('<int:pk>/comments/', views.comment_create, name='comment_create'), #create
    path('<int:article_pk>/comments/<int:comment_pk>/delete/', views.comment_delete, name='comment_delelte'),
]
```

```django
<!--articles/detail.html 댓글 삭제 버튼 만들기-->
{% extends 'base.html' %}

{% block content %}
  ...
  <hr>
  <h4>댓글 목록</h4>
  <ul> <!--보통 댓글 삭제 버튼은 댓글 옆에 나타나니까!-->
      {% for commen in comments %}
        <li>
            {{ commen.content }}
            <form action="{% url 'articles:comment_delete' article.pk commen.pk %}" method="POST">
                {% csrf_token %}
                <input type="submit" value="삭제">
            </form>
        </li>
      {% endfor %}
  </ul>
  <form action="" method="POST">
  	{% csrf_token %}
    {{ comment_form }}
    <input type="submit">
  </form>
{% endblock content %}
```

- 댓글 삭제를 위한 form action 작성 주의! pk인자가 두 개가 필요하다!



### Comment CREATE, DELETE 정리

```python
# articles/views.py

@require_POST
def comment_create(request, pk):
    if request.user.is_authenticated: 
        article = get_object_or_404(Article, pk=pk)
        comment_form = CommentForm(request.POST)
        if comment_form.is_valid():
            comment = comment_form.save(commit=False)
            comment.article = article
            comment.save()
    	return redirect('articles:detail', article.pk)
    return redirect('accounts:login')


@require_POST
def comment_delete(request, article_pk, comment_pk):
    if request.user.is_authenticated:
        comment = get_object_or_404(Comment, pk=comment_pk)
        comment.delete()
    return redirect('articles:detail', article_pk)
```



**+ 추가 기능**

```django
<!--articles/detail.html -->
<!--articles/detail.html 댓글 삭제 버튼 만들기-->
{% extends 'base.html' %}

{% block content %}
  ...
  <hr>
  <h4>댓글 목록</h4>
  <ul> <!--보통 댓글 삭제 버튼은 댓글 옆에 나타나니까!-->
      {% for commen in comments %}
        <li>
            {{ commen.content }}
            <form action="{% url 'articles:comment_delete' article.pk commen.pk %}" method="POST">                                  <!--인자 두 개 받아야한다는 것 주의! -->
                {% csrf_token %}
                <input type="submit" value="삭제">
            </form>
        </li>
      {% endfor %}
  </ul>
  <form action="" method="POST">
  	{% csrf_token %}
    {{ comment_form }}
    <input type="submit">
  </form>
{% endblock content %}
```





### User - Article (1:N)

 ```python
 # articles/models.py
 # foreign key는 N에게 주어야 한다!
 
 from django.db import models
 
 class Article(models.Model):
     # model의 fields 명은 단수 소문자로!
     user = models.ForeignKey('user model', on_delete=models.CASCADE)
     title = models.CharField(max_length=10)
     content = models.TextField()
     created_at = models.DateTimeField(auto_now_add=True)
     updated_at = models.DateTimeField(auto_now=True)
 
     def __str__(self):
         return self.title
 
 ```

- 모델에서의 user model 참조는 함수로 하지 않는다! get_user_model() 쓰지 않는다!

  ```python
  class Article(models.Model):
      user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
  ```

  `settings.AUTH_USER_MODEL` 반환값은 문자열!

  이후 migration 과정을 거친다.

- User model 참조 방법

  - `settings.AUTH_USER_MODEL` : return **'str'**. models.py에서만 쓴다!  **장고에서 강조!**
  - `get_user_model()` : retrun **object**. models.py가 아닌 다른 곳에서 유저 모델을 참조할 때 사용한다!
  - 이유 : django에서는 app을 INSTALLED_APPS에 설치된 순서대로 순차적으로 APP을 import하는데, 각 앱의 model을 import할 때 get_user_model()을 import하게 된다면 아직 뒷 앱, accounts의 모델이 처리되지 않은 상태에서 객체를 불러오게 된다. 만약 앱 순서를 바꾼다면 해결은 될 수 있지만, 장고 app 등록 및 실행 순서에 관계 없이 유저 모델을 제대로 불러오기 위해 settings.AUTH_USER_MODEL 사용!



#### migration 과정

기존 데이터에는 새로 추가되는 필드에 null값이 허용되지 않는다면 외래키를 무엇으로 할 것인지 정의해야한다!

user_id

해당 화면에서 1을 입력하면 현재 화면에서 기본 값 설정, 

이후 이경우 다시 게시글을 쓰는 화면에서 user를 선택하는 칸이 나타나게 됨! => articles/forms.py 수정

```python
# articles/forms.py
class ArticleForm(forms.ModelForm):

    class Meta:
        model = Article
        exclude = ('user',)
```

이 form에는 user 정보가 빠져있기 때문에 이대로 글 작성하려 할 시 user가 null이 될 수 있다! => views.py 수정

```python
# articles/views.py

@login_required
@require_http_methods(['GET', 'POST'])
def create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save(commit=False)     # 일단 저장 말고 객체만 반환!
            article.user = request.user           # 반환된 객체의 user 지정!
            article.save()                        # 이제 저장!
            return redirect('articles:detail', article.pk)
    else:
        form = ArticleForm()
    context = {
        'form': form,
    }
    return render(request, 'articles/create.html', context)
```

작성자가 누군지 저장되었기 때문에 이제 작성자만 글 삭제가 가능하도록 할 수 있다!



#### DELETE

```python
# articles/views.py

@require_POST
def delete(request, pk):
    article = get_object_or_404(Article, pk=pk)
    if request.user.is_authticated:
        # 이 위치에 article 넣어도 된다
        if request.user == article.user:  # 외래키 필드가 추가되어 있어 이제 작성자만 삭제 가능!
            article.delete()
    return redirect('articles:index')
```



#### UPDATE

DELETE와 동일한 조건으로 작성하면 가능!

```python
# articles/views.py

@login_required
@require_http_methods(['GET', 'POST'])
def update(request, pk): # 작성자가 아니라면 수정 페이지조차 접속 되면 안 된다!
    article = get_object_or_404(Article, pk=pk)
    if request.user == article.user:
        if request.method == 'POST':
            form = ArticleForm(request.POST, instance=article)
            if form.is_valid():
                article = form.save()
                return redirect('articles:detail', article.pk)
        else:
            form = ArticleForm(instance=article)
    else:
        return redirect('articles:index')  # 작성자가 아니라면 그냥 메인 페이지로!
    context = {
        'article': article,
        'form': form,
    }
    return render(request, 'articles/update.html', context)
```

```django
<!--articles/index.html -->
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
	<p>작성자 : {{ article.user }}</p>                      <!-- 작성자를 보여준다! -->
    <p>글 번호: {{ article.pk }}</p>  
    <p>글 제목: {{ article.title }}</p>
    <p>글 내용: {{ article.content }}</p>
    <a href="{% url 'articles:detail' article.pk %}">DETAIL</a>
    <hr>
  {% endfor %}
{% endblock content %}
```



#### READ

애초에 작성자가 다르다면 수정, 삭제 버튼을 안 보여주면 된다!

```django
<!-- articles/detail.html -->
{% extends 'base.html' %}

{% block content %}
  <h1>DETAIL</h1>
  <h3>{{ article.pk }}번째 글</h3>
  <hr>
  <p>제목 : {{ article.title }}</p>
  <p>내용 : {{ article.content }}</p>
  <p>작성 시각 : {{ article.created_at }}</p>
  <p>수정 시각 : {{ article.updated_at }}</p>
  <hr>
  {% if request.user == article.user %}         <!-- 작성자가 같을 때만 버튼 보여주기 -->
  <a href="{% url 'articles:update' article.pk %}">수정</a>
    <form action="{% url 'articles:delete' article.pk %}" method="POST">
      {% csrf_token %}
      <input type="submit" value="삭제">
    </form>
  {% endif %}
  ...
```

- request.user를 설정 안 해도 받아올 수 있는 것은 settings.py의 TEMPLATES/'OPTIONS'에서 context_processors가 제공하는 것들 때문! 모든 템플릿에서 사용할 수 있다!





### User - Comment (1:N)

앞과 같은 과정을 반복한다!



1. 외래키 설정

   ```python
   # articles/models.py
   class Comment(models.Model):                        # 댓글은 외래키가 두 개가 된다!
       article = models.ForeignKey(Article, on_delete=models.CASCADE)
       user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
       content = models.CharField(max_length=200)
       created_at = models.DateTimeField(auto_now_add=True)
       updated_at = models.DateTimeField(auto_now=True)
   
       def __str__(self):
           return self.content
   ```

   ```bash
   $ python manage.py makemigrations
   $ python manage.py migrate
   ```

   makemigrations 후 기존 게시글에서 user 란을 어떻게 할 것인지 묻는 창이 뜨면, 일단 1 + Enter, 1 + Enter!

   이런 부분들 때문에 모델 설계가 중요하다!

2. migration

   ```bash
   $ python manage.py makemigrations
   $ python manage.py migrate
   ```

   makemigrations 후 기존 게시글에서 user 란을 어떻게 할 것인지 묻는 창이 뜨면, 일단 1 + Enter, 1 + Enter!

   이런 부분들 때문에 모델 설계가 중요하다!

3. 불필요한 필드 출력 확인 및 수정

   ```python
   # articles/forms.py
   
   class CommentForm()
   ```

4. 댓글 작성 시 작성자 정보 추가되도록 코드 작성

   ```python
   # articles/views.py
   @require_POST
   def comment_create(request, pk):
       if request.user.is_authenticated: 
           article = get_object_or_404(Article, pk=pk)
           comment_form = CommentForm(request.POST)
           if request.user == article.user:
           if comment_form.is_valid():
               comment = comment_form.save(commit=False)
               comment.article = article
               comment.user = request.user          # 추가!
               comment.save()
       	return redirect('articles:detail', article.pk)
       return redirect('accounts:login')
   
   
   @require_POST
   def comment_delete(request, article_pk, comment_pk):
       if request.user.is_authenticated:
           comment = get_object_or_404(Comment, pk=comment_pk)
           comment.delete()
       return redirect('articles:detail', article_pk)
   ```

   

#### CREATE

```python
# articles/views.py
@require_POST
def comment_create(request, pk):
    if request.user.is_authenticated: 
        article = get_object_or_404(Article, pk=pk)
        comment_form = CommentForm(request.POST)
        if request.user == article.user:
        if comment_form.is_valid():
            comment = comment_form.save(commit=False)
            comment.article = article
            comment.user = request.user          # 추가!
            comment.save()
    	return redirect('articles:detail', article.pk)
    return redirect('accounts:login')

```





#### READ + DELETE

비로그인 시 댓글 Form 출력 숨기기 + 댓글 작성자 출력하기 + 작성자만 댓글 삭제 가능하게 하기

```django
<!--articles/detail.html 댓글 삭제 버튼 만들기-->
{% extends 'base.html' %}

{% block content %}
  ...
  <hr>
  <h4>댓글 목록</h4>
  <ul> 
      {% for commen in comments %}
        <li>
            {{ commen.user }} - {{ commen.content }} <!-- 작성자 추가! -->
            {% if request.user == commen.user %}  <!-- 작성자일 때에만 삭제 가능하도록! -->
              <form action="{% url 'articles:comment_delete' article.pk commen.pk %}" method="POST">
                  {% csrf_token %}
                  <input type="submit" value="삭제">
              </form>
            {% endif %}
        </li>
      {% empty %}
        <p>댓글이 없습니다.</p>
      {% endfor %}
  </ul>
  {% if request.user == commen.user %}
    <form action="" method="POST">
  	  {% csrf_token %}
      {{ comment_form }}
      <input type="submit">
   {% else %}
      <a href="{% url 'accounts:login' %}">댓글을 작성하려면 로그인하세요.</a>
   {% endif %}
    </form>
{% endblock content %}
```

템플릿에서는 막아놨지만 url을 통해 강제 입력 가능하기 때문에 view함수를 수정한다!



##### UPDATE 과정?

=> 댓글을 수정한다고 해서 다른 페이지로 이동하는 것은 아니다.

=> 자바스크립트를 통해 페이지 일부만 동적으로 바꿔야 한다.



