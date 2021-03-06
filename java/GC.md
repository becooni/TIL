# GC (Garbage Collector)

출처
- https://d2.naver.com/helloworld/184615
- https://youtu.be/vZRmCbl871I
- https://asfirstalways.tistory.com/159

# GC

동적으로 할당한 메모리 영역 중 사용하지 않는 영역을 탐지(Mark)하여 해제(Sweep)하는 기능

# Stack, Heap

- Stack - 정적으로 할당한 메모리 영역 
  - 원시(primitive) 타입의 데이터가 값과 함께 할당
  - Heap 영역에 생성된 Object 타입의 데이터의 참조 값 할당

- Heap - 동적으로 할당한 메모리 영역
  - 모든 Object(최상위 클래스) 타입의 데이터 할당
  - Heap 영역의 Object를 가리키는 참조 변수가 Stack에 할당

```java
ex)
void stackHeap() {
    // Stack num1 = 10
    int num1 = 10; 
    // Stack num1 = 10, num2 = 5 
    int num2 = 5;
    // Stack num1 = 10, num2 = 5, sum = 15 
    int sum = num1 + num2;
    
    // Stack num1 = 10, num2 = 5, sum = 15, name
    // Heap String jake
    String name = "jake"

    // 메소드가 종료되면 Stack은 모두 clear 되고 Heap에 남아있는 String jake는 Stack에 참조하는 데가 없어 짐 이것을 unreachable object라고 함
    // gc의 대상이 됨
}
```

# GC의 과정

1. GC가 Stack 모든 변수를 스캔하면서 어떤 객체를 참조하고 있는지 mark해둠 (Reachable Object)
2. Reachable Object가 참조하는 객체도 mark함
3. mark되지 않은 객체를 Heap에서 제거함

1,2를 Mark라고 하고 3을 Sweep이라고 함 Mark and Sweep

# GC 과정 Heap 구조

- New Generation
  - Eden
  - Survivor 0
  - Survivor 1
- Old Generation

1. 새로운 객체는 New Generation내에 Eden 영역에 할당됨
2. Eden 영역이 가득차면...
  - GC 발생 (Mark and Sweep)
  - Reachable Object는 Survival 0 or 1 영역으로 이동 (객체가 존재하는 영역으로 둘다 비어있다면 0부터)
  - 이동한 객체는 Age값 증가
3. 1~2번 과정이 반복하여 Survival 0 or 1 영역이 가득차면 0 or 1 영역으로 이동
  - 이때 이동하는 객체도 Age값 증가
4. 1~3번 과정이 반복하여 특정 Age가 된 객체는 Old Generation 영역으로 이동
  - 이를 Promotion 이라고 함
5. Old Generation 영역이 가득차면 GC 발생

New Generation 영역 내 gc를 minor gc (2번 과정)

Old Generation 영역 내 gc를 major gc (4번 과정)

# Stop-The-World

- Major GC 때 발생
- GC를 실행하기 위해 JVM이 application을 잠시 멈추는 것
- stop-the-world가 발생하면 gc를 실행하는 스레드를 제외한 나머지 스레드는 모두 작업을 멈춤
- GC가 완료되면 중단한 작업을 재개

# GC의 종류

- Serial GC
- Parallel GC
- Concurrent Mark Sweep GC (CMS GC)
- G1 GC

## Serial GC

- GC를 처리하는 스레드가 1개
- CPU 코어, 메모리가 적을때
- mark-sweep-compact 알고리즘 사용
> Sweep 과정 후 파편화 되어 있는 메모리를 한데 모아주는 알고리즘

## Parallel GC

- GC를 처리하는 스레드가 여러개
- Serial GC보다 객체 처리속도가 빠름
- 메모리가 충분하고 코어의 개수가 많을때 사용

## Concurrent Mark Sweep GC (CMS GC)

- stop-the-world 시간을 줄여 빠름
- 다른 gc 방식보다 메모리와 cpu를 더 많이 사용
- Compaction 단계를 기본적으로 제공하지 않음
- 조각난 메모리가 많아 Compaction 작업을 실행하면 다른 GC 방식의 stop-the-world 시간보다 더 길기 때문에 Compaction 작업이 얼마나 자주, 오랫동안 수행되는지 확인해야함

![](https://d2.naver.com/content/images/2015/06/helloworld-1329-5.png)

1. Initial Mark 단계에서 클래스 로더에서 가장 가까운 객체 중 Rechable Object만 찾는것으로 끝, 그래서 멈추는 시간이 짧음
2. Concurrent Mark 단계는 1번에서 확인한 객체에서 참조하고 있는 객체들을 따라가면서 확인 - 이때 다른 스레드가 실행 중인 상태에서 동시에 진행된다는게 특징
3. Remark 단계는 2번에서 새로 추가되거나 참조가 끊긴 객체를 확인 
4. Concurrent Sweep 단계는 쓰레기를 정리하는 작업을 실행 - 이 작업도 다른 스레드가 실행되고 있는 상황에서 진행

## G1 GC

![](https://d2.naver.com/content/images/2015/06/helloworld-1329-6.png)

- 그림처럼 바둑판의 각 영역에 객체를 할당하고 GC를 실행하는 방식
- 그러다가 해당 영역이 가득차면 다른 영역에 객체를 할당하고 GC를 시행함
- 기존의 GC 방식과는 다른 New Generation -> Old Generation Promotion 단계가 사라진 방식

장점
- 성능
단점
- 불안정성
