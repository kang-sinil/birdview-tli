---
layout: post
title:  "Counter, deque, namedtuple"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/collections.html) 내용을 읽고 이해한것을 정리한 글입니다.

### Collections
`Python` 은 `Collections` 라 부르는 여러데이터를 담을수 있는 데이터타입을 제공한다.
그중에 오늘 알아볼 데이터타입은 다음과 같다.
* Counter
* deque
* namedtuple

### Counter
`Counter` 를 이용하면 특정항목의 개수를 셀 수 있다. 예를들어 사람이름이 몇 번 중복되는지 알고 싶을 때 다음과 같이 사용할 수 있다.
```
from collections import Counter

names = (
    'Yasoob',
    'Ali',
    'Arham',
    'Ali',
    'Yasoob',
    'Ahmed'
)

favs = Counter(name for name in names)

type(favs)
# Output: <class 'collections.Counter'>

print(favs)
# Output: Counter({'Ali': 2, 'Yasoob': 2, 'Arham': 1, 'Ahmed': 1})
```

### deque
`deque` 는 `Queue` 의 양쪽에 구성요소를 추가,삭제 할 수 있는 `Queue` 이다.

```
from collections import deque
deq = deque(range(5))
print(deq)
# Output: deque([0, 1, 2, 3, 4])

deq.popleft() # 0
print(deq)
# Output: deque([1, 2, 3, 4])

deq.pop() # 4
print(deq)
# Output: deque([1, 2, 3])
```

또한 `deque` 구성요소 개수를 제한 할 수 있다. `deque` 의 구성요소가 제한수까지 도달했을때 데이터를 추가하면 ***반대쪽 끝의 구성요소가 제거*** 된다.

```
deq = deque([0, 1, 2, 3, 4], maxlen=5)
print(deq)
# Output: deque([0, 1, 2, 3, 4], maxlen=5)

deq.extend([5])
print(deq)
# Outout: deque([1, 2, 3, 4, 5], maxlen=5)

deq.extendleft([0])
print(deq)
# Output: deque([0, 1, 2, 3, 4], maxlen=5)
```

### namedtuple
이미 `tuple` 에 대해서는 알고 있을것이다. 그렇다면 `namedtuple` 은 무엇일까? `namedtuple` 을 사용하면 `tuple` 구성요소에 접근할때 `index` 를 사용하지 않아도 된다.
데이터를 접근하는 면에서는 `dict` 와 비슷하지만 `immutable` 하다는 차이가 있다.
```
from collections import namedtuple

Animal = namedtuple('Animal', 'name age type')
perry = Animal(name="perry", age=31, type="cat")
print(perry)
# Output: Animal(name='perry', age=31, type='cat')

print(perry.name)
# Output: perry

print(perry[0])
# Output: perry
print(perry[1])
# Output: 31
print(perry[2])
# Output: cat

perry.age = 42
# Outputs: Traceback (most recent call last):
#  File "<stdin>", line 1, in <module>
# AttributeError: can't set attribute
```

조금더 알아보자. 예제에서 보았듯이 `tuple` 의 구성요소에 접근하기 위해서 `.` 을 이용 할 수 있고 `index` 로도 접근 가능하다.
`namedtuple` 은 `tuple_name` 과 `tuple_field_names` 2개의 `arguement` 를 이용한다.<br>
예제에서는 `Animal` 이라는 `tuple_name` 과 `name, page, type` 이라는 `tuple_field_names` 를 갖고 있다.
또한 `immutable` 하기때문에 정의후 값을 변경하려 할경우 오류가 발생한다. <br>
마지막으로 `namedtuple` 은 `dict` 로 변경이 가능하다.

```
from collections import namedtuple

Animal = namedtuple('Animal', 'name age type')
perry = Animal(name="Perry", age=31, type="cat")
print(perry._asdict())
# Output: OrderedDict([('name', 'Perry'), ('age', 31), ('type', 'cat')])
```

오늘은 여기까지~!
