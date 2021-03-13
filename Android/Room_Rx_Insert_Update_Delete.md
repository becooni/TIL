# Room_Rx_Insert_Update_Delete

Reference - https://medium.com/androiddevelopers/room-rxjava-acb0cd4f3757

`Completable`, `Single`, `Maybe` 사용가능

## Insert

### Success

- `Completable` -> `onComplete`
- `Single`, `Maybe` -> row id **emit** on `onSuccess`

```kotlin
Completable

Single<Long>
Single<List<Long>>

Maybe<Long>
Maybe<List<Long>>
```

### Exception

- `Completable`, `Single`, `Maybe` -> `onError` **emit**

## Update/Delte

### Success

- `Completable` -> `onComplete`
- `Single`, `Maybe` -> 영향을 받은 row count **emit** on `onSuccess`

```kotlin
Completable

Single<Int>

Maybe<Int>
```
