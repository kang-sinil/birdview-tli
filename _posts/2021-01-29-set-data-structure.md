---
layout: post
title:  "set 자료구조"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/set_-_data_structure.html) 내용을 읽고 이해한것을 정리한 글입니다.

### set 자료구조

`set` 은 매우 유용한 자료구조 이다. `set` 은 `list` 처럼 보이지만 구성요소들은 모두 `유니크`하다. <br> `set` 은 다음과 같이 생성 할 수 있다.

```
a_set = {'red', 'blue', 'green'}
print(type(a_set))
b_set = set(['red', 'blue', 'green'])
print(type(b_set))

# Output
<class 'set'>
<class 'set'>
```

이러한 `set` 의 특성을 활용하면 다음과 같은 경우에 유용하게 사용할 수 있다.

#### list 의 구성요소들중 2개이상의 중복된 요소들만 추출하고 싶을경우

`for` 반복문을 사용한다면 다음과 같은 것이다.
```
some_list = ['a', 'b', 'c', 'b', 'd', 'm', 'n', 'n']

duplicates = []

for value in some_list:
    if some_list.count(value) > 1:
        if value not in duplicates:
            duplicates.append(value)

print(duplicates)

# Output
['b', 'n']
```

하지만 `set` 을 이용한다면 조금더 간단하고 깔끔하게 이를 처리할 수 있다.

```
some_list = ['a', 'b', 'c', 'b', 'd', 'm', 'n', 'n']
duplicates = set([x for x in some_list if some_list.count(x) > 1])
print(duplicates)

# Output
{'n', 'b'}
```

#### 2개의 set 의 요소중 교집합을 찾고 싶을때

```
valid = set(['yellow', 'red', 'blue', 'green', 'black'])
input_set = set(['red', 'brown'])
print(input_set.intersection(valid))

# Output
{'red'}
```


#### 2개의 set 중에 특정 set 에만 있는 요소를 찾고싶을때
```
valid = set(['yellow', 'red', 'blue', 'green', 'black'])
input_set = set(['red', 'brown'])
print(input_set.difference(valid))

# Output
{'brown'}
```

오늘은 여기까지~!
