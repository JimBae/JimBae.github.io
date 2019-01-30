---
layout: post
title: Django setting
category: Django
permalink: /django/:year/:month/:day/:title/

tag: [Django, Python]
---

~~~
# 1. Install virtualenv for project
$ python3 -m venv ENV_NAME

# 2. run virtualenv
$ source ENV_NAME/bin/activate

# 3. Check installed packages
$ pip3 freeze

# 4. Install Django 
$ pip3 install django

# 5. Change directory to install project
$ cd ~/PROJECT_DIR

# 6. Create django project with project name
$ django-admin startproject PROJECT_NAME

# 7. Update setting.py
LANGUAGE_CODE = 'ko-kr'
TIME_ZONE = 'Asia/Seoul'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')

# 8. Create app with APP_NAME
$ python3 manage.py startapp APP_NAME

# 9. Register settings.py
INSTALLED_APPS = [
    'APP_NAME', #admin 위에 등록해야함
    ...
]

# 10. Run server
$ python3 manage.py runserver
Starting development server at http://127.0.0.1:8000/
~~~


[Reference0](https://wayhome25.github.io/django/2017/02/28/django-03-lotto-project-1/)

