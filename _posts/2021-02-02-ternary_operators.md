---
layout: post
title:  "3항 연산"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/ternary_operators.html) 내용을 읽고 이해한것을 정리한 글입니다.

### 3항연산
3항 연산은 `Python` 에서 `조건문`으로 알려져 있다. 이러한 연산자는 조건의 `참`과 `거짓`으로 실행여부를 판단한다.

```
value_if_true if condition else value_if_false
```

이를 이용하면 `멀티라인 if` 문을 간략하게 표현할 수 있습니다.
```
is_nice = True
state = "nice" if is_nice else "not nice"
```

잘 알려지지 않은 3항연산 사용방법은 `Tuple`을 이용한 방식이다.
```
(if_test_is_false, if_test_is_true)[test]
```

```
nice = True
personality = ("mean", "nice")[nice]
print("The cat is " + personality)

# Output
The cat is nice
```
동작 원리는 간단하다. `True` 를 `1`로 `False` 를 `0`으로 판단해서 동작하고 동일한 원리로 `list` 도 적용가능하다.<br>
위의 예제는 널리 쓰이지 않으며 일부사람들은 참,거짓 값의 위치를 혼동할 수 있고 `Pythonic` 하지 않다고 해서 사용하길 꺼려한다.
또 다른 이유는 if-else 문은 참,거짓중 한쪽에 대한 연산만 실행하지만 `Tuple` 은 모든연산을 실행한다는점도 있다. <br>
여러가지 이유로 이 방식은 ***추천하지 않는다.***

```
condition = True
print(2 if condition else 1/0)
# Output
2

print((1/0, 2)[condition])
# Output
ZeroDivisionError: integer division or modulo by zero
```

### 더 간략한 3항연산
Python 에서는 위에서 살펴본 일반적인 3항연산 보다 더 간략한 방식으로 3항연산을 사용할 수 있다.

```
True or "Some"
# Output : True

False or "Some"
# Output : 'Some'
```
이러한 방식은 빠르게 결과를 체크하고 결과가 없을경우 메시지를 남길때 유용하다.

```
output = None
msg = output or "No data returned"
print(msg)

# Output : No data returned
```

또한 매개변수의 기본값을 정의할때 유용하게 사용할 수 있다.
```
def my_function(real_name, optional_display_name=None):
    optional_display_name = optional_display_name or real_name
    print(optional_display_name)

my_function("John")
# Output : John

my_function("Mike", "anonymous123")
# Output : anonymous123
```

오늘은 여기까지~!
