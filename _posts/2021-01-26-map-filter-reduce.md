---
layout: post
title:  "Map,Filter,Reduce"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>

### [Map](https://docs.python.org/2/library/functions.html#map)

`map` 은 list_of_input 의 모든항목을 function_to_apply 함수에 적용한다.

```
map(function_to_apply, list_of_inputs)
```

list 의 요소들을 특정함수에 하나씩 대입하고 그 결과를 수집하려고 할때 유용하게 사용할수 있다. 예를들어 다음과 같은 작업을 하고 싶을때..

```
items = [1, 2, 3, 4, 5]
squared = []
for i in items:
    squared.append(i**2)
```

`map` 을 이용하면 더욱 간단하게 동일한 기능을 처리할 수 있다.

```
items = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, items))
```

대부분의 `map` 은 `lambda` 와 함께 사용한다. 또한 list 의 구성요소는 `함수`도 가능하다.
```
def multiply(x):
    return (x*x)
def add(x):
    return (x+x)

funcs = [multiply, add]
for i in range(5):
    value = list(map(lambda x: x(i), funcs))
    print(value)

# output
    [0, 0]
    [1, 2]
    [4, 4]
    [9, 6]
    [16, 8]
```


### [Filter](https://docs.python.org/2/library/functions.html#filter)

이름에서 알 수 있듯이 `filter` 는 목록에서 `true` 를 반환하는 구성요소들의 목록을 생성한다.

```
number_list = range(-5, 5)
less_than_zero = list(filter(lambda x: x < 0, number_list))
print(less_than_zero)

# output
[-5, -4, -3, -2, -1]
```

### [Reduce](https://docs.python.org/2/library/functions.html#reduce)

`reduce` 는 목록을 이용해 결과값을 계산할때 유용한 함수이다. `reduce` 는 목록의 구성요소들을 순차적으로 계산한다.
예를들어 목록 요소들의 곱을 계산하는 경우 다음과 같이 할수도 있지만..

```
product = 1
list = [1, 2, 3, 4]
for num in list:
    product = product * num

# output
product = 24
```

`reduce` 를 이용하면 다음과 같이 할 수 있다.

```
from functools import reduce
product = reduce((lambda accum_value, current_value: accum_value * current_value), [1, 2, 3, 4])

# output
product = 24
```

오늘은 여기까지~!
