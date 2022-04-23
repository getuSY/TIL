## REST API

### HTTP(HyperText Transfer Protocol)

웹 상에서 컨텐츠를 전송하기 위한 규약

요청과 응답에 의한 데이터 전송이 이루어짐

특징 : Stateless, Connectionless

쿠키와 세션을 통해 서버 상태를 요청과 연결한다.



#### HTTP request methods

자원에 대한 행위를 정의, 주어진 자원에 수행하길 원하는 행동을 나타냄

메소드에 따라 동작이 다를 수 있다.

GET : 조회, POST : 작성, PUT : 수정, DELETE : 삭제



#### HTTP response status codes

특정 HTTP 요청이 성공적으로 완료되었는지 나타냄(1XX ~ 5XX)



#### 웹에서 리소스 식별

HTTP 요청의 대상 : 리소스, 자원

리소스 식별을 위해 HTTP 전체에서 사용되는 URI로 식별됨



#### URL(Uniform Resource Locator)

특정 자원의 위치를 URL로 나타냄

네트워크 상 자원이 어디 있는지 알려주기 위함.



#### URN(Uniform Resource Name)

통합 자원 이름

자원의 위치에 영향을 받지 않는 유일한 이름 역할을 한다.

URN만의 표기법이 따로 있다.



#### URI(Uniform Resource Identifier)

통합 자원 식별자

인터넷의 자원을 식별하는 유일한 주소이면서 정보의 자원을 표현.

인터넷에서 자원을 식별하거나 이름을 지정하는데 사용하는 간단한 문자열로 URL, URN의 상위 개념.

URN을 사용하는 비중이 적기 때문에 일반적으로 URL과 URI를 같은 개념으로 보기도 한다.

인터넷에서 정보를 표현하는 데에 목적을 두고 있다.

`Schema/Host/port/path/?=query/fragment`

- Schema (protocol) : 브라우저가 사용해야 하는 프로토콜. https, date, file, ftp, mailto
- Host (Domain name) : 요청을 받는 웹 서버의 이름. IP address를 직접 사용할 수 있지만 실 사용 시 불편하므로 잘 사용되지 않음.
- Port : 웹 서버 상 리소스에 접근하는데 사용되는 기술적인 문. HTTP 프로토콜의 표준 포트(HTTP 80, HTTPS 443)
- Path : 웹 서버 상 리소스 경로. 초기엔 실제 파일의 물리적 위치였지만 오늘날은 추상화 형태의 구조로 표현.
- Query (Identifier) : 식별자, Query String Parameters.웹 서버에 제공되는 추가적인 매개 변수, &로 구분되는 key-value 목록
- Fragment : Anchor, 자원 안에서 북마크의 한 종류를 나타냄. 특정 부분을 보여주기 위한 방법. 브라우저에게 알려주는 요소이기 때문에 부분 식별자라고 부르며 '#' 뒤의 부분은 요청이 서버에 보내지지 않음. (Query와 비교하면, query는 ? 뒤의 부분이 서버에 보내진다?)



### RESTful API

#### API

Application Programming Interface, 프로그래밍 언어가 제공하는 기능을 수행할 수 있게 만든 인터페이스

애플리케이션과 프로그램으로 소통하는 방법.

CLI는 명령으로 소통, GUI는 아이콘으로, API는 프로그래밍을 통해 특정 기능 수행.

Web API : 웹 애플리케이션 개발에서 다른 서비스에 요청을 보내고 응답을 받기 위해 정의된 명세.



#### REST

REpresentational State Transfer

API 서버를 개발하기 위한 일종의 소프트웨어 설계 방법론(규약 아님)

네트워크 구조 원리의 모음, 자원을 정의하고 자원에 대한 주소를 지정하는 전반적인 방법

REST 원리를 따르는 시스템을 RESTful이라는 용어로 지칭함.

자원을 정의하는 방법에 관한 고민



**REST의 자원과 주소 지정 방법**

1. 자원 : URI
2. 행위 : HTTP method
3. 표현 : JSON (자원과 행위를 통해 궁극적으로 표현되는 추상화된 결과물)



**JSON?**

JavaScript Object Notations

자바스크립트에서의 객체 표기법을 따른 **단순 문자열**.

python에선 해석을 위해서(파싱을 통해) 딕셔너리로 변환해 사용했다.

특징 : 사람이 읽거나 쓰기 쉽고 기계가 파싱하고 만들어내기 쉬움. => 키와 값 형태

키와 값 형태는 C 계열의 어떤 프로그래밍 언어에서든 지원하는 자료 구조. python dictionary, jsvascript object 등

파싱 : 쓸 수 있는 자료 형태로 바꾸는 것



**REST 핵심 규칙**

1. 자원에 대한 정보는 URI로 표현

2. 자원에 대한 행위는 HTTP methods로 표현 (GET, POST, PUT, DELETE).

   같은 주소더라도 메소드에 따라 달라진다.

설계 방법론을 지키지 않더라도 큰 상관은 없다. **BUT** 지키는 것이 더 낫다.



#### RESTful API

REST 원리를 따라 설계한 API

프로그래밍을 통해 클라이언트의 요청에 JSON을 응답하는 서버를 구성



### Response

urls.py 및 models.py 확인

**django-seed** : 한 번에 데이터를 많이 넣을 때 쓴다? 데이터를 심는다.

https://github.com/Brobin/django-seed

```bash
$ python manage.py migrate
$ python manage.py seed articles --number=20
```

랜덤으로 데이터 구조에 맞는 데이터를 20개 생성



```python
# articles/urls.py

urlpatterns = [
    path('html/', views.article_html),       # 기존 HTML 이용
    path('json-1/', views.article_json_1),
    path('json-2/', views.article_json_2),   # serialization 이용
    path('json-3/', views.article_json_3),
]
```

**HTML 응답 서버**의 경우 기존과 방식이 같음. HTML 문서를 받는다.

```python
# views.py

def article_html(request):
    articles = Article.objects.all()
    context = {
        'articles': articles,
    }
    return render(request, 'articles/article.html', context)
```



**JsonResponse 객체**를 활용한 JSON 데이터 응답의 경우

json 리스트에 객체 정보를 딕셔너리로 담는다.



**content-Type entity header** : 데이터의 media type을 나타내기 위해 사용됨. 응답 내 컨텐츠 유형이 실제로 무엇인지 사용자에게 알려준다.

**JsonResponde objects** : JSON-encoded response를 만드는 서브클래스

**"safe" parameter**

```python
# articles/views.py

def article_json_1(request):
    articles = Article.objects.all()
    articles_json = []

    for article in articles:
        articles_json.append(
            {
                'id': article.pk,
                'title': article.title,
                'content': article.content,
                'created_at': article.created_at,
                'updated_at': article.updated_at,
            }
        )
    return JsonResponse(articles_json, safe=False)
```



#### Serialization

**직렬화**

데이터 구조나 객체 상태를 동일하거나 다른 컴퓨터 환경에 저장하고 나중에 재구성할 수 있는 포맷으로 변환하는 과정

다른 데이터 타입으로 변환하기 용이하게 바꿔주는 것 => serializers라는 객체로 만든다.

queryseta 및 model instance와 같은 복잡한 데이터를 JSON, XML 등의 유형으로 쉽게 변환할 수 있는 **Python 데이터 타입**으로 만들어 준다.

유연하게 만들 수 있도록 중간 과정의 데이터 타입을 만든다. 응답에 따라 자유롭게 다른 데이터 타입으로 만들 준비를 마칠 수있게 한다.

바로바로 바꾸는 게 아니라 중간 과정이 필요하다.

django에서는 serializer라고 하는 내장 함수를 제공

```python
# views.py
from django.core import serializers

def article_json_2(request):
    articles = Article.objects.all()
    data = serializers.serialize('json', articles)     # date <= 직렬화된 객체
    return HttpResponse(data, content_type='application/json')
```

주어진 모델 정보를 활용하기 때문에 필드를 개별적으로 직접 만들어줄 필요가 없음.

=> 그러나 JSON 타입, 앞의 경우와는 데이터 구성이 다르다. 똑같이 페이지를 주지는 않는다!



**장고에서는 REST한 서버를 개발할 수 있도록 라이브러리를 제공**

Django REST framework(DRF) (requirement.txt 에 있는지 없는지 확인하고 설치 진행)

```bash
$ pip install djangorestframework
```

앱 등록 확인

```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

참조 : https://www.django-rest-framework.org/#requirements

```python
# articles/views.py
from .serializers import ArticleSerializer

# @api_view(['GET'])
@api_view()
def article_json_3(request):
    articles = Article.objects.all()
    serializer = ArticleSerializer(articles, many=True)
    # 앞에 들어오는 객체가 단일 객체가 아닐 때 쓰는 옵션 many, 기본값은 false(단일 객체)
    return Response(serializer.data)
```

```python
# sericlizer.py

from rest_framework import serializers
from .models import Article


class ArticleSerializer(serializers.ModelSerializer):
      # 게시글의 정보를 담고 있는 쿼리셋을 serialize 해주는 도구 => 나중에 json 변경
    class Meta:
        model = Article
        fields = '__all__'
```

DRF 라이브러리가 ModelForm과 똑같이 맞춰서 사용하기 쉽도록 만들어두었다.



이후 url 연결하면 json view가 아니라 drf 화면이 나온다.

문서를 받고, contenttype = html

api로 데이터를 받아올 때는 json으로 받아오고 브라우저에서 출력할 때는 문서를 출력



확인을 위해 리퀘스트를 보낼 수 있는 파일을 하나 배경쪽에 만든다

```bash
$ pip install request (venv 에서는 안 깔려있다)
```

```python
# sth.py
import request
from pprint import pprint

response = requests.get('http://127.0.0.1:8000/api/v1/json-3/')
# pprint(response)
pprint(dir(response)) # 리스폰스 객체에서 쓸 수 있는 메소드 확인을 위한 dir 사용
# 메소드 중에 json이 있다
pprint(response.json()) 
# json viewer에서 본 것처럼 리스트로 변환이 되어 온 것을 볼 수 있다.
# views.py return Response(serializer.data) 에 의한 응답
# json으로 변경 안하면 requests.models.Response 라는 일종의 객체
# 이것이 변환이 가능한 건 ArticleSerializer라는 도구가 serializer라는 객체로 바꿔주었고 받는 입장에서 파싱을 하면 되는 것이기 때문
# serialize => 내가 쓸 수 있는 데이터로 바꿔주는 과정

# 이후 쓰려고 하면?
articles_list = response.json()
for article in articles_list:
    print(type(article))    # type : dictionary
    print(article.get('title'))
# 이런 데이터를 주는 제공자가 우리 => DRF 라이브러리를 통해 준다!
# 문서만 주는 것이 아니라 JSON 데이터를 통해서 데이터를 전달한다.
```

최종적으로 Model과 View만을 이용해서 RESTful한 웹을 만들 수 있다!

templates를 사용하지 않고, serializer를 사용한다!

데이터를 시리얼라이즈를 해주는 도구, model serializer를 사용.

별도 serializer.py를 사용 (serializer는 DRF 라이브러리에 있다)





### Django REST Framework (DRF)

웹 API 구축을 위한 강력한 툴킷 제공 라이브러리

DRF의 serializer는 form 및 modelform 클래스와 매우 유사하게 구성되고 작동한다.



#### Django의 ModelForm과 DRF serializer 비교

Django : HTML 응답, ModelForm 사용

DRF : JSON 응답(더 이상 페이지는 없다!), serializer(model serializer) 사용



##### *어떻게 페이지가 없는 WEB을 만들 수 있는가?

왜 더 이상 페이지는 없고 RESTful한 웹을 만들 수 있는가?

이때까지의 장고는 획일적 => 요청이 와서 응답을 할 때 다 완성된 페이지를 주어 그대로 출력.

우리가 보는 웹페이지는 모든 것을 완성해두지는 않는다.

서버측에서 json 데이터를 주면 브라우저가 이 데이터를 기반으로 만든다.

서버 입장에서는 완성된 페이지를 만들어주는 게 아니라 json 데이터를 주는것

why? 웹 페이지 한 번을 본다고 하면 한 화면에 굉장히 많은 데이터가 존재.

서버가 한 번에 다 만들어줄 수 있는가? 에 관한 의문.

데이터를 조회해서 json으로 넘겨주면 브라우저가 그려나가는 것. 

그 과정 => Vue.js frontent framework

django는 backend로만 사용하게 됨.

django가 넘겨주는 데이터를 vue.js가 받아 만들어주는 것.



페이지는 변하지 않는데 뭔가 계속 (숫자 등이)변하는 사이트 => 주식?

만약 페이지나 문서를 받는 거라면 계속해서 새로고침을 해야함.

=> 주기적으로 업데이트 되는 json을 받아서 일부분만 만들고 있음. 페이지는 첫 페이지 그대로 유지.



### Single Model

#### DRF with Single Model

단일 모델의 데이터를 직렬화하여 JSON으로 변환하는 방법

DRF built-in form, Postman 사용



우선 app 및 urls, model.py 확인

#### ModelSerializer

모델 필드에 해당하는 필드가 있는 serializer 클래스를 자동으로 만들 수 있는 shortcut

1. 모델 정보에 맞춰 자동으로 필드 생성
2. serializer에 대한 유효성 검사기를 자동으로 생성
3. .create(), update() 등의 간단한 기본 구현이 포함되어 있음



Article 전체 쿼리셋을 직렬화해주는 model serializer 생성

```python
# serializers.py
from rest_framework import serializers
from .models import Article

class ArticleListSerializer(serializers.MdoelSerializers):
    
    class Meta:
        model = Article
        fields = '__all__'
```



shell_plus를 통해 확인(ipython  설치 여부 확인)

shell_plus가 serializers를 자동으로 import해주지는 않는다

```shell
from articles.serializers import ArticleListSerializer
serializer = ArticleListSerializer()
# 단일 객체의 경우
aritcle = Article.objects.get(pk=1)    # 직렬화할 객체 하나 불러오기
serializer = ArticleListSerializer(article)
serializer.data   # 원하는 결과물, 직렬화된 데이터'.date' 속성을 하면 보인다
# type : rest_framework.utils.serializer_helpers.B ...
# 서버에서는 여기까지 만들어서 주면 된다\

# 전체 게시글 전송
articles = Article.objects.all()  # 쿼리셋
serializer = ArticleListSerializer(articles) # 단일 객체가 아닐 때에는 에러가 뜬다!
serializer = ArticleListSerializer(articles, many=True) 
serializer.data 
# 단일 객체가 아닐 때에는 many 값 넣어줘야 한다!
```

이 응답을 받는 사람은 결국 json으로 변환해 사용하기 때문에 준비한 serializer!

- many : 단일 인스턴스가 아닌 QuerySet 등의 multiple objects를 직렬화하기 위해서는 serializer를 인스턴스화 할 때 many=True를 해줘야 한다!



#### Build RESTfurl API

articles/ : GET - 전체 글 조회, POST - 글 작성

articles/1/ : GET - 1번 글 조회, PUT - 1번 글 수정, DELETE - 1번 글 삭제 



#### GET

```python
# aritcles/urls.py

urlpatterns = [
    path('articles/', views.article_list)
]
```

- app_name 등을 쓰지 않는 이유? : 템플릿을 만들지 않을 거기 때문에 쓸 데 없음!

```python
# articles/views.py

from rest_framework.response import Response
from .serializers import ArticleListSerializer

def articles_list(request):
    articles = Article.objects.all()
    serializer = ArticleListSerializer(articles, many=True)
    return Response(serializer.data)
```

- **api_view decorator** : 기본적으로 GET 메소드만 허용되며 다른 메소드 요청에 대해 405 메소드 에러로 응답.

  VIEW 함수가 응답해야 하는 HTTP 메소드의 목록을 리스트의 인자로 받는다.

  DRF에서는 필수적으로 작성해야만 VIEW 함수가 정상적으로 작동한다!!

```python
# articles/views.py

from rest_framework.response import Response
from rest_framework.decorators import api_view
from .serializers import ArticleListSerializer

@api_view(['GET'])
def articles_list(request):
    articles = Article.objects.all()
    serializer = ArticleListSerializer(articles, many=True)
    return Response(serializer.data)
```

- 이 경우 POST 메소드로 보내면 405 Methods Error가 뜬다!
- view 함수 이름을 기준으로 언더바를 떼서 DRF에서 보여준다.

```python
# serializers.py
# id와 title만 받고 싶을 때는 필드 조정 => 그에 대한 데이터만 보여줄 것!
from rest_framework import serializers
from .models import Article

class ArticleListSerializer(serializers.MdoelSerializers):
    
    class Meta:
        model = Article
        fields = ('id', 'title',)
```

- 페이지가 아니라 데이터를 조회하고 있다 => 조회하고자 하는 데이터가 없을 때에는 404가 나와야 한다!  => get_list_or_404 필요!

- 게시판 프로젝트에서는 get_objects_or_404를 썼다. <= get_list_or_404가 부적합해서!

  게시글이 없을 때 get_list_or_404를 쓰면 404 에러가 났기 때문이다.

```python
# articles/views.py

from rest_framework.response import Response
from rest_framework.decorators import api_view
from .serializers import ArticleListSerializer

@api_view(['GET'])
def articles_list(request):
    # articles = Article.objects.all()
    articles = get_list_or_404(Article)
    serializer = ArticleListSerializer(articles, many=True)
    return Response(serializer.data)
```

- 모든 필드 정보를 받아보고 싶을 때는? => 전체 조회를 위한 다른 시리얼라이저 생성!

```python
# serializers.py

from rest_framework import serializers
from .models import Article

class ArticleListSerializer(serializers.MdoelSerializers):
    
    class Meta:
        model = Article
        fields = ('id', 'title',)
        
class ArticleListSerializer(serializers.MdoelSerializers):
    
    class Meta:
        model = Article
        fields ='__all__'
```

```python
# aritcles/urls.py

urlpatterns = [
    path('articles/', views.article_list),
    path('articles/<int:article_pk>/', views.article_detail),
]
```

```python
# articles/views.py

from rest_framework.response import Response
from rest_framework.decorators import api_view
from .serializers import ArticleListSerializer

@api_view(['GET'])
def articles_list(request):
    # articles = Article.objects.all()
    articles = get_list_or_404(Article)
    serializer = ArticleListSerializer(articles, many=True)
    return Response(serializer.data)


@api_view(['GET'])
def article_datail(request, article_pk): 
    aritcle = get_object_or_404(Article, pk=article_pk) # 단일 객체니까! object!
    serializer = ArticleListSerializer(article)  # 단일 객체니까! many 사항 없음!
    return Response(serializer.data)
```



#### POST

게시글 작성  : 201 Created 상태 코드 및 메시지 응답(글 작성이 잘 되었다는 메시지)

RESTful 구조에 맞게 작성 => 똑같은 view 함수로 POST 분기 처리만 하면 생성에 대한 처리도 간으!

<= RESTful은 method에 따라 자원을 조작할 수 있기 때문!

```python
# articles/views.py

from rest_framework.response import Response
from rest_framework.decorators import api_view
from .serializers import ArticleListSerializer

@api_view(['GET', 'POST'])
def articles_list(request):
    if request.method == 'GET':
    # articles = Article.objects.all()
    articles = get_list_or_404(Article)
    serializer = ArticleListSerializer(articles, many=True)
    return Response(serializer.data)

    elif request.method == 'POST':  # data를 받아야 하기 때문에 첫번째 인자 data=
        serializer = ArticleListSerializer(data=request.data)
        if serializer.is_valid():   # 유효성 검사
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors)   # 유효성 검사 통과하지 못했을 때!
    
```

- view 함수의 직관성을 중시 => elif 를 통해 기능을 명확하게 보여줄 수 있도록 구조를 나누다!

  참조 : https://www.django-rest-framework.org/tutorial/2-requests-and-responses/

- 유효성 검사를 통과하지 못하면 serializer.data에 에러 메시지가 채워진다!

- POST 방식 데이터 확인 : BODY의 form-data에서 확인할 수 있다.

- 이대로만 보내게 되면 201이 아닌 200 메시지가 뜬다!

  => DRF에서 제공하는 상태 코드 관련이 필요하다!

```python
# articles/views.py

from rest_framework.response import Response
from rest_framework.decorators import api_view
from .serializers import ArticleListSerializer
from rest_framework import status      # 상태 코드 관련 거의 모든 메시지 제공!

@api_view(['GET', 'POST'])
def articles_list(request):
    if request.method == 'GET':
    # articles = Article.objects.all()
    articles = get_list_or_404(Article)
    serializer = ArticleListSerializer(articles, many=True)
    return Response(serializer.data)

    elif request.method == 'POST': 
        serializer = ArticleListSerializer(data=request.data)   # data??
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

- 상태 코드 관련 정확한 메시지가 뜨면서 게시글이 작성됨을 확인할 수 있다!

- Status Codes in DRF : DRF에서는 상태 코드를 보다 명확하고 읽기 쉽게 만드는 데 사용할 수 있는 정의된 상수 집합을 제공 => **status** 모듈에 HTTP status code 집합 포함

- `Response(serializer.data, status=201)` 숫자로 상태 코드 표현도 가능하나 DRF는 권장X

- VIEW 함수에서 POST 요청 관련을 작성하고 나면 페이지 아래에 POST 및 작성을 위한 FORM이 뜬다! => 그러나 {} 안에 딕셔너리 양식을 맞춰 기입해야함!

- 유효성 검사 is_valid() 이후에는 400_BAD_REQUEST가 자동! => 어떻게 설정할 순 없을까?

  - `is_valid(raise_exception=True)` : 기본값은 False, 자동으로 데이터가 invalid 할 때 400 에러를 발생시킴. 

- request.data 이유 ? 바디에 포함된 데이터 필요, ㅍ파싱된데이터 담고있다

  참조 : https://www.django-rest-framework.org/api-guide/requests/#request-parsing



#### DELETE

204 No Content 상태 코드 및 메시지 응답

article_detail 함수로 상세 게시글 조회 및 삭제 기능 처리 가능

```python
# articles/views.py

from rest_framework.response import Response
from rest_framework.decorators import api_view
from .serializers import ArticleListSerializer
from rest_framework import status  

...

@api_view(['GET', 'DELETE'])
def article_datail(request, article_pk): 
    aritcle = get_object_or_404(Article, pk=article_pk)
    if request.method == 'GET':
        serializer = ArticleListSerializer(article)
        return Response(serializer.data)
    
    elif request.method == 'DELETE':
        article.delete()   # 삭제는 항상 간단, but 응답에 데이터가 필요한데 뭐가 없다
        data = {
            'delete': f'데이터_{article_pk}번이 삭제되었습니다',
        }  # 삭제할 때 보여줄 데이터가 없기 때문에 data를 만들어 이를 보여줄 수 있음
        return Response(data, status=status.HTTP_204_No_Content)
```

- DRF의 페이지에서는 DELETE 구현이 끝날 때 DELETE 버튼이 생긴다!
- VIEW함수의 기능이 추가될 때마다 DRF 제공 페이지에 추가되고 있음!



#### PUT

ricles_detauil

```python
# articles/views.py

from rest_framework.response import Response
from rest_framework.decorators import api_view
from .serializers import ArticleListSerializer
from rest_framework import status  

...

@api_view(['GET', 'DELETE', 'PUT'])
def article_datail(request, article_pk): 
    aritcle = get_object_or_404(Article, pk=article_pk)
    if request.method == 'GET':
        serializer = ArticleListSerializer(article)
        return Response(serializer.data)
    
    elif request.method == 'DELETE':
        article.delete()
        data = {
            'delete': f'데이터_{article_pk}번이 삭제되었습니다',
        }
        return Response(data, status=status.HTTP_204_No_Content)
    
    elif request.method = 'PUT':
        serializer = ArticleListSerializer(article, request.data)
        # serializer = ArticleListSerializer(article, data=request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
        return Response(serializer.data)
```

- HTTP body에 form_data로 title, content 수정 데이터 작성(딕셔너리 규격에 맞춰 직접 써야함)



### 1:N Relation

article에 댓글이 달려 있다면? 

2개 이상의 1:N 관계를 맺는 모델을 두고 CRUD 로직을 수행 가능하도록 수행하기



데이터베이스 초기화 후 Comment 모델 작성! => django-seed 로 랜덤 데이터도 생성

#### GET

```python
# articles/models.py
class Article():
    
    
class Comment(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
    content = models.TestField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

시리얼라이저 만들기

```python
# serializers.py

from rest_framework import serializers
from .models import Article, Comment

class ArticleListSerializer(serializers.MdoelSerializers):
    
    class Meta:
        model = Article
        fields = ('id', 'title',)
        
class ArticleListSerializer(serializers.MdoelSerializers):
    
    class Meta:
        model = Article
        fields ='__all__'
        
class CommentSerializer(serializers.ModelSeializers):
    
    class Meta:
        model = Comment
        fields = '__all__'
```

urls.py

```python
# aritcles/urls.py

urlpatterns = [
    path('articles/', views.article_list),
    path('articles/<int:article_pk>/', views.article_detail),
    path('comments/', views.comment_list)
]
```

```python
# articles/views.py
from .models import Article, Comment
from .serializers import CommentSerializer, ArticleSerializer

@api_view(['GET'])
def comment_list(request):
    comments = get_list_or_404(Comment)
    serializer = CommentSerializer(comments, many=True)
    return Response(serializer.data)
```

- json을 볼 때 comment의 article에는 article의 pk가 달려 있다.

```python
# aritcles/urls.py

urlpatterns = [
    path('articles/', views.article_list),
    path('articles/<int:article_pk>/', views.article_detail),
    path('comments/', views.comment_list),
    path('comments/<int:comment_pk>/', view.comment_detail),
]
```



```python
# articles/views.py
...
from .models import Article, Comment
from .serializers import CommentSerializer, ArticleSerializer

@api_view(['GET'])
def comment_list(request):
    comments = get_list_or_404(Comment)
    serializer = CommentSerializer(comments, many=True)
    return Response(serializer.data)

@api_view(['GET'])
def comment_detail(request, comment_pk):
    comment = get_object_or_404(Comment, pk=comment_pk)
    serializer = CommentSerializer(comment)
    return Response(serializer.data)
```



#### POST

article같은 경우는 GET, POST가 같이 작성되었다. => 댓글은 아님!

```python
# aritcles/urls.py

urlpatterns = [
    path('articles/', views.article_list),
    path('articles/<int:article_pk>/', views.article_detail),
    path('articles/<int:article_pk>/comments', views.comment_create),
    # urls 형식은 자유! article_pk를 받아야 해서 따로 작성해본다!
    path('comments/', views.comment_list),
    path('comments/<int:comment_pk>/', view.comment_detail),
]
```

```python
# articles/views.py

...
@api_view(['POST'])
def comment_create(request, article_pk):
    serializer = CommentSerializer(data=request.data)
    if serializer.is_valid(raise_exception=True):
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)
```

- 이 코드는 미완성

- POST 데이터는 HTTP 바디에 FORM 데이터로 넘어간다, 확인!

- SEND = > 400 HTTP ERROR => 유효성 검사 통과 못함! =>article 항목이 없다!

  => view 함수에서 `commit=False`로 처리했었다.

  => 여기서는 미리 article을 찾고 .save()에다가 집어넣어준다!

  ```python
  # articles/views.py
  
  ...
  @api_view(['POST'])
  def comment_create(request, article_pk):
      article = get_object_or_404(Article, pk=article_pk)
      serializer = CommentSerializer(data=request.data)
      if serializer.is_valid(raise_exception=True):
          serializer.save(article=article)
          return Response(serializer.data, status=status.HTTP_201_CREATED)
  ```

  - 이때 유효성 검사는 `CommentSerializer`의 필드에 작성된 모든 필드를 검사한다!

    그래서 유효성 검사 아래에 article을 작성해주는 건 같은 에러를 확인하게 된다!

    => **serializer 필드를 유효성 검사에서 빼줘야한다!**

- **READ Only Field** : 어떤 게시글에 작성하는 댓글인지에 대한 정보를 form-data로 넘겨주지 않았기 때문에 직렬화 하는 과정에서 article필드가 유효성 검사를 통과하지 못함.

- 읽기 전용 필드(read_only_field) 설정을 통해 직렬화하지 않고, 유효성 검사에서는 빠지지만 리턴값에는 포함되게 한다!

  ```python
  # article/serializers.py
  
  class CommentSerializer(serializer.ModelSerializers):
      
      class Meta:
          model = Comment
          fields = '__all__'
          read_only_fields = ('article',)
  ```



#### DELETE & PUT

SDAD

```python
# articles/views.py
...
from .models import Article, Comment
from .serializers import CommentSerializer, ArticleSerializer

...

@api_view(['GET', 'DELETE', 'PUT'])
def comment_detail(request, comment_pk):
    comment = get_object_or_404(Comment, pk=comment_pk)
    if request.method == 'GET':
        serializer = CommentSerializer(comment)
        return Response(serializer.data)

	# 그냥 article에서 가져온다
	elif request.method == 'DELETE':
        comment.delete()
        data = {
            'delete': f'데이터_{article_pk}번이 삭제되었습니다',
        }
        return Response(data, status=status.HTTP_204_No_Content)
    
    elif request.method = 'PUT':
        serializer = ArticleListSerializer(comment, request.data)
        # serializer = ArticleListSerializer(comment, data=request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
        return Response(serializer.data)
    
    ...
```



단일 게시글을 조회할 때, 게시글에 작성될 댓글 데이터도 보고 싶다면?

=> article에는 외래키가 작성되어있지 않기 때문에 json을 수정해야 한다!



### 1:N Serializer

1. 특정 게시글에 작성된 댓글 목록 출력하기 : 기존 필드 override
2. 특정 게시글에 작성된 댓글의 갯수 구하기 : json 새로운 필드 추가



#### 특정 게시글에 작성된 댓글 목록 출력

serializer는 기존 필드를 override하거나 추가 필드 구성이 가능하다.



1. **PrimaryKeyRelatedField**

   pk를 사용하여 관계된 대상을 나타내는데 사용할 수 있음 <= 일종의 역참조

   필드가 to many relationship(N)을 나타내는데 사용되는 경우 `many=True` 필요

   comment_set(역참조) 필드 값을

   ```python
   # articles/serializers.py
   
   def ArticleSerializer(serializers.ModelSerializer):
       comment_set = serializers.PrimaryKeyRelatedField(many=True, read_only=True)
       
       class Meta:
           model = Article
           fields = '__all__'
           # 기존 모델의 필드에 포함이 안 되어 있는 필드면 read_only속성값으로 써준다!
           
   def CommentSerializer(serializers.ModelSerializer):
       
       class Meta:
           model = Comment
           fields = '__all__'
           read_only_fields = ('article',)
           # Meta 안에 작성하는 이유는 fields=all에 포함되어 있는 필드이기 때문!
   ```

   - comment_set이라는 이름은 꼭 이래야 하나?

     => 이 이름을 바꾸려먼 모델에서 1:N 관계의 필드에서 related_name을 바꿔준 후 바꾼다!

     클래스 변수명과 역참조 시의 이름이 일치해야 함!

     

2. **Nested relationships**

   모델 관계상으로 참조된 대상은 참조하는 대상의 응답에 표현되거나 중첩될 수 있다.

   이런 중첩 관계는 serializers를 필드로 사용하여 표현할 수 있다. => 두 클래스의 상하위치 변경!

   ```python
   # articles/serializers.py
   
   def CommentSerializer(serializers.ModelSerializer):
       
       class Meta:
           model = Comment
           fields = '__all__'
           read_only_fields = ('article',)
   
   
   def ArticleSerializer(serializers.ModelSerializer):
       comment_set = CommentSerializer(many=True, read_only=True)
       # N관계에 있는 serializer 호출 가능! but 먼저 선언되어 있어야 한다!
       class Meta:
           model = Article
           fields = '__all__'
   ```

   - 1번의 경우에는 comment_set에 comment_pk 정보만 받아오지만, 2번의 경우에는 comment_set을 보면 json에 comment의 모든 정보가 포함되어 있다!
   - M:N 관계의 경우 서로의 ModelSerializer를 호출할 수 있다!



#### 특정 게시글에 작성된 댓글의 갯수 구하기

comment_set 매니저는 모델 관계로 인해 자동으로 구성되어 comment_set을 fields 옵션에 작성만 해도 사용할 수 있었다.

별도 값을 위한 필드를 사용하는 경우, 자동으로 구성되는 매니저가 아니기 때문에 직접 필드 작성!

```python
# articles/serializers.py

def CommentSerializer(serializers.ModelSerializer):
    
    class Meta:
        model = Comment
        fields = '__all__'
        read_only_fields = ('article',)


def ArticleSerializer(serializers.ModelSerializer):
    comment_set = CommentSerializer(many=True, read_only=True)
    comment_count = serializers.IntegerField(source='comment_set.count')
    
    class Meta:
        model = Article
        fields = '__all__'
```

- comment_set과 달리 완전히 없던 필드를 만들어주는 것이기 때문에 변수 명은 마음대로!

- 일반적으로 댓글에 작성된 모든 댓글의 갯수를 가져온다면 `article.comment_set.count()`

  => 여기서 앞의 객체와 괄호를 제외하고 가져온다!

  => 카운트한 값을 주는 json 데이터가 생성된다!

- `source` arguments : 필드를 채우는데 사용할 속성의 이름, 점표기법을 사용하여 속성을 탐색

  - `.count()`는 built-in QuerySet API 중 한 가지



##### ※ 주의사항!

`read_only=` 속성과 `read_only_fields`를 혼합하여 사용할 수 없다!

comment_set과 comment_count에 read_only를 작성하고자 할 경우 특정 필드를 override 했다면 

 `read_only_fields`shortcut으로 사용할 수 없다!
