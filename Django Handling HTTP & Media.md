## Django- Handling HTTP

**요청 처리 방법**

1. Django shortcut function
2. View decorators (`@decorator`)



### shortcut function

: render(), redirect(), get_object_or_404(), get_list_or_404()

- render()가 없으면 : 템플릿을 로드하는 과정이 없어져서 따로 로드해야 함.(loader)

- `get_object_or_404()`

  모델 manager인 objects에서 get()을 호출하지만, 해당 객체가 없을 경우 Does Not Exist 예외 발생.

  코드 실행 단계에서 발생한 예외 및 에러에 대해 브라우저는 http status code 500으로 인식.

  => 클라이언트가 이유를 정확히 알수 없기 때문에 제대로 이유를 알려주기 위해 HTTP 404가 낫다!

  get_object_or_404()는 HTTP 404를 raise. <= 상황에 따라 적절한 예외 처리를 하고 클라이언트에게 올바른 에러 상황을 전달하기 위한 것!

  - HTTP 응답 상태 코드 참조 : https://developer.mozilla.org/ko/docs/Web/HTTP/Status
  - 100번대 : 정보를 제공하는 응답
  - 200번대 : 성공적인 응답
  - 300번대 : 리다이렉트
  - 400번대 : 클라이언트 에러(403 : CSRF 토큰 안 넣고 보냈을 때 접근 권함 없음, 404 : URL, 요청하는 리소스 찾을 수 없음)
  - 500번대 : 서버 에러(500 : 서버가 처리 방법을 모르는 상황 발생)

- `article=get_object_or_404(Article, pk=pk)` : 인자 모델 class, 찾고자 하는 속성

- `get_object_or_404()` 사용하지 않으면 `from django.http import Http404` 통해 따로 예외 처리

  ```python
  from django.http import Http404
  
  def detail(request, pk):
      try:
          article = get_object_or_404(Article, pk=pk)
      except Article.DoesNotExist:
          raise Http404('No Article matches the given query')
      context = {
          'article':article,
      }
      return render(rquest, 'articles/detail.html', context)
  ```

- get_list_or_404() : 게시판의 형태의 사이트는 맞지 않는다.(게시판 내용이 아무것도 없을 때 404를 띄울 순 없음)  API 로써 서버를 쓸 때 사용.



### Django View decorators

다양한 HTTP 기능을 지원, view 함수에 적용할 수 있는 여러 ***데코레이터**가 있음

- Allowed HTTP methods

  - 요청 메소드에 따라 view 함수에 대한 접근 제한. 해당 요청이 메소드 조건을 충족하지 못하면 `HttpResponseNotAllowed`를 리턴 (405 Method Not Allowed) < 메소드를 잘못 썼다 알 수 있음

- `require_htp_methods()` : view 함수에서 특정한 메소드 요청만 허용하도록 하는 데코레이터

- `require_POST()` : view 함수가 POST 메소드 요청만 승인

- `reqire_safe(`) :  view 함수가 get만(+HEAD) 허용하도록 함. 장고에서는 require_GET 대신 require_safe() 사용 권장

  - 이유 : Web servers should automatically strip the content of responses to HEAD requests while leaving the headers unchanged, so you may handle HEAD requests exactly like GET requests in your views. Since some software, such as link checkers, rely on HEAD requests, you might prefer using `require_safe` instead of `require_GET`.

    ```python
    from django.views.decorators.http import require_POST, require_safe, require_http_methods
    @reqire_http_methods(['GET', 'POST'])
    def create(request):                      # update에도 적용 가능
        if request.method == 'POST':
            pass
        else:
            pass
        return 
    ```

    데코레이터가 없을 때에는 POST 이외 모든 메소드, 데코레이터가 있을 때에는 오직 GET만 else문에 들어가게 됨. 데코레이터에 해당되는 메소드가 아닐 경우 자동으로 클라이언트에게 405 응답을 준다. => 장고 서버가 더 친절해진다

- `@require_POST` 사용 시 delete 과정에서 `if`문 안에서 메소드 판단할 필요가 사라짐



#### * decorator

어떤 함수에 기능을 추가하고 싶을 때, 해당 함수를 수정하지 않고 기능을 연잘해주는 **함수**. 

원본 함수 수정 없이 추가 기능만을 구현할 수 있다.



#### * postman

API 서버를 개발할 때 사용할 수 있는 도구. 서버로 여러 요청을 보낼 수 있다.





## Django - Media file

사용자가 웹에서 업로드하는 정적(static 범주 안의) 파일(user-uploaded), 유저가 업로드 한 모든 파일



### Model Field

- ImageField() : 이미지 업로드에 사용하는 모델 필드. FileField를 상속받는 서브클래스, 파일필드의 모든 속성 및 메서드 사용 가능. 사용자에 의해 업로드 된 객체가 유효한 이미지인지 검사한다.

  ImageField 인스턴스는 최대 길이가 100자인 문자열로 DB로 생성되며, max_length 인자를 사용하여 최대 길이를 변경할 수 있음. (이미지 필드에 테이블이 생성되고 이미지 파일의 경로가 저장되기 때문)

  반드시 **Pillow** 라이브러리가 필요.(이미지필드 migration을 위해 필요한 서드 파티 라이브러리)

- FileField(): 파일 업로드에 사용하는 모델 필드. 2개의 선택 인자(upload_to, storage)

  - `upload_to='images/'` 저장될 경로를 작성하는 부분.

    ```python
    # articles/models.py
    
    class Article(models.Model):
        # 앞에는 기존 모델 속성
        image = models.ImageField(upload_to='images/', blank=True)
  ```
  
- `blank=True` : 이미지 필드에 빈 값(빈 문자열)이 허용되도록 설정. <= 없으면 이미지를 무조건적으로 넣어야 함. 이어서 이미지 없이도 업로드 가능, 선택적으로 업로드 할 수 있는 것.
  
- 참조 : https://docs.djangoproject.com/en/4.0/ref/models/fields/#filefield



#### upload_to

'upload_t'o argument는 업로드 디렉토리와 파일이름을 설정하는 2가지 방법 제공 :

문자열 값이나 경로 지정, 함수 호출

- 문자열 경로 지정 방식 : 파이썬의 strftime() 형식이포함될 수 있으며 파일 업로드 날짜/시간으로 대체

  ```python
  ```

  다양한 방식으로 업로드 되는 경로를 설정할 수 있다!

- 함수 호출 방식 : 좀 더 세부적으로 설정할 수 있다. 경로에 <filename> 이름으로 업로드

  'MEDIA_ROOT/image_<pk>/<filename>'

- blank : 기본 값 false, True인 경우 필드를 비워둘 수 있음! (DB에 '' 저장)

  유효성 검사에서 사용됨(is_valid : 빈값 허용 안됨. True가 되면 빈 값 허용하게 됨)

- null : 기본 값 false. True인 경우 Django는 빈 값을 DB에 NULL로 저장.

  - 주의사항 : 문자열 기반 필드(CharField, TextField)에는 사용하는 것을 피해야 함. 

    문자열 기반 필드에 True로 설정 시 데이터 없음을 뜻하는 것이 **''**, **NULL** 두 가지 가능하게 됨.

    데이터베이스 상에서 데이터 없음이 두 가지 가능한 값을 가지게 되면 중복되는 것.

  - 문자열 필드가 아닌 경우에는 상관 없음! <= 데이터 없음을 뜻하는 건 오직 NULL만 들어가게 됨.

- blank : validation_related & null : database_related 블랭크 트루, 널 트루는 ㅎ나만 쓰길 권장...??



#### ImageField 설정을 위한 단계

1. settings.py에 MEDIA_ROOT, MEDIA_URL 설정 <= static과 비슷!

2. upload_to 속성을 정의하여 업로드 파일에 사용할 MEDIA_ROOT의 하위 경로를 지정

3. 업로드 파일의 경로는 장고가 제공하는 'url' 속성을 통해 얻을 수 있다

   ```django
   <img src="{{ article.image.url }}" alt="{{ article.image }}">
   ```

- MEDIA_ROOT : 사용자가 업로드 한 파일을 보과할 디렉토리의 절대 경로

  - 실제 파일은 데이터베이스에 저장되지 않음!! 데이터베이스에 저장되는 것은 **파일의 경로!**
  - STATIC_ROOT와 MEDIA_ROOT는 겹치면 안 된다.

  ```python
  # settings.py
  MEDIA_ROOT = BASE_DIR / 'media'
  ```

  

- MEDIA_URL : 미디어 루트에서 제공되는 미디어를 처리하는 URL. 업로드 된 파일의 주소를 만들어주는 역할. 

  - 비어있지 않은 값이라면 반드시 slash/ 로 끝나야 함.
  - 마찬가지로 MEDIA_ROOT 등과 겹치면 안 됨.

  ```python
  # settings.py
  MEDIA_URL = '/media/'
  ```



#### 개발 단계에서 사용자가 업로드 한 파일 제공

- settings.MEDIA_URL : 업로드된 파일의 URL

- settings.MEDIA_ROOT : MEDIA_URL이 참조하는 파일의 실제 위치

  ```python
  # 프로젝트의 urls.py
  from django.conf import settings
  from django.conf.urls.static import static
  
  urlpatterns = [
      # ... the rest of your URLconf goes here ...
  ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
  ```

- 참조 : https://docs.djangoproject.com/en/4.0/howto/static-files/

- 이미지 필드 마이그레이션을 위해서는 pillow가 필요! 깐다! 이미 만들어진 필드가 있다면 마지막 컬럼으로 추가될 것

```bash
$ pip install Pillow
$ python manage.py makemigrations
$ python manage.py migrate
$ pip freeze > requirements.txt
```



##### * time 모듈의 strftime()

날짜 및 시간 객체 관련?



### 이미지 업로드(READ)

create 과정에 이미지 업로드 기능 필요!

```django

```

#### form 태그 - enctype(인코딩) 속성

1. `multipart/form-data` : 파일, 이미지 업로드 시, 인풋 타입이 파일일 경우 반드시 사용해야 한다.

   `<input type='file'>`을 사용할 경우에 쓴다

   ModelForm이미지 필드를 썼기 때문에 자동으로 `<input type='file' name='image' accept='image/*' id='id_image'>` 라고 뜬다. 

   **다른 파일 속성을 올리려면 FileField 사용!**

2. 기본값 <= 모든 문자 인코딩. 기본 값이 파일은 인코딩해주지 않기 때문에 위의 사항 추가 작성이 필수!

3. **(+)** input 요소 - accept : 고유 파일 유형 지정자, 파일을 검증하는 것은 아님!



#### views.py 수정

request.FILES 는 request.POST와는 다른 곳에 저장됨!

views 함수에서 POST로만 받으면 출력, 저장이 되지 않는다!

ModelForm 의 두번째 인자가 Files!  첫번째 인자는 Data.

```python
def create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST, request.FILES)
        # form = ArticleForm(request.POST, files=request.FILES)
        # form = ArticleForm(data=request.POST, files=request.FILES)
```

이후 파일 트리에 `MEDIA`생겨있음. <= settings.py에 MEDIA_ROOT에 지정해둠! (+upload_to 설정값)

그 안 `images/smaple.png/`.



#### 이후 출력

```django
<!-- detail.html -->
{% block %}
<h1>DETAIL</h1>
<img src="{{ article.image.url }}" alt="{{ article.image }}">
{% endblock %}
```

실제 이미지의 경로는 https://127.0.0:8000/media/images/smaple.png    <-중간 media가 MEDIA_URL

settings.py의 MEDIA_URL을 통해 만들어진 주소! 이 주소가 있어야 웹에서 이미지를 볼 수 있다.

미디어 루트는 물리적으로 미디어 파일이 어디에 저장될지 결정하는 요소

미디어 URL은 실제 사용자들이 이미지를 보기 위해 필요한 주소를 만들어주는 요소

static, media 파일 모두 서버에 요철해서 조회하는 것 => 서버 요청을 위해 url이 필요!



### 이미지 수정(UPDATE)

원래는 하나의 덩어리(바이너리 데이터)이기 때문에 일부 수정이 불가능함

=> 새로운 사진을 덮어 씌우는 방식을 사용한다

인코딩 타입에 속성 값이 있어야 이미지 데이터가 전송된다!

```django
<!-- update.html -->
{% block %}
<form action="{% url %}"
{% endblock %}
```

```python
# views.py
def update(request, pk):
    article = 
    if request.method == 'POST':
        form = ArticleForm(request.POST, request.FILES, instance=article)
```

* 인자 위치 상 instance는 request.POST, request.FILES보다 뒤쪽에 위치한다!
* 나중에 이미지를 여러 개 올리는 것은 formset을 활용한다



**if** 누군가가 같은 이름의 이미지를 업로드하게 된다면?

이름이 겹치니까 파일 이름 뒤에 랜덤 해쉬 값의 문자열을 붙여서 저장한다.



이미지 없이 올리는 글을 제대로 보여주기 위해(이미지가 없다면 출력할 이미지 경로가 없기에)

```django
<!-- detail.html -->
{% block %}
<h1>DETAIL</h1>
{% if article.image.url %}
<img src="{{ article.image.url }}" alt="{{ article.image }}">
{% endif %}
{% endblock %}
```

- if문 활용
- 정적 이미지 활용하여 대체 이미지를 출력하도록 하게 함



### 이미지 크기 변경(Resizing)

실제 원본 이미지를 서버에 그대로 업로드 하는 것은 서버에 부담이 크다. => 원본 저장 안 함

img 태그에서 직접 css를 통해 사이즈를 조절할 수도 있지만, 이미지 자체의 리사이징을 고려하기.

라이브러리 django imagekit 사용!

```bash
$ pip install django-imagekit
$ pip freeze > requirements.txt
```

```python
# settings.py
INSTALLED_APP = [
    ...
    'imagekit',
    ...
]
```

```python
# models.py 추가
from imagekit.models import ProcessedImageField
from imagekit.processors import Thumbnail     # 원래는 ResizeToFill
# 기존에 있던 이미지필드는 주석 처리!
class Article(models.Model):
    ...
    image = ProcessedImageField(blank=True, 
                                upload_to='thumbnails/',
                                processors=[Thumbnail(200, 300)],
                                format='JPEG',
                                options={'quality': 60})
    ...
```

- 문서 참조 : https://github.com/matthewwithanm/django-imagekit
- 만약 모델이 바뀌었다면 `makemigrations`, `migrate` 과정을 다시 거친다.



원본도 저장하고, 썸네일도 같이 쓰는 방법도 있음!

=> 원본을 위해 기존 이미지 필드도 쓰고, imagekit 통한 이미지 필드도 추가 작성!

현재는 django imagekit을 사용했지만 다르게 리사이징할 수 있는 방법도 존재함.
