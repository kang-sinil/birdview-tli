---
layout: post
title:  "Exceptions"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://book.pythontips.com/en/latest/exceptions.html) 내용을 읽고 이해한것을 정리한 글입니다.

오늘은 예외처리를 할 수 있는 몇가지 방법에 대해서 알아보겠습니다.
### Exceptions

`Python` 기본지식으로 우리는 `try/catch` 를 이용하면 예외처리를 할 수 있다는 것을 알고 있지만 한번 더 설명하겠습니다.
예외가 발생할 수 있는 코드가 `try` block 에 포함되고, `except` block 에서 예외처리를 구현합니다.
`except` block 은 `try` block 에서 예외가 발생했을때만 실행합니다.

다음은 간단한 예시입니다.
```
try:
    file = open('test.txt', 'rb')
except IOError as e:
    print('An IOError occurred. {}'.format(e.args[-1]))

# Output: An IOError occurred. No such file or directory
```

위의 예제에서는 `IOError` 라는 예외만 처리합니다.
그렇다면 여러가지 예외를 다룰때는 어떻게 해야할까요?

#### Handling multiple exceptions:
우리는 여러가지 예외를 다루기 위해서 3가지 방법을 사용할 수 있습니다.
첫번째는 발생할 수 있는 모든 예외를 확인한 후 `tuple` 에 넣는것입니다.

```
try:
    file = open('test.txt', 'rb')
except (IOError, EOFError) as e:
    print("An error occurred. {}".format(e.args[-1]))

# Output: An error occurred. No such file or directory
```

다른방법은 별도의 `except` block 으로 각각의 예외를 나누어서 처리하는것입니다.
이를 이용하면 우리는 원하는 수만큼의 예외를 나누어서 처리할 수 있습니다.
```
try:
    file = open('test.txt', 'rb')
except EOFError as e:
    print("An EOF error occurred.")
    raise e
except IOError as e:
    print("An error occurred.")
    raise e

# Outputs:
# An error occurred.
# Traceback (most recent call last):
#   File "<stdin>", line 8, in <module>
#   File "<stdin>", line 2, in <module>
# FileNotFoundError: [Errno 2] No such file or directory: 'test.txt'
```
예제를 기준으로 설명하자면 만약 발생한 예외가 `EOError` 에서 처리할 수 없는 타입이라면 다음 `IOError` 타입으로 에서 처리를 시도할 것이고 계속해서 순차적으로 구현되어 있는 `except` block 을 시도하다 결국 처리가 되거나 처리하지 못했다면 `try/catch` clause 외부로 예외를 전파하게 될것입니다.

마지막 방법은 `Python` 의 모든 예외처리를 처리할 수 있도록 최상위 예외클래스를 `except` block 에 지정하는 방법입니다.
```
try:
    file = open('test.txt', 'rb')
except Exception as e:
    # Some logging if you want
    raise e

# Outputs:
# Traceback (most recent call last):
#   File "<stdin>", line 4, in <module>
#   File "<stdin>", line 2, in <module>
# FileNotFoundError: [Errno 2] No such file or directory: 'test.txt'
```

이 방식은 프로그램에서 어떤 예외가 발생할지 모르는 경우 유용합니다.
다만 모든 예외를 잡아서 공통된 방식으로 처리하도록 하는것은 의도하지 않은 결과를 초래할 수 있습니다.
예를 들어 `command-line` 기반 프로그램에서 `control + c` 를 누르면 프로그램이 종료되지만,
만약 모든 예외들을 잡으면, `KeyboardInterrupt` 또한 예외로 잡히므로 `control + c`를 눌렀지만 프로그램이 종료되지 않는 문제가 발생합니다.

#### finally clause
위에서 배운 `try`, `except` clause 외에도 사용할 수 있는 `finally` clause 이 있습니다.
`finally` clause 에 있는 로직은 예외발생 여부와 상관없이 실행됩니다. 이는 스크립트 실행후 정리하는 단계에서 사용할 수 있습니다.
```
try:
    file = open('test.txt', 'rb')
except IOError as e:
    print('An IOError occurred. {}'.format(e.args[-1]))
finally:
    print("This would be printed whether or not an exception occurred!")

# Outputs:
# An IOError occurred. No such file or directory
# This would be printed whether or not an exception occurred!
```

#### try/else clause
종종 `try` clause 에서 예외가 발생하지 않았을때만 실행되길 원하는 로직들이 있을수 있습니다.
이럴경우 `else` clause 를 사용하면 쉽게 해결 할 수 있습니다.

```
try:
    print('I am sure no exception is going to occur!')
except Exception:
    print('exception')
else:
    # any code that should only run if no exception occurs in the try,
    # but for which exceptions should NOT be caught
    print('This would only run if no exception occurs. And an error here would NOT be caught.')
finally:
    print('This would be printed in every case.')

# Outputs:
# I am sure no exception is going to occur!
# This would only run if no exception occurs. And an error here would NOT be caught
# This would be printed in every case.
```
`else` clause 는 예외가 발생하지 않을때만 실행될것이고, `finally` clause 보다 먼저 실행됩니다.

오늘은 여기까지~!
