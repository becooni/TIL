# Maybe

https://pythonq.com/so/java/415709
https://www.javaer101.com/ko/article/494515.html
https://github.com/ReactiveX/RxJava/issues/5358

데이터를 0개 혹은 1개를 흘려주는 source

꼭 subscribeWith(DisposableMaybeObserver()) 로 구독 해줘야하는데,

single 혹은 다른 maybe로 변환할수 있음

바로

```kotlin
    switchIfEmpty(/*Single or Maybe source*/)
```