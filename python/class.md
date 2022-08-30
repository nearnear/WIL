# Class

## 0. Class

### 클래스 정의, 생성, 초기화

#### 클래스 정의
- 클래스는 class 키워드로 다음과 같은 형태로 정의한다.

```python
class Time:
    ...
```

#### 클래스 초기화
- 매직 메소드 `__init__`을 초기화자(initializer)라고 한다. 
- 실제로는 `__init__` 메소드를 오버라이딩하여 초기화 값을 저장하는 것이다.

```python
class Time:
    def __init__(self, now): # initiate
        self.current_time = now
``` 

#### 클래스 (객체) 생성
- 클래스 객체는 생성자(constructor)에 의해 생성된다.

```python
import datetime
time = Time(datetime.datetime.now()) # constructor
```

- 생성자로 객체 생성을 호출하면 먼저 `__new__`를 호출하여 객체를 생성하고, `__new__` 메소드가 `__init__` 메소드를 호출해서 객체를 초기화한다.

```python
class Time:
    def __init__(self, now):
        print('init')
        self.current_time = now
    
    def __new__(cls):
        print('new')
        return super().__new__(cls)

    def get_current_time(self):
        return self.current_time
```
다음과 같이 `__new__` 메소드를 오버라이딩하여 먼저 `'new'`를 출력한 후 이전과 같이 실행되도록 해보자.

```python
>>> time = Time(datetime.datetime.now())
new
init
```
- `__new__`가 `__init__` 보다 먼저 실행되는 것을 확인할 수 있다.

 
### Class attribute vs. Instance attribute
- 인스턴스란 특정 클래스 객체이다.
- 클래스 속성은 class 안에서 바로 값을 할당하는 반면, 인스턴스 속성은 `self.attrs`로 할당한다.
- 클래스 속성은 객체에서 공유되는 반면, 인스턴스 속성은 객체에서 공유되지 않는다.
- 클래스 속성과 인스턴스 속성의 이름이 같으면 인스턴스 속성을 먼저 찾는다. 

```python
class Time:
    # class attribute
    current_time = 0

    def __init__(self):
        # instance attribute
        self.default_time = 0

    def add_default_time(self, time):
        self.default_time += time

    def add_current_time(self, time):
        Time.current_time += time
```
- add_deafult_time은 인스턴스 속성을 업데이트한다.
- add_current_time은 클래스 속성을 업데이트한다.

```python
>>> t1 = Time()
>>> t2 = Time()
>>> t1.add_current_time(3)
>>> t1
3
>>> t2.current_time
3
>>> Time.current_time
3
>>> t1.add_default_time(3)
>>> t1
3
>>> t2.default_time
0
>>> Time.default_time
AttributeError: type object 'Time' has no attribute 'default_time'
```
- 클래스에 직접 접근하는 방식으로
    - 클래스 속성에 접근할 수 있다.
    - 인스턴스 속성에는 접근할 수 없다. 
- 한 클래스 객체에서 클래스 속성을 변경하면 
    - 다른 클래스 객체에서도 클래스 속성이 변경된다.
    - 클래스로 직접 접근한 클래스 속성 또한 변경된다.
- 한 클래스 객체에서 인스턴스 속성을 변경하면 다른 객체에는 반영되지 않는다.


### Static Method vs. Class Method vs. Instance Method

- 정적 메소드란 클래스에서 직접 접근할 수 있는 메소드이다. 
- 파이썬에서 클래스에서 접근할 수 있는 메소드는 classmethod와 staticmethod 두가지이다.
- 일반적으로 정적 메소드는 인스턴스에서 접근이 불가능하지만, 파이썬의 정적 메소드는 인스턴스에서 접근이 가능하다.

```python
# times.py
class Time:
    default_point = 0

    def __init__(self):
        self.show = self.default_point

    # instance method
    def print_time(self):
        print(self.show)

    # classmethod
    @classmethod
    def class_method(cls, time):
        return cls.default_point + time

    # staticmethod
    @staticmethod
    def static_method(time):
        return Time.default_point + time

class Future(Time):
    default_point = 1
```

- 인스턴스 메소드는 첫번째 인자로 `self`를 입력받는다.
- classmethod는 첫번째 인자로 클래스를 입력받는다.
- staticmethod는 필수적인 인자가 없다.

```python
>>> from time import *
>>> Time.print_time()
TypeError: print_time() missing 1 required positional argument: 'self'
>>> Time.print_time(None)
AttributeError: 'NoneType' object has no attribute 'show'
>>> Time.print_time(Time)
AttributeError: type object 'Time' has no attribute 'show'
```
- 클래스에서 바로 인스턴스 메서드에 접근할 수 없다.

```python
>>> Time.class_method(3)
3
>>> Time.static_method(3)
3
```
- 클래스에서 바로 classmethod와 staticmethod에 접근할 수 있다.

#### 객체에서 접근하기

```python
>>> a = Time()
>>> a.class_method(3)
3
>>> a.static_method(3)
3
```
- classmethod와 staticmethod 모두 객체(인스턴스)에서 접근이 가능함을 확인할 수 있다.
- 일반적으로 다른 언어에서 정적 메소드는 객체에서 접근이 불가능하다!

#### classmethod vs. staticmethod

```python
>>> Future.class_method(3)
4
>>> Future.static_method(3)
3
```
- classmethod는 cls인자로 입력받은 클래스의 속성을 가져오는 반면, 
- staticmethod는 부모 클래스의 클래스 속성을 가져온다.  



## 1. Inheritance; 상속
- 클래스 상속은 다른 클래스의 속성과 메소드를 물려받는 것이다. 
- 상속을 해주는 클래스를 부모 클래스, 상속 받은 클래스를 자식 클래스라고 한다. 
- `super()` 메소드를 통해 부모 클래스의 메소드를 불러올 수 있다.

```python
class Dictionary:
    search_word = 'apple'

    def __init__(self):
        self.language = 'ko'

    def lookup_url(self, word):
        return lambda x, y: f'https://{x}.dict.naver.com/#/search?query={y}'


class English2Korean(Dictionary):
    def __init__(self):
        self.language = 'en'

    def lookup_e2k_url(self, word):
        # inherit from lookup_url method
        url = super().lookup_url(word)
        return url(self.language, word)

    def lookup_search_word(self):
        return self.lookup_e2k_url(English2Korean.search_word)
```
- Dictionary 클래스는 인스턴스 속성을 초기화하며 하나의 메소드를 가진다.
- English2Korean 클래스는 Dictionary를 상속받아 인스턴스 속성을 초기화했다. 
- lookup_e2k_url은 부모 클래스의 lookup_url 메소드를 상속받으며, 
- lookup_search_word는 부모 클래스에서 정의한 클래스 속성값을 호출한다.

```python
>>> e2k = English2Korean()
>>> e2k.lookup_e2k_url('lighthouse')
https://en.dict.naver.com/#/search?query=lighthouse
>>> e2k.lookup_search_word()
https://en.dict.naver.com/#/search?query=apple
```
- lookup_e2k_url은 부모 클래스의 메서드에서 정의된 url값을 받아 활용한다.
- lookup_search_word에서 부모 클래스의 클래스 속성을 성공적으로 상속했다.

## 2. Polymorphism; 다형성
- 같은 이름의 메소드로 다른 내부로직을 작성할 수 있는 것을 다형성이라고 한다.
- 파이썬에서 Overriding 또는 Abstract method로 구현된다.

### 2.1. Overriding
- 같은 부모클래스를 상속한 자식클래스에서 같은 이름을 가지지만 기능이 다른 메소드를 정의할 수 있다.

```python
class Animal:
    def __init__(self, name):
        self.name = name
    
    def identity(self):
        pass

class Cat(Animal):
    def identity(self):
        print(f'Cat {self.name}')

class Dog(Animal):
    def identity(self):
        print(f'Dog {self.name}')
```
- Animal 클래스는 name 인스턴스로 초기화된다.
- Cat과 Dog 클래스는 Animal을 상속하며 각각 다른 결과를 출력하는 identity 메소드를 가진다.

```python
>>> cat = Cat('honeybear')
>>> cat.identity()
Cat honeybear
>>> dog = Dog('honeybear')
>>> dog.identity()
Dog honeybear
```
- identity 메소드는 각각의 클래스에서 정의된 대로 작동한다. 

#### 파이썬은 method overloading이 없다.
- 메소드 오버로딩이란 하나의 클래스에서 이름이 같고, 인자가 다른 메소드를 허용하는 것이다.
- 파이썬은 메소드 오버로딩이 없기 때문에, 클래스 내에서 같은 이름의 메소드를 정의할 경우, 가장 뒤에 정의된 메소드만 작동한다.

```python
# time.py
class Time:
    def __init__(self):
        self.default_time = 0

    def show_time(self, time):
        print(self.default_time + time)

    def show_time(self):
        print(self.default_time)

    def show_time(self, start, time):
        print(start + time)
```
다음과 같이 정의할 경우, 세번째 show_time만 작동한다.

```python
>>> t = Time()
>>> t.show_time()
TypeError: show_time() missing 2 required positional arguments: 'start' and 'time'
>>> t.show_time(1, 2)
3
```

### 2.2. Abstract class

추상 클래스는 클래스의 확장가능성을 위해 정의할 수 있는 클래스의 틀이다.
- 추상 클래스는 구현되지 않은 추상 메소드를 한개 이상 가지며, 자식 클래스에서 추상 메소드를 구현하도록 강제한다.
- 추상 클래스를 상속한 자식 클래스에서 추상 메소드를 구현하지 않으면 객체를 생성할 때 TypeError를 발생시킨다.

다음은 추상 클래스의 가장 기본적인 형식이다.
```python
from abc import *
class AbstractClass(metaclass=ABCMeta):
    @abstractmethod
    def abstract_method(self):
        pass
```
- `abc` 모듈을 호출한다.
- `@abstractmethod`로 감싼 추상 메소드를 가진다.

```python
# abstract.py
from abc import *


class AbstractBook(metaclass=ABCMeta):
    title = 'Book Title'
    author = 'Authors'
    isbn = 'ISBN-10'

    def show(self):
        print('책 클래스의 메소드입니다.')

    # abstract method
    @abstractmethod
    def show_author(self):
        print('책의 저자는?')


class ScienceBook(AbstractBook):
    def __init__(self, title, author, isbn):
        self.title = title
        self.author = author
        self.isbn = isbn

    def show_title(self):
        print('책 제목 : ', self.title)

    # implement abstract method
    def show_author(self):
        super().show_author()
        print(self.author)
```
- `super()`를 통해 부모 클래스의 메소드를 상속받았다.


```python
>>> from abstract import *
>>> selfish_gene = ScienceBook('The Selfish Gene', 'Richard Dawkins', '0198788606')
TypeError: Can't instantiate abstract class ScienceBook with abstract method show_author
```
- 만약 ScienceBook 클래스에서 show_author를 정의하지 않으면 객체 생성 단계에서 TypeError가 일어난다.

```python
>>> from abstract import *
>>> selfish_gene = ScienceBook('The Selfish Gene', 'Richard Dawkins', '0198788606')
>>> selfish_gene.show()
책 클래스의 메소드입니다.
>>> selfish_gene.show_title()
책 제목 : The Selfish Gene
>>> selfish_gene.show_author()
책의 저자는?
Richard Dawkins
``` 
추상 클래스를 상속한 ScienceBook의 객체에서 
- 추상 클래스에 정의한 메소드
- 자식 클래스에서 정의한 메소드
- 자식 클래스에서 구현한 추상 메소드

모두가 잘 실행되는 것을 확인할 수 있다.

```python
>>> a_book = AbstractBook()
TypeError: Can't instantiate abstract class AbstractBook with abstract methods show_author
```
- 추상 클래스의 객체를 정의하려고 하면 TypeError를 일으킨다.



## 3. Visibility; 가시성

- 클래스 간의 간섭이나 정보 공유를 최소화하기 위해, 객체의 속성이나 메소드에 접근을 제한할 수 있다.
- Information hiding 또는 Encapsulation은 외부로 부터의 임의적인 접근을 제한해서 데이터의 일관성을 보장하는 객체의 특징을 일컫는다.
- 파이썬에서는 private variable을 이용하는 방법과 protected members 개념을 이용하는 방법이 있다.

### 3.1. Private Variable

- 파이썬에서는 객체 내에서만 접근할 수 있는 엄밀한 의미의 "private"한 인스턴스 변수가 없다.
- _single underscore : 변수나 함수, 메소드 앞에 underscore를 붙여 private 객체임을 표현하고, 접근을 제한하도록 장려한다. 
    - 실제로는 객체에서도 접근이 가능하다.
- __double underscores : 두개의 underscore는 클래스 간의 속성 충돌을 방지하기 위해 이름을 덧붙인(name mangling) 것이다.
    - 실제로 객체에서 접근이 불가능하다.

```python
class Citizen:
    def __init__(self, name, ctzn_num):
        # one underscore
        self._name = name
        # double underscore
        self.__citizen_number = ctzn_num

    def show_citizen_number(self):
        if len(self.__citizen_number) == 7:
            print(self.__citizen_number)
```
- 초기화에서 인스턴스 변수를 `_name`과 `__citizen_number`로 정의했다.
- 클래스 내 메소드에서 더블 스코어가 붙은 변수 `__citizen_number`를  참조해서 출력한다.

```python
>>> citizen1 = Citizen('Baek', '0000000')
>>> citizen1._name # accesible
digital
>>> citizen1.__citizen_number # not accesible
AttributeError: 'Citizen' object has no attribute '__citizen_number'
>>> citizen1.show_citizen_number()
0000000
```
- 객체에서 하나의 언더스코어가 붙은 인스턴스 변수 `_name` 에는 접근할 수 있지만, 
- 더블 언더스코어가 붙은 인스턴스 변수 `__citizen_number`에 접근시 AttributeError를 발생시킨다.
- 만약 객체를 통해 `__citizen_number`에 접근하여 값을 변경할 수 있다면, 길이의 제한 없이 변수의 값을 변경하게 되므로 원하지 않는 결과를 도출할 것이다.

#### magic method
- 특별히 정해진 변수나 메서드에 대해 underscore가 양쪽에 2개씩 붙은 것을 매직 메소드(magic method)라고하며, 메직 메소드는 각각 특별한 기능을 가지고 작용한다.
    - `__init__` 은 클래스를 인스턴스로 초기화한다.
    - `__len__` 은 길이를 정의해 `객체.len`으로 접근가능하다.
    - `__file__` 은 파이썬 파일의 위치를 반환한다.
    - `__name__` 은 현재 모듈의 이름을 저장하는 변수로, 커맨드라인을 통해 직접 파일을 실행할 경우 `__main__`이라는 네임스페이스가 설정된다.


### 3.2. Protected Members

- 위와 같은 설정대신, 인스턴스에 메소드를 통해서만 접근하도록 해서 메소드 내에서 접근을 제한할 수 있다.
    - 인스턴스 변수 값을 업데이트하는 함수를 setter,
    - 인스턴스 변수 값을 참조하는 함수를 getter라고 부른다.
- double underscore 없이는 여전히 객체에서 인스턴스 변수에 접근이 가능하다.
- 이때 객체에서 인스턴스에 접근하는 형식으로 getter와 setter를 바꾸기 위해 `@property` 와 `@fn.setter` 데코레이터를 활용한다.
- 프로퍼티 데코레이터의 활용으로 객체에서 인스턴스 변수에 직접 접근하는 것을 실제로 차단할 수 있다.

```python
class Citizen:
    def __init__(self, name, ctzn_num):
        self._name = name
        self._citizen_number = ctzn_num

    def show_citizen_number(self):
        print(self._citizen_number)

    # getter
    def get_citizen_number(self):
        return self._citizen_number

    # setter
    def set_citizen_number(self, ctzn_num):
        if len(ctzn_num) != 7:
            raise ValueError('Expected 7 numbers for citizen number!')
        self._citizen_number = ctzn_num 
```
- 우선 setter와 getter를 구현하였다.
- setter와 getter 메소드를 통해 인스턴스 변수에 접근 제한을 설정하였다.
- 실제로는 여전히 객체를 통해 인스턴스 변수에 접근이 가능하다.

```python
class Citizen:
    def __init__(self, name, ctzn_num):
        self._name = name
        self._citizen_number = ctzn_num 

    def show_citizen_number(self):
        print(self._citizen_number)

    @property
    def citizen_number(self):
        return self._citizen_number

    @citizen_number.setter
    def citizen_number(self, ctzn_num):
        if len(ctzn_num) != 7:
            raise ValueError('Expected 7 numbers for citizen number!')
        self._citizen_number = ctzn_num 
```
- getter와 setter의 이름을 접근을 제한하고자 하는 인스턴스 변수의 이름과 같게한 후, @property로 getter를 감싸고, @변수이름.setter로 setter를 감쌌다.

```python
>>> citizen_baek = Citizen('Baek', '0000000')
>>> citizen_baek.citizen_number
0000000
>>> citizen_baek.citizen_number = '3333333x'
ValueError: Expected 7 numbers for citizen number!
```
- 데코레이터를 활용함으로서 객체의 프로퍼티로 인스턴스 변수값을 가져올 수 있게 되었다.
- 객체의 프로퍼티에 값을 할당하므로서, 인스턴스 메소드를 거쳐 인스턴스 변수에 접근하게 되었다.
- 프로퍼티로 값을 가져오면 실제로는 메소드인 대상을 변수로 여기게 된다. 

즉 인스턴스에서 "직접" 인스턴스 변수를 변경할 수 없고, 프로퍼티와 setter로 정의된 메소드를 통해서만 인스턴스 변수를 변경할 수 있다.


### [참고] Duck Typing

- duck typing이란 클래스 A와 클래스 B가 같은 이름의 메서드를 가지기만 하면 두 클래스를 동일한 타입으로 간주하는 것이다. 
- duck typing은 상속과 관련이 없이 서로 다른 클래스 객체에 같은 작용을 할 수 있다.
- 파이썬은 duck typing을 지원하지만, C나 Java는 지원하지 않는다.

```python
# animals.py
class Duck:
    def cry(self):
        print('꽥꽥')

class Lion:
    def cry(self):
        print('어흥')

class Owl:
    def cry(self):
        print('부엉부엉')

class Dog:
    def bark(self):
        print('월월')

def animal_sound(animal):
    animal.cry()
```
- animal_sound는 클래스를 입력받아 메서드를 실행하는 함수이다.
- 이렇게 정의했을 때 클래스 Duck, Lion, Owl은 같은 타입으로 간주하고, 클래스 Dog는 다른 타입으로 간주하는지 확인해보자.

```python
>>> duck = Duck()
>>> animal_sound(duck) 
꽥꽥
>>> lion = Lion()
>>> animal_sound(lion) 
어흥
>>> owl = Owl()
>>> animal_sound(owl)
부엉부엉
>>> dog = Dog()
>>> animal_sound(dog)  
AttributeError: 'Dog' object has no attribute 'cry'
```
- 객체를 정의한 후 Duck, Lion, Owl 객체는 animal_sound를 통해 같은 이름의 메서드 cry를 실행한다.
- 반면 이름이 cry인 메서드가 없는 Dog 객체는 animal_sound에서 AttributeError를 반환한다.
