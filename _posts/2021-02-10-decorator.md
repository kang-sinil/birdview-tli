---
layout: post
title:  "Decorator"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/decorators.html) 내용을 읽고 이해한것을 정리한 글입니다.

### Decorator

`Decorator`는 ***함수를 감싸는 함수*** 로 함수의 내부를 수정하지 않고 기능의 변화를 주고 싶을때 사용할 수 있으며 코드를 더욱 간략하고 `Pythonic` 하게 만들수 있다.
Python 을 처음접하는 사람들은 대부분 `Decorator` 를 어디서 어떻게 사용해야하는지 모르기때문에 우선은 기반지식부터 알아보려 한다.

#### 함수를 반환하는 함수  

```
def hi(name="yasoob"):
    def greet():
        return "now you are in the greet() function"
    def welcome():
        return "now you are in the welcome() function"
    if name == "yasoob":
        return greet
    else:
        return welcome

a = hi()
type(a)
print(a)
# Outputs:
<type 'function'>
<function greet at 0x102087140>

print(a())
#Output: now you are in the greet() function
```

`def hi` 함수를 보면 `if/else` 에 따라서 `greet` 이나 `welcome` 을 반환한다. 여기서 주의할점은 `greet()` 이나 `welcome()` 이 아니라는 점이다.
`hi 함수`는 `greet` 이나 `welcome` 함수반환시 괄호를 붙이지 않으므로써 함수를 실행하지 않고, 함수자체를 반환하게 된다.
위의 예시를보면 `hi()` 의 호출결과로 `greet` 이라는 함수를 반환받게 된다. 반대로 `hi(name = "ali")` 호출할경우 `welcome` 이라는 함수를 반환받게 된다.
그렇다면 `hi()()` 할경우는 어떤결과가 발생할까? 이는 당연히도 `greet()` 를 호출한것과 동일하다.

```
hi()()
#Output : now you are in the greet() function
```

#### 함수를 파라미터로 사용하기
```
def hi():
    return "hi yasoob!"

def doSomethingBeforeHi(func):
    print("I am doing some boring work before executing hi()")
    print(func())

doSomethingBeforeHi(hi)
#Outputs:
# I am doing some boring work before executing hi()
# hi yasoob!
```

함수도 객체이기 때문에 arguement 로 전달이 가능하다. 물론 전달받은 함수는 함수내에서 실행이 가능하다.
이제 `Decorator` 를 위한 기본지식은 다 배웠다. 이제부터 본격적으로 `Decorator` 를 이해해보자.

#### 처음으로 만들어보는 Decorator
```
def a_new_decorator(a_func):
    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")
        a_func()
        print("I am doing some boring work after executing a_func()")
    return wrapTheFunction

def a_function_requiring_decoration():
    print("I am the function which needs some decoration to remove my foul smell")

a_function_requiring_decoration()
#Output: I am the function which needs some decoration to remove my foul smell

a_function_requiring_decoration = a_new_decorator(a_function_requiring_decoration)
# 이제 a_function_requiring_decoration 는 wrapTheFunction() 에 의해서 전,후 처리 로직이 추가되었다.

a_function_requiring_decoration()
#Outputs:
# I am doing some boring work before executing a_func()
# I am the function which needs some decoration to remove my foul smell
# I am doing some boring work after executing a_func()
```

우리는 위에서 알게된 내용들을 바탕으로 `Decorator` 를 구현했다. 또한 위의 예시를 통해 `Decorator` 가 ***함수의 기능을 감싸는 함수*** 라는것을 이해할 수 있었다.<br>

#### Decorator 간략하게 사용하기

위에서 만든 예시를 `@` 를 이용해서 간략하게 사용 할 수 있다.  
```
@a_new_decorator
def a_function_requiring_decoration():
    """Hey you! Decorate me!"""
    print("I am the function which needs some decoration to remove my foul smell")

a_function_requiring_decoration()
#Outputs:
# I am doing some boring work before executing a_func()
# I am the function which needs some decoration to remove my foul smell
# I am doing some boring work after executing a_func()

# @a_new_decorator 는 아래의 로직을 수행한것과 동일하다.
# a_function_requiring_decoration = a_new_decorator(a_function_requiring_decoration)
```

이제는 `Decorator` 가 Python 에서 어떻게 동작하는지는 이해했을것이라 기대한다. 하지만 해결해야하는 문제가 하나있다.
```
print(a_function_requiring_decoration.__name__)
# Output : wrapTheFunction
```

`a_function_requiring_decoration` 의 이름을 확인했을때 `wrapTheFunction` 가 나오는것은 우리가 기대했던 결과가 아니다.
다행히도 Python 에서는 `functools.wraps` 를 이용하면 간단하게 이 문제를 해결할수 있다.

```
from functools import wraps

def a_new_decorator(a_func):
    @wraps(a_func)
    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")
        a_func()
        print("I am doing some boring work after executing a_func()")
    return wrapTheFunction

@a_new_decorator
def a_function_requiring_decoration():
    """Hey yo! Decorate me!"""
    print("I am the function which needs some decoration to remove my foul smell")

print(a_function_requiring_decoration.__name__)
# Output: a_function_requiring_decoration
```

### USE CASE

이제는 `Decorator` 를 사용하는 부분들을 알아보자.

#### Authorize

`Decorator` 를 이용하면 함수실행전에 사용자의 권한을 확인할 수 있다. 이는 `Flask` 나 `Django` 에서도 사용되는 방식이다.
Django 의 [login-required](https://docs.djangoproject.com/en/3.1/topics/auth/default/#the-login-required-decorator) 이다.
```
from functools import wraps

def requires_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        auth = request.authorization
        if not auth or not check_auth(auth.username, auth.password):
            authenticate()
        return f(*args, **kwargs)
    return decorated
```

#### Logging

`logging` 은 `Decorator` 를 적극적으로 활용할 수 있는 부분이다.

```
def logit(func):
    @wraps(func)
    def with_logging(*args, **kwargs):
        print(func.__name__ + " was called")
        return func(*args, **kwargs)
    return with_logging

@logit
def addition_func(x):
   """Do some math."""
   return x + x


result = addition_func(4)
# Output: addition_func was called
```


### Decorator 의 Argument
그런데 가만히보니 `@wraps` 도 `Decorator` 가 아닌가? 그런데 `wraps` 는 일반함수처럼 `arguement` 를 전달받아서 사용하고 있다.
우리가 만든 `Decorator` 도 그렇게 할 수 있을까? 당연히 가능하다. `Decorator` 를 반환하는 함수로 `Decorator` 를 감싸면 된다. [예시](https://stackoverflow.com/a/15300191)

```
from functools import wraps

def logit(logfile='out.log'):
    def logging_decorator(func):
        @wraps(func)
        def wrapped_function(*args, **kwargs):
            log_string = func.__name__ + " was called"
            print(log_string)
            with open(logfile, 'a') as opened_file:
                opened_file.write(log_string + '\n')
            return func(*args, **kwargs)
        return wrapped_function
    return logging_decorator

@logit()
def myfunc1():
    pass

myfunc1()
# Output: myfunc1 was called
# out.log 파일이 생성되었고, 위의 문자열이 입력되었다.

@logit(logfile='func2.log')
def myfunc2():
    pass

myfunc2()
# Output: myfunc2 was called
# func2.log 파일이 생성되었고, 위의 문자열이 입력되었다.
```


### Decorator Class

`Decorator` 는 `Class` 로도 정의가 가능하다.

```
class DecoratorWithOutParam(object):
    def __init__(self, func):
        print("DecoratorWithOutParam.__init__")
        self.func = func

    def __call__(self, *args, **kwargs):
        print("DecoratorWithOutParam.__call__")
        return self.func(*args, **kwargs)

@DecoratorWithOutParam
def func1(*args, **kwargs):
    print("func1.call start")
    print(args)
    print(kwargs)
    print("func1.call end")

# Output: DecoratorWithOutParam.__init__

func1(1,2,3,k=1,z=1)
# Outputs:
# DecoratorWithOutParam.__call__
# func1.call start
# (1, 2, 3)
# {'k': 1, 'z': 1}
# func1.call end


class DecoratorWithParam(object):
    def __init__(self, *args, **kwargs):
        print("DecoratorWithParam.__init__ start")
        print(args)
        print(kwargs)
        print("DecoratorWithParam.__init__ end")

    def __call__(self, func, *args, **kwargs):
        def inner_func(*args, **kwargs):
            print("inner_func_call")
            return func(*args, **kwargs)
        return inner_func


@DecoratorWithParam(9,8,7,class_params=1)
def func2(*args, **kwargs):
      print("func2.call start")
      print(args)
      print(kwargs)
      print("func2.call end")

# Outputs
# DecoratorWithParam.__init__ start
# (9, 8, 7)
# {'class_params': 1}
# DecoratorWithParam.__init__ end

func2(1,2,3,k=1, z=1)
# Outputs:
# inner_func_call
# func2.call start
# (1, 2, 3)
# {'k': 1, 'z': 1}
# func2.call end

```
오늘은 여기까지~!
