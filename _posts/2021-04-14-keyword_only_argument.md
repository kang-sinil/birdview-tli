---
layout: post
title:  "Keyword-Only Argument"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>

### Keyword-Only Argument
`Python3` 이상에서 사용가능한 `keyword-only arguments` 는 함수호출시 `Argument` 에 `Keyword` 를 명시하도록 강제하는 방법입니다.
오늘은 이에 대해서 알아보겠습니다.

```
def update(major_version, minor_version):
    print('major:{}, minor:{}'.format(major_version, minor_version))
```

다음과 같이 `positional arguments` 로 `major_version` 과 `minor_version` 을 입력받는 `update()` 라는 함수가 있다고 가정해보겠습니다.
만약 다른개발자가 이 `update()` 함수를 사용한다면 다음과 같이 사용할 것입니다.
```
update(3, 5)  # ok
update(3, minor_version=5)  # ok
update(major_version=3, minor_version=5)  # ok
update(minor_version=5, major_version=3)  # ok
```

위와 같이 4가지 방식이 모두 가능하지만 위의 2가지 방법은 호출하는 로직으로만 봤을때 `arguments` 가 어떤 용도로 사용되는지 그 의미를 알아내기 어렵습니다.
이와 같은 상황에서 함수호출시에 `keyword` 를 명시하도록 강제하는 방법이 `keyword-only arguments` 입니다.

```
def update(*, major_version, minor_version):
    print('major:{}, minor:{}'.format(major_version, minor_version))

update(3,5)
# Output: TypeError: update() takes 0 positional arguments but 2 were given
update(3, minor_version=5)
# Output: update() takes 0 positional arguments but 1 positional argument (and 1 keyword-only argument) were given
update(1, major_version=3, minor_version=5)
# Output: update() takes 0 positional arguments but 1 positional argument (and 2 keyword-only arguments) were given

update(minor_version=5, major_version=3)
# Output: major:3, minor:5
```
위와 같이 `keyword-only arguments` 로 지정할 `arguments` 앞에 `*` 를 붙여서 하위 `arguments` 들을 `keyword-only arguments` 로 지정했습니다.

### Positional, Keyword Arguments and Keyword-Only Arguments

그렇다면 이번에는 이전에 배운 `*args(positional arguments)` 와 `**kwargs(keyword arguments)` 들을 응용해서 확인해봅시다.
만약 함수에 약속된 `positional arguments` 를 받지만 `keyword arguments` 는 `keyword-only arguments` 로 강제하고 싶을경우 다음과 같이 사용할 수 있습니다.
```
def update(pos_arg1, pos_arg2, *, major_version, minor_version):
    print('pos_arg1: {}, pos_arg2: {}'.format(pos_arg1, pos_arg2))
    print('major:{}, minor:{}'.format(major_version, minor_version))

update(3,5)
# Output: update() missing 2 required keyword-only arguments: 'major_version' and 'minor_version'

update(3, minor_version=5)
# Output: update() missing 1 required positional argument: 'pos_arg2'

update(1, 2, 3, minor_version=5)
# Output: update() takes 2 positional arguments but 3 positional arguments (and 1 keyword-only argument) were given

update(1, 2, major_version=3, minor_version=5)
# Outputs:
# pos_arg1: 1, pos_arg2: 2
# major:3, minor:5
```

또한 N개의 `positional arguments` 를 받지만 받지만 `keyword arguments` 는 `keyword-only arguments` 로 강제하고 싶을경우 다음과 같이 사용할 수 있습니다.
```
def update(*args, major_version, minor_version):
    print('args: {}'.format(args))
    print('major:{}, minor:{}'.format(major_version, minor_version))

update(3,5)
# Output: update() missing 2 required keyword-only arguments: 'major_version' and 'minor_version'

update(3, minor_version=5)
# Output: update() missing 1 required keyword-only argument: 'major_version'

update(1, major_version=3, minor_version=5)
# Outputs:
# args: (1,)
# major:3, minor:5

update(1,2,3, major_version=3, minor_version=5)
# Outputs:
# args: (1, 2, 3)
# major:3, minor:5
```

그리고 `keyword arguments` 들중 일부만 `keyword-only arguments` 로 강제하고 싶을경우 다음과 같이 사용할 수 있습니다.
```
def update(*, major_version, minor_version, **kwargs):
    print('kwargs: {}'.format(kwargs))
    print('major:{}, minor:{}'.format(major_version, minor_version))

update(major_version=3, minor_version=5)
# Outputs:
# kwargs: {}
# major:3, minor:5

update(kwargs1=1, major_version=3)
# Output: update() missing 1 required keyword-only argument: 'minor_version'

update(kwargs1=1, kwargs2=2, major_version=3, minor_version=5)
# Outputs:
# kwargs: {'kwargs1': 1, 'kwargs2': 2}
# major:3, minor:5
```

이번에는 약속된 `positional arguments` 를 받지만 `keyword arguments` 중 일부는 `keyword-only arguments` 로 강제하고 싶을경우 다음과 같이 사용할 수 있습니다.
```
def update(pos_arg1, pos_arg2, *, major_version, minor_version, **kwargs):
    print('pos_arg1: {}, pos_arg2: {}'.format(pos_arg1, pos_arg2))
    print('kwargs: {}'.format(kwargs))
    print('major:{}, minor:{}'.format(major_version, minor_version))

update(1,2, kwargs1=1, kwargs2=2, major_version=3)
# Output : update() missing 1 required keyword-only argument: 'minor_version'

update(1,2, kwargs1=1, kwargs2=2, major_version=3, minor_version=5)
# Outputs:
# pos_arg1: 1, pos_arg2: 2
# kwargs: {'kwargs1': 1, 'kwargs2': 2}
# major:3, minor:5
```

마지막으로 위에서 확인한 모든것을 응용하면 다음과 같습니다.
```
def update(pos_arg1, pos_arg2, *args, major_version, minor_version, **kwargs):
    print('pos_arg1: {}, pos_arg2: {}'.format(pos_arg1, pos_arg2))
    print('args: {}'.format(args))
    print('kwargs: {}'.format(kwargs))
    print('major:{}, minor:{}'.format(major_version, minor_version))

update(1,2,3,4,5,6,7, kwargs1=1, kwargs2=2, major_version=3, minor_version=5)
# Outputs:
# pos_arg1: 1, pos_arg2: 2
# args: (3, 4, 5, 6, 7)
# kwargs: {'kwargs1': 1, 'kwargs2': 2}
# major:3, minor:5
```

오늘은 여기까지~!
