---
layout: post
title:  "Generator"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>

### Generator

`Generator` 는 `Iterator` 를 반환하는 `함수`이고, 기존에 알고 있던 함수와는 다르게 `yield` 라는 키워드를 사용한다.
`Generator` 는 `next()` 를 이용해서 실행되고, `yield` 키워드가 실행될때 실행위치를 기억한상태로 일시적으로 처리를 중단하고 `yield` 의 값을 `next()` 를 호출한쪽으로 반환하면서 제어권을 넘긴다. 그 후 다음 `next()` 를 이용해 재개할경우 함수를 처음부터 다시 시작하는게 아니라 중단된 위치부터 시작한다.<br>
`Generator` 는 `return` 이용해서 함수를 빠져나가거나 끝까지 도달할경우 `StopIteration Exception` 이 발생한다.

```
def test_generator():
    print("before yield 1")
    yield 1
    print("before yield 2")
    yield 2
    print("before yield 3")
    yield 3
    print("done generator")

gen = test_generator()
>>> print("START")
START
>>> next(gen)
before yield 1
1
>>> next(gen)
before yield 2
2
>>> next(gen)
before yield 3
3
>>> next(gen)
done generator
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration

```

### Generator Expression

`Generator` 를 만드는 또다른 방법이 있다.

```
>>> gen = (i*i for i in range(10))
>>> type(gen)
<class 'generator'>
>>>
>>> for i in gen:
...   print(i)
...
0
1
4
9
16
25
36
49
64
81
```

`Generator Expression` 를 이용하면 간단하게 `Generator` 를 생성할 수 있다.
`Generator Expression` 은 [List Comprehension](https://docs.python.org/2/tutorial/datastructures.html?list-comprehensions#list-comprehensions) 과 유사하지만 `[]` 가 아닌 `()` 를 사용한다.

### Generator 사용이유

`Generator` 를 사용했을때 얻을수 있는 장점은 다음과 같다.

* Lazy Evaluation()

`Lazy Evaluation` 어떤 값을 실제로 쓰기전까지 값의 계산을 뒤로 미루는 동작방식이다.<br>
가장 많이 사용하는 예시는 다음과 같다.
```
import time

L = [1, 2, 3]

def print_iter(iter):
	for element in iter:
    		print(element)

def lazy_return(num):
    print("sleep 1s")
    time.sleep(1)
    return num

>>> print("comprehension_list=")
comprehension_list=
>>> comprehension_list = [ lazy_return(i) for i in L ]
sleep 1s
sleep 1s
sleep 1s
>>> print_iter(comprehension_list)
1
2
3

>>> print("generator_exp=")
generator_exp=
>>> generator_exp = ( lazy_return(i) for i in L )
>>> print_iter(generator_exp)
sleep 1s
1
sleep 1s
2
sleep 1s
3
```

예시에서 보여주는 둘의 차이점은 처음에 생성한 comprehension_list 는 생성시점에 lazy_return() 이 동작해서 `sleep()` 로직을 실행시켜 버리지만
두번째 생성한 generator_exp 는 `Generator` 이기때문에 동작하지 않고 있다가 print_iter() 시점에 동작하게 된다.
이를 통해서 불필요한 연산을 미리 실행하지 않아도 되고, 원하는 시점까지 계산을 늦출 수 있게된다.

* Memory 를 효율적으로 사용할 수 있다.

연산을 미리 하지 않아도 되는 덕분에 우리는 아주 큰 배열이나 File 을 읽을때 모든값을 Memory 에 로드 할 필요가 없게된다.
예를 들어 매우 큰 File 의 경우 `Generator` 을 통해서 원하는 부분만 Memory 에 로드 함으로써 Memory 이슈를 피해갈 수 있다.

### 참고

1. [https://youtu.be/xTaCosid1-k?t=1](https://youtu.be/xTaCosid1-k?t=1)
2. [https://docs.python.org/2/glossary.html#term-generator](https://docs.python.org/2/glossary.html#term-generator)
3. [https://docs.python.org/2/tutorial/datastructures.html?list-comprehensions#list-comprehensions](https://docs.python.org/2/tutorial/datastructures.html?list-comprehensions#list-comprehensions)
4. [https://bluese05.tistory.com/56?category=559959](https://bluese05.tistory.com/56?category=559959)
5. [https://dojang.io/mod/page/view.php?id=2412](https://dojang.io/mod/page/view.php?id=2412)
6. [https://realpython.com/introduction-to-python-generators/](https://realpython.com/introduction-to-python-generators/)
