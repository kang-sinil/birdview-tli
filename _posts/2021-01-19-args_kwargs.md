---
layout: post
title:  "*args 와 **kwargs"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/args_and_kwargs.html) 내용을 읽고 이해한것을 정리한 글입니다.

## *args 와 **kwargs

Python 소스코드를 보다보면 심심치 않게 *args 와 **kwargs 변수를 볼 수 있다.<br>
오늘은 이 변수들이 어떤것을 의미하는지 알아볼 예정이다.<br>
우선은 *args 와 **kwargs 의 변수명은 중요하지 않다. 중요한것은 `*` 를 사용한다는 것이다. <br>
그렇기 때문에 우리는 *vars, **kwvars 등 `*`만 사용한다면 변수명은 원하는대로 지정할 수 있다.<br>

### *args 알아보기
*args 와 **kwargs 는 주로 함수정의 시점에 많이 사용된다. *args 와 **kwargs 를 사용하면 함수에 전달하는 argument 의 개수를 고정하지 않아도 된다.
그렇기 때문에 함수를 정의할때 함수에 몇개의 parameter 가 전달되어도 유연하게 대응 할 수 있다. <br>
*args 는 keyword argument 들을 함수에 전달하고, 함수는 이를 하나의 tuple 로 만들어서 사용한다. 다음의 예시를 보자. <br>

```
def test_var_args(f_arg, *args):
    print(type(args))
    print("first normal arg:", f_arg)
    for arg in args:
        print("another arg through *args:", arg)

test_var_args('yasoob', 'python', 'eggs', 'test')
```

위의 함수 실행결과는 다음과 같다.
```
<class 'tuple'>
first normal arg: yasoob
another arg through *args: python
another arg through *args: eggs
another arg through *args: test
```

### **kwargs 알아보기
**kwargs 는 keyword argument 들을 함수에 전달하고, 함수는 이를 하나의 dictionary 로 만들어서 사용한다. 다음의 예시를보자. <br>
```
def greet_me(**kwargs):
    for key, value in kwargs.items():
      print("{0} = {1}".format(key, value))
```

위의 함수 실행결과는 다음과 같다.
```
greet_me(name="yasoob", name2='foo', name3='bar')
name3 = bar
name = yasoob
name2 = foo
```

### *args 와 **kwargs 를 함수 호출시 사용하기
지금까지는 *args 와 **kwargs 가 함수 내부에서 parameter 로 어떻게 사용되는지를 알아보았다면, 이번에는 함수 호출시 argument 로 *args 와 **kwargs 를 어떻게 사용하는지 알아보자.
다음과 같은 함수를 정의 했을때 *args 와 **kwargs 를 함수에 전달하는 방법은 아래와 같다.

```
def test_args_kwargs(arg1, arg2, arg3):
    print("arg1:", arg1)
    print("arg2:", arg2)
    print("arg3:", arg3)
```

```
>>> # tuple argument with *args
>>> args = ("two", 3, 5)
>>> test_args_kwargs(*args)
arg1: two
arg2: 3
arg3: 5

>>> # list argument with *args
>>> args = ['list', 2, 4]
>>> test_args_kwargs(*args)
arg1: list
arg2: 2
arg3: 4

>>> # dictionary argument with **kwargs
>>> kwargs = {"arg3": 3, "arg2": "two", "arg1": 5}
>>>
arg1: 5
arg2: two
arg3: 3
```

그리고 만약 함수에서 *args 와 **kwargs 를 같이 사용하고 싶으면 다음과 같이 정의하면 된다.
```
def some_func(fargs, *args, **kwargs):
    for arg in args:
      print("arg through *args:", arg)
    for key, value in kwargs.items():
      print("kwarg is {0} = {1}".format(key, value))


>>> some_func('fargs', 1,2,3, k=1, kk=2)    
arg through *args: 1
arg through *args: 2
arg through *args: 3
kwarg is k = 1
kwarg is kk = 2
```

오늘은 여기까지~!
