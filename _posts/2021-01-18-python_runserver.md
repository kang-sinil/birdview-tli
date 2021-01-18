---
layout: post
title:  "python_runserver"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.

## Django 서버 실행하기
이번에는 생성된 Django 프로젝트를 로컬환경에서 실행시켜보자.
장고 프로젝트 생성 및 설정은 [여기](https://wikidocs.net/72377)를 참고하면 된다.

```
$ python manage.py runserver
Performing system checks...

System check identified no issues (0 silenced).
January 18, 2021 - 13:31:26
Django version 2.0.5, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

만약 다음과 같은 오류가 발생한다면 가상환경에 Django 가 설치되어 있지 않아서 발생하는 이슈이다.
```
Traceback (most recent call last):
 File "manage.py", line 8, in <module>
   from django.core.management import execute_from_command_line
ImportError: No module named 'django'

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
 File "manage.py", line 14, in <module>
   ) from exc
ImportError: Couldn't import Django. Are you sure it's installed and available on your PYTHONPATH environment variable? Did you forget to activate a virtual environment?
```

이럴땐 가상환경에서 다음과 같은 명령어를 통해 Django 를 설치하고 재시도하자.
```
pip install django==<django_version>
```
<br>

## PyCharm 을 이용해서 서버 실행하기
터미널을 통해서는 위와 같은 방식으로 서버를 구동할 수 있었다.<br>
하지만 로컬개발은 PyCharm 을 이용해서 진행하기때문에 PyCharm 을 이용해서 서버를 실행시키고 싶다.<br>
그런데 우리는 pyenv, pyenv-virtualenv 를 이용해서 가상환경에 python 을 설치하고, django 를 설치했기때문에 PyCharm 도 우리가만든 virtualenv 를 이용해서 구동하도록 설정해야한다.

1. PyCharm 의 Preferences > Project > Python Interpreter 에 접근한다.
2. Python Interpreter 목록에서 설정버튼 > add > Existing environment > 이전에 만든 가상환경을 선택한다.
3. Run > Edit Configurations > Django Server 선택 > Python interpreter 를 2)에서 생성한 값으로 설정

위의 과정을 거치면 이제 PyCharm 에서 virtualenv를 이용해서 Django 서버를 실행시킬수 있다.

#### 참고
* [PyCharm 으로 파이썬 프로젝트에 virtualenv 적용하기](https://psychoria.tistory.com/447)
* [장고 개발환경 구축하기](https://www.askcompany.kr/r/articles/8b6e34a/)

오늘은 여기까지~!
