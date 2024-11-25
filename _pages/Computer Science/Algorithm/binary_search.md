---
title: 이진 탐색
tags:
  - Algorithm
  - Binary-Search
date: 2020-11-18
thumbnail: ../../../assets/img/post_img/algorithm_img/this_is_coding_test_cover.png
---

## 순차 탐색

 일반적으로 자주 사용되는 탐색으로, **앞에서부터 데이터를 하나씩 차례대로 확인하며** 리스트 안에 있는 특정 데이터를 찾는 방법이다. 보통 **정렬되지 않은 리스트**에서 데이터를 찾을 때 사용한다. 충분한 시간이 있다면 데이터가 아무리 많아도 항상 원하는 데이터를 찾을 수 있는 것이 장점이다. 시간 복잡도는 최악의 경우 **O(N)**을 보장한다.

```python
# 순차 탐색 함수 구현
def sequential_search(target, array):
    for i in range(len(array)):
        if array[i] == target:
            return i + 1  # 현재 위치 반환 (인덱스이므로 1을 더함)


array = [4, 5, 1, 3, 2]
target = 3

print(sequential_search(target, array))
```

```
4
```

## 이진 탐색

 이진 탐색은 탐색 범위를 절반씩 좁혀가며 데이터를 탐색하는 방법이다. 순차 탐색과는 다르게 **배열 내부의 데이터가 정렬된 상태**여야만 사용 가능하다. 이진 탐색에는 탐색하고자하는 범위의 **시작점**, **끝점** 그리고 **중간점**을 위치를 나타내는 변수로서 사용한다. **찾으려는 데이터와 중간점 위치에 있는 데이터를 반복적으로 비교해서 원하는 데이터를 찾는 것**이 이진 탐색 과정이다. 한 번 확인할 때마다 확인하는 원소의 개수가 대략 절반씩 줄어든다는 점에서 시간 복잡도가 **O(logN)**이다.

 1. 재귀함수를 이용한 이진 탐색

```python
n = 10
target = 7
array = [1, 3, 5, 7, 9, 11, 13, 15, 17, 19]


def binary_search(array, target, start, end):
    if start > end:
        return None
    mid = (start + end) // 2
    if array[mid] == target:
        return mid
    elif array[mid] > target:
        return binary_search(array, target, start, mid - 1)
    else:
        return binary_search(array, target, mid + 1, end)


result = binary_search(array, target, 0, n - 1)
if result == None:
    print("원소가 존재하지 않습니다.")
else:
    print(result + 1)
```

```
4
```

 2. 반복문을 이용한 이진 탐색

```python
n = 10
target = 7
array = [1, 3, 5, 7, 9, 11, 13, 15, 17, 19]


def binary_search(array, target, start, end):
    while start <= end:
        mid = (start + end) // 2
        # 찾은 경우 중간점 인덱스 반환
        if array[mid] == target:
            return mid
        # 중간점의 값보다 찾고자 하는 값이 작은 경우 왼쪽 확인
        elif array[mid] > target:
            end = mid - 1
        # 중간점의 값보다 찾고자 하는 값이 큰 경우 오른쪽 확인
        else:
            start = mid + 1
    return None


result = binary_search(array, target, 0, n - 1)
if result == None:
    print("원소가 존재하지 않습니다.")
else:
    print(result + 1)
```

```
4
```

## 파이썬 이진 탐색 라이브러리 bisect

 - bisect_left(array, x): 정렬된 순서를 유지하면서 배열 array에 x를 삽입할 가장 왼쪽 인덱스를 반환
 - bisect_right(array, x): 정렬된 순서를 유지하면서 배열 array에 x를 삽입할 가장 오른쪽 인덱스를 반환

```python
from bisect import bisect_left, bisect_right

a = [1, 2, 4, 4, 8]
x = 4

print(bisect_left(a, x))  # 정렬된 순서를 유지하면서 배열 a에 x를 삽입할 가장 왼쪽 인덱스를 반환
print(bisect_right(a, x))  # 정렬된 순서를 유지하면서 배열 a에 x를 삽입할 가장 오른쪽 인덱스를 반환
```

```
2
4
```

 - 값이 특정 범위에 속하는 데이터 개수 구하기

```python
from bisect import bisect_left, bisect_right


# 값이 [left_value, right_value]인 데이터의 개수를 반환하는 함수
def count_by_range(a, left_value, right_value):
    right_index = bisect_right(a, right_value)
    left_index = bisect_left(a, left_value)
    return right_index - left_index


a = [1, 2, 3, 3, 3, 3, 4, 4, 8, 9]

# 값이 4인 데이터 개수 출력
print(count_by_range(a, 4, 4))

# 값이 [-1, 3] 범위에 있는 데이터 개수 출력
print(count_by_range(a, -1, 3))
```

```
2
6
```

***

_본 포스팅은 '안경잡이 개발자' 나동빈 님의 저서_
_'이것이 코딩테스트다'와 그 유튜브 강의를 공부하고 정리한 내용을 담고 있습니다._