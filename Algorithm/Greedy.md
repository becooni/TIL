# Greedy

- 현재 상황에서 지금 당장 좋은 것만 고르는 방법
- 정당성 분석이 중요함
- 거스름돈 문제가 자주 등장

## (문제) 거스름돈

> 최적의 해를 보장해주는 정당성 이유는?
> 가지고 있는 동전중에 큰 단위가 항상 작은 단위의 배수이기 때문에 작은 단위의 동전들을 종합해도 다른 해가 나올 수 없음

### 해설

- 가장 큰 화폐 단위부터 거슬러 주면 됨

```python
거스름돈 N을 입력받아 화폐로 거슬러 줄수 있는 동전의 개수 세기

N = 1260
count = 0

array = [500, 100, 50, 10]

for coin in array:
    count += n // coin
    n %= coin

print(count)
```

## (문제) 1이 될때까지

N, K를 입력받아 아래의 두 과정을 중 하나를 거쳐 N이 1이 될때까지 반복한 횟수를 구하라
- N - 1
- N / K

### 해설

- 최대한 나누기를 하여 횟수를 많이 줄여 최적의 해를 구할 수 있음

```python
N, K = map(int, input().split())

result = 0

while True:
N, K = map(int, input().split())

result = 0

while True:
    # N을 K로 나눌수 있는 가장 인접한 값을 설정
    target = N // K * K 
    # -1 을 수행한 횟수를 저장
    result += N - target
    # -1 을 result번 수행한 값을 저장
    N = target

    if N < K:
        break

    N //= K
    result += 1

result += N - 1
print(result)
```

## (문제) 곱하기 혹은 더하기

각 자리가 숫자(0부터 9)로만 이루어진 문자열 S가 주어졌을때, 왼쪽부터 오른쪽으로 모든 숫자를 확인하며 숫자 사이에 'x' 혹은 '+' 연산자를 넣어 결과적으로 만들어질 수 있는 가장 큰 수를 구하는 알고리즘

단, +보다 x를 먼저 계산하는 일반적인 방식과 달리 왼쪽부터 차례대로 연산함

### 해설

- 대부분 + 보다 x가 더 값을 크게 만들지만, 두 수 중에서 하나라도 0 혹은 1면 곱하기보다 더하는게 낫다

```python
S = input()

result = int(S[0])

for i in range(1, len(S)):
    num = int(S[i])
    if num <= 1 or result <= 1:
        result += num
    else:
        result *= num

print(result)
```

## (문제) 모험가 길드

한 마을에 모험가 N명이 있고 N명의 모험가는 각각 '공포도'를 갖고 있음. 모험가 그룹을 안전하게 구성하고자 '공포도가 X인 모험가는 반드시 X명 이상으로 구성한 모험가 그룹에 참여'가능한 제약이 있을때 만들수 있는 그룹의 최댓값을 구하라

- 몇명의 모험가는 마을에 남아도 되기 때문에 모든 모험가를 특정 다 넣을 필요는 없음

### 해설

- 오름차순 정렬 후 공포도가 가장 낮은 모험가부터 하나씩 확인
- '현재 그룹에 포함된 모험가의 수'가 '현재 확인 중인 공포도' 보다 크거나 같다면 이를 그룹으로 설정함 
- 이러한 방법으로 공포도가 오름차순으로 정렬되어 항상 최소한의 모험가의 수만 포함하여 그룹을 만듬

```python
N = int(input())
array = list(map(int, input().split()))
array.sort()

result = 0

group = 0

for i in array:
    group += 1
    if group >= i:
        result += 1
        group = 0
    
print(result)
```

> 출처 - https://youtu.be/2zjoKjt97vQ
