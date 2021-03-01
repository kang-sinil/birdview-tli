---
layout: post
title:  "defaultdict, OrderedDict"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/collections.html) 내용을 읽고 이해한것을 정리한 글입니다.

### Collections
Python 은 Collections 라 부르는 여러데이터를 담을수 있는 데이터타입을 제공한다.
그중에 오늘 알아볼 데이터타입은 다음과 같다.
* defaultdict
* OrderedDict

#### defaultdict
`defaultdict` 는 생성시 전달받은 데이터의 ***초기값을 default_value 로 사용*** 한다. `int`, `list`, `set` 등을 초기값으로 지정할수 있기때문에 여러용도로 사용할 수 있다.
`default_value` 가 할당되어 있기때문에 `dict` 와 다르게 ***key 의 존재여부를 확인할 필요가 없다.***
```
from collections import defaultdict
colours = ['Yellow', 'Blue']

favourite_colours = dict()

for colour in colours:
    print(favourite_colours[colour])
# Output: KeyError: 'Yellow'

favourite_defaultdict_colours = defaultdict(str)
for colour in colours:
    print(favourite_defaultdict_colours[colour])
# Outputs:
# ''
# ''
```

`dict` 를 사용할때 만약 `key` 가 없다면 `KeyError` 를 반환하는데 `defaultdict` 를 이용하면 이러한 문제를 피해갈수 있다.
`defaultdict` 를 사용한 예시로는 다음과 같이 `list` 를 `value` 로 갖고 데이터를 계속해서 쌓을때도 유용하게 사용할 수 있다.
```
from collections import defaultdict
colours = (
    ('Yasoob', 'Yellow'),
    ('Ali', 'Blue'),
    ('Arham', 'Green'),
    ('Ali', 'Black'),
    ('Yasoob', 'Red'),
    ('Ahmed', 'Silver'),
)

favourite_defaultdict_colours = defaultdict(list)

for name, colour in colours:
    favourite_defaultdict_colours[name].append(colour)

print(favourite_defaultdict_colours)
# Outputs:
# defaultdict(<class 'list'>,
#             {'Ahmed': ['Silver'],
#              'Arham': ['Green'],
#              'Yasoob': ['Yellow', 'Red'],
#              'Ali': ['Blue', 'Black']})
```

다음과 같이 `defaultdict` 를 `value` 로 갖는 `defaultdict` 를 만들수 있고, `json.dumps` 을 이용하면 `defaultdict` 의 내용만 확인 가능하다.  
```
some_dict = {}
some_dict['colours']['favourite'] = "yellow"
# Raises KeyError: 'colours'
```

```
from collections import defaultdict
import json
tree = lambda: defaultdict(tree)
some_dict = tree()
some_dict['colours']['favourite'] = "yellow"

print(some_dict)
# Outputs: defaultdict(<function <lambda> at 0x1018bc840>,
#                      {'colours': defaultdict(<function <lambda> at 0x1018bc840>,
#                                              {'favourite': 'yellow'})})

print(json.dumps(some_dict))
# Output: {"colours": {"favourite": "yellow"}}
```

#### OrderedDict

`OrderedDict` 는 `dict` 에 데이터가 추가될때의 ***순서를 기억*** 한다. `key` 가 변경되지 않는다면 데이터가 변경되어도 순서를 유지하지만, `key` 를 제거하고 다시 추가할경우 ***제일 마지막 순서에 기억*** 된다.
```
# Python 3.5.10
colours =  {"Red" : 198, "Green" : 170, "Blue" : 160}
for key, value in colours.items():
    print(key, value)
# Outputs :
# Green 170
# Red 198
# Blue 160

from collections import OrderedDict
ordered_dict_colours = OrderedDict([("Red", 198), ("Green", 170), ("Blue", 160)])
for key, value in ordered_dict_colours.items():
    print(key, value)
# Outputs :
# Red 198
# Green 170
# Blue 160
```

그런데 `Python 3.7` 부터는 `dict` 가 기본적으로 순서를 기억하도록 개선되었다.[참고](https://docs.python.org/3.7/whatsnew/3.7.html#summary-release-highlights)<br>
그렇다면 `OrderedDict` 는 더이상 필요없는것일까? ***그렇지 않다.*** 다음과 같은 두가지 이유 때문이라도 순서가 보장되어야 하는 `dict` 를 사용할경우 `OrderedDict` 를 사용하는게 좋다.
* `Python 3.6` 이하의 로직에서 `dict` 를 이용할경우 순서를 보장해주지 않기때문에 하위버전에서도 동일한 동작을 보장을 해줘야하기 위해서 필요하다.
* 사용하는 `dict` 가 순서를 보장하는 `dict` 를 라는것을 다른개발자들에게 알려주기 위해서 필요하다.

오늘은 여기까지~!
