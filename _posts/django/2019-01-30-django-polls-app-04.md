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
```

