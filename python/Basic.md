# Basic

출처 - https://youtu.be/m-9pAwq1o3w

# Grammar

## 자료형

### 정수형

- 양의 정수
- 음의 정수
- 0

### 실수형

- 양의 실수
- 음의 실수
- 소수부 0 생략
> 5.
- 정수부 0 생략 
> -.7

- 컴퓨터는 0.9를 정확히 표현 할 수 없으므로 round() 활용할것
> round(0.3 + 0.6)

### 지수 표현 방식

- e 혹은 E를 이용해 지수 표현
> 1e9 -> 1,000,000,000

- 752.5 
> 75.25e1

- 3.954
> 3954e-3

### 연산자

나누기 | 나머지 | 몫 | 거듭제곱 | 제곱근
--- | --- | --- | --- | ---
a / b | a & b | a // b | a ** b | a ** 0.5

### 리스트

- 자바의 ArrayList랑 비슷함
- append, remove, insert, sort, reverse, count 등 가능

```python
# 초기화
a = [1, 2, 3]

a = list()

a = [0] * 5
> [0, 0, 0, 0, 0]

# 접근
a[7]

# 뒤에서 첫번째
a[-1]

# 값 변경
a[3] = 7

# 슬라이스 
a[1 : 4] # (두번째부터 네번째)
```

### 리스트 컴프리헨션

- 대괄호 안에 조건문과 반복문으로 리스트 초기화

```python
array = [i for i in range(10)]
> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# 홀수만 포함
array = [i for i in range(10) if i % 2 == 1]
> [1, 3, 5, 7, 9]

# 1~9 수들의 제곱값
array = [i * i for in i in range(1, 5)]
> [1, 4, 9, 16, 25]

# 2차원 리스트 초기화
array = [[0] * m for _ in range(n)] # O
array = [[0] * m] * n # X -> 리스트 안에 각 리스트가 모두 같은 객체로 인식됨

# 변수 값 blank
for _ in range(5):
    print("hello world")

# remove로 모든 원소 제거하기 (집합 자료형)
a = [1, 2, 3, 4, 5, 5, 5]
remove_set = {3, 5}

result = [i for i in a if i not in remove_set]
>  [1, 2, 4]
```

### 문자열

- ", ' 둘다 사용가능
- escape 문자(\)로 따옴표 작성가능
- 문자열은 immutable임
- 슬라이싱 가능

```python
a = "Hello"
b = "World"
print(a + " " + b)
> Hello world

a = "String"
print(a * 3)
> StringStringString

a = "ABCDEF"
print(a[2:4])
> CD
```

### 튜플

- 리스트랑 비슷, 하지만 immutable함
- 리스트는 [], 튜플은 ()
- 리스트에 비해 공간 효율적
- 서로 다른 성질의 데이터를 묶어서 관리할때 활용
- 데이터의 나열을 해싱의 키값으로 사용할때
- 리스트보다 메모리를 효율적으로 사용해야 할때

```python
a = (1, 2, 3, 4, 5)

a[3]
> 4

a[1 : 4]
> (2, 3, 4)
```

### 딕셔너리

- key, value 
- 리스트나 튜플처럼 순차적으로 데이터를 저장하지 않음
- immutable 자료형을 키로 사용
- Hash Table을 사용하므로 데이터 접근이 O(1)시간에 처리 가능

```python
data = dict()
data["사과"] = "apple" 

print(data)

if "사과" in data:
    print("사과 키 존재함")

data.keys()
data.values()

for key in data.keys():
    print(data[key])

key_list = list(data.keys())

```

### 집합 (세트)

- 중복을 허용하지 않음
- 순서가 없음
- 리스트 혹은 문자열로 초기화 가능 - set() 이용
- 초기화 ({})
- 데이터 접근 O(1)

```python
data = set([1, 1, 2, 3, 4, 4])
# 혹은
data = {1, 1, 2, 3, 4, 4}
> {1, 2, 3, 4} # 중복 제거

# 원소 추가
data.add(4)

# 복수 원소 추가
data.update([5, 6])

# 삭제
data.remove3


a = set([1, 2, 3, 4, 5])
b = {3, 4, 5, 6, 7}

# 합집합
a | b
> {1, 2, 3, 4, 5, 6, 7} 

# 교집합
a & b
> {3, 4, 5}

# 차집합
a - b
> {1, 2}
```

## 기본 입출력

### 입력

- input() 한줄의 문자열을 입력받는 함수
- map() 리스트의 모든 원소에 각각 특정한 함수를 적용하는 함수

```python
# 공백으로 구분된 정수 배열 입력
list(map(int, input().split())

# Kotlin의 그것과 비슷 (Destructuring Declarations)
# 언패킹    #패킹
a, b, c = map(int, input().split())

n = int(input())

# 빠르게 입력하려면
sys.stdin.readline() + rstrip() # enter 줄바꿈 함께 사용
```

### 출력

- print()
- 콤마(,)를 사용하여 띄어쓰기로 구분하여 출력가능
- 기본적으로 줄바꿈

```python
# 줄바꿈 안하려면 end 매개변수
print(7, end=" ")

a = 7
print("행운을 가져다 주는 숫자는 " + str(a) + " 이다")

# Python 3.6 부터 사용하는 f-string
print(f"행운을 가져다 주는 숫자는 {a} 이다")
```

## 조건문과 반복문

- 코드의 블록을 들여쓰기로 지정함
```python
if 50 > 10:
    print("블록")
print("여기는 블록 아님")
```

### 조건문

- 여타 다른 언어와 비슷
- if ~ elif ~ else
- 논리연산자는 and, or, not 으로 표현 (&&, || 아님)

```python
a = 15
if a <= 20 and a > = 0:
    print("True")
if 20 < a > 0
    print("True")

# 조건부 표현식 (Conditional Expression)
score = 85

# 이걸
if score >= 80: result = "Success
else: result = "Fail"
# 한줄로 (참의 값이 먼저옴)
result = "Success" if score >= 80 else "Fail"
```

### 기타 연산자

- `in`, `not in`
- 리스트, 튜플, 스트링, 딕셔너리 모두에서 사용가능
- 암것도 처리 안하고 싶을때 `pass`

```python
if score >= 80:
    pass # 나중에 처리
```

### 반복문

- while, for 있음
- 주로 for가 많이 쓰임
- range(시작 값, 끝 값 + 1) 많이 쓰임, 인자를 안넣으면 자동으로 0 시작
- continue, break 다른 언어와 동일

```python
scores = [90, 85, 77, 65, 97]
cheating_students_list = {2, 4}

for i in range(5):
    if i + 1 in cheating_students_list:
        continue
    if scores[i] >= 80:
        print(i + 1, "번 학생은 합격입니다.")
> 1 번 학생은 합격입니다.
> 5 번 학생은 합격입니다.
```

## 함수와 람다 표현식

### 함수

- 다른 언어와 비슷

```python
def name(arguments):
    # blah blah
    return value

def add(a, b):
    return a + b

add(3, 7)
add(b = 3, a = 7)
```

### global 키워드

- 함수 내에서는 글로벌 변수 Read Only 임
- Write 하려면 global 키워드 선언을 해줘야함
- 함수 리턴 값이 여러개 가능함 (패킹, 언패킹)

```python
a = 0

def func():
    # global 선언
    global a
    a += 1

array = [1, 2, 3]

def func():
    array.append(5) # 이건 가능

def unpacking():
    return a, b, c

a, b, c = unpacking()
```

### 람다 표현식

- 다른 언어 람다와 비슷

```python
def add(a, b):
    print(a + b)

print((lambda a, b: a + b))(3, 7))

# 활용 1
array = [("A", 50), ("B", 32), ("C", 74)]

def my_key(x):
    return x[1]

print(sorted(array, key=my_key))
print(sorted(array, key=lambda x: x[1]))

# 활용 2
list1 = [1, 2, 3, 4, 5]
list2 = [6, 7, 8, 9, 10]

result = map(lambda a, b: a + b, list1, list2)
> [7, 9, 11, 13, 15]
```

## 실전에서 유용한 표준 라이브러리

- 내장함수 - sum(), min(), max(), eval(), sorted([], reverse=True),
- itertools - 순열과 조합 라이브러리 굿
- heapq - 우선순위 큐 기능 구현 
- bisect - 이진 탐색 기능
- collections - 덱, 카운터 등의 유용한 자료구조 포함
- math - 팩토리얼, 제곱근, 최대공약수, 삼각함수, 파이 

### 순열과 조합

- 순열 {A, B, C} 
> 3개 나열 -> ABC, ACB, BAC, BCA, CAB, CBA
- 조합 {A, B, C}
> 두개 뽑 -> AB, AC, BC

```python
data = ["A", "B", "C"]

# 모든 순열 구하기
from iterttools import permutations
result = list(permutations(data, 3)) 

# 2개를 뽑는 모든 조합 구하기
from iterttools import combinations
result = list(combinations(data, 2))

# 2개를 뽑는 모든 순열 구하기 (중복 허용)
from iterttools import product
result = list(product(data, repeat=2))

# 2개를 뽑는 모든 조합 구하기 (중복 허용)
from iterttools import combinations_with_replacement
result = list(combinations_with_replacement(data, 2))
```

### Counter

- 등장 횟수를 세는 기능 like WordCloud
- iterable 객체가 주어졌을때 내부 원소가 몇번씩 등장하는지 알려줌

```python
from collections import Counter
counter = Counter(["blue", "blue", "green", "red", "green"])

counter["blue"]
> 2
dict(counter)
> {"blue": 2, "green": 2, "red": 1}
```

### 최대 공약수와 최소 공배수

- math 라이브러리 gcd() 함수 이용

```python
import math

# 최소 공배수(LCM)를 구하는 함수
def lcm(a, b):
    return a * b // math.gcd(a, b)

a = 21
b = 14

# 최대 공약수(GCD)
math.gcd(a, b)
> 7
# 최소 공배수(LCM)
lcm(a, b)
> 42
```