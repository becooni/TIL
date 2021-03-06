# Implementation

- 구현이란, 머릿속에 있는 알고리즘을 소스코드로 바꾸는 과정
- 구현 유형의 문제란, 풀이를 떠올리기는 쉽지만, 코드로 작성하기는 어려운 문제
  - 알고리즘은 간단한데 코드가 지나칠 만큼 길어지는 문제
  - 실수 연산을 다루고, 특정 소수점 자리까지 출력해야 하는 문제
  - 문자열을 특정한 기준에 따라서 끊어 처리해야 하는 문제
  - 적절한 라이브러리를 찾아서 사용해야 하는 문제
- 일반적으로 알고리즘 문제에서 2차원 공간은 **행렬(Matrix)**의 의미로 사용됨

## (문제) 상하좌우
여행가 A는 N x N 크기의 안에 1 x 1 크기 정사각형 공간에 있음. 가장 왼쪽 위 좌표는 (1, 1)이며 가장 오른쪽 아래 좌표는 (N, N)에 해당. L, R, U, D 가 반복적으로 적혀있는 계획서를 보고 최종목적지 좌표를 구하라
- L 왼쪽으로 한칸이동
- R 오른쪽으로 한칸이동
- U 위로 한칸이동
- D 아래로 한칸이동 

### 해설

- 요구사항 대로 충실히 구현하면 됨
- 명령에 따라 개체를 차례대로 이동시킨다는 점에서 시뮬레이션 유형으로도 분류됨

> 해설 답과 나의 답이 달라서 두가지 모두 기술할꺼임

```python
n = int(input())
plans = input().split()

x, y = 1, 1

# L, R, U, D에 따른 이동 방향
dx = [0, 0, -1, 1]
dy = [-1, 1, 0, 0]
move_types = ['L', 'R', 'U', 'D']

for plan in plans:
    for i in range(len(move_types)):
        if plan == move_types[i]:
            nx = x + dx[i]
            ny = y + dy[i]
    
    if 1 > nx or nx > n or 1 > ny or ny > n:
        continue

    x, y = nx, ny

print(x, y)
```

> 나의 풀이 dictionary를 활용
```python
n = int(input())
plans = input().split()

x, y = 1, 1

# L, R, U, D에 따른 이동 방향 dic
move_dic = {'L':(0, -1), 'R':(0, 1), 'U':(-1, 0), 'D':(1, 0)}

for plan in plans:
    nx = x + move_dic[plan][0]
    ny = y + move_dic[plan][1]
    
    if 1 > nx or nx > n or 1 > ny or ny > n:
        continue

    x, y = nx, ny

print(x, y)
```

## (문제) 시각

정수 N이 입력되면 00시 00분 00초부터 N시 59분 59초까지의 모든 시각 중에서 3이 포함 되어 있는 모든 경우의 수를 구하라

### 해설

- 완전 탐색 (Brute Force)
- 가능한 모든 시각의 경우의 수를 세도 됨
  - 하루는 86400초 이므로 부담이 없음

```python
n = int(input())

result = 0

for i in range(n + 1):
    for j in range(60):
        for k in range(60):
            if '3' in str(i) + str(j) + str(k):
                result +=1
                
print(result)
```

## (문제) 왕실의 나이트

체스판과 같은 8 x 8 공간이 있는데 나이트는 실제 체스처럼 L자 형태로만 이동할 수 있음. 체스판은 가장 왼쪽 위 좌표가 (a, 1) 가장 오른쪽 아래 좌표가 (h, 8)임.
특정 좌표를 입력 받았을때 나이트가 이동 가능한 경우의 수를 구하라

나이트 이동
- 수평으로 2칸, 수직으로 1칸
- 수직으로 2칸, 수평으로 1칸

### 해설 

- 요구사항대로 충실히 구현하면됨
- 나이트가 이동가능한 경로는 8가지에 대한 방향 벡터를 정의하고 모두 탐색해보면됨

```python
c = input()

row = int(c[1])
# ascii 값으로 변환한뒤 a ascii 값을 빼서 숫자로 
column = int(ord(c[0])) - int(ord('a')) + 1

steps = [(2, 1), (2, -1), (-2, 1), (-2, -1), (1, 2), (1, -2), (-1, 2), (-1, -2)]

result = 0

for step in steps:
    next_row = row + step[0]
    next_column = column + step[1]
    
    if 1 <= next_row <= 8 and 1<= next_column <= 8:
        result += 1

print(result)
```