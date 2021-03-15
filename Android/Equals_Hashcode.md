# Equals, Hashcode

## Java Equals

- `==`와 같음
- Primitive type은 변수에 담긴 '값'을 비교
- Reference type은 변수가 참조하고 있는 '참조' 주소를 비교 

## Kotlin

### Reference type

- `==` 값이 같은지 비교
- `===` 객체가 같은 객체(같은 주소를 참조하는지)인지 비교
- 두 연산은 기본적으로 차이가 없으나 equals를 Override 구현 했다면 결과가 달라질 수 있음

### Primitive type

- `==`, `===` 모두 값에 대한 비교
- `Nullable` 일 경우 Int는 Integer로 AutoBoxing 되므로 비교에 주의를 요함

