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

​    

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

  ​    

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

## Reference

[가상환경](https://medium.com/@psychet_learn/python-%EA%B0%80%EC%83%81%ED%99%98%EA%B2%BD-a87fc6e4d12b)

[장고걸스 튜토리얼](https://tutorial.djangogirls.org/ko/)

