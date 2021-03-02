# Hilt

# 2020 드로이드 나이츠 - 옥수환 Hilt 세션

https://youtu.be/gkUCs6YWzEY

## Quick Setup

```kotlin
class MemoRepository @Inject constructor(
    private val db: MemoDatabase
) {
    fun load() {...}
}

@HiltAndroidApp
class MemoApp: Application()

@AndroidEntryPoint
class MemoActivity: AppCompatActivity() {

    @Inject lateinit var repository: MemoRepository

    override fun onCreate(savedInstanceState: Bundle) {
        super.onCreate(savedInstanceState)
        
        repository.load("ABC")
    }
}

@InstallIn(ApplicationComponent::class)
@Module
object DataModule {

    @Provides
    fun provideMemoDB(@ApplicationContext context: Context) =
    Room.databaseBuilder(context, MemoDatabase::class.java, "Memo.db")
    .build()
}
```

# Android developers practice

https://developer.android.com/training/dependency-injection

# Overview

### Di를 쓰면 좋은점

- 클래스 재사용성
- 리팩토링 편의성
- 테스트 편의성

> 이점을 완전히 이해하려면 Manual Di를 해봐야함

### Di Basic

- Constructor Injection
- Field Injection

```kotlin
// Constructor Injection
class Car(private val engine: Engine) {
    fun start() {
        engine.start()
    }
}

fun main(args: Array) {
    val engine = Engine()
    val car = Car(engine)
    car.start()
}

// Field Injection
class Car {
    lateinit var engine: Engine

    fun start() {
        engine.start()
    }
}

fun main(args: Array) {
    val car = Car()
    car.engine = Engine()
    car.start()
}
```

## Manual Di

### Basic

```kotlin
class LoginActivity: Activity() {

    private lateinit var loginViewModel: LoginViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // In order to satisfy the dependencies of LoginViewModel, you have to also
        // satisfy the dependencies of all of its dependencies recursively.
        // First, create retrofit which is the dependency of UserRemoteDataSource
        val retrofit = Retrofit.Builder()
            .baseUrl("https://example.com")
            .build()
            .create(LoginService::class.java)

        // Then, satisfy the dependencies of UserRepository
        val remoteDataSource = UserRemoteDataSource(retrofit)
        val localDataSource = UserLocalDataSource()

        // Now you can create an instance of UserRepository that LoginViewModel needs
        val userRepository = UserRepository(localDataSource, remoteDataSource)

        // Lastly, create an instance of LoginViewModel with userRepository
        loginViewModel = LoginViewModel(userRepository)
    }
}
```

- 보일러 플레이트 코드가 많음 (중복 코드 많이생김)
- 순서대로 선언해야함 (Repository를 만들어야 ViewModel을 만들수 있음)
- 재사용 어려움 (그러면 싱글톤으로 만들어야하는데 그러면 테스트가 더 어려워짐)

### 컨테이너로 만들어 보자

```kotlin
// Container of objects shared across the whole app
class AppContainer {

    // Since you want to expose userRepository out of the container, you need to satisfy
    // its dependencies as you did before
    private val retrofit = Retrofit.Builder()
                            .baseUrl("https://example.com")
                            .build()
                            .create(LoginService::class.java)

    private val remoteDataSource = UserRemoteDataSource(retrofit)
    private val localDataSource = UserLocalDataSource()

    // userRepository is not private; it'll be exposed
    val userRepository = UserRepository(localDataSource, remoteDataSource)
}

    // Custom Application class that needs to be specified
// in the AndroidManifest.xml file
class MyApplication : Application() {

    // Instance of AppContainer that will be used by all the Activities of the app
    val appContainer = AppContainer()
}


class LoginActivity: Activity() {

    private lateinit var loginViewModel: LoginViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Gets userRepository from the instance of AppContainer in Application
        val appContainer = (application as MyApplication).appContainer
        loginViewModel = LoginViewModel(appContainer.userRepository)
    }
}
```

- 컨테이너로 만들어도 Repository를 만들기 위한 종속성은 그대로
- 싱글톤 Repository는 얻지 못함
- LoginViewModel이 많은 위치에서 필요하면 한곳에서 instance를 만드는게 좋다
- LoginViewModel을 컨테이너로 이동하고 팩토리를 만들어라

```kotlin
// Definition of a Factory interface with a function to create objects of a type
interface Factory {
    fun create(): T
}

// Factory for LoginViewModel.
// Since LoginViewModel depends on UserRepository, in order to create instances of
// LoginViewModel, you need an instance of UserRepository that you pass as a parameter.
class LoginViewModelFactory(private val userRepository: UserRepository) : Factory {
    override fun create(): LoginViewModel {
        return LoginViewModel(userRepository)
    }
}

// AppContainer can now provide instances of LoginViewModel with LoginViewModelFactory
class AppContainer {
    ...
    val userRepository = UserRepository(localDataSource, remoteDataSource)

    val loginViewModelFactory = LoginViewModelFactory(userRepository)
}

class LoginActivity: Activity() {

    private lateinit var loginViewModel: LoginViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Gets LoginViewModelFactory from the application instance of AppContainer
        // to create a new LoginViewModel instance
        val appContainer = (application as MyApplication).appContainer
        loginViewModel = appContainer.loginViewModelFactory.create()
    }
}
```

- `AppContainer`를 직접 관리 해야하고 모든 instance를 수동으로 만들어줘야함
- 여전히 보일러플레이트 코드 임

### Application flow 종속성 관리

- 기능이 많아지고 다양한 흐름이 도입되기 시작하면 더 많은 문제가 생김
1.  flow가 다양해지면 instance가 flow 범위 내에서만 존재하길 원함 
> ex) 로그인 flow에서 `login data`를 만들때 다른 사용자의 이전 `login data` 흐름을 유지하지 않으려 함
-> 이를 위해 `AppContainer` 내에 `FlowContainer` 객체 생성하면 가능
2. App Graph 와 FlowContainer를 최적화하는 것은 어려움. 흐름에 따라 필요하지 않은 instance를 삭제해야함

```kotlin
class LoginContainer(val userRepository: UserRepository) {

    val loginData = LoginUserData()

    val loginViewModelFactory = LoginViewModelFactory(userRepository)
}

// AppContainer contains LoginContainer now
class AppContainer {
    ...
    val userRepository = UserRepository(localDataSource, remoteDataSource)

    // LoginContainer will be null when the user is NOT in the login flow
    var loginContainer: LoginContainer? = null
}

    class LoginActivity: Activity() {

    private lateinit var loginViewModel: LoginViewModel
    private lateinit var loginData: LoginUserData
    private lateinit var appContainer: AppContainer

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        appContainer = (application as MyApplication).appContainer

        // Login flow has started. Populate loginContainer in AppContainer
        appContainer.loginContainer = LoginContainer(appContainer.userRepository)

        loginViewModel = appContainer.loginContainer.loginViewModelFactory.create()
        loginData = appContainer.loginContainer.loginData
    }

    override fun onDestroy() {
        // Login flow is finishing
        // Removing the instance of loginContainer in the AppContainer
        appContainer.loginContainer = null
        super.onDestroy()
    }
}
```

- 여전히 많은 보일러 플레이트 코드

## Hilt

## Setup

- `build.gradle`에 플러그인 추가
- `app/build.gradle`에 종속성 추가

## Hilt Application Class

- Hilt를 사용하는 모든 앱은 `@HiltAndroidApp` 어노테이션이 명시된 `Application` 클래스를 포함해야함.
- Application 수준 종속성 컨테이너 역할을 함.

```kotlin
@HiltAndroidApp
class ExampleApplication : Application() { ... }
```

## Injection Android Class

- `@AndroidEntryPoint` 어노테이션으로 Android 클래스에 di 제공

```kotlin
@AndroidEntryPoint
class ExampleActivity : AppCompatActivity() {

  // 주입된 필드는 무조건 공개해야한다
  @Inject lateinit var analytics: AnalyticsAdapter
  ...
}
```

## Define Bindings

- 생성자 삽입

```kotlin
class AnalyticsAdapter @Inject constructor(
  private val service: AnalyticsService
) { ... }
```

- 위 코드는의 생성자 파라미터인 `AnalyticsService`의 인스턴스를 제공하는 방법도 알아야함

## Module

- 여러가지 이유로 constructor-inject를 못할때도 있다
1. `interface`
2. 소유하지 않은 외부
- 위 경우 Hilt Module을 사용하여 binding가능 - `@Module`
- Hilt Module에 `@InstallIn`을 추가하여 각 모듈이 사용되거나 설치될 Android class를 Hilt에게 제공해야함

### Inject interface instaces with @Binds

- `AnalyticsService`는 인터페이스라서 constructor-inject가 불가능하다.
- 대신 Hilt Module내에 `@Binds`를 명시한 추상 함수를 만들어 Hilt에 binding 하면 가능.

```kotlin
interface AnalyticsService {
  fun analyticsMethods()
}

// Constructor-injected, because Hilt needs to know how to
// provide instances of AnalyticsServiceImpl, too.
class AnalyticsServiceImpl @Inject constructor(
  ...
) : AnalyticsService { ... }

@Module
@InstallIn(ActivityComponent::class)
abstract class AnalyticsModule {

  @Binds
  abstract fun bindAnalyticsService(
    analyticsServiceImpl: AnalyticsServiceImpl
  ): AnalyticsService
}
```

- `ExampleActivity`에서 사용된 모듈이어서 `@InstallIn(ActivityComponent::class)`를 명시
- `AnalyticsModule`은 앱내의 모든 `Activity`에서 사용가능

### Inject instances with @Provides

- `interface`만이 constructor-inject이 안되는 유일한 경우가 아님
- constructor-inject는 외부 라이브러리(retrofit, room 등)에서 가져 오기 때문에 클래스를 소유하지 않았거나 `instance`를 `Builder Pattern`으로 만들어야 하는 경우에도 불가능

- `AnalyticsService` 클래스를 직접 소유하지 않은 경우 Hilt Module내 함수를 만들고 `@Provides`로 명시해 Hilt에 Binding할수 있다

- Hilt에게 어떤 타입의 `instance`를 제공할지 함수 리턴타입에 명시
- 함수 인자가 해당 타입의 종속성을 알려줌
- instance를 제공할때마다 함수 내부를 실행

```kotlin
```@Module
@InstallIn(ActivityComponent::class)
object AnalyticsModule {

  @Provides
  fun provideAnalyticsService(
    // Potential dependencies of this type
  ): AnalyticsService {
      return Retrofit.Builder()
               .baseUrl("https://example.com")
               .build()
               .create(AnalyticsService::class.java)
  }
}
```

### Provide multiple bindings for the same type

- 같은 타입에 여러 구현을 제공하기 위해 Hilt에 여러 Binding을 제공해야함
- `@Qualifier`를 사용하여 여러 바인딩을 정의할수 있음
- `@Qualifier`는 한가지 타입에 여러 Binding이 있을 경우 해당 타입에 대한 특정 Binding을 식별하는데 사용하는 Annotation

- `retofit`에서 api call을 intercept해야 하는 경우 `interceptor` 와 `OkHttpClien`가 필요함
- 다른 서비스의 경우 다른 방식으로 intercept 할수도 있음
- 그럴때 `Hilt`에게 두 가지로 구현된 `OkHttpClient`를 제공

`@Binds` or `@Provides` 메서드에 추가할 `@Qualifier`를 먼저 정의

```kotlin
@Qualifier
@Retention(AnnotationRetention.BINARY)
annotation class AuthInterceptorOkHttpClient

@Qualifier
@Retention(AnnotationRetention.BINARY)
annotation class OtherInterceptorOkHttpClient
```

이 다음 각 Qualifier에 해당하는 타입의 instance를 제공하는 방법을 알아야 한다. 이 경우 `@Provides`와 함께 Hilt Module을 사용할 수 있다. 두 메서드 리턴타입이 같지만 다른 Binding으로 레이블을 지정

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {

  @AuthInterceptorOkHttpClient
  @Provides
  fun provideAuthInterceptorOkHttpClient(
    authInterceptor: AuthInterceptor
  ): OkHttpClient {
      return OkHttpClient.Builder()
               .addInterceptor(authInterceptor)
               .build()
  }

  @OtherInterceptorOkHttpClient
  @Provides
  fun provideOtherInterceptorOkHttpClient(
    otherInterceptor: OtherInterceptor
  ): OkHttpClient {
      return OkHttpClient.Builder()
               .addInterceptor(otherInterceptor)
               .build()
  }
}
```

필드 또는 파라미터에 해당 Qualifier로 주석을 달아 필요한 특정 타입을 injection 할 수 있다.

```kotlin
// As a dependency of another class.
@Module
@InstallIn(ActivityComponent::class)
object AnalyticsModule {

  @Provides
  fun provideAnalyticsService(
    @AuthInterceptorOkHttpClient okHttpClient: OkHttpClient
  ): AnalyticsService {
      return Retrofit.Builder()
               .baseUrl("https://example.com")
               .client(okHttpClient)
               .build()
               .create(AnalyticsService::class.java)
  }
}

// As a dependency of a constructor-injected class.
class ExampleServiceImpl @Inject constructor(
  @AuthInterceptorOkHttpClient private val okHttpClient: OkHttpClient
) : ...

// At field injection.
@AndroidEntryPoint
class ExampleActivity: AppCompatActivity() {

  @AuthInterceptorOkHttpClient
  @Inject lateinit var okHttpClient: OkHttpClient
}
```

가장 좋은 방법은 Qualifier을 타입에 추가하는 경우 해당 종속성을 제공할 모든 방법에 Qualifier을 추가하는것.

한정자 없이 기본 또는 공통 구현을 그대로 두면 오류가 나기 쉽고 잘못된 Di가 될수 있음

### Predefined qualifers in Hilt

Hilt는 미리 정의된 Qualifier를 제공함.
> ex) @ApplicationContext, @ActivityContext

```kotlin
class AnalyticsAdapter @Inject constructor(
    @ActivityContext private val context: Context,
    private val service: AnalyticsService
) { ... }
```

## Generated components for Android classes

필드 삽입을 수행 할 수있는 각 Android 클래스에는 `@InstallIn` 주석에서 참조 할 수있는 관련 Hilt 구성 요소가 있습니다.

Hilt component | Injector for
--- | ---
SingletonComponent | Application
ActivityRetainedComponent | N/A
ViewModelComponent | ViewModel
ActivityComponent | Activity
FragmentComponent | Fragment
ViewComponent | View
ViewWithFragmentComponent | View annotated with @WithFragmentBindings
ServiceComponent | Service

### Component lifetimes

Hilt는 해당 Android 클래스의 수명주기에 따라 생성 된 구성 요소 클래스의 인스턴스를 자동으로 만들고 제거합니다.

Generated component | Created at | Destroyed at
--- | --- | ---
SingletonComponent | Application#onCreate() | Application#onDestroy()
ActivityRetainedComponent | Activity#onCreate() | Activity#onDestroy()
ViewModelComponent | ViewModel created | ViewModel destroyed
ActivityComponent | Activity#onCreate() | Activity#onDestroy()
FragmentComponent | Fragment#onAttach() | Fragment#onDestroy()
ViewComponent | View#super() | View destroyed
ViewWithFragmentComponent | View#super() | View destroyed
ServiceComponent | Service#onCreate() | Service#onDestroy()

### Component scopes

힐트는 기본적으로 모든 바인딩 범위가 지정되지 않음. 즉, 앱이 바인딩을 요청할 때마다 새 인스턴스 생성.

힐트는 바인딩 범위를 지정하면 범위 내에선 인스턴스 당 한번만 범위 바인딩을 만들고 범위 내 모든 요청은 동일한 인스턴스를 공유함.

Android class | Generated component | Scope
--- | --- | ---
Application | SingletonComponent | @Singleton
Activity | ActivityRetainedComponent | @ActivityRetainedScoped
ViewModel | ViewModelComponent | @ViewModelScoped
Activity | ActivityComponent | @ActivityScoped
Fragment | FragmentComponent | @FragmentScoped
View | ViewComponent | @ViewScoped
View annotated with @WithFragmentBindings | ViewWithFragmentComponent | @ViewScoped
Service | ServiceComponent | @ServiceScoped

예에서 @ActivityScoped를 사용하여 AnalyticsAdapter의 범위를 ActivityComponent로 지정하면 Hilt는 해당 활동의 수명 동안 동일한 AnalyticsAdapter 인스턴스를 제공합니다.

```kotlin
@ActivityScoped
class AnalyticsAdapter @Inject constructor(
  private val service: AnalyticsService
) { ... }
```

앱내 모든 위치에서 `AnalyticsService`를 매번 동일한 인스턴스를 사용해야 하는 상황이라고 가정. 이럴때는 `AnalyticsService`의 범위를 `SingletonComponent`으로 하는게 적절하다

```kotlin
// If AnalyticsService is an interface.
@Module
@InstallIn(SingletonComponent::class)
abstract class AnalyticsModule {

  @Singleton
  @Binds
  abstract fun bindAnalyticsService(
    analyticsServiceImpl: AnalyticsServiceImpl
  ): AnalyticsService
}

// If you don't own AnalyticsService.
@Module
@InstallIn(SingletonComponent::class)
object AnalyticsModule {

  @Singleton
  @Provides
  fun provideAnalyticsService(): AnalyticsService {
      return Retrofit.Builder()
               .baseUrl("https://example.com")
               .build()
               .create(AnalyticsService::class.java)
  }
}
```

### Component hierarchy

컴포넌트에 모듈을 설치하면 해당 컴포넌트 또는 컴포넌트 계층 구조에 하위에 있는 컴포넌트에서 해당 바인딩의 종속성으로 바인딩 액세스 가능

### Component default bindings

각 Hilt 구성 요소에는 Hilt가 사용자 지정 바인딩에 종속성으로 삽입 할 수있는 기본 바인딩 세트가 함께 제공됩니다. 이러한 바인딩은 특정 하위 클래스가 아니라 일반 활동 및 조각 유형에 해당합니다. Hilt는 단일 활동 구성 요소 정의를 사용하여 모든 활동을 주입하기 때문입니다. 각 활동에는이 구성 요소의 다른 인스턴스가 있습니다.

Android component | Default bindings
--- | ---
SingletonComponent | Application
ActivityRetainedComponent | Application
ViewModelComponent | SavedStateHandle
ActivityComponent | Application, Activity
FragmentComponent | Application, Activity, Fragment
ViewComponent | Application, Activity, View
ViewWithFragmentComponent | Application, Activity, Fragment, View
ServiceComponent | Application, Service

```kotlin
class AnalyticsServiceImpl @Inject constructor(
  @ApplicationContext context: Context
) : AnalyticsService { ... }

// The Application binding is available without qualifiers.
class AnalyticsServiceImpl @Inject constructor(
  application: Application
) : AnalyticsService { ... }

class AnalyticsAdapter @Inject constructor(
  @ActivityContext context: Context
) { ... }


// The Activity binding is available without qualifiers.
class AnalyticsAdapter @Inject constructor(
  activity: FragmentActivity
) { ... }
```

## Inject dependencies in classes not supported by Hilt

`ContentProvider`는 지원안하는데 `@EntryPoint`로 해야함
> 필요할떄 다시 보자

```kotlin
class ExampleContentProvider : ContentProvider() {

  @EntryPoint
  @InstallIn(SingletonComponent::class)
  interface ExampleContentProviderEntryPoint {
    fun analyticsService(): AnalyticsService
  }

  ...
}


class ExampleContentProvider: ContentProvider() {
    ...

  override fun query(...): Cursor {
    val appContext = context?.applicationContext ?: throw IllegalStateException()
    val hiltEntryPoint =
      EntryPointAccessors.fromApplication(appContext, ExampleContentProviderEntryPoint::class.java)

    val analyticsService = hiltEntryPoint.analyticsService()
    ...
  }
}
```

