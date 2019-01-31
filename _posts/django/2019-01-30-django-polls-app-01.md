---
layout: post
title: Django Polls App Part1/7
category: Django
permalink: /django/:year/:month/:day/:title/

tag: [Django, Python]
---

# 설문조사 앱 만들기, Part1/7

이번 app은 submodule 말고, top-level에서 바로 import 할 수 있게끔 manage.py 와 같은 레벨에서 생성해보자.

~~~
$ python3 manage.py startapp polls
~~~

polls 라는 디렉토리가 생겼고, 구조는 아래와 같음
~~~
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
~~~

***

## 첫번째 View 작성하기

첫번째 뷰를 작성해보자. "polls/view.py" 를 열어서 다음과 같이 입력하자.

~~~
# polls/views.py

from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
~~~

장고에서 가장 간단한 형태의 view이다. view를 호출하려면 이와 연결된 URL이 있어야 하는데, 이를 위해 URLconf가 사용됨

polls 디렉토리에서 URLconf를 생성하려면,  urls.py 파일을 생성하자. 

~~~
# polls/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]

~~~

다음 단계는, project 최상단의 URLconf에서 polls.urls 모듈을 바라보게 설정합니다. mysite/urls.py 파일을 열고, django.conf.urls.include를 import 하자. 그리고 urlpatterns 리스트에 include() 함수를 다음과 같이 추가한다.
~~~
# mysite/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
~~~
include() 함수는 다른 URLconf 를 참조할 수 있도록 도와준다. 장고는 inlcude()를 발견할때마다, 그 지점에 일치하는 URL의 부분을 잘라버리고, 남은 부분을 후 처리를 위해 포함된 URLconf에 나머지 string을 보냅니다.
즉, /polls/some/method 를 요청받으면 some/method 가 polls/urls.py의 URLconf로 넘어갑니다.

inlcude() 에 숨은 아이디어 덕분에, URL을 쉽게 연결할 수 있다.(plug-and-ply) polls 앱 자체적은 URLconf(/polls/urls.py)가 존재하는 한, "/polls/", 혹은 "/fun_polls/" 혹은 "/content/polls/" 같은 그 어떤 경로에 붙이더라도 app은 여전히 잘 동작할 것이다.

    * 언제 inlcude()를 사용해야하나?
        - admin.site.urls 를 제외한, 다른 URL 패턴을 include할 때마다 항상 include()를 사용해야 함.

이제 index view 가 URLconf에서 연결이 되었다. 확인을 위해서 다음을 입력하자
~~~
$ python3 manage.py runserver
~~~

url() 함수에는 4개의 인수가 전달되었습니다. 두개의 필수인수로 regex와 view가 있고,
두개의 옵션 인수로 kwargs와 name이 있습니다. 여기서 인수가 무엇인지 알아보자

***
url() 인수: view
장고에서 일치하는 정규 표현식을 찾아내면, HttpRequest 객체를 첫번째 인수로 하고, 정규표현식에서 '잡힌' 값들을 나머지 인수로 하여 특정한 view 함수를 호출합니다. 만약 정규표현식이 간단한 형석이라면, 잡힌 값들을 단순히 순서 기반의 인수로서 함수에 넘겨진다. 만약 이름 기반의 정규 표현식이라면, 잡힌 값들은 키워드 인수들로 함수에 넘겨진다.

***
url() 인수: kwargs
임의의 키워드 인수들은 목표한 view에 사전형으로 전달된다. 그러나 이번 앱에서 사용하지 않을 것임.

***
url() 인수: name
URL 에 이름을 지으면, 템플릿을 포함한 장고 어디에나 명확하게 참조할 수 있다. 이 강력한 기능을 이용하여, 단 하나의 파일만 수정해도 project내의 모든 URL 패턴을 바꿀 수도 있도록 도와준다. 

***
[Reference 한글](https://docs.djangoproject.com/ko/1.11/intro/tutorial01/)
<br>
[Reference 영문](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)

