---
layout: post
title:  "Object introspection"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/object_introspection.html) 내용을 읽고 이해한것을 정리한 글입니다.

### Object introspection

프로그래밍에서 `introspection` 이라는것은 런타임중에 객체의 정보를 알아낼수 있는 능력을 뜻합니다. `Python` 의 모든것들은 객체이기 때문에 `introspection` 할 수 있고, 이는 파이썬의 장점중 하나입니다. `Python` 은 `introspection` 을 위한 몇가지 내장함수와 모듈을 제공합니다.

#### dir

`introspection` 을 위한 중요한 함수로써 `dir` 이 있습니다. 객체의 속성과 함수를 반환합니다.
```
my_list = [1, 2, 3]
dir(my_list)
# Outputs:
# ['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__',
# '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__',
# '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__',
# 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

함수 이름을 불러올수 없을때 이 방식을 사용 할 수 있습니다. 만약 `dir()` 을 아무 `argument` 없이 실행 할 경우 현재 `scope` 내의 모든 정보들을 반환할 것입니다.
```
my_list = [1, 2, 3]
dir()
# Output: ['__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'my_list']
```

#### type 과 id
`type()` 은 객체의 타입정보를 반환합니다.
```
print(type(''))
# Output: <type 'str'>

print(type([]))
# Output: <type 'list'>

print(type({}))
# Output: <type 'dict'>

print(type(3))
# Output: <type 'int'>
```

`id()` 는 객체의 유니크한 id 를 반환합니다.

```
name = "Yasoob"
print(id(name))
# Output: 4351338904
```

#### inspect 모듈
`inspect` 모듈은 객체의 정보를 알기위한 몇가지 유용한 함수를 제공합니다. <br>
예를 들어, 클래스의 내용을 검사하거나, 메서드의 소스 코드를 꺼내오거나, 함수의 인자 리스트를 추출하고 포맷하거나, 자세한 트레이스백을 표시하는 데 필요한 모든 정보를 얻는 데 도움이 될 수 있습니다.
```
import inspect
print(inspect.getmembers(list))
[('__add__', <slot wrapper '__add__' of 'list' objects>), ('__class__', <class 'type'>), ...]
```

오늘은 여기까지~!
