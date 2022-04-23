## REST API - M:N

### REST API - M:N Relationship

```python
# articles/modles.py card 추가 모델 작성

from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)


class Comment(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
# 이전까지 작업한 models

class Card(models.Model):   # manytomany 어디 작성하든 상관 없다
    articles = models.ManyToManyField(Articles, related_name='cards')
    name = models.CharField(max_length=100)
```

임의 데이터 생성

```bash
$ python manage.py seed --number=10
```

serializers

```python
# articles/serializers.py
# 이전까지 작성한 serializers. 
# 순서가 복잡해질 수 있다 => 유지보수 입장에서 모듈을 나눌 필요가 있다.

from rest_framework import serializers
from .models import Article, Comment


class ArticleListSerializer(serializers.ModelSerializer):

    class Meta:
        model = Article
        fields = ('id', 'title',)


class CommentSerializer(serializers.ModelSerializer):

    class Meta:
        model = Comment
        fields = '__all__'
        read_only_fields = ('article',)


class ArticleSerializer(serializers.ModelSerializer):
    # comment_set = serializers.PrimaryKeyRelatedField(many=True, read_only=True)
    comment_set = CommentSerializer(many=True, read_only=True)
    comment_count = serializers.IntegerField(source='comment_set.count', read_only=True)

    class Meta:
        model = Article
        fields = '__all__'
```

serializers 폴더 생성

=> serializers/article.py, serializers/comment.py 생성

```python
# serializers/article.py

from rest_framework import serializers
from ..models import Article
from .comment import CommentSerializer     # 나눠진 모듈에서 가져오기

class ArticleListSerializer(serializers.ModelSerializer):

    class Meta:
        model = Article
        fields = ('id', 'title',)

class ArticleSerializer(serializers.ModelSerializer):
    comment_set = CommentSerializer(many=True, read_only=True)
    comment_count = serializers.IntegerField(source='comment_set.count', read_only=True)

    class Meta:
        model = Article
        fields = '__all__'
```

```python
# serializers/comment.py

from rest_framework import serializers
from ..models import Comment

class CommentSerializer(serializers.ModelSerializer):

    class Meta:
        model = Comment
        fields = '__all__'
        read_only_fields = ('article',)
```

```python
# serializers/card.py

from rest_framework import serializers
from ..models import Card

class CardSerializer(serializers.MdoelSerializer):
    
    class Meta:
        model = Card
        fields = '__all__'
```

serializers의 구조가 바뀌었기 때문에 views에서 import한 것들을 따로 처리해주어야 함!

```python
# articles/urls.py

from django.urls import path
from . import views


urlpatterns = [
    path('articles/', views.article_list),
    path('articles/<int:article_pk>/', views.article_detail),
    path('articles/<int:article_pk>/comments/', views.comment_create),
    path('comments/', views.comment_list),
    path('comments/<int:comment_pk>/', views.comment_detail),
    path('cards/', views.card_list),
]
```

기존 serializers 파일은 지우고 import했던 것들 처리해주기!

```python
# articels/views.py

from rest_framework import status
from rest_framework.response import Response
from rest_framework.decorators import api_view
from django.shortcuts import get_list_or_404, get_object_or_404
# from .serializers import ArticleListSerializer, ArticleSerializer, CommentSerializer
from .serializers.article import ArticleSerializer, ArticleListSerializer
from .serializers.comment import CommentSerializer
from .serializers.card import CardSerializer   # 총 세개 임포트 구문으로 바뀜!!

from .models import Article, Comment
from articles import serializers

# Create your views here.
# @api_view()
@api_view(['GET', 'POST'])
def article_list(request):
    if request.method == 'GET':
        # articles = Article.objects.all()
        articles = get_list_or_404(Article)
        serializer = ArticleListSerializer(articles, many=True)
        return Response(serializer.data)

    elif request.method == 'POST':
        # print(request.data)
        serializer = ArticleSerializer(data=request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        # return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view(['GET', 'DELETE', 'PUT'])
def article_detail(request, article_pk):
    article = get_object_or_404(Article, pk=article_pk)

    if request.method == 'GET':
        serializer = ArticleSerializer(article)
        return Response(serializer.data)

    elif request.method == 'DELETE':
        article.delete()
        data = {
            'delete': f'데이터 {article_pk}번이 삭제되었습니다.',
        }
        return Response(data, status=status.HTTP_204_NO_CONTENT)

    elif request.method == 'PUT':
        serializer = ArticleSerializer(article, request.data)
        # serializer = ArticleSerializer(article, data=request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data)


@api_view(['GET'])
def comment_list(request):
    comments = get_list_or_404(Comment)
    serializer = CommentSerializer(comments, many=True)
    return Response(serializer.data)


@api_view(['GET', 'DELETE', 'PUT'])
def comment_detail(request, comment_pk):
    comment = get_object_or_404(Comment, pk=comment_pk)
    
    if request.method == 'GET':
        serializer = CommentSerializer(comment)
        return Response(serializer.data) 

    elif request.method == 'DELETE':
        comment.delete()
        data = {
            'delete': f'데이터 {comment_pk}번이 삭제되었습니다.',
        }
        return Response(data, status=status.HTTP_204_NO_CONTENT)

    elif request.method == 'PUT':
        serializer = CommentSerializer(comment, request.data)
        # serializer = CommentSerializer(comment, data=request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data)


@api_view(['POST'])
def comment_create(request, article_pk):
    article = get_object_or_404(Article, pk=article_pk)
    serializer = CommentSerializer(data=request.data)
    if serializer.is_valid(raise_exception=True):
        serializer.save(article=article)
        return Response(serializer.data, status=status.HTTP_201_CREATED)

# 새 부분
@api_view([])
def card_list(request):
    cards = get_list_or_404(Card)
    serializer = CardSerializer(cards, many=True)
    return Response(serializer.data)
```

- manytomany field에 article을 작성했기 때문에 card list에 article이 함께 출력

  => articles 관련에는 안 뜨는 게 당연! => 필드 작성한 것이 없기 때문이다.

- article_detail에서 card 관련을 보고 싶다면 관련 필드 작성해줘야함

```python
# serializers/article.py

from rest_framework import serializers
from ..models import Article
from .comment import CommentSerializer
from .card import CardSerializer    # card 가져온다!

class ArticleListSerializer(serializers.ModelSerializer):

    class Meta:
        model = Article
        fields = ('id', 'title',)

class ArticleSerializer(serializers.ModelSerializer):
    comment_set = CommentSerializer(many=True, read_only=True)
    comment_count = serializers.IntegerField(source='comment_set.count', read_only=True)
    cards = CardSerializer(many=True, read_only=True)    # 추가 작성

    class Meta:
        model = Article
        fields = '__all__'
```



post 방식에서는 생각해봐야할 것이 있을 것!

```python
# articles/urls.py

urlpatterns = [
    ...,
    path('cards/<int:card_pk>/', views.card_detail),
    path('<int:card_pk>/register/<int:article_pk>/', views.register),
]
```

```python
# articles/views.py
# card_detail은 다른 detail이랑 거의 똑같다!

...

@api_view(['GET'])
def card_list(request):
    cards = get_list_or_404(Card)
    serializer = CardSerializer(cards, many=True)
    return Response(serializer.data)

@api_view(['GET', 'DELETE', 'PUT'])
class card_detail(request, card_pk):
    card = get_object_or_404(Card, pk=card_pk)
    
    if request.method == 'GET':
        serializer = CardSerializer(card)
        return Response(serializer.data)
    
    elif request.method == 'PUT':
        pass
    
    elif request.method == 'DELETE':
        pass
    
    
@api_view(['POST'])
class register(request, card_pk, article_pk):
    
    card = get_object_or_404(Card, pk=card_pk)
    article = get_object_or_404(Article, pk=article_pk)
    
    if card.articles.filter(pk=article_pk).exists():  # 이미 해당 게시글에 카드가
        card.articles.remove(article)      # 있다면 삭제
    else:
        card.articles.add(article)         # 없다면 추가
    serializer = CardSerializer(card)
    return Response(serializer.data)
```





### REST API 문서화

#### drf-yasg 라이브러리

Yet another Swagger generator

API 서버를 설계하고 문서화하는데 도움을 주는 라이브러리

참조 : https://drf-yasg.readthedocs.io/en/stable/

```bash
$ pip install -U drf-yasg
```

```python
# settings 등록

INSTALLED_APPS = [
   ...
   'django.contrib.staticfiles',  # 이미 있는 것, 이 아래로 작성을 해주길 강조!
   'drf_yasg',
   ...
]
```

```python
# urls.py 등록
# 필요한 부분만 가지고 오면 된다 => import와 schema, swagger만

# articles/urls.py로!
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

schema_view = get_schema_view(                 # 일종의 변수화?
   openapi.Info(
      title="SSAFY Django API",
      default_version='v1',                    # 위 두개는 필수, 아래는 선택
      description="Test description",
      terms_of_service="https://www.google.com/policies/terms/",
      contact=openapi.Contact(email="contact@snippets.local"),
      license=openapi.License(name="BSD License"),
   ),
   public=True,
   # permission_classes=[permissions.AllowAny], 권한 관련된 부분은 안 하니 삭제
)

urlpatterns = [
    ...
    path('cards/<int:card_pk>/', views.card_detail),
    path('<int:card_pk>/register/<int:article_pk>/', views.register),
    path('swagger/', schema_views.with_ui('swagger')),
]  # 'swagger' 말고 'redoc' 하면 다른 형태의 api 볼 수 있다
```

```python
# drf-yasg quickstart 기본 사항 - 참고만

...
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

...

schema_view = get_schema_view(
   openapi.Info(
      title="Snippets API",
      default_version='v1',
       # 아래부터는 선택인자
      description="Test description",
      terms_of_service="https://www.google.com/policies/terms/",
      contact=openapi.Contact(email="contact@snippets.local"),
      license=openapi.License(name="BSD License"),
   ),
   public=True,
   permission_classes=[permissions.AllowAny],
)

urlpatterns = [
   re_path(r'^swagger(?P<format>\.json|\.yaml)$', schema_view.without_ui(cache_timeout=0), name='schema-json'),
   re_path(r'^swagger/$', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
   re_path(r'^redoc/$', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
   ...
]
```





### Fixtures

how to provied initial data for models

앱을 처음 설정할 때 미리 준비된 데이터로 데이터베이스를 미리 채우는 것이 필요한 상황이 있음.

마이그레이션 또는 fixtures와 함께 초기 데이터를 제공.

기존에 들어있는 데이터만 추출해서 제공을 하자?



**fixtures** : 데이터 베이스의 serialized된 내용을 포함하는 파일 모음

django가 fixtures 파일을 찾는 경로가 정해져 있다 => `app/fixtures/`



#### dumpdata

응용 프로그램과 관련된 데이터베이스의 모든 데이터를 표준 출력으로 출력(추출?)

```bash
$ python manage.py dumpdata [app_label.<modulename>] [app_label.<modelname>]...]
# --indent 옵션을 주지 않으면 한줄로 작성된다!
$ python manage.py dumpadata --indent 4 auth.User > user.json
# auth앱의 user 모델데이터를 indent 4칸의 user.json 파일로 출력

# loaddata
$ python manage.py loaddata user.json
```



#### fixtures 실습

```bash
$ python manage.py seed articles --number=10
$ python manage.py dumpdata --indent 4 articles.article > article.json
$ python manage.py dumpdata --indent 4 articles.comment > comments.json
$ python manage.py dumpdata --indent 4 accounts.user > users.json
```

기본적으로 장고가 인식하는 위치는 `app/fixtures/`

=> app 내에 fixtures 폴더 생성 후 json 파일을 옮겨준다.

이후 파일을 받은 다른 사람은 database가 없기 때문에 dump 파일을 로드한다.(있다면 삭제)

```bash
$ python manage.py loaddata articles.json comments.json users.json
```

만약에 fixtures를 안전하게 읽어오고 싶다면 각 앱 내에 fixtures 폴더 내 다시 app 이름 폴더를 생성한 후 그 안에 파일을 옮겨준다. 

<= 같은 이름의 fixtures 파일이 생겼을 경우 장고는 `app_폴더/fixtures/`까지만 읽기 때문에 다른 걸 읽어올 수 있기 때문!

```bash
$ python manage.py loaddata articles/articles.json articles/comments.json accounts/users.json
```

장고가 읽어오는 fixtures 폴더 이후의 디렉토리를 붙여 작성!



##### ※ 주의사항

fixtures 목적 : 기존의 레거시 데이터를 추출을 해서 데이터베이스를 만드는 것

fixtures랑 똑같이 생긴 파일을 **직접** 만들어서 할 생각은 하지 말 것!





### Improve Query

**"Querysets are lazy"**

장고에서는 쿼리셋이 **평가**될때까지 실제로 쿼리를 실행하지 않는다.

=> DB에 쿼리를 전달하는 일이 웹 애플리케이션을 느려지게 하는 주범 중 하나이기 때문!

**평가란?** 쿼리셋에 해당하는 DB의 레코드를 실제로 가져오는 것.

평가된 모델은 쿼리세의 내장 캐시에 저장되며, 쿼리셋을 다시 순회하더라도 똑같은 쿼리를 DB에 다시 전달하지 않음

 

**※ 캐시** : 데이터나 값을 미리 복사해놓는 임시 장소. 캐시에 데이터를 미리 복사해놓으면 계산이나 접근 시간 없이 더 빠르게 데이터 접근 가능. 시스템의 효율성을 위해 두루 사용됨.

