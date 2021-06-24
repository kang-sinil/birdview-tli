---
layout: post
title:  "Serializer#to_representation"
author: "강신일"
comments: False
tags: python
---
python 을 배우면서 검색을 통해 알게되었던 내용들을 정리하는 글입니다.<br>


### Serializer#to_representation
`Serializer` 의 `to_representation()` 는 `Serializer().data` 를 통해 데이터를 serialized 할때 실행되는 함수입니다.
```
class BaseSerializer(Field):
    ...
    @property
    def data(self):
        if hasattr(self, 'initial_data') and not hasattr(self, '_validated_data'):
            msg = (
                'When a serializer is passed a `data` keyword argument you '
                ...
                'or access `.initial_data` instead.'
            )
            raise AssertionError(msg)

        if not hasattr(self, '_data'):
            if self.instance is not None and not getattr(self, '_errors', None):
                self._data = self.to_representation(self.instance)
            elif hasattr(self, '_validated_data') and not getattr(self, '_errors', None):
                self._data = self.to_representation(self.validated_data)
            else:
                self._data = self.get_initial()
        return self._data
```
특별히 Override 를 하지 않았다면 `to_representation()` 은 자신이 가지고 있는 `fields` 를 순회하면서
field 의 값들을 정의된 형식으로 보여주기위해 구현체 클래스에 정의된 `to_representation()` 를 호출합니다.
예를들어 IntegerField 는 값을 int 로 변환하는 작업을 하고, SerializerMethodField 는 사전에 정의된 'get_{field_name}' 를 호출합니다.
```
class Serializer(BaseSerializer):
    ...
    def to_representation(self, instance):
        """
        Object instance -> Dict of primitive datatypes.
        """
        ret = OrderedDict()
        fields = self._readable_fields

        for field in fields:
            try:
                attribute = field.get_attribute(instance)
            except SkipField:
                continue
            ...
            check_for_none = attribute.pk if isinstance(attribute, PKOnlyObject) else attribute
            if check_for_none is None:
                ret[field.field_name] = None
            else:
                ret[field.field_name] = field.to_representation(attribute)

        return ret
```
그렇다면 `to_representation()` 는 어떨때 사용하면 좋을까요? 저는 data 반환시 fields 의 결과값들을 기반으로 추가적인 값을 만들때 사용했습니다.
예를들어 `A_Serailizer(ModelSerializer)` 가 fields 로 B 와 C 를 가지고 있다고 생각해보시죠
```
class A_Serailizer(ModelSerializer):
    B = SerializerMethodField()
    C = SerializerMethodField()

    def get_B()
      ...

    def get_C()
      ...

    class Meta:
      model = A
      fields = ['A_attr', B, C]
```
이때 `A_Serailizer().data` 를 할경우 'A_attr', B, C 의 결과물을 반환할것입니다.
그런데 만약 B와 C를 이용해서 요약정보를 D라는 이름으로 반환해야할때 `to_representation()` 를 이용했습니다.
```
class A_Serailizer(ModelSerializer):
    ...
    def to_representation(self, instance):
        represent = super().to_representation(instance)
        represent['D'] = self.__summary(instance, represent['B'], represent['C'])
        return represent
```
이럴경우 반환값은 'A_attr', B, C, D 가 됩니다. ;)
이처럼 다른 fields 의 결과값에 의존적인 신규 field 를 만들때면 `to_representation()` 를 활용해보는것도 방법중 한가지 일것 같습니다.

오늘은 여기까지~!
