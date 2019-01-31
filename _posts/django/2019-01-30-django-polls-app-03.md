---
layout: post
title: Django Polls App Part3/7
category: Django
permalink: /django/:year/:month/:day/:title/

tag: [Django, Python]
---

# 설문조사 앱 만들기, Part3/7
이번에는 poll app에 'view'를 추가해보자.

## 개요
view는 장고 app이 일반적으로 특정 기능과 템플릿을 제공하는 웹페이지의 한 종류이다. 예를들어, 블로그 app 의 경우 다음과 같은 view를 가질 수 있다.
    * Blog 홈페이지 - 가장 최근의 항목들을 보여준다.
    * 항목 detail 페이지 - 하나의 항목에 연결하는 영구적인 링크(permalink)를 제공한다.
    * 년도별 축적 페이지 - 주어진 연도의 모든 월별 항목들을 표시합니다.
    * 월별 축적 페이지 - 주어진 월의 날짜별 항목들을 표시합니다.
    * 날짜별 축적 페이지 - 주어진 날짜의 모든 항목들을 표시합니다.
    * 댓글 기능 - 특정 항목의 댓글을 다룰 수 있는 기능

우리가 만드는 poll 어플리케이션에서 다음과 같은 네개의 view 를 만들어 보겠습니다.
    * 질문 "색인" 페이지 -- 최근의 질문들을 표시합니다.
    * 질문 "세부" 페이지 -- 질문 내용과, 투표할 수 있는 서식을 표시합니다.
    * 질문 "결과" 페이지 -- 특정 질문에 대한 결과를 표시합니다
    * 투표 기능 -- 특정 질문에 대해 특정 선택을 할 수 있는 투표 기능을 제공합니다.

Django 에서는, 웹 페이지와 기타 내용들이 view 에 의해 제공됩니다. 각 view 는 간단한 Python 함수(혹은 클래스를 사용할 경우엔 메소드) 를 사용하여 작성됩니다. Django 는 요청된 URL 을 조사하여 view 를 선택합니다. (조금 더 정확히 말하자면, 도메인 네임에 따라오는 URL 부분을 이용합니다.)

웹에서 "ME2/Sites/dirmod.asp?sid=&type=gen&mod=Core+Pages&gid=A6CD4967199A42D9B65B1B" 같은 못생긴 주소를 본 적이 있을겁니다. 다행스럽게도 Django 에서는 적어도 이것보단 훨씬 예쁜 URL 패턴 을 만들 수 있습니다.

URL 패턴은 URL 을 간단하게 일반화 시킨것을 말합니다. 예를 들어, /newsarchive/<year>/<month>/ 같이 표현할 수 있습니다.

view 를 URL 에서 얻기위해, Django 는 **URLconfs** 라고 부르는 것을 사용합니다. URLconf 는 URL 패턴(정규 표현식으로 표현)과 view 를 연결한 것입니다.

이 튜토리얼은 URLconfs 를 사용하기 위한 기본적인 절차를 안내합니다. 더 자세한 정보를 얻으시려면 django.urls 를 참고해 주십시요.

***
## 조금 더 View 작성하기
이제, polls/views.py에 view를 추가해 보자. 이 view들은 인수를 받기 때문에 조금 모양이 다르다.
~~~
# polls/views.py

def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
~~~

polls.urls 모듈에서 새로 작성된 view들을 연결하기 위해 다음과 같이 url() 함수 호출을 추가한다.

~~~
# polls/urls.py
from django.urls import path

from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
~~~

브라우저에 "/polls/34/" 를 입력해 보세요. 이 주소에 접속하면 detail() 함수를 호출하여 URL 에 입력한 ID 를 출력할 것입니다. "/polls/34/results/" 와 "/polls/34/vote/" 도 실행해 보세요. 투표 결과와 투표 페이지의 뼈대가 되는 페이지가 출력될 것입니다.

사용자가 웹사이트의 페이지를 요청할때, ("/polls/34" 를 입력했다고 가정하겠습니다.) Django 는 mysite.urls 모듈을 불러옵니다. ROOT_URLCONF 설정에 의해 설정되어 있기 때문입니다. mysite.urls 에서 urlpatterns 이라는 변수를 찾아, 정규표현식을 순서대로 따라갑니다. 'polls/' 를 찾은 후, 일치하는 문자열("polls/")을 버립니다. 이후 남은 문자인 "34/" 를 polls.urls URLconf 에게 전달하여 남은 처리를 진행합니다. 이제 '<int:question_id>/' 에 패턴이 일치하고, 그 결과로 detail() 함수가 호출됩니다.

~~~
detail(request=<HttpRequest object>, question_id='34')
~~~

question_id='34' 부분은 <int:question_id>에서 왔습니다. 패턴을 괄호로 감싸 일치하는 패턴의 문자열을 잡아낸 후, 이를 view 함수의 인수로서 넘깁니다. :question_id> 는 일치하는 패턴을 구별하기 위해 정의한 이름입니다. 그리고 <>int: 는 URL 경로의 부분과 일치해야하는 패턴을 결정하는 변환기이다.

***

## View가 실제로 뭔가 하도록 하기
각 view 는 두가지중 하나를 하도록 되어 있습니다. 
    1. 요청된 페이지의 내용이 담긴 HttpResponse 객체를 반환하거나, 
    2. 혹은, Http404 같은 예외를 발생하게 해야합니다. 
나머지는 당신에게 달렸습니다.

당신이 작성한 view 는 데이터베이스의 레코드를 읽을 수도 있습니다. 또한 view 는 Django 나, Python 에서 서드파티로 제공되는 템플릿 시스템을 사용 할 수도 있습니다. view 는 PDF 를 생성하거나, XML 출력을 하거나, 실시간으로 ZIP 파일을 만들 수 있습니다. view 는 당신이 원하는 무엇이든, Python 의 어떤 라이브러리라도 사용할 수 있습니다.

모든 Django 는 HttpResponse 객체나, 혹은 예외(exception) 를 원합니다.

왜냐면, 그렇게 다루는게 편리하기 때문입니다. Tutorial 2 의 예제에서 Django 가 자신만의 데이터베이스 API 를 사용해봅시다. 새로운 index() view 하나를 호출했을 때, 시스템에 저장된 최소한 5 개의 투표 질문이 콤마로 분리되어, 발행일에 따라 출력됩니다.

~~~
# polls/views.py

from django.http import HttpResponse
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

# Leave the rest of the views (detail, results, vote) unchanged
~~~

여기 몇가지 문제가 있습니다. view 에서 페이지의 디자인이 하드코딩 되어 있습니다. 만약 페이지가 보여지는 방식을 바꾸고 싶다면, 이 Python 코드를 편집해야만 할겁니다. 그럼, view 가 사용할 수 있는 템플릿을 작성하여, Python 코드로부터 디자인을 분리하도록 Django 의 템플릿 시스템을 사용해 봅시다.

우선, polls 디렉토리에 templates 라는 디렉토리를 만듭니다. Django 는 여기서 템플릿을 찾게될 것입니다.

project 의 TEMPLATES 설정에는 Django 가 어떻게 template 을 불러오고 랜더링 할 것인지 를 서술합니다. 기본 설정 파일은 APP_DIRS 옵션이 True 로 설정된 DjangoTemplates 백엔드를 구성합니다 관례에 따라, DjangoTemplates 은 각 INSTALLED_APPS 디렉토리의 "templates" 하위 디렉토리를 탐색합니다.

방금 만든 templates 디렉토리 내에 polls 라는 디렉토리를 생성하고, 그 안에 index.html 을 만듭니다. 즉, template 은 polls/templates/polls/index.html 과 같은 형태가 됩니다. app_directories template 로더가 그렇게 동작하기 때문에, Django 에서 이 template 을 단순히 polls/index.html 로 참조할 수 있습니다

~~~
> 템플릿 네임스페이싱
polls/templates/polls 라고 만들 필요 없이, 그냥 polls/templates 에 넣어도 되지 않을까? 라고 생각할지도 모릅니다. 그러나 이것은 별로 좋은 생각이 아닙니다. Django 는 이름이 일치하는 첫번째 template 을 선택하는데, 만약 동일한 template 이름이 다른 어플리케이션에 있을 경우 Django 는 이 둘간의 차이를 구분하지 못합니다. Django 에게 정확한 template 을 지정하기 위해서 가장 편리한 방법은 이름공간 으로 구분짓는 것인데, 어플리케이션의 이름으로 된 디렉토리에 이러한 template 들을 넣으면 됩니다.
~~~

template에 다음과 같은 코드를 입력합니다.
~~~
polls/templates/polls/index.html
{% if latest_question_list %}
    <ul>
        {% for question in latest_question_list %}
            <li><a href="/polls/{{ question.id }}/">{{question.question_text }}</a><li>
        {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
~~~
이제, template 을 이용하여 polls/views.py 에 index view를 업데이트 해보자.

~~~
polls/views.py

from django.http import HttpResponse
from django.template import loader

from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
~~~

이 코드는 polls/index.html template 을 불러온 후, context 를 전달합니다. context 는 template 에서 쓰이는 변수명과, Python 의 객체를 연결하는 사전형 값입니다.

브라우저에서 "/polls/" 페이지를 불러오면, Tutorial 2 에서 작성한 "What's up" 질문이 포함된 리스트가 표시됩니다. 표시된 질문의 링크는 해당 질문에 대한 세부 페이지를 가르킵니다.

## A shortcut: Render()

template에 context를 채워넣어 표현한 결과를 HttpResponse 객체와 함께 돌려주는 구문은 자주 쓰는 용법이다. 따라서 장고는 이런 표현을 쉽게 표현할 수 있도록 단축 기능을 제공한다. index() view 를 단축기능으로 작성하면 다음과 같다. 

~~~
# polls/views.py
from django.shortcuts import render
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_data')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
~~~

모든 view에 적용한다면, 더이상 loader와 HttpResponse를 import 하지 않아도 된다. 
(만약, detail, results, vote에서 stub 메소드를 가지고 있다면, HttpResponse를 유지해야할 것이다.)

render()함수는 request 객체를 첫번째 인수로 받고, template 이름을 두번째 인수로 받으며, context 사전형 객체를 세번째 선택적 인수로 받습니다. 인수로 지정된 context로 표현된 template 의 HttpResponse 객체가 반환된다. 


***
## 404 에러 일으키기
이제, 질문의 detail view 에 태클을 걸어보겠습니다. detail view 는 지정된 설문조사의 질문 내용을 보여줍니다. 다음과 같습니다.

~~~
# polls/views.py
from django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
~~~
여기 새로운 내용이 추가되었습니다. view는 요청된 질문의 ID가 없을 경우 Http404 예외를 발생시킨다.

조금 후에 polls/detail.html template에 무엇ㅇ르 넣을 수 있는지 논의하겠지만, 일단 위의 예제를 동작시키기 위해 아래의 내용이 들어있는 파일을 작성하세요.
~~~
# polls/templates/polls/detail.html
{{ question }}
~~~

이제 시작해도 된다.

## A shortcut: get_object_or_404()
만약 객체에 존재하지 않을 때 get()을 사용하여 Http404 예외를 발생시키는 것은 자주 쓰이는 용법이다. 
Django에서 이 기능에 대한 단축기능을 제공한다. detail() view 를 단축 기능으로 작성하면 다음과 같습니다.

~~~
# polls/views.py
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
~~~

get_object_or_404() 함수는 Django 모델을 첫번째 인자로 받고, 몇개의 키워드 인수를 모델 관리자의 get()함수에 넘긴다. 만약 객체가 존재하지 않을 경우, Http404 예외가 발생한다.

~~~
> 철학
상위 계층에서 ObjectDoesNotExist 예외를 자동으로 잡아 내는 대신 get_object_or_404() 도움 함수를 사용하거나, ObjectDoesNotExist 예외를 사용하는 대신 Http404 를 사용하는 이유는 무엇일까요?

왜냐하면, model 계층을 view 계층에 연결하는 방법이기 때문입니다. Django 의 중요한 설계 목표는, 약결합(loose coupling)을 관리하는데에 있습니다. 일부 제어된 결합이 django.shortcuts 모듈에서 도입되었습니다.
~~~

또한, get_object_or_404() 함수처럼 동작하는 get_list_or_404() 함수가 있다. get() 대신 filter()를 쓴다는 것이 다르다. 리스트가 비어있을 경우, Http404 예외를 발생시킨다.

