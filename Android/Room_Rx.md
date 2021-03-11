# Room + Rx 주의점

Select시 반환이 stream에 따라 다름

Referece - https://medium.com/androiddevelopers/room-rxjava-acb0cd4f3757

```kotlin

@Query(“SELECT * FROM Users WHERE id = :userId”)
fun getUserById(String userId): Maybe<User>;

@Query(“SELECT * FROM Users WHERE id = :userId”)
fun getUserById(String userId): Single<User>;

@Query(“SELECT * FROM Users WHERE id = :userId”)
fun getUserById(String userId): Flowable<User>;

```