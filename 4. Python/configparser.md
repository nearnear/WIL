
# configparser

### 1. Config 파일 쓰기

가장 먼저 할 일은 `ConfigParser` 객체를 생성하는 것이다.
```python
from configparser import ConfigParser

config = ConfigParser()
```

다음과 같이 `section`과 `option`을 설정할 수 있다.
- config['option_name'] = { 'option_name': value }

```python
# section for settings
config['settings'] = {
    # config options of settings section.
    'debug': 'true',
    'secret_key': 'abc123',
    'log_path': '/my_app/log'
}

# section for database
config['db'] = {
    'db_name': 'myapp_dev',
    'db_host': 'localhost',
    'db_port': '8889'
}

# section for files
config['files'] = {
    'use_cdn': 'false',
    'images_path': '/my_app/images'
}
```

configuration 객체에 내용 작성을 끝내면 파일 객체를 생성해 파일 형태로 저장한다.
```python
# write a config file 
with open('./dev.ini', 'w') as f:
      config.write(f)
```

### 2. Config 파일 읽기

마찬가지로 객체를 생성한 후, 저장한 Config 파일을 읽어들인다.
- parser.read('file_name')

```python
from configparser import ConfigParser

parser = ConfigParser()
parser.read('dev.ini')
```

필요한 섹션과 파일을 다음과 같이 읽을 수 있다.
- parser.sections()
- parser.options('section_name')
- parser.get('section_name', 'option_name')

```python
print(parser.sections())  # ['settings', 'db', 'files']
print(parser.options('settings'))  # ['debug', 'secret_key', 'log_path']
print(parser.get('settings', 'secret_key'))  # abc123
print('db' in parser)  # True
```

특정 자료값으로 바로 읽어들일 수 있는 함수가 있다.
- parser.getint()
- parser.getfloat()
- parser.getboolean()

이 함수들의 인자로 `fallback`을 설정할 수 있는데, 읽어들이려는 값이 parser 내에 존재하지 않는 경우 default 값으로 반환하는 값이다. 이름처럼 Python `dict`에서 `get(key, (default_value))` 함수를 쓰는 것과 비슷하다. (거의 같다.)

```python
print(parser.get('db', 'db_port'), type(parser.get('db', 'db_port')))  # 8889 <class 'str'>
print(int(parser.get('db', 'db_port')))  # 8889 (as int)
print(parser.getint('db', 'db_default_port')) # 8889 (as int)
print(parser.getint('db', 'db_default_port', fallback=3306))  # 3306
```

## 3. String Interpolation 사용하기

예를 들어서 config를 설정하는 도중에 다른 section의 option의 값을 불러와서 문자열 안에서 활용하고 싶다면 `ConfigParser()`가 문자열을 해석하도록 인자를 전달해야 한다. 예를 들어, 다음과 같이 `settings` section에 있는 option값을 `files` section에서 이용하고자 한다. 

- '${section_name : option_name}other_strings'

```python
from configparser import ConfigParser

config = ConfigParser()

config['settings'] = {
    'debug': 'true',
    'secret_key': 'abc123',
    'log_path': '/my_app/log',
    'python_version': '3',
    'packages_path': '/usr/local'
}

# ...

config['files'] = {
    'use_cdn': 'false',
    'images_path': '/my_app/images',
    'python_path': '${settings:packages_path}/bin/python${settings:python_version}'
}
```

그러면 Config 파일을 읽을 때 문자열을 해석할 수 있도록 `ConfigParser`에 인자 `ExtendedInterpolation()`을 전달한다. (method call이 되어야 한다.)

```python
from configparser import ConfigParser, ExtendedInterpolation

parser = ConfigParser(interpolation=ExtendedInterpolation())
parser.read('dev.ini')

print(parser.get('files', 'python_path'))  # /usr/local/bin/python3
```

> [More about Interpolation on Python docs](https://docs.python.org/ko/3/library/configparser.html#interpolation-of-values)


## 참고 자료
1. KishStats blog, https://kishstats.com/python/2018/03/07/python-config-parser.html
2. Python docs, https://docs.python.org/ko/3/library/configparser.html#interpolation-of-values