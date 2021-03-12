# Room + Rx 주의점

Select시 반환이 stream에 따라 다름

Referece - https://medium.com/androiddevelopers/room-rxjava-acb0cd4f3757

```kotlin
// 데이터가 없으면 complete 호출
@Query(“SELECT * FROM Users WHERE id = :userId”)
fun getUserById(String userId): Maybe<User>;

// EmptyResultSetException
@Query(“SELECT * FROM Users WHERE id = :userId”)
fun getUserById(String userId): Single<User>;

// 아무것도 발행하지 않음
@Query(“SELECT * FROM Users WHERE id = :userId”)
fun getUserById(String userId): Flowable<User>;

// 아무것도 발행하지 않음
@Query(“SELECT * FROM Users WHERE id = :userId”)
fun getUserById(String userId): Observable<User>;

```