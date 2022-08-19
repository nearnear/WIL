# Binary search


## 이진 탐색 방법

- 이진 탐색은 정렬된 배열에 대해 탐색 범위를 매번 2배로 줄이며 탐색하는 방법이다.
- 따라서 수행시간은 배열 길이 $N$ 에 대해 $O(logN)$ 이다.

아래는 정렬된 배열 `nums`에서 `target`의 값을 이진 탐색으로 찾아서 인덱스를 반환하는 방법들을 묘사하고 있다. 또한 `target`이 배열에 존재하는 경우, 단 하나만 존재한다고 가정한다. `target`을 찾을 수 없는 경우, `-1`을 반환한다.


### 1. 재귀 이진 탐색

```python
def search(nums: list[int], target: int) -> int:
    # 재귀 함수를 내부 함수로 정의한다.
    def recursive_bs(left, right):
        # left와 right의 차가 0이상일 때 까지 탐색한다.
        if left <= right:
            mid = left + (right - left) // 2
            if nums[mid] < target:
                return recursive_bs(mid+1, right)
            elif nums[mid] > target:
                return recursive_bs(left, mid-1)
            else:
                return mid
        # 탐색이 끝날 때까지 재귀가 종료되지 않은 경우
        else:
            return -1

    # 전체 리스트 범위를 탐색한다.
    return recursive_bs(0, len(nums-1))
```

- recursive_bs는 search 함수 내에 정의되어 있으므로 search 함수의 인자인 `target`에 접근할 수 있다.
- 이진 탐색에서 `mid`를 `(right + left) // 2`로 계산할 경우 overflow가 발생에 유의한다.
- 파이썬 재귀 호출 횟수 제한은 1,000번이다. 이진 탐색은 아주 빠른 알고리즘으로 재귀 횟수 제한을 마주칠 일은 없지만, 에러 분석시 참고하자. 재귀 호출 횟수 제한은 `$ sys.getrecursionlimit()`으로 확인할 수 있다.


### 2. 반복 이진 탐색

```python
def iterative_bs(nums: list[int], target: int) -> int:

    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] < target:
            left = mid + 1
        elif nums[mid] > target:
            right = mid - 1
        else:
            return mid

    return -1
```

- 재귀적인 방법과 `left`, `right`, `mid`를 정의하는 방식은 같다. 다만 탐색 조건을 while문으로 명시해서 코드가 깔끔해졌다.
- 수행시간은 반복 탐색이 재귀 보다 빠르다.


### 3. 파이썬 이진 탐색 모듈

```python
import bisect

def module_bs(nums, target):
    # 배열이 정렬된 상태로 target을 삽입할 수 있는 가장 왼쪽 인덱스를 찾는다.
    index = bisect.bisect_left(nums, target)

    # 찾은 인덱스의 배열 값이 target과 일치하는지 확인한다.
    if index < len(nums) and nums[index] == target: 
        return index
    else:
        return -1
```

> - bisect.bisect_left(a, x, lo=0, hi=len(a), *, key=None)
> - bisect.bisect_right(a, x, lo=0, hi=len(a), *, key=None)

- 파이썬의 이진 탐색 모듈 bisect를 활용한다. `bisect_left`는 `nums`에 `target`을 정렬된 상태로 삽입할 수 있는 가장 왼쪽 인덱스를 찾는다. `bisect_right`는 가장 오른쪽 인덱스를 찾는다. 
- 따라서 목표값이 하나만 존재하는 경우, `bisect_right(a,x)`는 `bisect_left(a,x)`에 비해 1만큼 더 큰 값을 반환한다.
- 목표값이 배열에 존재하지 않는 경우, `bisect_left()`로 반환된 인덱스의 배열 값은 `target`과 일치하지 않는다. 따라서 반드시 확인이 필요하다.
- 두 함수는 모두 탐색할 범위의 인덱스를 lo와 hi로 전달할 수 있다. 또한 key인자로 정렬할 함수를 전달할 수 있다.

### 4. 파이썬 인덱스 메서드

```python
def search(nums: list[int], target:int) -> int:
    try:
        return nums.index(target)
    except ValueError:
        return -1
```

- 파이썬의 인덱스 메서드 `.index()`를 활용한다. 
- 주의할 점은 `.index()`는 배열에서 목표값을 찾을 수 없는 경우 ValueError를 일으킨다는 점이다. 따라서 예외처리가 필요하다.
- 시간 복잡도는 이진 탐색 모듈 및 반복 탐색과 비슷했다.




