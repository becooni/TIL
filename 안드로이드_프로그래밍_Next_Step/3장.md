# 3장 HandlerThread

- 3.1 HanderThread 클래스
  - Thread 확장 클래스
  - 내부에서 Looper.prepare(), Looper.loop()를 실행하는 Looper 스레드
  - Looper를 가졌고 Handler에서 사용하기 위한 스레드
  - 활용
    - 순차 BG 작업 - 내부 looper가 MessageQueue를 사용하므로 순차적으로 처리
> https://github.com/aosp-mirror/platform_frameworks_base/blob/master/core/java/android/os/HandlerThread.java#L111
> - getThreadHandler() 5년전에 추가됬지만 javadoc @hide로 접근할수 없음
> https://stackoverflow.com/questions/52060920/getthreadhandler-method-is-unaccessible-from-a-handlerthread-instance

- 3.2 스레드 풀
  - ThreadPoolExecutor 클래스
    - AsyncTask에서 사용
  - ScheduledThreadPoolExecutor 클래스
    - 지연/반복 시 사용
  - Executors 클래스
    - 팩토리 메서드
> 스레드풀은 상황에따라 적합하게 생성

- 3.3 AsyncTask
  - BG / UI 스레드 구분
    - Handler#handleMessage() / Handler#post()
    - View#post() / Activity#runOnUiThread()
    - AsyncTask
  - Activity LifeCycle 이슈
    - 메무리 누수
      - 대안 - Loader
    - 순차 실행으로 인한 속도 저하
    - Fragment가 Activity로 부터 detach 된 경우 Context NPE 가능성
  - AsyncTask 취소
    - isCancelled()
    - 실행중인 task interrupt 여부
  - 예외 처리 메서드 부재
    - 기본 패턴 변형 - 예외 발생시 doInBackground() 에서 예외 값 리턴 (null, false 등)
    - 대안 - RxJava
  - 병렬 실행시 doInBackground() 실행 순서가 보장되지 않음
    - Task간 의존성이 있을경우
      - 순차 실행
      - CountDownLatch
> 단점 추가 - 재사용불가, 반드시 UI스레드에서 호출
> Rx 혹은 코루틴 쓰자...