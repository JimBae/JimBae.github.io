---
layout: post
title: Django Polls App Part5/7
category: Django
permalink: /django/:year/:month/:day/:title/

tag: [Django, Python]
---
# 설문조사 앱 만들기, Part5/7
자동화 테스트


## 자동화된 테스트 소개

### 자동화된 테스트란 무엇인가?

테스트는 코드의 동작을 체크하는 간단한 일련의 작업들이다.

테스트는 다양한 수준에서 작동한다. 일부 테스트는 작은 세부 사항에 적용될 수 있다.(특정 모델 메소드는 예상대로 값을 반환합니까?) 또 다른 테스트는 소프트웨어의 전반적인 작동을 검사한다.(사이트에서 사용자 입력 시퀀스가 원하는 결과를 생성합니까?) 이것은 이전 튜토2에서 shell을 사용하여 메소드의 동작을 검사한거나 app을 실행하고 어떻게 작동하는지 확인하기위해 데이터를 입력해서 테스트했던 것과 다르지 않다.

자동화된 테스트에서 다른 점은 테스트 작업이 시스템에서 수행된다는 것이다. 한 번 테스트 세트를 작성한 이후에는 앱을 변경할 때 수동 테스트를 수행하지 않아도 원래 의도대로 코드가 작동하는지 확인할 수 있다.

***
## 테스트를 만들어야하는 이유
그래서 왜 테스트를 만들어야하지?

아마 당신은 Python/Django 를 배우는 것만으로도 충분하다고 느낄수 있고 또 다른 것을 배우고 써보는 것은 지나치거나 불필요해 보일 수 있다. 어쨋든 우리의 설문조사 어플리케이션은 지금 꽤 잘돌아가고 있다. 문제들을 해쳐 나가며 자동화된 테스트를 만드는게 이 어플리케이션을 작동하게 하거나 더 좋게 하지는 않습니다. 만약 투표 어플리케이션을 만드는게 Django 프로그래밍의 최종 단계라면, 자동화된 테스트를 어떻게 만드는지 알 필요는 없습니다. 하지만 아직 더 많은것을 하려 한다면 지금이 자동화된 테스트 작성을 배우기 딱 좋은 시간입니다.

### 테스트를 통해 시간을 절약할 수 있다.
특정 시점까지는 '제대로 작동하는지 확인' 하는것이 테스트로서 충분할 것 입니다. 더 정교한 어플리케이션에서는 구성 요소간에 수십 개의 복잡한 상호 작용이있을 수 있습니다.

이들 컴포넌트들에 어떠한 변경이라도 생기게 되면 어플리케이션의 동작에 예상치 못했던 결과가 발생할수 있습니다. 이 상황에서 '제대로 작동하는지 확인'한다는 것은 20개의 서로 다른 테스트 데이터를 가지고 코드의 기능들을 돌려보는것입니다. 단지 당신이 뭔가를 망가뜨리지 않았다는것을 확인하기 위해서 말이죠 - 시간은 귀한데 말입니다.

이 수동 테스트 작업을 자동화된 테스트가 몇초만에 해낼수 있다면 귀한시간을 많이 아낄수 있겠죠?. 무언가가 잘못되어도 테스트를 통해 예기치 않은 동작을 일으키는 코드를 식별하는 데 도움이됩니다.

때로는 코드가 제대로 작동하고 있음을 알 때 테스트를 작성하는 것은 허드렛일로 보여서 당신의 생산적이고 창의적인 프로그래밍 작업에서 떠나 매력적이지도 흥분되지도 않는 테스트 작성이라는 일을 하는게 어려울수도 있습니다.

그러나 테스트를 작성하는 작업은 어플리케이션을 수동으로 테스트하거나 새로 발견된 문제의 원인을 확인하는 데 많은 시간을 투자하는 것보다 훨씬 더 효과적입니다.

***
### 테스트는 문제를 그저 식별하는 것이 아니라 예방합니다.
테스트를 그저 개발의 부정적 측면으로 생각하는 것은 실수입니다.
테스트가 없으면 어플리케이션의 목적 또는 의도 된 동작이 다소 불투명 할 수 있습니다. 심지어 자신의 코드 일 때도, 정확히 무엇을하고 있는지 알아 내려고 노력하게 됩니다.
테스트는 이 불투명함을 바꿉니다. 그들은 내부에서 코드를 밝혀 내고, 어떤 것이 잘못 될 때, 그것이 잘못되었다는 것을 깨닫지 못했다고 할지라도, 잘못된 부분에 빛을 집중시킵니다.

***
### 테스트가 코드를 더 매력적으로 만듭니다.
당신이 화려한 소프트웨어를 만들더라도, 다른 많은 개발자들은 그저 테스트가 부족하기 때문에 그것을 사용하려 하지 않을 것입니다. 테스트 없이 그들은 그것을 신뢰하지 않을 것입니다. Django의 원 개발자 중 한 명인 Jacob Kaplan-Moss는 "테스트없는 코드는 설계상 망가져 있는것" 이라고 말합니다.
테스트 작성을 시작해야하는 또다른 이유는 다른 개발자들이 당신의 소프트웨어를 사용하는것을 진지하게 고려하기 전에 테스트 코드를 보기를 원하기 때문입니다.

***
### 테스트는 팀이 함께 일하는것을 돕습니다
이전의 내용은 어플리케이션을 유지 관리하는 단일 개발자의 관점에서 작성되었습니다. 복잡한 애플리케이션은 팀별로 유지 관리됩니다. 테스트는 동료가 실수로 코드를 손상시키지 않는다는 것을 보증합니다 (그리고 당신이 동료의 코드를 모르는새에 망가트리는것도). 장고 프로그래머로서 생계를 꾸려 나가려면 테스트를 잘해야합니다.

***
## 기초 테스팅 전략
테스트 작성에 대한 많은 접근법이 있다.
어떤 프로그래머들은 'TDD'라는 원칙을 따른다. 그들은 정말로 코드를 작성하기도 전에 테스트를 먼저 작성한다. 이는 직관에 위배된느 것처럼 보일 수있지만, 사실은 대부분의 사람들이 자주하는 방식과 비슷하다. 문제를 서술한 뒤, 코드를 만들어서 그것을 풀어내는 것이죠. 테스트 주도 개발은 파이썬 테스트 케이스로 문제를 간추려 공식화합니다.

더 흔하게는, 테스팅 입문자들은 코드를 작성하고 시간이 흐른 뒤에 테스트들이 필요하다고 판단할 것이다. 아마도 몇몇 테스트는 더 빨리 작성하는 것이 나을지도 모른다. 하지만 시작하기에 너무 늦어서는 안된다.

어디서부터 테스트를 작성해야할지 종잡을 수 없을 때가 있다. 당신이 수천 줄의 파이썬 코드를 작성해 놓았다면, 테스트할 것을 고르는 것이 쉽지 않을지도 모른다. 그럴때는 다음 새로운 기능을 넣거나 버그를 수정하는 등 코드를 변경할 일이 있을때, 당신의 첫 테스트를 작성하는 것이 유익할 것이다.

***
## 우리의 첫 테스트 작성하기

### 우리는 버그를 확인한다.
다행히 polls application에는 우리가 즉시 해결할 수 있는 약간의 버그가 있다.
Question.was_published_recently() 메소드는 Question이 어제 안에 게시된 경우 True를 반환할 수도 있지만,
Question의 pub_data 필드가 미래로 설정되어 있을때도 있다.(잘못된 동작)

버그가 실제로 존재하는지 확인하기 위해 Admin을 사용하여 미래의 날짜로 질문을 만들고 shell을 사용해서 확인다.

~~~
>>> import datetime
>>> from django.utils import timezone
>>> from polls.models import Question
# create a Question instance with pub_date 30 days in the future
>>> future_question = Question(pub_date=timezone.now() + datetime.timedelta(days=30))
>>> # was it published recently?
>>> future_question.was_published_recently()
True
~~~

미래는 최근이 아니기 때문에 이는 분명히 틀린말이다. 

***

## 버그를 노출하는 테스트 만들기
문제를 테스트하기 위해 shell 에서 방금 수행 한 작업은 자동화 된 테스트에서 수행 할 수있는 작업이므로 자동화 된 테스트로 바꾸도록 합시다.

애플리케이션 테스트는 일반적으로 애플리케이션의 tests.py 파일에 있습니다. 테스트 시스템은 test 로 시작하는 파일에서 테스트를 자동으로 찾습니다.

polls 어플리케이션의 tests.py 파일에 다음을 입력하십시오:

~~~
# polls/tests.py
import datetime

from django.utils import timezone
from django.test import TestCase

from .models import Question


class QuestionModelTests(TestCase):

    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() returns False for questions whose pub_date
        is in the future.
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)
~~~

우리가 여기서 한 것은 설정된 pub_data를 가진 Question instance를 생성하는 메소드를 가진 django.test.TestCase 서브 클래스를 생성한 것이다. 그런 다음 was_published_recently()의 결과를 확인한다. 이는 반드시 False가 되어야 한다. 

***
## 테스트 실행

터미널에서 테스트를 실행할 수 있다.

~~~
$ python3 manage.py test polls
~~~

그러 다음과 같은 결괄르 볼 수 있다.
~~~
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionModelTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/path/to/mysite/polls/tests.py", line 16, in test_was_published_recently_with_future_question
    self.assertIs(future_question.was_published_recently(), False)
AssertionError: True is not False

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
~~~

무슨 일이 일어난건가?
    * python manage.py test polls 는 polls 어플리케이션에서 테스트를 찾았습니다.
    * django.test.TestCase 클래스의 서브 클래스를 찾았습니다.
    * 테스트 목적으로 특별한 데이터베이스를 만들었습니다.
    * 테스트 메소드-이름이 test로 시작한느 것들을 찾습니다.
    * test_was_published_recently_with_future_question에서 pub_data필드가 30일 미래인 Question instance를 생성했습니다.
    * ...assertIs() 메소드를 사용하여, 우리가 False가 반환되기를 원함에도 불구하고 was_published_recently() 가 True를 반환한다는 것을 발견했습니다.

테스트는 어떤 테스트가 실패했는지와 실패가 발생한 행까지 알려줍니다.

***
## 버그 수정
우리는 이미 문제가 무엇인지 알고 있다. Question.as_published_recently()는 pub_date가 미래에 있다면 False를 반환해야한다. models.py에서 메소드를 수정하십시오. 그래서 날짜가 과거에 있을 때만 True를 반활 할 것이다. 

~~~
# polls/models.py
def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
~~~
이제 테스트를 다시 실행하십시오.

~~~
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
Destroying test database for alias 'default'...
~~~

버그를 확인한 후에 우리는 이를 드러내는 테스트를 작성하였으며 코드에서 버그를 수정하고 테스트를 통과했다.
앞으로는 다른 많은 것들이 우리 어플리케이션에 잘못 될수도 있지만 단순히 테스트를 실행하면 우리에게 즉시 경고하므로 실수로 이 버그가 재발하지는 않을 것이다. 우리는 애플리케이션이 작은 부분을 영원히 안전한 상태로 고정시켰다고 생각할 수 있다.

***
## 보다 포괄적인 테스트
우리가 여기있는 동안, 우리는 was_published_recently()메소드를 고정하는것 이상을 할수 있습니다; 사실 하나의 버그를 고치면서 다른 새로운 버그를 만들어 낸다면 분명 곤란할것입니다.

메소드의 동작을보다 포괄적으로 테스트하기 위해 동일한 클래스에 두 가지 테스트 메소드를 추가하십시오.

~~~
# polls/tests.py

def test_was_published_recently_with_old_question(self):
    """
    was_published_recently() returns False for questions whose pub_date
    is older than 1 day.
    """
    time = timezone.now() - datetime.timedelta(days=1, seconds=1)
    old_question = Question(pub_date=time)
    self.assertIs(old_question.was_published_recently(), False)

def test_was_published_recently_with_recent_question(self):
    """
    was_published_recently() returns True for questions whose pub_date
    is within the last day.
    """
    time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
    recent_question = Question(pub_date=time)
    self.assertIs(recent_question.was_published_recently(), True)
~~~
이제 우리는 Question.was_published_recently()가 과거, 최근, 미래의 질문에 대해 올바른 값을 반환한다는걸 확인시켜주는 세가지 테스트를 가졌습니다.

다시 말하지만 설문조사 (polls) 는 단순한 프로그램이지만 추후에 복잡도가 증가되고 다른 어떤 코드들과 상호작용을 하더라도 테스트를 작성한 메소드가 예상대로 동작 할 것이라는 보장을 할수있습니다.

***
## 뷰 테스트
설문조사 어플리케이션은 상당히 대충대충 만들어져 있습니다. 이 어플리케이션은 pub_date필드가 미래에있는 질문 까지도 포함하여 게시합니다. 이것을 개선 해야합니다. 미래로 pub_date를 설정하는 것은 그 시기가 되면 질문이 게시되지만 그때까지는 보이지 않는 것을 의미 해야 합니다.

## 뷰에 대한 테스트
위의 버그를 수정했을 때 먼저 테스트를 작성한 다음 코드 수정을 했습니다. 사실 그것은 테스트 주도 개발의 간단한 예제 였지만 어떤 순서로 작업하는지는 중요하지 않습니다.

첫 번째 테스트에서 코드의 내부 동작에 대해 자세히 설명했습니다. 이 테스트에서는 웹 브라우저를 통해 사용자가 경험하는대로 동작을 확인하려고합니다.

버그를 수정하기 전에 우리가 사용 할 수있는 도구를 살펴 보겠습니다.

***
## 장고 테스트 클라이언트
Django는 뷰 레벨에서 코드와 상호 작용하는 사용자를 시뮬레이트하기위해 테스트 클라이언트 클래스 Client를 제공합니다. 이 테스트 클라이언트를 tests.py또는 shell에서 사용할 수 있습니다.

우리는 shell로 다시 시작하면서 tests.py 에서 필요하지 않았던 것들을 몇 가지 해야합니다. 첫 번째는 shell에서 테스트 환경을 설정하는 것입니다.

~~~
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
~~~


response.context와 같은 response의 추가적인 속성을 사용할수 있게 하기위해서 setup_test_environment()를 사용하여 템플릿 렌더러를 설치합니다. 이 메소드는 테스트 데이터베이스를 셋업하지 않습니다. 그렇기때문에 테스트는 현재 사용중인 데이터베이스위에서 돌게되며 결과는 데이터베이스에 이미 만들어져있는 질문들에 따라서 조금씩 달라질수 있습니다. 또한 settings.py의 TIME_ZONE이 올바르지 않으면 예기치 않은 결과가 발생할 수 있습니다. 초기에 어떻게 설정해놨는지 기억나지 않는다면 진행하기전에 먼저 확인하십시오.

다음으로 우리는 테스트 클라이언트 클래스를 import 해야합니다. (나중에 tests.py에서는 django.test.TestCase 클래스에 같이 딸려오는 클라이언트를 사용할 것이므로 이것은 필요하지 않을 것입니다)

~~~
>>> from django.test import Client
>>> # create an instance of the client for our use
>>> client = Client()
~~~

이런것들이 준비가 되었다면 이제 우리는 클라이언트에세 우리를 위해 일을 하라고 시킬수 있습니다.

~~~
>>> # get a response from '/'
>>> response = client.get('/')
Not Found: /
>>> # we should expect a 404 from that address; if you instead see an
>>> # "Invalid HTTP_HOST header" error and a 400 response, you probably
>>> # omitted the setup_test_environment() call described earlier.
>>> response.status_code
404
>>> # on the other hand we should expect to find something at '/polls/'
>>> # we'll use 'reverse()' rather than a hardcoded URL
>>> from django.urls import reverse
>>> response = client.get(reverse('polls:index'))
>>> response.status_code
200
>>> response.content
b'\n    <ul>\n    \n        <li><a href="/polls/1/">What&#39;s up?</a></li>\n    \n    </ul>\n\n'
>>> response.context['latest_question_list']
<QuerySet [<Question: What's up?>]>
~~~

***
## 뷰를 개선시키기
설문 조사 목록에는 아직 게시되지 않은 설문 조사 (즉, 장래에 pub_date가 있는 설문 조사)가 표시됩니다. 그것을 수정합시다.
Tutorial 4에서 ListView 클래스 기반의 뷰를 소개했습니다.

~~~
# polls/views.py

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'lastest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]
~~~

우리는 get_queryset()메소드를 수정하여 timezone.now()와 비교하여 날짜를 검사하도록 변경해야합니다. 먼저 가져 오기를 추가해야한다.

~~~
# polls/views.py
from django.utils import timezone
~~~

그리고 다음과 같이 get_queryset 메소드를 수정해야한다.

~~~
# polls/views.py

def get_queryset(self):
    """
    Return the last five published questions (not including those set to be
    published in the future).
    """
    return Question.objects.filter(
        pub_date__lte=timezone.now()
    ).order_by('-pub_date')[:5]
~~~
Question.objects.filter (pub_date__lte = timezone.now ()) 는 timezone.now 보다 pub_date 가 작거나 같은 Questions 를 포함하는 queryset을 리턴합니다.

***

## 새로운 뷰 테스트
이제는 runserver를 실행하고 브라우저에서 사이트를 로드하고 과거와 미래의 날짜가 포함된 질문을 만들고 게시된 것만 표시되는 확인하여 예상대로 작동합니다. 이 작업에 영향을 줄 수 있는 변경을 할때마다 매번 그렇게하지 않으려고 합니다. 위의 셀 세션을 기반으로 테스트를 만들어 봅시다.

polls/tests.py
~~~
from django.urls import reverse
~~~

새로운 테스트 클래스와 함께 질문들을 생성하는 함수를 만들것이다.

~~~
#polls/test.py
def create_question(question_text, days):
    """
    Create a question with the given `question_text` and published the
    given number of `days` offset to now (negative for questions published
    in the past, positive for questions that have yet to be published).
    """
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)


class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        """
        If no questions exist, an appropriate message is displayed.
        """
        response = self.client.get(reverse('polls:index'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_past_question(self):
        """
        Questions with a pub_date in the past are displayed on the
        index page.
        """
        create_question(question_text="Past question.", days=-30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )
    def test_future_question(self):
        """
        Questions with a pub_date in the future aren't displayed on
        the index page.
        """
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_future_question_and_past_question(self):
        """
        Even if both past and future questions exist, only past questions
        are displayed.
        """
        create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_two_past_questions(self):
        """
        The questions index page may display multiple questions.
        """
        create_question(question_text="Past question 1.", days=-30)
        create_question(question_text="Past question 2.", days=-5)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question 2.>', '<Question: Past question 1.>']
        )
~~~

이 중 일부를 더 자세히 살펴 보겠습니다.

먼저, 질문 생성 함수인 create_question 은 테스트 과정 중 설문을 생성하는 부분에서 반복 사용합니다.

'test_no_questions'는 질문을 생성하지는 않지만 "폴이 없습니다."라는 메시지를 확인합니다. 
'latest_question_list'가 비어 있음을 확인합니다. : class :django.test.TestCase 클래스는 몇 가지 추가적인 선언 메소드를 제공합니다. 이 예제에서 우리는 : meth :`~ django.test.SimpleTestCase.assertContains ()`와 
                   :meth :`~ django.test.TransactionTestCase.assertQuerysetEqual()`을 사용합니다.

'test_past_question'에서 우리는 질문을 생성하고 그 질문이 리스트에 나타나는지 확인합니다.
'test_future_question'에서 우리는 장래에 'pub_date'로 질문을 만듭니다. 
데이터베이스는 각 테스트 메소드마다 재설정되므로 첫 번째 질문은 더 이상 존재하지 않으므로 다시 인덱스에 질문이 없어야합니다.
요컨데, 사이트에서 관리자 입력 및 사용자 경험에 대한 이야기를 하는 테스트를 만들었고, 모든 상태와 시스템 상태의 모든 새로운 변경 사항에 대해 예상하는 결과가 출력되는지 확인합니다.

***
## DetailView 테스트하기

우리가 만든 것이 잘 작동합니다; 그러나 미래의 설문들은 목록에 나타나지는 않지만, 사용자가 URL을 알고 있거나, 추측하면 접근할 수 있습니다. 그래서 우리는 DetailView에 비슷한 제약 조건을 추가 할 필요가 있습니다.

~~~
# polls/views.py
class DetailView(generic.DetailView):
    ...
    def get_queryset(self):
        """
        Excludes any questions that aren't published yet.
        """
        return Question.objects.filter(pub_date__lte=timezone.now())
~~~

물론, 우리는 시간이 지난 pub_date 값을 가지고 있는 설문은 표시 되고, 미래의 pub_date는 표시되지 않게 몇 가지 검사를 추가 할 것입니다.

~~~
# polls/tests.py

class QuestionDetailViewTests(TestCase):
    def test_future_question(self):
        """
        The detail view of a question with a pub_date in the future
        returns a 404 not found.
        """
        future_question = create_question(question_text='Future question.', days=5)
        url = reverse('polls:detail', args=(future_question.id,))
        response = self.client.get(url)
        self.assertEqual(response.status_code, 404)

    def test_past_question(self):
        """
        The detail view of a question with a pub_date in the past
        displays the question's text.
        """
        past_question = create_question(question_text='Past Question.', days=-5)
        url = reverse('polls:detail', args=(past_question.id,))
        response = self.client.get(url)
        self.assertContains(response, past_question.question_text)
~~~

*** 
## 더 많은 테스트를 위한 아이디어
우리는 비슷한 get_queryset 메소드를 ResultsView에 추가하고 그 뷰에 대한 새로운 테스트 클래스를 생성해야합니다. 그것은 우리가 방금 만든 것과 매우 유사합니다. 사실 계속 반복 작업을 할 것입니다.

테스트를 추가하면서 다른 방법으로 애플리케이션을 개선 할 수도 있습니다. 예를 들어, 선택 사항이 없는 사이트에 설문을 게시 할 수 있다는 것은 바보같은 일입니다. 그래서 우리의 뷰는 이를 확인하고 그러한 질문을 배제 할 것입니다. 우리의 테스트는 선택사항이 없는 설문을 생성 한 다음, 실제로 게시되지 않는지 테스트하고, 선택사항이 있는 설문을 작성하고 게시 여부를 테스트합니다.

아마도 일반 사용자가 아닌 로그인 한 관리자는 게시되지 않은 설문을 볼 수 있어야합니다. 다시 말하면: 소프트웨어를 추가하기 위해 필요한 것은 무엇이든 테스트를 수반해야합니다, 먼저 테스트를 작성한 다음 코드가 테스트를 통과하게 만들 것인지, 아니면 먼저 코드에서 로직을 처리 한 다음 이를 증명할 테스트를 작성하십시오.

어느 순간엔가 너무 많은 테스트 코드들을 보고 관리하기 힘들도록 너무 비대해 지는것은 아닌가 생각할수도 있습니다.

***
## 테스트 할 때는 많이 할 수록 좋습니다.
우리의 테스트가 통제 불능으로 성장하고있는 것처럼 보일 수 있습니다. 이 속도라면 곧 우리의 어플리케이션에서 보다 우리의 테스트의 코드가 더 많아질 것이고, 나머지 코드의 우아한 간결함과 비교했을 때, 반복하는 것은 미학적입니다.

사실 비대해지는것은 중요하지 않습니다. 테스트 코드들이 늘어나게 하십시오. 대부분의 경우 테스트를 한 번 작성한 다음 신경을 끄게 됩니다. 그래도 이 테스트 코드의 유용한 기능들은 프로그램을 개발하는 동안 계속 해서 작동할것입니다.

때로는 테스트를 업데이트해야합니다. 우리가 선택지를 가진 설문들만 출력되도록 뷰를 수정한다고 가정 해 보겠습니다. 이 경우 기존 테스트 중 상당수가 실패 할 것입니다. 테스트 결과를 최신으로 유지하기 위해 어떤 테스트를 수정해야하는지 정확하게 알려주므로 테스트가 스스로를 돌보는 데 도움이됩니다.

최악의 경우 개발을 계속할 때 중복되는 테스트가 있을 수 있습니다. 그것은 문제가 아닙니다. 테스팅에서 반복하는 것은 좋은 일입니다.

테스트들이 현명하게 배열되어있는 한 관리가 어려워지지 않을 것입니다. 경험에 근거한 좋은 방법 중에는 다음과 같은 내용이 있습니다.

    * 각 모델이나 뷰에 대한 별도의 TestClass
    * 테스트하려는 각 조건 집합에 대해 분리된 테스트 방법
    * 기능를 설명하는 테스트 메소드 이름

***
## 추가 테스팅
이 튜토리얼에서는 테스트의 기본 사항에 대해서만 소개합니다. 여러분은 더 많은 것을 할 수도 있고, 또 사용할 수 있는 똑똑한 도구들이 많이 있습니다.

예를 들어, 이 전에 수행 한 테스트에서는 모델의 내부 로직과 뷰에서 정보를 게시하는 방법을 다루었지만 Selenium_과 같은 "브라우저 내" 프레임 워크를 사용하여 HTML이 브라우저에서 실제로 렌더링되는 방식을 테스트 할 수 있습니다. 이러한 도구를 사용하면 장고 코드의 동작뿐만 아니라 JavaScript도 확인할 수 있습니다. 테스트가 브라우저를 시작하고 인간이 그것을 다루는 것처럼 사이트와 상호 작용 것은 매우 중요합니다! Django에는 LiveServerTestCase가 포함되어있어 Selenium과 같은 도구와 쉽게 통합할 수 있게 해줍니다.

복잡한 어플리케이션을 사용하는 경우 연속적으로 통합 하기 위해 모든 커밋마다 자동으로 테스트를 실행하여 품질 제어가 적어도 부분적으로 자동화되도록 할 수 있습니다.

어플리케이션에서 테스트되지 않은 부분을 탐지하는 좋은 방법은 코드 커버리지를 확인하는 것입니다. 이것은 또한 깨지기 쉬운 코드나 심지어는 죽은 코드를 식별하는 데 도움이됩니다. 코드를 테스트 할 수 없다는 것은 대개 코드가 리팩터링해야하거나 제거해야 함을 의미합니다. 커버리지는 죽은 코드를 확인하는 데 도움이됩니다. 자세한 내용은 Integration with coverage.py 를 참조하십시오.

장고 테스트는 테스트에 대한 포괄적인 정보를 제공합니다.

***
[Reference 한글](https://docs.djangoproject.com/ko/1.11/intro/tutorial05/)
<br>
[Reference 영문](https://docs.djangoproject.com/en/2.1/intro/tutorial05/)

