# json
*python package*

> [python json 표준 문서](https://docs.python.org/ko/3/library/json.html)

### JSON

JSON; JavaScript Object Notation 은 key와 value를 가지는 텍스트 파일이다. 웹 서버와 클라이언트의 통신 또는 프로그램 사이의 데이터 교환에서 사용되고 있다.

```json
"parrot": [
    {"name": "식빵", "species": "퀘이커", "age": 5, "weight": 0.141},
    {"name": "핑퐁", "species": "왕관앵무", "age": 1, "weight": 0.092},
    {"name": "돌돌이", "species": "금강앵무", "age": 7, "weight": 1.2}
]
```

- 데이터의 이름은 큰따옴표 ""로 표기한다.
- 쉼표 ,로 값을 나열한다.
- 객체(object)는 중괄호 {}로 표현한다.
- 배열(array)은 대괄호 []로 표현한다.

위의 예시는 배열 parrot에 3개의 JSON 객체가 들어있다. 첫번째 객체는 키(key) name에 해당하는 값(value)이 식빵이며, species 값은 퀘이커, age 값은 5, weight는 0.141이다.

#

파이썬 라이브러리 json은 이런 JSON 타입의 파일과 파이썬의 dictionary 객체를 서로 변환하기 위해 만들어졌다. 

$$
\bold{json} \ \ \xleftrightharpoons[ \bold{deserialization}]{\bold{serialization}} \ \ \bold{dict}
$$


## Serialization

직렬화(serialization)란 파이썬 dict를 JSON 파일 타입으로 변환하는 것으로, JSON 파일로 인코딩하는 작업이다.

이때 파이썬의 자료형들은 다음과 같이 매핑된다.

| 파이썬 | $\rightarrow$ | JSON |
| --- | --- | --- |
| dict | | object |
| list, tuple | | array |
| str | | string |
| int, float, Enum | | number |
| True | | true |
| False | | false |
| None | | null |

* 파이썬 딕셔너리 키는 immutable 객체이지만, JSON의 키는 string 자료형이다. 따라서 직렬화를 할 때 딕셔너리 키의 정보가 손실될 수 있음에 유의해야 한다. 

### json.dumps() : 
> json.dumps(obj, *, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)

입력받은 객체를 JSON 형식 스트림을 통해 fp 객체로 직렬화한다.
- fp 객체는 `.read()`를 통해 읽을 수 있는 텍스트 또는 바이너리 파일이다. 
- `skipkeys`가 참이면 딕셔너리 키에 `str`, `int`, `float`, `bool`, `None` 외의 자료형을 가질 때 TypeError를 발생시키지 않는다. 즉 `tuple` 자료형의 키를 그대로 직렬화하고 싶지 않으면 거짓으로 둔다.
- `allow_nan`이 거짓이면 `float`의 값 `nan`, `int`, `-inf`에 대해 ValueError를 일으킨다.
- `indent`가 음이 아닌 정수나 문자열(`'\t'`)이면 JSON array와 object가 예쁘게 인쇄된다. 문자열을 입력하면 그 문자열로 들여 쓴다. 0이나 음수 혹은 `''`이면 줄 넘김만 들여 쓰고, `None`이면 들여쓰기하지 않는다. 
- `sort_keys`가 참이면 키를 정렬한 객체를 반환한다.
- `cls`에 사용자 정의 JSONEncoder를 입력할 수 있다.

### json.JSONEncoder()

파이썬 데이터 구조를 위한 확장 가능한 JSON 인코더

다음은 파이썬 공식 문서에 나와있는 예시이다.
```python
import json
class ComplexEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, complex):
            return [obj.real, obj.imag]
        return json.JSONEncoder.default(self, obj)
```

위의 표를 보면 complex 자료형은 그 자체로는 인코딩을 지원하지 않는다. 따라서 JSONEncoder를 상속받아 default 함수에 복소수 자료형을 처리하는 부분을 추가했다. 이 클래스를 json.dumps()의 cls 인자 값으로 전달하면 복소수도 `json.dumps()`로 인코딩할 수 있다.

```python
>>> json.dumps(2 + 1j, cls=ComplexEncoder)
'[2.0, 1.0]'
```

## Deserialization

역직렬화(deserialization)란 JSON 파일을 파이썬 dict로 읽어들이는 것이라고 할 수 있다. 즉 JSON 파일을 디코딩하는 작업이다.

JSON의 각 자료형은 다음과 같이 매핑된다.

| JSON | $\rightarrow$ | 파이썬 |
| --- | --- | ---|
| object | | dict |
| array | | list |
| string | | str |
| number(integer) | | int |
| number(real) | | float |
| true | | True |
| false | | False |
| null | | None |

* 유효하지 않은 JSON 문서를 역직렬화하려고 하면, JSONDecodeError가 발생한다.

### json.loads()
> json.loads(s, *, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)

입력을 파이썬 객체로 역직렬화한다.
- 입력은 fp 객체 또는 utf-8, utf-16, utf-32로 인코딩된 bytes 또는 bytearray(python 3.6)를 받을 수 있다.
- `cls`에 사용자 정의 JSONDecoder를 입력할 수 있다.
- `parse_float`가 지정되면, float으로 반환 될(JSON number 중 실수) 값 모두가 지정한 자료형으로 반환된다.
- `parse_int`가 지정되면, int로 반환될 값 모두를 지정 자료형으로 반환한다.
- `parse_constant`가 지정되면, 로 반환될 값 모두를 지정 자료형으로 반환한다.

다음은 파이썬 공식 문서의 예제이다.

```python
>>> import decimal
>>> json.loads('1.1')
float('1.1')
>>> json.loads('1.1', parse_float=decimal.Decimal)
Decimal('1.1')
```

parse_float 인자를 지정하면 float 자료형인 값을 지정 자료형으로 변환한다. [함수를 인자로 전달할 수도 있다.](https://stackoverflow.com/questions/1805072/python-json-parse-float-decimal-decimal-not-working)


### json.JSONDecoder()
> class json.JSONDecoder(*, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, strict=True, object_pairs_hook=None)

json.JSONEncoder와 같이 default 함수를 수정해서 사용할 수 있다.
