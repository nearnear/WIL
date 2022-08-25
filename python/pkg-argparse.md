# argparse

argparse는 명령 프롬프트에서 파이썬 스크립트를 실행할 시에 실행 옵션을 파싱하는 모듈이다.

## 머신러닝 활용법

모델의 하이퍼파라미터를 명령 프롬프트에서 설정하여 실행할 수 있다.

#### main.py
```python
import argparse

# 실행 옵션을 입력받을 인스턴스 생성
parser = argparse.ArgumentParser(description='XGBoost Implementation')

# 실행 옵션 값을 설정
parser.add_argument('--input_dir', type = str, default =None, help='input_dir')
parser.add_argument('--output_dir', default=None, help='output_dir')
parser.add_argument('--max_depth', type=int, default=4, help='max depth'
                                                             ' of leaf nodes')
parser.add_argument('--learning_rate', type=float, default=0.001,
                    help='learning rate')
parser.add_argument('--booster', type=str, default=0.001, help='booster type')
parser.add_argument('--min_child_weight', type=float, default=1,
                    help='min child weight')
parser.add_argument('--eval_metric', type=str, default='auc',
                    help='evaluation metric')
parser.add_argument('--is_training', action='store_true', help='is_training' )

# 인스턴스를 파싱해 옵션 목록 저장
args = parser.parse_args()

# 입력받은(혹은 디폴트) 인자값을 출력
print('is_training:',args.is_training)
print('input_dir:',args.input_dir)
print('learning_rate:',args.learning_rate)
print('eval_metric:',args.eval_metric)
print('max_depth:',args.max_depth)
print('min_child_weight:',args.min_child_weight)
```
다음 과정을 통해 모듈을 활용할 수 있다.
1. ArgumentParser 인스턴스를 생성한다.
2. add_argument() 메서드로 인자값을 추가한다.
3. parse_args() 메서드로 인자를 파싱한다.
4. 인자값을 출력한다.



```shell
$ python main.py \
--is_training \
--booster='gbtree' \
--learning_rate=0.000001
```
다음을 입력해 `main.py`파일을 특정한 하이퍼파라미터로 실행할 수 있다.

- `action='store_true'`로 인해 `is_training`를 입력해 `True`값을 반환한다.
- `booster`은 문자열 값으로 입력받는다.
- `learning_rate`는 실수 값으로 입력받는다.


뒤쪽에 -h 또는 --help 를 붙이면 가능한 옵션 값을 보여준다.
```shell
$ python main.py -h 
usage: main.py [-h] [--input_dir INPUT_DIR] [--output_dir OUTPUT_DIR]
               [--max_depth MAX_DEPTH] [--learning_rate LEARNING_RATE]
               [--booster BOOSTER] [--min_child_weight MIN_CHILD_WEIGHT]
               [--eval_metric EVAL_METRIC] [--is_training]
```



## 참고 자료
1. https://supermemi.tistory.com/69
2. https://donghwa-kim.github.io/argparser.html