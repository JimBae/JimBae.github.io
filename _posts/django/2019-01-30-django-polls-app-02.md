---
layout: post
title: Django Polls App Part2/7
category: Django
permalink: /django/:year/:month/:day/:title/

tag: [Django, Python]
---

## 설문조사 앱 만들기, Part2/7

데이터베이스를 설치하고 첫 모델을 생성한 후, 장고에서 자동 생성된느 관리용 사이트에 대해서 소개하겠다.

## 데이터베이스 설치
mysite/settings.py 파일을 열자. 
기본적으로 SQLite 를 사용하도록 구성되어 있다. SQLite는 Python에서 기본으로 제공되기 때문에 별로 설치가 필요없다. 그러나 실제 project를 시작할 때엔느, 나중에 데이터베이스를 교체하느라 골치아파질 일을 피하기 위해서라도 PostgreSQL 같이 더 확장성이 있는 데이터베이스를 사용하고 싶을 것이다.

다른 데이터베이스를 사용하고 싶다면, 적절한 [데이터베이스 연결](https://docs.djangoproject.com/ko/1.11/topics/install/#database-installation) 설치하고, 데이터베이스 연결 설정과 맞게끔 DATABASES 'default' 항목의 값을 다음의 키 값으로 바꿔주라.
    * ENGINE - 'django.db.backend.sqlite3',
               'django.db.backend.postgresql',
               'django.db.backend.mysql',
               'django.db.backend.oracle' 등

    * NAME - 데이터베이스의 이름. 만약 SQLite를 사용 중이라면, 데이터베이스는 당신의 컴퓨터의 파일로서 저장된다. 이 경우 NAME은 파일명을 포함한 절대 경로로서 지정되어야 한다. 기본 값은 os.path.join(BASE_DIR, 'db.dqlite3')로 정의되어 있으며, project 디렉토리 내에 db.sqlite3 파일로 저장된다. 

SQLite를 데이터베이스로 사용하지 않을 경우, USER, PASSWORD, HOST 같은 추가 설정이 반드시 필요하다. 
[DATABASE](https://docs.djangoproject.com/ko/1.11/ref/settings/#std:setting-DATABASES)

    * SQLite가 아닌 데이터베이스라면
        - 만약 SQLite 이외의 데이터베이스를 사용하는 경우, 이 시점에서 데이터베이스를 생성해야 한다. 데이터베이스의 대화형 프롬프트 내에서 "CREATE DATABASE DB_NAME;" 명령을 실행하면 된다.
        - 또한, mysite/settings.py에 설정된 데이터베이스가 사용자가 'create database' 권한이 있는지도 확인해야한다. 튜토리얼을 진행하며 필요한 경우 [테스트 데이터베이스](https://docs.djangoproject.com/ko/1.11/topics/testing/overview/#the-test-database) 를 자동으로 생성할 수있도록 해준다.
        - SQLite 사용 중이라면, 아무것도 미리 생성할 필요가 없다. 데이터베이스 파일은 필요할때마다 자동으로 생성될 것이다. 

mysite/settings.py 를 편집하는 동안, 당신의 시간대에 맞춰 set TIME_ZONE 값을 설정하자.
Ex) America/Los_Angeles

또한, 이 파일의 윗쪽에 있는 INSTALL_APPS에 대한 언급을 하자면, 이 피일은 현재 장고의 instance에서 활성화된 모든 장고 어플리케이션들의 이름이 담겨 있다. App들은 다수의 project에서 사용될 수 있고, 다른 project에서 쉽게 사용될 수 있도록 패키지하여 배포할 수 있다.

기본적으로, INSTLL_APPS는 장고와 함께 딸려오는 다음의 app들을 포함한다.
    * django.contrib.admin - 관리용 사이트
    * django.contrib.auth  - 인증 시스템
    * django.contrib.contenttypes - 컨텐츠 타입을 위한 프레임워크
    * django.contrib.sessions - 세션 프레임워크
    * django.contrib.messages - 메시징 프레임워크
    * django.contrib.staticfiles - 정적 파일을 관리하는 프레임워크

이 앱들은 일반적인 경우에 사용되기 편리하도록 기본으로 제공된다.

이러한 기본 앱들 중 몇몇은 최소한 하나 이상의 데이터베이스 테이블을 사용하는데, 그러기 위해서는 DB에서 테이블을 미리 만들 필요가 있다. 이를 위해, 다음의 명령을 실행해 보자
~~~
$ python3 manage.py migrate
~~~

migrate 명령은 INSTALLED_APPS의 설정을 탐색하여, mysite/settings.py의 DB 설정과 app과 함께 제공되는 DB migrations에 때라, 필요한 데이터 테이블을 생성한다. 이 명령을 수행하면 각 migration이 적용되는 메세지가 화면에 출력되는 것을 확인할 수 있다. 어떤 내용이 생성되었는지 궁금하다면, DB 클라이언트를 접속 후, \dt(PostgreSQL), SHOW TABLES;(MySQL), .schema(SQLite), SELECT TABLE_NAME FROM USER_TABLES;(oracle)을 통해서 장고가 생성한 테이블을 확인해 볼 수 있다.

***

## 모델 만들기
이제 모델을 정의해보자. 본질적으로, 모델이란 부가적인 메타데이터를 가진 DB의 layout을 말한다.

~~~
> 철학
장고는 DRY(Don't Repeat yourself) 원칙을 따른다. 이 원칙에 따라 데이터 모델을 한 곳에 정의하고, 이것으로부터 자동으로 뭔가를 유도하는 것이 목표이다. 이 규칙은 migration에도 해당된다. Ruby on Rails와다르게, Django의 경우 migration 들은 모두 모델 파일로 부터 유도되며, 이 migration들은 장고가 데이터 스키마의 업데이트를 통해 현재의 모델에 도달할 수 있게 해주는 일종의 history와 같다.
~~~

우리가 만드는 단순한 설문조사 앱을 위해 Question과 Choice라는 두개의 모델을 만들어보겠다. Quetion은 질문과 발행일을 위한 두개의 필드를 가진다. Choice는 선택지와 표계산을 위한 두개의 필드를 가진다. 각 Choice 모델은 Question 모델과 연관이 된다.

이런 개념을 간단히 python 클래스로 표현할 수 있다. polls/models.py 파일을 수정하자.
~~~
# polls/models.py
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_data      = models.DataTimeField('date published')

class Choice(models.Model):
    question    = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes       = models.IntegerField(default=0)
~~~

각 모델은 django.db.models.Model 이라는 클래스의 서브클래스로 표현된다.
DB의 각 필드는 Field 클래스의 instance로서 표현된다. CharField는 문자를 표현하고, DateTimeField는 날짜와 시간을 필드를 표현한다. 
Field 클래스의 생성자에 선택적인 첫번째 위치 인수를 전달하여 사람이 읽기 좋은 이름을 지정할 수도 있다. 이방법은 Django 내보를 설명하는 용도로 종종 사용되는데, 이는 마치 문서가 늘어가는 것 같은 효과를 가진다. 만약 이 선택적인 첫번째 위치 인수를 사용하지 않으면,  장고는 기계가 읽기 좋은 형식의 이름을 사용한다. 이 예제에서는, Question.pub_data 에 한해서만 인간이 읽기 좋은 형태의 이름을 정의하겠다. 그 이외의 다른 필드들은 기계가 읽기 좋은 형태의 이름이라도 사람이 읽기에는 충분하다.

몇몇 Field 클래스은 필수 인수가 필요하다. 예를들면, CharField 경우 max_lenght를 입력해 주어야한다. 
또한 Field는 다양한 선택적 인수들을 가질 수 있다. 이 예제에서는 default로 하여금 votes의 기본값을 0으로 설정하였다. 

마지막으로, ForeignKey를 사용한 관계설정에 대해 설명하겠다. 이 예제에는 각각의 Choice가 하나의 Question에 관계된다는 것을 Django에게 알려준다.  장고는 many-to-one, many-to-many, one-to-one 과 같은 모든 일반 데이터베이스의 관계들을 지원한다.

***

## 모델의 활성화

모델에 대한 이 작은 코드가 장고에게는 상당한 양의 정보를 전달한다. 이 정보로 장고는 다음을 할 수 있다.
    - 이 app에 대하여 DB 스키마 생성(CREATE TABLE statements)
    - Question과 Choice 객체에 접근하기 위한 Python DB 접근 API를 생성
그러나 가장 먼저 현재 project에게 polls app이 설치되어 있다는 것을 알려야한다. 

~~~
> 철학
장고의 app들은 착탈가능("pluggable") 합니다. app을 다수의 project에서 사용할 수 있으며, app을 배포할 수도 있다. 특정 장고 app들이 묶여있지 않아도 되기 때문이다.
~~~

app을 현재의 project에 포함시키기 위해서는, app의 구성 클래스에 대한 참조를 INSTALLED_APPS 설정에 추가시켜야한다. PollsConfig 클래스는 polls/apps.py 파일 내에 존재한다. 따라서, 점으로 구분된 경로는 'polls.apps.PollsConfig'가 된다. 이 점으로 구분된 경로를, mysite/settings.py 파일을 편집하여 INSTALLED_APPS설정에 추가하면 된다. 이는 다음과 같다.
~~~
# mysite/settings.py
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttype',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
~~~

이제 Django는 polls app이 포함된 것을 알게 되었다. 다른 명령을 내려보자.

~~~
$ python3 manage.py makemigrations polls
~~~

아래와 같은 메시지를 보게될 것이다.
~~~
Migrations for 'polls':
    polls/migrations/0001_initial.py:
        - Create model Choice
        - Create model Question
        - Add field question to choice
~~~

makemigrations을 실행시킴으로서, 당신이 모델을 변경시킨 사실과(이 경우에는 새로운 모델을 만들수 있었다.) 이변사항을 migration으로 저장시키고 싶다는 것을 Django에게 알려준다.

migration은 Django가 모델(DB 스키마를 포함한)의 변경사항을 저장하는 방법으로써, 디스크상의 파일로 존재한다. 원한다면, polls/migrations/0001_initial.py 파일로 저장된 새 모델에 대한 migration을 읽어볼 수 있다. 수동으로 Django 변경점을 조정하고 싶을때 사람이 직접 변경할 수 있도록 설계되어 있다. 

당신을 위해 migration들을 실행시켜주고, 자동으로 DB 스키마를 관리해주는 migrate라는 명령어가 존재한다. 이 명령을 알아보기 전에 migration 이 내부적으로 어떤 SQL 문장을 실행하는지 살펴보자. sqlmigrate 명령은 migration 이름을 인수로 받아, 실행하는 SQL문장을 보여준다.

~~~
$ python3 manage.py sqlmigrate polls 0001
~~~

다음과 같은 결과물을 볼 수 있다.(가독성을 위해 수정을 하였음)

~~~
BEGIN;
--
-- Create model Choice
--
CREATE TABLE "polls_choice" (
    "id" serial NOT NULL PRIMARY KEY,
    "choice_text" varchar(200) NOT NULL,
    "votes" integer NOT NULL
);
--
-- Create model Question
--
CREATE TABLE "polls_question" (
    "id" serial NOT NULL PRIMARY KEY,
    "question_text" varchar(200) NOT NULL,
    "pub_date" timestamp with time zone NOT NULL
);
--
-- Add field question to choice
--
ALTER TABLE "polls_choice" ADD COLUMN "question_id" integer NOT NULL;
ALTER TABLE "polls_choice" ALTER COLUMN "question_id" DROP DEFAULT;
CREATE INDEX "polls_choice_7aa0f6ee" ON "polls_choice" ("question_id");
ALTER TABLE "polls_choice"
  ADD CONSTRAINT "polls_choice_question_id_246c99a640fbbd72_fk_polls_question_id"
    FOREIGN KEY ("question_id")
    REFERENCES "polls_question" ("id")
    DEFERRABLE INITIALLY DEFERRED;

COMMIT;
~~~

다음을 참고하시오.
    * 사용하는 데이터베이스에 따라서 출력결과는 다를 수 있습니다. 위의 출력결과는 PostgreSQL 에서 생성되었습니다.

    * 테이블 이름은 app 의 이름과 모델의 이름(소문자)이 조합되어 자동으로 생성됩니다. 이 경우, 앱의 이름인 polls 와 소문자로 표기된 모델의 이름인 question 과 choice 가 합쳐집니다. 이 동작을 재지정(override) 하여 수정할 수 있습니다.)

    * 기본 키(Primary, ID) 는 자동으로 추가됩니다. (역시 이 동작도 재지정(override) 할 수 있습니다.)

    * 관례에 따라, Django 는 외래 키(foreign key) 필드명에 "_id" 이름을 자동으로 추가합니다. (물론 이것도 재지정 할 수 있습니다.)

    * 외래 키 관계는 FOREIGN KEY 제약이 명시적으로 생성됩니다. 제약 조건 연기(DEFERRABLE) 에 대해서는 걱정하지 않으셔도 됩니다. 이것은 단순히 PostgreSQL 에게 transaction 이 끝날때까지 외래 키를 강제하지 말라고 알려주는 것 뿐입니다.

    * 사용하는 데이터베이스에 따라, 데이터베이스 고유의 필드타입이 조정됩니다. 따라서, 자동 증가 필드를 생성할 경우 auto_increment (MySQL), serial (PostgreSQL), integer primary key autoincrement (SQLite) 와 같이 사용하는 데이터베이스에 따라 적절한 필드타입이 자동으로 선택됩니다. 필드 명에 사용되는 인용부호도 상황에 따라 겹따옴표나 홑따옴표가 적절히 선택됩니다.

    * sqlmigrate 명령은 실제로 데이터베이스의 migration 을 실행하지는 않습니다. 이 명령은 단순히 결과만 출력할 뿐이며, Django 가 필요로 하는 SQL 이 무엇인지 확인할 수 있습니다. 이 결과를 이용하여 Django 가 무엇을 할 것인지 미리 확인하거나, 데이터베이스 관리자에게 필요한 SQL 스크립트를 요청할 수도 있습니다.

관심이 있다면, "python manage.py check" 명령을 통해 migration을 생성하거나 데이터베이스를 건드리지 않고도 project에서 문제를 확인할 수 있다.

이제 migrate 를 실행시켜 데이터베이스에 모델과 관련되 테이블을 생성해보자.
~~~
$ python3 manage.py migrate
Operations to perform:
    Apply all migrations: admin, auth, contentteypes, polls, sessions
Running migrations:
    Rendering model states... DONE
    Applying polls.0001_initial... OK
~~~

migrate 명령은 아직 적용되지 않은 모든 migration들을 수집하여 이를 실행한다.
(장고는 migration의 적용 여부를 기록하는 django_migrations 테이블이 있다.) 이 과정을 통해 모델에서의 변경 사항들과 데이터베이스의 스키마의 동기화가 이루어진다.

migration은 매우 기능이 강력하여, 마치 project를 개발할 때처럼 데이터베이스나 테이블에 손대지 않고도 모델의 반복적인 변경을 가능하게 해준다. 동작중인 데이터베이스를 자료 손실 없이 업그레이드 하는데에 최적화 되어 있다. 튜토리얼의 나머지 부분에서 이 부분을 조금 더 살펴 보겠습니다만, 지금은 모델의 변경을 만드는 3단계의 지침을 기억하세요.

    1. (models.py에서) 모델을 변경합니다.
    2. python manage.py makemigrations 를 통해 이 변경사항에 대한 migration을 만드세요
    3. python manage.py migrate 명령을 통해 변경사항을 데이터베이스에 적용하세요.

migrate을 만드는 명령과, 적용하는 명령이 분리된 이유는 버전 관리 시스템에 migration을 커밋할 수 있게하여 app과 함께 제공하기 위해서이다. 이것은 단순히 당신의 개발을 쉽게 해주는 것 뿐 아니라, 다른 개발자들과 운영 환경에서도 유용하게 사용될 수 있다.

[django-admin documentation](https://docs.djangoproject.com/ko/1.11/ref/django-admin/)문서에 manage.py 유틸리티가 어떤 일을 할수 있는지 알수있다. 


***

## API 가지고 놀기

이제 대화식 Python shell에서 Django API를 자유롭게 가지고 놀아보자. Python Shell을 실행하려면 다음의 명령을 입력한다.
~~~
$ python3 manage.py shell
~~~

단순히 'python' 이라고 실해하는 대신에, 위 명령을 실행한 까닭은, manage.py에 설정된 DJANGO_SETTINGS_MODULE 환경변수 때문이다. 이 환경변수는 mysite/settings.py 파일에 대한 Python import 경로를 Django에게 제공하여, 대화식 Python 쉘에서 Django가 접근할 수 있는 Python 모듈 경로를 그대로 사용할 수 있다. 즉, Django에서 동작하는 모든 명령을 대화식 Python shell에서 그대로 시험해 볼 수 있다.

shell에 진입한 후, database API를 탐험해보자
~~~
>>> from polls.models import Question, Choice   # Import the model classes we just wrote.

# No questions are in the system yet.
>>> Question.objects.all()
<QuerySet []>

# Create a new Question.
# Support for time zones is enabled in the default settings file, so
# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
# instead of datetime.datetime.now() and it will do the right thing.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.
>>> q.save()

# Now it has an ID. Note that this might say "1L" instead of "1", depending
# on which database you're using. That's no biggie; it just means your
# database backend prefers to return integers as Python long integer
# objects.
>>> q.id
1

# Access model field values via Python attributes.
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# Change values by changing the attributes, then calling save().
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() displays all the questions in the database.
>>> Question.objects.all()
<QuerySet [<Question: Question object>]>
~~~

잠깐만요, <Question: Question object> 는 이 객체를 설명하는데에 정말 하나도 도움이 안되네요. (polls/models.py 파일의) Question 모델을 수정하여 __str__() 메소드를 Question 과 Choice 에 추가해 봅시다.

~~~
# polls/ models.py
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
~~~

당신의 모델에 __str__() 메소드를 추가하는것은 객체의 표현을 대화식 프롬프트에서 편하게 보려는 이유 말고도, Django 가 자동으로 생성하는 관리 사이트 에서도 객체의 표현이 사용되기 때문입니다.

이것들은 모두 보통의 Python 메소드입니다. 예시를 위해 수정된 메소드를 추가해 보겠습니다.

~~~
# polls/models.py
import datetime

from django.db import models
from django.utils import timezone


class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
~~~        

import datetime 은 Python 의 표준 모듈인 datetime 모듈이며, from django.utils import timezone 은 Django 의 시간대 관련 유틸리티인 django.utils.timezone 을 의미합니다. 만약 Python 에서 시간대를 조작하는 방법에 대해 익숙하지 않다면, 시간대 지원 문서 에서 더 많은것을 배울 수 있습니다.

변경된 사항을 저장하고, python manage.py shell 를 다시 실행해보세요:

~~~
>>> from polls.models import Question, Choice

# Make sure our __str__() addition worked.
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>

# Django provides a rich database lookup API that's entirely driven by
# keyword arguments.
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>

# Get the question that was published this year.
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# Request an ID that doesn't exist, this will raise an exception.
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# Lookup by a primary key is the most common case, so Django provides a
# shortcut for primary-key exact lookups.
# The following is identical to Question.objects.get(id=1).
>>> Question.objects.get(pk=1)
<Question: What's up?>

# Make sure our custom method worked.
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# Give the Question a couple of Choices. The create call constructs a new
# Choice object, does the INSERT statement, adds the choice to the set
# of available choices and returns the new Choice object. Django creates
# a set to hold the "other side" of a ForeignKey relation
# (e.g. a question's choice) which can be accessed via the API.
>>> q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.
>>> q.choice_set.all()
<QuerySet []>

# Create three choices.
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice objects have API access to their related Question objects.
>>> c.question
<Question: What's up?>

# And vice versa: Question objects get access to Choice objects.
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>> q.choice_set.count()
3

# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
# Find all Choices for any question whose pub_date is in this year
# (reusing the 'current_year' variable we created above).
>>> Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()
~~~

모델의 관계에 대한 더 많은 정보는 [Accessing related objects](https://docs.djangoproject.com/ko/1.11/ref/models/relations/) 를 참고하세요. API 에서 이중 밑줄(__) 을 이용해서 어떻게 필드를 조회할 수 있는지는 [Field lookups](https://docs.djangoproject.com/ko/1.11/topics/db/queries/#field-lookups-intro) 를 읽어보세요.데이터베이스 API 에 대한 자세한 내용을 보시려면, [Database API reference](https://docs.djangoproject.com/ko/1.11/topics/db/queries/) 를 읽어보세요.

***

## Django Admin 모듈 소개

~~~
> 철학
직원들이나 고객들이 컨텐츠의 내용을 수정하기 위한 관리 사이트를 만드는것은 딱히 창의적일 필요없는 지루한 작업입니다. 이런 이유로, Django 는 모델에 대한 관리용 인터페이스를 모두 자동으로 생성합니다.

Django 는 Lawrence Journal-World 신문사의 프로그래머가 처음 개발하였습니다. 이런 태생적인 이유 때문에, "컨텐츠 게시자" 와 "공개" 사이트의 구분이 명확합니다. 사이트 관리자는 뉴스 기사, 사건, 스포츠 경기 결과 같은 것들을 시스템에 추가합니다. 그렇게 추가된 컨텐츠는 "공개" 사이트에 노출됩니다. Django 는 사이트 관리자가 컨텐츠를 편집할 수 있는 통합적인 인터페이스를 생성하는 문제를 해결합니다.

관리 사이트는 사이트 방문자를 위한 것이 아니라, 사이트 관리자를 위한 것입니다.
~~~

### 관리자 생성하기

우선, 관리 사이트에 로그인 할 수 있는 사용자를 생성해보자. 다음 명령과 같다.
~~~
$ python3 manage.py createsuperuser
~~~

원하는 username을 입력하고 엔터를 누르세요
~~~
Username: admin
~~~

그런 다음 원하는 이메일 주소를 입력하라는 메시지가 표시된다.
~~~
Email address: admin@example.com
~~~

마지막으로 암호를 입력하세요.
~~~
Password: **********
Password (again): *********
Superuser created successfully.
~~~


***
### 개발 서버를 실행하세요.

Django의 관리 사이트는 기본으로 활성화 되어있다. 개발 서버를 켜고, 탐험해보자.
서버가 가동되지 않았다면, 다음과 같은 명령어를 시작하세요
~~~
$ python3 manage.py runserver
~~~

이제 웨 브라우져를 열고 로컬 도메인의 '/admin/'으로 이동합니다. 예를들면, http://127.0.0.1:8000/amin/ 으로 접근할 수 있다. 

***
### 관리자 사이트로 들어가세요
앞서 생성한 superuser 계정으로 로그인 해보자. 다음과 같은 django 관리 index page가 보일 것이다. 

![](/assets/2019-01-30-django-polls-app-02/admin02.png)

편집가능한 그룹과 사용자와 같은 몇 종류의 컨텐츠를 볼 수 있다. 이것들은 django.contrib.auth 모듈에서 제공되는데, Django 에서 제공되는 인증 프레임워크이다.

***
### 관리 사이트에서 poll app을 변경가능하도록 만들기
그런데, poll app이 관리 index page에서 보이지 않네요. 
여기서 하나만 더 하면 됩니다. 관리 사이트에 Quetions 객체가 관리 interface를 가지고 있다는 것을 알려주는 것이다. 이것을 하기 위해서는, polls/admin.py 파일을 열어 다음과 같이 수정하자.
~~~
# polls/admin.py
from django.contrib import admin
from .models import Question
admin.site.register(Question)
~~~

***
### 자유로운 관리 기능을 탐색하기
이제 Question을 등록시켰으니, Django는 이를 알아채고 관리 index page에 이를 표시할 것이다.
![](/assets/2019-01-30-django-polls-app-02/admin03t.png)

"Question"을 클릭해라. 그러면 질문들을 위한 "change list"로 이동합니다. 이 페이지 데이터베이스에 저장된 모든 질문들을 보여주며, 그 중 하나를 선택하여 변경할 수 있다. 이전에 등록했던 "what's up?" 질문이 있을 것이다.

![](/assets/2019-01-30-django-polls-app-02/admin04t.png)

"What's up?" 질문을 클릭하여 수정합니다.
![](/assets/2019-01-30-django-polls-app-02/admin05t.png)

여기서 알아둘 것!
    * 이 서식은 Question 모델에서 자동으로 생성되었습니다

    * 모델의 각 필드 유형들은 (DateTimeField, CharField) 적절한 HTML 입력 위젯으로 표현됩니다. 필드의 각 유형들은 Django 관리 사이트에서 어떻게 표현해되어야 할지 알고 있습니다.

    * 각각의 DateTimeField 는 JavaScript 로 작성된 단축 기능과 연결됩니다. 날짜는 "오늘"("Today") 버튼과 달력 팝업에서 입력할 수 있으며, 시간은 "지금"("Now") 버튼과 일반적으로 입력하는 시간들을 제공하는 편리한 팝업을 통해서도 입력할 수 있습니다.

페이지의 아래 부분에서 다음과 같은 몇가지 옵션을 제공합니다.

    * 저장(Save) -- 이 유형의 객체에 대한 변경사항을 저장하고, 변경된 목록 페이지를 보여줍니다

    * 저장 및 편집 계속(Save and continue editing) -- 이 객체에 대한 변경사항을 저장하고, 현재 편집창을 갱신합니다
    * 저장 및 다른 이름으로 추가(Save and add another) -- 변경사항을 저장하고, 이 유형의 객체에 대한 비어있는 새로운 입력창을 불러옵니다

    * 삭제(Delete) -- 삭제를 확인하는 페이지를 띄웁니다.

만약 "Date published" 의 값이 Tutorial 1 에서 질문을 생성했을때의 시간과 일치하지 않는다면, TIME_ZONE (시간대) 설정을 깜빡 하신것일지도 모릅니다. 이 설정을 바꾸시고 다시 페이지를 불러오시면 올바른 값이 표현됩니다.

"Date published" 의 값을 "오늘"("Today") 과 "지금"("Now") 단축버튼을 눌러 바꾸십시요. 그런 후, "저장 및 편집 계속"("Save and continue editing") 을 누르십시요. 그런 후, 우측 상단의 "히스토리"("History") 버튼을 누르십시요. Django 관리사이트를 통해 누가(username) 언제(timestamp) 무엇을 바꾸었는지 목록을 확인할 수 있습니다.

![](/assets/2019-01-30-django-polls-app-02/admin06t.png)


***
[Reference 한글](https://docs.djangoproject.com/ko/1.11/intro/tutorial02/)
<br>
[Reference 영문](https://docs.djangoproject.com/en/2.1/intro/tutorial02/)

