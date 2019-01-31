---
layout: post
title: Django Polls App Part4/7
category: Django
permalink: /django/:year/:month/:day/:title/

tag: [Django, Python]
---
# 설문조사 앱 만들기, Part4/7
간단한 폼 처리와 소스코드를 줄여가는데 중점을 두겠다.

## 간단한 폼 만들기

```
# polls/templates/polls/detail.html

<h1>{{ question.question_text }}</h1>

{\% if error_message \%}<p><strong>{{ error_message }}</strong></p>{\% endif \%}

<form action="{% url 'polls:vote' question.id %}" method="post">
{\% csrf_token \%}
{\% for choice in question.choice_set.all \%}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
{\% endfor \%}
<input type="submit" value="Vote" />
</form>
```

