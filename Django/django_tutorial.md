# Django 간단한 블로그 만들기

​    

## 1. 가상환경 설정하기 (Window)

가상환경이란 자신이 원하는 환경을 구축하기 위해 필요한 모듈만 담아 놓는 바구니를 말한다. 프로젝트 기초 전부를 분리해 사용할 수 있기 때문에 유용하다.

​    

### Virtualenv를 통한 설정

* 가상환경 생성하기

먼저, 명령 프롬프트에서 가상환경을 생성할 폴더를 만들고 해당 폴더로 이동한다. 홈 디렉토리(C:\Users\Name)에 생성하면 적당한 선택이다.

```
mkdir djangogirls
cd djangogirls
```

그리고 가상 환경을 생성한다. 가상환경을 이름을 설정할 수 있는데 여기서는 myvenv로 생성하기로 한다.

```
python -m venv myvenv
```

* 가상환경 사용하기

```
myvenv\Scripts\activate
```

만일 실행이 안될 경우, cmd를 관리자 권한으로 실행한다.

​    

## 2. 장고 설치하기

* pip을 최신 버전으로 업데이트하기

  ```
  python3 -m pip install --upgrade pip
  ```

* 장고 설치하기

  ```
  pip install django~=2.0.0
  ```

​    

## 3. 장고 프로젝트 시작하기

* 생성할 장고 프로젝트의 구조

  ```
  djangogirls
  ├───manage.py
  └───mysite
          settings.py
          urls.py
          wsgi.py
          __init__.py
  ```

  * manage.py: 사이트 관리를 도와주는 파일
  * settings.py: 웹사이트 설정이 있는 파일
  * urls.py: urlresolver가 사용하는 패턴 목록을 포함하는 파일

​    

* 장고 프로젝트 시작하기 명령 (mysite는 프로젝트 이름이므로 변경가능)

  * 현재 디렉토리에서 장고 프로젝트 생성

    ```
    (myvenv) C:\Users\Name\djangogirls> django-admin.py startproject mysite .
    ```

​    

* settings.py 설정 변경

  * 정확한 현재 시간 설정 (선택)

    ```python
    TIME_ZONE = 'Asia/Seoul'
    ```

  * 정적파일 경로 추가

    * 파일 끝에 STATIC_URL 바로 밑에 STATIC_ROOT 추가

      ```python
      STATIC_URL = '/static/'
      STATIC_ROOT = os.path.join(BASE_DIR, 'static')
      ```

  * 호스트 이름 일치시키기

    * DEBUG가 True이고 ALLOWED_HOSTS가 비어 있으면, 호스트는 ['localhost', '127.0.0.1', '[::1]'] 에 유효

    * PythonAnywhere에 앱을 배포한다면 다음과 같이 수정

      ```python
      ALLOWED_HOSTS = ['127.0.0.1', '.pythonanywhere.com']
      ```

  * 데이터베이스 설정하기

    * settings.py 파일 안에 sqlite 데이터베이스가 기본적으로 설치되어 있음 (기본 장고 데이터베이스 어댑터)

      ```python
      DATABASES = {
          'default': {
              'ENGINE': 'django.db.backends.sqlite3',
              'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
          }
      }
      ```

    * 데이터 베이스 생성 명령

      ```
      (myvenv) ~/djangogirls$ python manage.py migrate
      ```

​    

* 웹 서버 시작하기

  ```
  (myvenv) ~/djangogirls$ python manage.py runserver
  ```

​    

## 4. 장고 앱 만들기

* 장고 앱 만들기

  * 프로젝트 내부에 장고 애플리케이션 생성 (blog는 앱 이름이므로 변경 가능)

    ```
    (myvenv) ~/djangogirls$ python manage.py startapp blog
    ```

  * settings.py 속 INSTALLED_APPS에 새로 생성한 앱 등록 (앱 이름을 끝에 추가)

    ```python
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'blog',
    ]
    ```

  * 앱 생성 후 프로젝트 구조

    ```
        djangogirls
        ├── mysite
        |       __init__.py
        |       settings.py
        |       urls.py
        |       wsgi.py
        ├── manage.py
        └── blog
            ├── migrations
            |       __init__.py
            ├── __init__.py
            ├── admin.py
            ├── models.py
            ├── tests.py
            └── views.py
    ```

​    

## 5. 모델 만들기

* 블로그 글 모델 객체 만들기

  * blog/models.py에 Model 객체를 선언해 모델 생성 (파일 내 내용 전부 삭제 후 아래 코드 추가)

    ```python
    from django.conf import settings
    from django.db import models
    from django.utils import timezone
    
    
    class Post(models.Model):
        author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
        title = models.CharField(max_length=200)
        text = models.TextField()
        created_date = models.DateTimeField(
                default=timezone.now)
        published_date = models.DateTimeField(
                blank=True, null=True)
    
        def publish(self):
            self.published_date = timezone.now()
            self.save()
    
        def __str__(self):
            return self.title
    ```

* 데이터베이스에 모델 추가하기

  * 모델에 생긴 변화를 알리기 위해 migration 파일 생성

    ```
    python manage.py makemigrations blog
    ```

  * 데이터베이스에 반영하기

    ```
    python manage.py migrate blog
    ```

​    

## 6. 장고 관리자

* 관리자 페이지 언어 변경 (선택)
  * setting.py의 LANGUAGE_CODE = 'ko'로 바꿀 것

* blog/admin.py 파일 내용 수정

  * 생성한 모델 import 및 모델 등록

  ```python
  from django.contrib import admin
  from .models import Post
  
  admin.site.register(Post)
  ```

* 관리자 계정 생성

  * 서버를 실행하는 중에 관리자 계정을 생성해야만 한다.
  * 코드 실행 후 유저 네임, 이메일 주소 및 암호 입력

  ```
  (myvenv) ~/djangogirls$ python manage.py createsuperuser
  Username: admin
  Email address: admin@admin.com
  Password:
  Password (again):
  Superuser created successfully.
  ```

​    

## 7. 서버 배포하기

### PythonAnywhere로 배포

* .gitignore 파일 설정 (github에 코드 push 전)

  * 특정 파일들을 .gitignore 파일에 등록하면, git이 해당 파일들의 변화는 무시하고 추적하지 않게끔 할 수 있다. 

  * 여기서 db.sqlite3는 로컬 데이터베이스이고 이는 테스트 공간으로만 사용하는 것이 좋으므로, github 저장소에 저장하지 않는다.

  * 에디터를 사용해 아래와 같은 내용으로 .gitignore 파일을 프로젝트 폴더(djangogirls)에 만들자.

    ```python
    *.pyc
    *~
    __pycache__
    myvenv
    db.sqlite3
    /static
    .DS_Store
    ```

​    

* PythonAnywhere 서버에 Github에서 코드 가져오기

  * PythonAnywhere 콘솔에 다음 코드 입력 (my-first-blog는 github 저장소 이름)

  ```
  git clone https://github.com/<your-github-username>/my-first-blog.git
  ```

​    

* PythonAnywhere에서 가상환경 생성 및 활성화하기

  ```
  $ cd my-first-blog
  
  $ virtualenv --python=python3.6 myvenv
  Running virtualenv with interpreter /usr/bin/python3.6
  [...]
  Installing setuptools, pip...done.
  
  $ source myvenv/bin/activate
  
  (myvenv) $  pip install django~=2.0
  Collecting django
  [...]
  Successfully installed django-2.0.6
  ```

​    

* PythonAnywhere에서 데이터베이스 초기화 및 관리자 계정 생성하기

  ```
  (mvenv) $ python manage.py migrate
  Operations to perform:
  [...]
    Applying sessions.0001_initial... OK
  (mvenv) $ python manage.py createsuperuser
  ```

​    

* web app으로 블로그 배포하기

  * PythonAnywhere 대시보드에서 **Web**을 클릭하고 **Add a new web app**을 선택

  * 도메인 이름 확정 후, **수동설정(munual configuration)**을 클릭하고 **Python 3.6**을 선택한 다음, 다음(Next)을 클릭

  * 가상환경 설정하기

    * PythonAnywhere 설정 화면의 가상환경(Virtualenv) 섹션에서 **가상환경 경로를 입력해주세요(Enter the path to a virtualenv)**라고 쓰여 있는 빨간색 글자를 클릭하고 **/home/<your-username>/my-first-blog/myvenv/** 라고 입력
    * 이동 경로를 저장하려면, 파란색 박스에 체크 표시하고 클릭

  * WSGI 파일 설정하기

    * PythonAnywhere에게 웹 애플리케이션의 위치와 Django 설정 파일명을 알려주는 역할

    * "WSGI 설정 파일(WSGI configuration file)" 링크(페이지 상단에 있는 "코드(Code)" 섹션 내 **/var/www/<your-username>_pythonanywhere_com_wsgi.py** 부분)를 클릭

    * 모든 내용을 삭제하고 아래 코드 추가

      ```python
      import os
      import sys
      
      path = '/home/<your-PythonAnywhere-username>/my-first-blog'  # PythonAnywhere 계정으로 바꾸세요.
      if path not in sys.path:
          sys.path.append(path)
      
      os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'
      
      from django.core.wsgi import get_wsgi_application
      from django.contrib.staticfiles.handlers import StaticFilesHandler
      application = StaticFilesHandler(get_wsgi_application())
      ```

    * **저장(Save)**을 누르고 **웹(Web)** 탭 누르기

  * 큰 녹색 **다시 불러오기(Reload)** 버튼을 누르면, 모든 배포 작업 완료

​    

## 8. URL 설정 및 뷰(View) 만들기

* 장고는 **URLconf (URL configuration)**를 사용

* **URLconf**는 장고에서 URL과 일치하는 뷰를 찾기 위한 패턴들의 집합이다.

* mysite/urls.py에서 url 설정

  * mysite/urls.py의 초기 코드

    ```python
    """mysite URL Configuration
    
    [...]
    """
    from django.contrib import admin
    from django.urls import path
    
    urlpatterns = [
        path('admin/', admin.site.urls),
    ]
    ```

  * blog 앱에서 mysite/urls.py로 url들 가져오기

    ```python
    from django.contrib import admin
    from django.urls import path, include  # include 추가
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('blog.urls')),  # blog.urls를 가져오는 코드 추가
    ]
    ```

* blog/urls.py 파일 생성 및 코드 추가

  ```python
  from django.urls import path
  from . import views
  
  urlpatterns = [
      path('', views.post_list, name='post_list'),  # post_list 뷰를 루트 url에 할당
  ]
  ```

* 뷰 만들기

  * 뷰는 애플리케이션의 로직을 넣는 곳으로, 모델에서 필요한 정보를 받아와 템플릿에 전달하는 역할을 한다.

  * blog/views.py 안에 뷰 만들기

    * 초기 코드

      ```python
      from django.shortcuts import render
      
      # Create your views here.
      ```

    * 뷰 만들기

      ```python
      from django.shortcuts import render
      
      # Create your views here.
      def post_list(request):
          '''
          요청(request)을 넘겨받아 render메서드를 호출한다. 
          이 함수는 render 메서드를 호출하여 받은(return) blog/post_list.html 템플릿을 보여준다.
          '''
          return render(request, 'blog/post_list.html', {})
      ```

​    

## 9. 템플릿 만들기

* 템플릿이란 서로 다른 정보를 일정한 형태로 표시하기 위해 재사용 가능한 파일을 말한다. (장고의 템플릿 양식은 HTML)

* blog/templates/blog  디렉토리를 만들고, 디렉토리 내부에 html 파일 생성

  * 하위 디렉토리를 만드는 것은 폴더가 구조적으로 복잡해졌을 때 찾기 쉽게 하기 위한 관습적 방법이다!
  * post_list.html 파일 생성 및 원하는 html 코드 추가
  * 예시

  ```html
  <html>
      <head>
          <title>Django Girls blog</title>
      </head>
      <body>
          <div>
              <h1><a href="">Django Girls Blog</a></h1>
          </div>
  
          <div>
              <p>published: 14.06.2014, 12:14</p>
              <h2><a href="">My first post</a></h2>
              <p>Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum. Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus.</p>
          </div>
  
          <div>
              <p>published: 14.06.2014, 12:14</p>
              <h2><a href="">My second post</a></h2>
              <p>Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum. Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut f.</p>
          </div>
      </body>
  </html>
  ```

​        

## 10. 모델로부터 템플릿에 동적으로 데이터 가져오기

* 뷰에서 모델 연결하기

  * blog/views.py 파일 수정

    ```python
    from django.shortcuts import render
    from django.utils import timezone  # 쿼리셋 동작을 위해 추가
    from .models import Post  # Post 모델을 사용하기 위해 import
    
    def post_list(request):
        # 쿼리셋 추가
        posts = Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')
        return render(request, 'blog/post_list.html', {'posts': posts})  #  'posts' 매개변수 추가
    ```

* 템플릿에서 템플릿 태그를 사용해 보여주기

  * blog/templates/blog/post_list.html 에서 템플릿 태그 사용

    ```python
    <div>
        <h1><a href="/">Django Girls Blog</a></h1>
    </div>
    
    # 장고 템플릿에서의 루프 테크닉
    {% for post in posts %}  
        <div>
            <p>published: {{ post.published_date }}</p>
            <h1><a href="">{{ post.title }}</a></h1>
            <p>{{ post.text|linebreaksbr }}</p>
        </div>
    {% endfor %}
    ```

​    

## 11. 간략하게 CSS 다루기

* 부트스트랩 설치하기

  * 인터넷에 있는 파일을 연결하므로써 진행

  * html 파일 내 <head>에 아래 링크 추가

    ```html
    <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
    <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">
    ```

* 정적 파일 (static files)

  * css 파일과 이미지 파일이 해당

  * 앱에 static 폴더를 추가하고 폴더 안에 정적 파일 저장 (장고는 static 폴더를 자동을 찾을 수 있음!)

    * blog 앱 안에 static 폴더 생성

    ```
    djangogirls
        ├── blog
        │   ├── migrations
        │   ├── static
        │   └── templates
        └── mysite
    ```

  * static 폴더 내부에 css 폴더를 만들고, css 파일을 생성해 저장

    * blog/static/css/blog.css 파일 생성

    ```
    djangogirls
        └─── blog
             └─── static
                  └─── css
                       └─── blog.css
    ```

    * blog.css 파일에 다음과 같은 예시 코드 추가

    ```css
    .page-header {
        background-color: #ff9400;
        margin-top: 0;
        padding: 20px 20px 20px 40px;
    }
    
    .page-header h1, .page-header h1 a, .page-header h1 a:visited, .page-header h1 a:active {
        color: #ffffff;
        font-size: 36pt;
        text-decoration: none;
    }
    
    .content {
        margin-left: 40px;
    }
    
    h1, h2, h3, h4 {
        font-family: 'Lobster', cursive;
    }
    
    .date {
        color: #828282;
    }
    
    .save {
        float: right;
    }
    
    .post-form textarea, .post-form input {
        width: 100%;
    }
    
    .top-menu, .top-menu:hover, .top-menu:visited {
        color: #ffffff;
        float: right;
        font-size: 26pt;
        margin-right: 20px;
    }
    
    .post {
        margin-bottom: 70px;
    }
    
    .post h1 a, .post h1 a:visited {
        color: #000000;
    }
    ```

  * html 파일에서 정적 파일 로딩 및 css 파일 링크 추가

    * class를 추가하고 정적 파일을 로딩하여 css파일과 링크한 html 파일 예시

    ```html
    {% load static %}  // 정적 파일 로딩
    <html>
        <head>
            <title>Django Girls blog</title>
            <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
            <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">
            <link rel="stylesheet" href="{% static 'css/blog.css' %}">  // css 파일 링크 추가
        </head>
        <body>
            <div>
                <h1><a href="/">Django Girls Blog</a></h1>
            </div>
            <div class="content container">
                <div class="row">
                    <div class="col-md-8">
                        {% for post in posts %}
                            <div class="post">
                                <div class="date">
                                    <p>published: {{ post.published_date }}</p>
                                </div>
                                <h1><a href="">{{ post.title }}</a></h1>
                                <p>{{ post.text|linebreaksbr }}</p>
                            </div>
                        {% endfor %}
                    </div>
                </div>
            </div>
        </body>
    </html>
    ```

​    

## 12. 장고 템플릿 확장하기

* 템플릿 확장은 웹사이트 안의 서로 다른 페이지에서 HTML의 일부를 동일하게 재사용 할 수 있게 하는 것을 말한다.

* 기본 템플릿 생성하기

  * blog/templates/blog/ 에 base.html 파일 생성

  * block 템플릿 태그를 적절히 삽입한 뼈대 html 코드 추가

    *  post_list.html 파일의 전체 코드 중 <body> 태그 내용만 바꿔 다음과 같이 base.html에 코드 추가

    ```html
    {% load static %}
    <html>
        <head>
            <title>Django Girls blog</title>
            <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
            <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">
            <link href='//fonts.googleapis.com/css?family=Lobster&subset=latin,latin-ext' rel='stylesheet' type='text/css'>
            <link rel="stylesheet" href="{% static 'css/blog.css' %}">
        </head>
        <body>
            <div class="page-header">
                <h1><a href="/">Django Girls Blog</a></h1>
            </div>
            <div class="content container">
                <div class="row">
                    <div class="col-md-8">
                    {% block content %}
                    {% endblock %}
                    </div>
                </div>
            </div>
        </body>
    </html>
    ```

* 기본 템플릿과 확장 템플릿 연결하기

  * 확장할 html 파일에는 블록에 대한 템플릿의 일부만 남김

  * block 템플릿 태그 추가

  * 확장 태그를 파일 맨 앞에 추가

  * blog/templates/blog/post_list.html을 다음 코드로 변경

    ```html
    {% extends 'blog/base.html' %}
    
    {% block content %}
        {% for post in posts %}
            <div class="post">
                <div class="date">
                    {{ post.published_date }}
                </div>
                <h1><a href="">{{ post.title }}</a></h1>
                <p>{{ post.text|linebreaksbr }}</p>
            </div>
        {% endfor %}
    {% endblock %}
    ```

​    

## Reference

[가상환경](https://medium.com/@psychet_learn/python-%EA%B0%80%EC%83%81%ED%99%98%EA%B2%BD-a87fc6e4d12b)

[장고걸스 튜토리얼](https://tutorial.djangogirls.org/ko/)

