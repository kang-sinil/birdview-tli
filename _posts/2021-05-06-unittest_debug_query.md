---
layout: post
title:  "Unittest debug query"
author: "강신일"
comments: False
tags: python test
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>

### SHOW ME THE QUERY

일반적으로 로컬환경에서 서버를 띄우고 서버에서 동작하는 Query 를 확인할때는 다음과 같이 설정합니다.
```
#settings/local.py
...
DEBUG = True
...

LOGGING = {
  ...
  'loggers': {
    ...
    'django.db.backends': {
            'handlers': ['console',],
            'level': 'DEBUG',
            'propagate': False,
        }
  }
```
이렇게 할경우 `django.db.backends` 의 로그를 `console` 로 바로 확인 할 수 있게됩니다.<br>
그렇다면 unittest 를 이용해서 테스트케이스를 작성하다 테스트케이스에서 동작하는 로직의 Query 를 보고 싶을때는 어떻게 해야할까요?
테스트환경 파일(`local_test.py`)에서 `local.py` 와 동일하게 설정했더라도 테스트케이스 수행시 실행되는 Query 가 보이지 않습니다.<br>
그 이유는 아래의 과정에서 확인할 수 있습니다.
```
# django/test/utils.py

def setup_test_environment(debug=None):
  ...
  settings.DEBUG = debug
  ...

```
테스트 케이스를 실행하기전에 `setup_test_environment()` 를 설정하는데 이때 전달받은 `debug` 를 `settings.DEBUG` 값에 덮어씁니다.
그리고 전달받은 `debug` 는 `DiscoverRunner` 생성시 `instance_attribute` 인 `debug_mode` 를 참조하는데 기본값은 `False` 입니다.<br>
때문에 `DiscoverRunner` 생성시 `debug_mode` 를 별도로 처리해주지 않는다면 아무리 `settings/local_test.py` 를 변경해도 DEBUG 모드로 동작하지 않습니다.


#### override_settings
이때 간단하게 debug 모드로 설정하는 방법이 있습니다. <br>
아래와 같이 테스트케이스에 `override_settings` 으로 debug 를 다시 True 로 바꾸면 해당 테스트케이스 동작시 debug 모드로 동작하게 되며 기대한대로 수행 Query 를 `console` 에서 확인 할 수 있게 됩니다.
```
from django.test import override_settings

@override_settings(DEBUG=True)
def test_XXXX(self):
  ...
end
```

[참고링크](https://docs.djangoproject.com/en/3.2/topics/testing/tools/#django.test.override_settings)

오늘은 여기까지~!
