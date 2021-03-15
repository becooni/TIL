# LifeCycle

## Activity

![](https://developer.android.com/guide/components/images/activity_lifecycle.png)

- A 실행

1. [A] onCreate
2. [A] onStart
3. [A] onResume

- A 종료

1. [A] onPause
2. [A] onStop
3. [A] onDestroy

- A에서 B 실행

1. [A] onPause
2. [B] onCreate
3. [B] onStart
4. [B] onResume
5. [A] onStop

- B 종료 후 A로 복귀

1. [B] onPause
2. [A] onRestart
3. [A] onStart
4. [A] onResume
5. [B] onStop
6. [B] onDestroy

## Fragment

## ViewModel

