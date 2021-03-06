# Basic

https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Java

## JVM, GC

- [JVM](https://github.com/becooni/TIL/blob/main/java/JVM.md)
- [GC](https://github.com/becooni/TIL/blob/main/java/GC.md)

# Collection

`Stack`, `Queue`, `List`, `Map`, `Set`

## `List`

- `List` 인터페이스를 직접 @Override 할수 있음
- 대표적이 구현체 -> 기존의 `Vector`를 개선한 `ArrayList`
- 이외 `LinkedList` 등의 구현체가 있음

## `Map`

- 대표적인 구현체 -> `HashMap`
- `HashTable`과 차이가 있음 (멀티스레드 환경에서)
- key-value의 구조
- DataStructure에서의 HashTable과 일치
- key는 중복된 value를 저장하지 않음
- 순서 보장 X
- key의순서 보장 -> `LinkedHashMap`

## `Set`

- 대표적인 구현체 -> `HashSet`
- 중복된 value를 저장하지 않음
- `Map`의 key-value 구조에서 value가 key를 대체
- 순서 보장 X
- 순서를 보장 -> `LinkedHashSet`

## `Stack` 과 `Queue`
> Data Structure 부분에서 자세한 설명

- `Stack` 객체 생성은 -> `new`
- `Queue`의 구현체 -> `LinkedList`

# Annotation

- 본래 주석이란 뜻으로 인터페이스를 기반으로 한 문법
- 주석처럼 코드에 달아 클래스에 특별한 의미, 기능을 주입
- 해석되는 시점 정의 가능 (Retention Policy)
- 3가지 종류
  - JDK 내장 `built-in annnitation`
  > @Override, @Deprecated, @SuppressWarning 등
  - 어노테이션의 대한 정보를 나타내기 위한 `Meta annotation`
  > @Target, @Retention, @Documented 등
  - 개발자가 직접 만드는 `Custom annotation`
  > 클래스명 앞에 @interace를 붙여 만듬

# Generic

- 여러 타입의 객체를 다루는 메서드나 컬렉션에서 사용
- 컴파일 과정에서 타입 체크를 함 
  - 안전성이 높음
  - 형변환의 번거로움을 줄여줌
  - 코드도 간결해짐
- `Collection`에 특정 객체만 추가될 수 있도록 하거나 특정 `Class`의 특징을 갖고 있는 경우에만 추가될 수 있도록 하는것
- `Collection` 내부에서 들어온 값이 올바른 Type인지 체크할 로직이 없어도됨
- Api 설계에서 명확한 의사전달 가능

# `final` keyword

- final class -> 상속 불가
- final method -> 오버라이딩 불가
- final variable -> 상수값이 되어 immutable 변수

# Overriding vs Overloading

- 둘 다 다형성을 위한 개념
- 둘 다 같은 이름의 다른 함수를 호출

## Overriding

- 상위 클래스 혹은 인터페이스에 존재하는 메소드를 하위 클래스에서 메서드 내용을 재정의하는 것
- 자바는 Ovveride시 동적 바인딩

>  SubClass의 fun()이 실행
```java
SuperClass cls = new SubClass();
cls.fun();
```

## Overloading

- 매개변수만 다른 메소드를 만드는 것
- 다른 메서드 시그니쳐를 만드는 것이므로 다른 함수를 만드는 것과 같음
- 시그니쳐가 다르므로 자바의 경우 정적 바인딩

> void fun(SuperClass cls)이 실행됨
```java
SuperClass cls = new SubClass();
cls.fun();

void fun(SuperClass cls) {}

void fun(SubClass cls) {}
```

# Access Modifier

변수 또는 메소드의 접근 범위를 설정해주기 위해 사용하는 Java의 예약어

- public
> 어떤 클래스에서도 접근 가능
- protected
> 클래스가 정의 되어 있는 해당 패키지 내, 해당 클래스를 상속 받은 외부 패키지의 클래스 접근 가능
- (default)
> 클래스가 정의되어 있는 해당 패키지 내에서만 접근이 가능
- private
> 정의된 해당 클래스에서만 접근이 가능

# Wrapper class

- 원시 타입에 대한 클래스
- `Integer`, `Float`, `Boolean` 등
- Generic을 사용하기 위해
- `null`을 반환하기 위해 return type을 Wrapper class로 지정
- 원시 타입은 `==`로 비교하는 것에 반해 `intValue()`등의 메서드로 값을 가져와 비교

## AutoBoxing

`AutoBoxing`과 `AutoUnBoxing`은 Wrapper class에 상응하는 Primitive data type (원시타입)일 경우에만 가능

 primitives type | wrapper class
 --- | ---
byte | Byte
short | Short
int | Integer
long | Long
float | Float
double | Double
char | Charater
boolean | Boolean
void | Void

> void의 Wrapper class Void는 실체화 될 수 없으며 단지 공 참조 개념을 나타냄

```java
List<Integer> list = new ArrayList<>();

// AutoBoxing
list.add(1)

// AutoUnBoxing
int a = list.get(0);
```

Wrapper class `Integer`로 설정한 데이터를 리스트에 add할때 `Integer`객체로 넣지 않는다 이것이 `AutoBoxing` 반대로 리스트에서 객체를 `int`에 대입 가능한것이 `AutoUnBoxing`

# Multi-Thread

## Field

- 클래스에 변수를 정의하는 공간
- 여러 스레드가 접근하는 싱글톤 객체라면 field에서 상태 값을 가지면 안됨
> 모든 변수를 paramter로 넘겨받고 return하는 방식으로 구성해야함

## Synchronized

- field에 `Collcection`이 불가피하게 필요할때 -> `synchronized` 키워드를 사용하여 스레드간 race condition을 통제
- `List` -> `Vector`, `Map` -> `HashTable`
> 제공하는 API가 적고 성능도 구림
- `Collections` 라는 Util class에서 제공되는 static 메서드를 통해 해결
> `Collections.synchronizedList(), Collections.synchronizedSet(), Collections.synchronizedMap()` 등
- JDK 1.7 부터는 concurrent package를 통해 `ConcurrentHashMap` 구현체 제공 - 더 나은 성능

## ThreadLocal

- 스레드 사이에 간섭이 없어야 하는 데이터에 사용
- 멀티스레드 환경에서는 클래스의 필드에 멤버를 추가할 수 없고 매개변수로 넘겨받기 때문
- 스레드 내부의 싱글톤을 사용하기 위해 사용
- 주로 사용자 인증, 세션정보, 트랜잭션 컨텍스트에 사용

