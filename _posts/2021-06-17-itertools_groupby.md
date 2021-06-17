---
layout: post
title:  "itertools.groupby"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>


### itertools.groupby
혹시 [itertools.groupby](https://docs.python.org/ko/3/library/itertools.html#itertools.groupby) 이라는걸 보신적이 있으신가요?
```
itertools.groupby(iterable, key=None)
```
`itertools` 란 `iterator` 를 만들고 활용하는데 사용되는 라이브러리 입니다.
여러가지 유용한 기능들을 제공하지만 오늘 알아볼 `groupby` 는 정렬된 `iterable` 을 대상으로 `key` 함수를 이용해 데이터 분류후 반환하는 함수입니다.

예를들면 다음과 같습니다.
```
group_dict = [
    list(y)
    for x, y in itertools.groupby(
        sorted(data.get("targets"), key=lambda x: (x["number"])),
        lambda x: (x["number"]),
    )
]
```

이해를 돕기위해 다른 예시를 들면 다음과 같습니다.
```
import itertools
data = [
    {'name': '홍길동', 'blood': 'O'},
    {'name': '강호동', 'blood': 'AB'},
    {'name': '유재석', 'blood': 'B'},
    {'name': '고현정', 'blood': 'AB'},
    {'name': '최지우', 'blood': 'A'},
    {'name': '김철수', 'blood': 'B'},
    {'name': '박나래', 'blood': 'A'},
    {'name': '장윤주', 'blood': 'A'}
]

sorted_data = sorted(data, key=lambda x: x['blood'])
grouped_data = itertools.groupby(sorted_data, key=lambda x: x['blood'])

result = {}
for key, group_data in grouped_data:
    result[key] = list(group_data)

print(result)

# Outputs :
# {'A': [{'name': '최지우', 'blood': 'A'}, {'name': '박나래', 'blood': 'A'}, {'name': '장윤주', 'blood': 'A'}],
#  'AB': [{'name': '강호동', 'blood': 'AB'}, {'name': '고현정', 'blood': 'AB'}],
#  'B': [{'name': '유재석', 'bl': '김철수', 'blood': 'B'}],
#  'O': [{'name': '홍길동', 'blood': 'O'}]}
```

`itertools.groupby` 의 동작은 `iterable` 객체를 순차적으로 돌면서 현재 기억하고 있는 `key` 의 값과 다른값이 나오기 전까지 하나의 리스트에 모아둡니다.
그 후 새로운 `key` 의 값이 나올경우 신규 리스트를 만들고 그곳에 새로운 데이터를 쌓기 시작합니다.<br>
만약 정렬이 되어있지 않더라도 그룹이 나뉘어져 있을뿐 모든 데이터는 다 보관되고 있습니다. 다만 원천데이터의 순서에 의존적인 그룹핑을 하다보니 기대하는 결과와는 다른 결과가 아니게 됩니다. :(
```
import itertools
data = [
    {'name': '홍길동', 'blood': 'O'},
    {'name': '강호동', 'blood': 'AB'},
    {'name': '유재석', 'blood': 'B'},
    {'name': '고현정', 'blood': 'AB'},
    {'name': '최지우', 'blood': 'A'},
    {'name': '김철수', 'blood': 'B'},
    {'name': '박나래', 'blood': 'A'},
    {'name': '장윤주', 'blood': 'A'}
]

grouped_data = itertools.groupby(data, key=lambda x: x['blood'])

result = {}
  for key, group_data in grouped_data:
      print(f'{key}: {list(group_data)}')


# Outputs
# O: [{'name': '홍길동', 'blood': 'O'}]
# AB: [{'name': '강호동', 'blood': 'AB'}]
# B: [{'name': '유재석', 'blood': 'B'}]
# AB: [{'name': '고현정', 'blood': 'AB'}]
# A: [{'name': '최지우', 'blood': 'A'}]
# B: [{'name': '김철수', 'blood': 'B'}]
# A: [{'name': '박나래', 'blood': 'A'}, {'name': '장윤주', 'blood': 'A'}]
```

오늘은 여기까지~!
