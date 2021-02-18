---
layout: post
title:  "Mutable Default Parameter"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/mutation.html) 내용을 읽고 이해한것을 정리한 글입니다.

`Python` 의 `Default Parameter` 중 `mutable` 한 데이터타입을 사용할때 `주의할 상황`에 대한 내용이다.
`mutable` 한 데이터타입으로는 `Dictionary` 나 `List` 가 있다.<br>

### Mutable Default Parameter 의 문제

다음의 예제를 한번보자.
```
def add_to(num, target=[]):
    target.append(num)
    return target

add_to(1)
add_to(2)
add_to(3)
```
위와 같은 함수가 있을때 `add_to()` 함수의 `argument` 를 바꿔가면서 호출한다면 어떤결과가 나올까?
내가 기대한값은 다음과 같았다.
```
add_to(1)
# Output: [1]
add_to(2)
# Output: [2]
add_to(3)
# Output: [3]
```
그러나 실제로 나온 결과값은 다음과 같다.
```
add_to(1)
# Output: [1]
add_to(2)
# Output: [1, 2]
add_to(3)
# Output: [1, 2, 3]
```

처음에는 버그인가 했는데 버그가 아니었다!
`Python` 에서 함수의 `Default Parameter` 는 함수가 `정의될때 한번 생성`되고, 이후에는 생성된 객체를 `재사용`한다.
때문에 호출할때마다 target 에는 동일한 객체가 할당되어서 주소값은 매번동일하다.

```
def add_to(num, target=[]):
    target.append(num)
    print(id(target))
    return target

add_to(1)
# Outputs :
# 4473501816
# [1]

add_to(2)
# Outputs :
# 4473501816
# [1, 2]

add_to(3)
# Outputs :
# 4473501816
# [1, 2, 3]
```
`immutable` 한 데이터타입의 경우 값의 변경이 불가능하기 때문에 생성된값을 재사용해도 영향을 받지 않지만
`mutable` 한 데이터타입을 `Default Parameter` 로 사용했을경우 위와 같이 예상하기 힘든 문제가 발생할 수 있다.

#### 문제를 해결하기 위한 방법

위에서본 이슈때문에 `Python` 에서 `mutable` 한 데이터타입을 `Default Parameter` 로 사용하고 싶을경우에는 `함수내에서 명시적으로 할당`하도록 해야한다.
일반적으로 다음과 같은 방식을 추천한다.

```
def add_to(element, target=None):
    if target is None:
        target = []
    target.append(element)
    return target
```
`mutable parameter` 가 할당될 target 에는 `None` 을 할당하고 함수내의 로직에서 object 를 새로 할당하도록 한다.

오늘은 여기까지~!
