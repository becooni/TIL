# JVM

출처 - https://youtu.be/UzaGOXKVhwU

Java Virtual Machine

# JVM이란...?

- C/C++은 컴파일 플랫폼과 타겟 플랫폼이 다르면 프로그램이 동자가지 않음
> Cross Compile로 해결

- Java는 JVM으로 해결
- Java 코드를 javac가 Java bytecode를 생성(.class 파일)하고 JVM에게 넘겨줌
- 대신에 플랫폼에 JVM이 설치되어 있어야함
- 네트워크에 연결된 모든 디바이스에서 작동하는것이 목적

# JVM 내부구조

## Runtime Data Areas

JVM이 Java Bytecode를 실행하기 위해 사용하는 메모리 공간

### 모든 스레드가 공유되는 것

method area
- 클래스 로더가 클래스 파일을 읽어오면 클래스 정보를 파싱해서 Method area에 저장
- 변수, 메서드, 정적변수, 바이트코드 정보
heap
- 프로그램을 실행하면서 생성한 모든 객체를 Heap에 저장

### 스레드마다 존재하는 것

PC registers
- 각 스레드는 메서드를 실행하고 있고, pc는 그 메서드 안에서 몇번째 줄을 실행해야 하는지 나태내는 역할

Java stacks
- 스레드별 1개만 존재 

- 메서드 실행이 끝나면 스택프레임은 pop되어 스택에서 제거됨
- Stack Trace
- 스택 프레임은..
  - 메서드가 호출될때마다 생성됨
  - Local variables array, Operand stack, Frame data를 가짐
  - Frame data는 Constant pool, 이전 스택 프레임에 대한 정보, 현재 메서드가 속한 클래스/객체에 대한 참조 등의 정보를 가짐.

native method stacks
- Java Bytecode가 아닌 다른 언어로 작성된 메서드를 의미 (C/C++ 성능향상의 목적)

### Java Bytecode

JVM이 레지스터를 쓰지 않는 이유?

- 디바이스마다 레지스터 수가 달라서 디바이스의 의존성을 줄이기 위함
- 대신에 계산 과정이 복잡해질수 있음
- Bytecode가 컴팩트함
- 안드로이드는 레지스터를 씀 Dalvik
