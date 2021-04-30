---
layout: post
title:  "Unittest assert_called_with"
author: "강신일"
comments: False
tags: python test
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>


### Mock.assert_called_with
테스트케이스를 작성하다보면 **특정함수가 호출되었는지 혹은 특정함수가 호출되면서 특정 Parameter 를 매개변수로 전달받았는지를 검증** 하고 싶을때가 있습니다.
예를들면 `Request` 요청시 필수 `Parameter` 가 누락되었을때 `400 BadRequest` 를 반환하고 있다고 가정해보겠습니다.
이를 위한 테스트케이스로 API 요청시 `Parameter` 들을 제외하고 요청한다 했을때 테스트코드는 아래와 같이 구현될것입니다.
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

```
여기서 `Response` 에 실패에 대한 사유를 반환하지 않을경우 이 테스트코드가 실패했을때 작성자가 기대한대로 특정 `Parameter` 가 누락되어서 실패한것인지 아니면 다른 이유로 실패한것인지를 알 수 없다는 문제가 있습니다.
이를 해결하기 위한 여러가지 방법은 여러가지 있겠지만 만약 실패시 실패사유를 별도의 로그로 남기도록 구현되어 있다면 적용해 볼수있는 방법이 하나 있습니다.
바로 `mock` 과 `assert_called_with` 를 이용하는 방법으로 `assert_called_with` 는 특정함수가 호출되면서 특정 `Parameter` 를 매개변수로 전달받았는지를 검증하기 위한 함수입니다.
만약 `view` 의 아래와 같은 함수가 있고 필수파라미터 검증에 대한 실패처리 로직이 다음과 같이 구성되어 있다고 가정해봅시다.
```
def 내정보수정(self):
    serializer = MyProfileUpdateSerializer(data=request_data)
    if not serializer.is_valid():
        logger.error(serializer.fail_reason())

        response_data = self.__bad_request_response_data('내 정보 수정에 실패하였습니다.')
        return Response(status=status.HTTP_400_BAD_REQUEST, data=response_data)
    ...

```
위의 예시에서 처럼 `Response` 를 반환하기전 실패에 대한 상세사유를 `logger.error` 로 남기고 있고 전달하는 `fail_reason` 메시지의 포맷이
`XXX 가 없어서 실패함` 인것을 알고있다면 테스트케이스 에서는 다음과 같이 구현할 수 있습니다.
```
@mock.patch('XXX.views.logger.error')
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
        mock_logger.assert_called_with(f"{delete_param_key} 가 없어서 실패함")
```

이럴경우 테스트케이스가 성공하려면 반드시 `logger` 의 `error` 함수가 호출되면서 파라미터로 상황에 맞는 `XXX 가 없어서 실패함` 메시지가 전달받아야만 성공할 수 있습니다.

오늘은 여기까지~!
