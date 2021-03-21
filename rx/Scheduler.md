# Scheduler

## Scheduler

함수의 종류 | 설명
--- | ---
`subscribeOn()` | 함수는 한번 호출했을때 결정한 스레드를 고정하며 이후에는 다시 호출해도 스레드가 바뀌지 않는다.
`observeOn()` | 여러 번 호출할 수 있으며 호출되면 그 다음부터 동작하는 스레드를 바꿀 수 있다.

스케쥴러의 종류 | RxJava2 
--- | --- 
뉴 스레드 스케쥴러 | `Schedulers.newThread()`
싱글 스레드 스케쥴러 | `Schedulers.single()`
계산 스케쥴러 | `Schedulers.computation()`
IO 스케쥴러 | `Schedulers.io()`
트램펄린 스케쥴러 | `Schedulers.trampoline()`
메인(UI) 스레드 스케쥴러 (Android) | `AndroidSchedulers.mainThread()`
핸들러 스레드 스케쥴러 (Android) | `AndroidSchedulers.from()`

## Example

```kotlin
1 Observable.just(/*data*/)
2     .subscribeOn(Schedulers.io())
3     .observeOn(Schedulers.computation())
4     .map(/* data processing */)
5     .observeOn(AndroidSchedulers.mainThread())
6     .subscribe({/* init view stuff */ })
```

1. data 발행 Observable 생성
2. 구독할 때 IO 쓰레드에서 데이터 발행
3. 계산 쓰레드로 변경
4. 계산 쓰레드에서 map 함수 수행
5. 메인(ui) 쓰레드로 변경
6. 구독