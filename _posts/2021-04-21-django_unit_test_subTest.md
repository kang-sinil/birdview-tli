---
layout: post
title:  "Django Unittest 의 subTest"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>
[여기](https://docs.python.org/3/library/unittest.html) 의 내용을 참고해주세요.

### subTest
테스트케이스를 작성하다 보면 가끔 다음과 같이 특정결과가 나오는지를 조건을 변경해가면서 반복적으로 테스트해야 할 때가 있습니다.
예를들어 다음과 같은 테스트코드가 있다고 가정해봅시다.
```
def test_필수값_누락_확인(self):
    # 정상적인 params = {A:'1', B: '2', C:'3', D:'4'}
    params = {B: '2', C:'3', D:'4'}
    response = self.client.get(url, data=params)
    self.assertEqual(status.HTTP_4XX, response.status_code)
    self.assertEqual('A가 없어서 실패함', response.data)
```
이런 상황에서 B,C,D 역시 필수값이어서 **필수값이 누락된 경우에 대한 테스트케이스를 작성해야 한다**면 어떻게 해야할까요?
이럴때 일반적으로 적용해 볼 수 있는 방법은 3가지 정도 입니다.
* 하나의 테스트케이스에 필수값이 누락된 경우를 모두 구현하기
* 변경되는 Parameter 별로 테스트케이스 만들기
* subTest 를 이용해서 반복적으로 테스트하기


첫번째 방법은 하나의 테스트케이스에 필수값이 누락된 경우를 모두 구현하는 방법으로 다음과 같이 구현할 수 있습니다.
```
def test_필수값_누락_확인(self):
    params = {'B': '2', 'C':'3', 'D':'4'}
    response = self.client.get(url, data=params)
    self.assertEqual(status.HTTP_4XX, response.status_code)
    self.assertEqual('A가 없어서 실패함', response.data)

    params = {'A': '1', 'C':'3', 'D':'4'}
    response = self.client.get(url, data=params)
    self.assertEqual(status.HTTP_4XX, response.status_code)
    self.assertEqual('B가 없어서 실패함', response.data)

    params = {'A': '1', 'B':'2', 'D':'4'}
    response = self.client.get(url, data=params)
    self.assertEqual(status.HTTP_4XX, response.status_code)
    self.assertEqual('C가 없어서 실패함', response.data)

    params = {'A': '1', 'B':'2', 'C':'3'}
    response = self.client.get(url, data=params)
    self.assertEqual(status.HTTP_4XX, response.status_code)
    self.assertEqual('D가 없어서 실패함', response.data)
```
이렇게 구현할 경우 하나의 테스트케이스로 4가지 상황에 대한 경우를 검증 할 수 있습니다. 다만 `test_필수값_누락_확인` 테스트케이스가 실패했을경우 A,B,C,D 중 어떤 부분에서 문제가 발생했는지를 바로 알기 힘들고 **한개의 테스트케이스는 하나의 기능을 검증한다.** 라는 규칙이 모호해집니다.

두번째 방법으로는 Parameter 별로 누락된 경우에 대한 테스트케이스를 만드는 방법입니다.<br>
예를들면 다음과 같습니다.
```
def test_필수값_A_누락_확인(self):
    params = {'B': '2', 'C':'3', 'D':'4'}
    response = self.client.get(url, data=params)
    self.assertEqual(status.HTTP_4XX, response.status_code)
    self.assertEqual('A가 없어서 실패함', response.data)

def test_필수값_B_누락_확인(self):
    params = {'A': '1', 'C':'3', 'D':'4'}
    response = self.client.get(url, data=params)
    self.assertEqual(status.HTTP_4XX, response.status_code)
    self.assertEqual('B가 없어서 실패함', response.data)

def test_필수값_C_누락_확인(self):
    params = {'A': '1', 'B':'2', 'D':'4'}
    response = self.client.get(url, data=params)
    self.assertEqual(status.HTTP_4XX, response.status_code)
    self.assertEqual('C가 없어서 실패함', response.data)

def test_필수값_D_누락_확인(self):
    params = {'A': '1', 'B':'2', 'C':'3'}
    response = self.client.get(url, data=params)
    self.assertEqual(status.HTTP_4XX, response.status_code)
    self.assertEqual('D가 없어서 실패함', response.data)
```
이렇게 구현할 경우 한가지 기능을 검증하는 목적도 달성하고 테스트 실패시 어떤 부분에서 실패했는지를 테스트케이스 이름을 보고 쉽게 유추할 수 있습니다.
다만 _'필수값인 Request Parameter 의 누락이 되었을때 어떻게 동작해야하는가?'_ 의 관점에서 봤을때는 동일한 로직에서 파라미터만 계속변경되기 때문에 중복로직이 많이 발생한다는 느낌을 받을수 있습니다.

세번째 방법은 첫번째 방법과 두번째 방법의 사이에 있는 방법으로 subTest를 적용하는 방법입니다.<br>
subTest 는 **테스트케이스 내부에서 반복적으로 테스트를 시도하고 싶을때 사용하는 테스트케이스**로 위와 같은 상황일때 적용해 볼 수 있는 방법입니다.
테스트케이스의 관점을 _'A 가 없을때 4XX이 발생하다'_ 가 아닌 _'필수값인 Request Parameter 가 누락 되었을때 4XX이 발생하는것을 검증한다'_ 로 보면 다음과 같이 subTest 를 작성할 수 있습니다.
```
  def test_필수값_누락_확인(self):

    subtest_params = [
        {del_param_key: 'A'},
        {del_param_key: 'B'},
        {del_param_key: 'C'},
        {del_param_key: 'D'},
    ]

    for subtest_param in subtest_params:
      with self.subTest(subtest_param=subtest_param):
        params = {'A': '1', 'B':'2', 'C':'3', 'D':'4'}

        delete_param_key = subtest_param['del_param_key']
        del params[delete_param_key]

        response = self.client.get(url, data=params)
        self.assertEqual(status.HTTP_4XX, response.status_code)
        self.assertEqual(f'{delete_param_key}가 없어서 실패함', response.data)
```
이와 같이 subTest 를 구성할경우 `test_필수값_누락시_테스트` 라는 테스트케이스 내부에서 A,B,C,D 파라미터가 없는경우에 대한 테스트케이스가 각각 동작하고 이에 대한 성공실패를 확인 할 수 있습니다.
<br> 단 subTest 를 남발할경우 오히려 테스트케이스를 작성하기도 어려워지고 검증하기도 까다로워 지는 문제가 발생할 수 있습니다.
 때문에 subTest 적용전에 항상 **'이 테스트들을 subTest 로 묶어도 한가지 기능을 검증하는것으로 볼수 있는가?'** 를 고민해보고 적용하시기 바랍니다.
그리고 추가적으로 주의할점은 테스트케이스 시작시 동작하는 `setUp()` 의 경우 부모 테스트케이스인 `test_필수값_누락시_테스트` 가 실행될때 한번만 실행되고 subTest 들이 반복적으로 실행될때는 `setUp()` 이 실행되지 않으니 각 subTest 들이 서로에게 영향을 주는 상태변경이 있을경우 초기화 작업을 별도로 진행해 주셔야 합니다.

오늘은 여기까지~!
