---
layout: post
title: Django basic 01
category: Django
permalink: /django/:year/:month/:day/:title/

tag: [Django, Python]
---

## 프로젝트 만들기

~~~
$ django-admin startproject mysite
~~~

startproject에서 생성되는 것.
~~~
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
~~~

    * mysite/ 
        - 디텍토리 바깥의 디렉토리는 단순히 프로젝트를 담는 공간
    * manage.py
        - 장고 프로젝트와 다양한 방법으로 상호작용하는 커맨드라인의 유틸리티
        - 자세한 내용은 [Django-admin and manage.py](https://docs.djangoproject.com/ko/1.11/ref/django-admin/)
    * mysite/mysite/
        - 디토리 내부에는 프로젝트를 위한 실제 python package들이 저장됨
        - 이 디렉토리 이름을 이용하여, (mysite.urls와 같은 식으로) project 어디서나 python 패키지들을 import 할 수 있음
    * mysite/__init__.py
        - 해당 디렉토리를 패키지처럼 다루기 위한 용도
    * mysite/settings.py
        - 현재 장고 프로젝트의 환경/구성을 저장함
        - [Django settings](https://docs.djangoproject.com/ko/1.11/topics/settings/)
    * mysite/urls.py
        - 현재 장고 프로젝트의 URL 선언을 저장함
        - 장고로 작성된 사이트의 '목차'라고 할 수 있음
        - [URL dispatcher](https://docs.djangoproject.com/ko/1.11/topics/http/urls/)
    * mysite/wsgi.py
        - 현재 프로젝트를 서비스하기 위한 WSGI 호환 웹 서버의 진입점.
        - [How to deploy with WSGI](https://docs.djangoproject.com/ko/1.11/howto/deployment/wsgi/)

***

## 개발서버
    * 프로젝트가 제대로 동작하는지 확인해보자. mysite 디렉토리로 이동하고 다음 명령어를 입력해보자.
    ~~~
    $ python3 manage.py runserver
    ~~~

    * 서버가 동작하기 시작했으니, 웹브라우져에 http://127.0.0.1:8000 를 입력.
        - 기본 runserver 명령의 내부IP port는 8000
        - port를 변경하고 싶으면, 인수를 전달해주면 됨(python3 manage.py runserver 8080)
        - IP를 변경하고 싶으면, 인수로 전달가능(python3 manage.py runserver 0:8080)
    * runserver의 자동변경 기능
        - 개발 서버는 요청이 들어올 때마다 자동으로 Python 코드를 다시 불러줌.
        - 코드 변경 사항을 반영하기 위해서 굳이 서버를 재가동 하지 않아도 됨
        - 단, 파일을 추가하는 등의 몇몇의 동작은 개발 서버가 자동으로 인식하지 못함.



## project vs. app
    * app : 블로그나 공공 기록물을 위한 데이터베이스나 간단한 설문조사 앱과 같은 특정한 기능을 수행하는 웹 어플리케이션을 말함.
    * project : 이런 특정 웹 사이트를 위한 app들과 각 설정들을 한데 묶어 놓은 것.
    * project는 다수의 app을 포함할 수 있고, app은 다수의 project에 포함될 수 있음



[Reference 0](https://docs.djangoproject.com/ko/1.11/intro/tutorial01/)

