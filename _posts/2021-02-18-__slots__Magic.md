---
layout: post
title:  "__slots__Magic"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/__slots__magic.html) 내용을 읽고 이해한것을 정리한 글입니다.

기본적으로 Python 은 `object` 의 `instance attribute` 를 `dict` 로 관리하고 있는데, 이는 런타임중에 새로운 `attribute` 를 할당하는데 많은 도움이 된다.
```
class CLASS_WITHOUT_SLOTS(object):
    def __init__(self, id, name):
      self.id = id
      self.name = name

cwos = CLASS_WITHOUT_SLOTS(1,'a')
cwos.__dict__
# Output: {'i': 1, 'name': 'a'}

cwos.new_attribute = 'hello'
cwos.__dict__
# Output: {'i': 1, 'name': 'a', 'new_attribute': 'hello'}
```

하지만 런타임중에 `instance attribute` 를 추가할 일이 없는 `Class` 의 경우 불필요한 `dict` 로 `RAM 의 메모리 이슈`가 생길수 있다.
이러한 `Class` 에 한해서 `__slots__` 을 이용하면 이러한 문제를 피할 수 있다.`__slots__` 을 이용하면 `instance` 마다 `dict` 를 만들지 않고 `attribute set` 을 고정시켜서 메모리 이슈를 피할 수 있게된다.

```
class CLASS_WITH_SLOTS(object):
    __slots__ = ['id', 'name']
    def __init__(self, id, name):
        self.id = id
        self.name = name

cws = CLASS_WITH_SLOTS(1,'a')
cws.__dict__

# Outputs:
# Traceback (most recent call last):
#  File "<stdin>", line 1, in <module>
# AttributeError: 'CLASS_WITH_SLOTS' object has no attribute '__dict__'

cws.new_attribute = 'hello'
# Outputs:
# Traceback (most recent call last):
#  File "<stdin>", line 1, in <module>
# AttributeError: 'CLASS_WITH_SLOTS' object has no attribute 'new_attribute'

cws.__slots__
# Output: ['id', 'name']
```
`__slots__` 을 이용했을때 메모리 차이가 얼마나 발생하는지 확인해보자.

```
import ipython_memory_usage.ipython_memory_usage as imu

class CLASS_WITHOUT_SLOTS(object):
    def __init__(self, id, name):
        self.id = id
        self.name = name

class CLASS_WITH_SLOTS(object):
    __slots__ = ['id', 'name']
    def __init__(self, id, name):
        self.id = id
        self.name = name



size = 1024 * 256
imu.start_watching_memory()
# In [6] used 0.7656 MiB RAM in 57.08s, peaked 0.00 MiB above current, total RAM usage 47.53 MiB
x = [CLASS_WITHOUT_SLOTS(1, 'a') for _ in range(size)]
# In [7] used 40.1211 MiB RAM in 0.38s, peaked 0.00 MiB above current, total RAM usage 87.65 MiB
y = [CLASS_WITH_SLOTS(1, 'a') for _ in range(size)]
# In [8] used 16.3203 MiB RAM in 0.35s, peaked 0.00 MiB above current, total RAM usage 103.97 MiB

```
위의 예시에서 보는것과 같이 `__slots__` 를 사용하지 않는 기존방식에서는 `40Mb` 를 사용했지만 `__slots__` 을 이용할경우 `16Mb` 로 메모리사용량이 줄었다.

오늘은 여기까지~!
