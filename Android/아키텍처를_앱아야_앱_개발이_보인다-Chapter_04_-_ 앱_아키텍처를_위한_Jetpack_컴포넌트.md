# Chapter 04 - 앱 아키텍처를 위한 Jetpack 컴포넌트

- 개발자가 중요한 코드에만 집중할 수 있도록 도와주는 라이브러리, 도구, 가이드 모음
  - 애플리케이션 설계 권장사항 준수
  - 보일러 플레이트 코드를 줄임
  - 복잡한 작업을 간소화함

- Jetpack은 안드로이드 프레임워크가 아님
  - androidx.* 패키지로 제공
  - 하위 호환을 위함

## 1. Databinding

- 선언적 형식으로 레이아웃 UI 구성 요소를 앱 데이터와 결합하는 라이브러리

- 선언형 프로그래밍이란?
  - 문제에 대한 답을 정의하기보다는 문제를 설명 하는 것
  - 무엇(what)이 중점

- 명령형 프로그래밍이란?
  - 어떤 방법(how)이 중점

- 명령형vs선언형 예시) 친구가 회사에 놀러올때
  - 명령형 - 지하철 1호선 서울역 9번 출구 쪽 지하 통로를 통해 이동하여 서울스퀘어 19층으로 오면되!
  - 선언형 - 우리 회사 주소는 서울스퀘어 19층이야

- 명령형 코틀린 코드
```kotlin
val textView = findViewById(R.id.textView)
textView.text = viewModel.userName
```

- 선언형 데이터바인딩 코드
```xml
<TextView
    android:text="@{viewModel.getUserName()}" />
```

- findViewById 메서드를 호출할 필요가 없음
  - 앱 성능 향상
  - 메모리 누수 방지
    - Fragment에서 사용할 때는 주의 (onDestroyView 시점에 binding 객체를 해제해야함)
  - NPE 방지

 ### Databinding 설정

 - Databinding 설정 후 활성화 되는 기능
   - 구문 강조
   - Databinding 표현식 오류 검출
   - XML 코드 자동 완성
   - 빠른 코드 참조

### 바인딩 클래스 생성하기

- 생성되는 모든 바인딩 클래스는 ViewDataBinding을 상속함

- 루트 레이아웃을 `<layout>` 태그로 감싸면 자동 생성

- 바인딩 클래스 네이밍 규칙 - 파스칼 케이스
  - ex) activity_main.xml -> ActivityMainBinding.java

- 비즈니스 로직은 ~Binding 클래스가 아닌 ~BindingImpl 클래스에 구현되어 있음

### 바인딩 클래스로 바인딩 객체 생성하기

- 바인딩 클래스를 알 경우
  - layout inflate 동시에
    - ~Binding.inflate()
  - layout inflate 이후
    - ~Binding.bind()

- 바인딩 클래스를 모르는 경우
  - DataBindingUtil.inflate()
  - DataBindingUtil.bind()
  - Activity의 경우
    - DataBindingUtil.setContentView()

- 활용
```kotlin
abstract class BaseBindActivity<B : ViewDataBinding>(
    @LayoutRes
    private val layoutId: Int
) : AppCompatActivity() {

    protected lateinit var binding: B
        private set

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, layoutId)
    }
}

class MainActivity : BaseBindActivity<ActivityMainBinding>(R.layout.activity_main) {

  override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding.root
    }
}
```

### 바인딩 클래스 이름 사용자화하기

- `<data>` 태그 내 class 속성을 사용하여 변경

### ID로 View 참조하기

- findViewById() 결과를 캐싱해두기 때문에 호출할 필요 없음
- 바인딩 클래스의 멤버 변수에 접근하여 View 참조

### 레이아웃에 변수 선언하기

- 레이아웃에 변수를 선언하고 변수에 값을 대입하는 것으로 뷰의 상태를 변경함

- `<layout>` 태그 내 `<data>` 태그 내 `<variable>` 태그에 변수를 선언하며 name(이름)과 type(자료형) 속성을 가짐

```xml
<layout>
    <data>
        <variable
            name="myText"
            type="String" />
    </data>

    <TextView
        android:text="@{myText}" />

</layout>
```

```kotlin
val binding = ActivityMainBinding.inflate()
binding.myText = "HelloWorld"
```

- POJO 클래스
  - getter, setter 생략 가능
    - like kotlin

```xml
<layout>
    <data>
        <variable
            name="user"
            type="User" />
    </data>

    <TextView
        android:text="@{user.getFirstName()}" />

    <TextView
        android:text="@{user.firstName}" />

</layout>
```

- 변수가 Observable 타입이거나 구현을 했다면, 컴파일 타임에 따로 계산되어 반영됨

- 파일명은 같지만 화면 반향, 언어 등과 같은 환경 값에 대한 여러 레이아웃에 대해서 변수가 다른 경우 변수들을 모두 합친 뒤 바인딩 클래스를 생성 -> 변수명 혼동 주의

- 바인딩 표현식에서는 context 제공
  - `<data>` 태그 내에서 변수명 context으로 선언하면 재정의 되므로 주의

- BG thread에서 데이터 모델을 변경해도 문제가 없음
  - 그러나 컬렉션 타입일 경우 동시성 오류 발생함

### 바인딩 표현식 알아보기

#### escaspe 문자
- && -> `&amp;&amp;`
- < -> `&lt;`

#### 지원 목록
- instanceof
- 그룹핑 -> ()
- 문자, 문자열, 수, null
- 캐스팅
- 메서드 호출
- 필드 접근
- 배열 접근
- 삼항 연산자

#### 지원하지 않는 기능
- this
- super
- new
- 명시적 제네릭 호출
  - XXX.set<>()

#### null 병합 연산자 (??)
- 왼쪽의 피연산자부가 null 이면 오른쪽 채택

```
android:text="@{user.firstName ?? user.lastName}"
```

#### NPE 피하기
- 바인딩 클래스는 자동으로 null을 검사함
- user.name 참조의 경우 user가 null일 경우 user.name도 null로 처리
- user.age 참조의 경우 user가 null일 경우 user.age 디폴트 0 처리

#### Collections 클래스
- [] 연산자 사용 가능함

#### 문자열 그대로 사용하기
- 작은따옴표
```
android:text='@{map["firstName"]}'
```
- 큰따옴표
- backtick (`)
```
android:text="@{map[`firstName`]}"
```

#### 안드로이드 리소스 참조
```
@dimen/largeSize
@string/nameFormat(firstName, lastName)
@plurals/orange(orangeCount, orageCount)
```

### `<import>` 사용하기
- alias를 통해 이름 변경하여 참조 가능

```xml
<layout>
    <data>
        <import type="android.view.View" />
        <import type="com.elevenst.View"
            alias="Vista" />
    </data>

    <TextView
        android:visibility="@{true ? View.VISIBLE : View.GONE}" />

    <TextView
    android:visibility="@{true ? Vista.VISIBLE : Vista.GONE}" />

</layout>
```

- 캐스팅
- static 필드/메서드 참조
- java.lang.* 패키지는 자동 import 되므로 생략 가능

#### `<include>` 사용하기
```xml
<include layout="@layout/myInclude"
    app:user="@{user}" />
```

```xml
<layout>
    <data>
        <variable
            name="user"
            type="User" />
    </data>
</layout>
```

> 주의! 데이터바인딩은 `<merge>` 하위에 `<include>`를 허용하지 않는다.

### 이벤트 처리하기
- onClick() 등과 같은 이벤트를 뷰로부터 가져와서 처리하는 기능을 제공
- 메서드 참조
  - 리스너 메서드의 시그니처를 따라 참조
  - 바인딩 표현식이 null이면 리스너를 null로 설정
- 리스너 바인딩
  - 이벤트가 발생할때 마다 해당 람다 표현식을 실행할지 결정
  - 대상 객체의 null여부과 관계 없이 항상 리스너 생성

#### 메서드 참조
- 컴파일 타임에 해당 표현식 검사함

- 메서드 참조 vs 리스너 바인딩
  - 데이터 바인딩이 일어날 때 실제 리스너의 생성 여부

```kotlin
class MyHandlers {
  fun onClickFriend(view: View) {
      //... 
  }
}
```

```xml
<layout>
    <data>
        <variable 
            name="handlers" 
            type="com.example.MyHandlers"/>
    </data>

    <TextView
        android:onClick="@{handlers::onClickFriend}" />
</layout>
```

#### 리스너 바인딩
- 리턴 타입만 이벤트 리스너와 일치하면 됨
```kotlin
class Presenter {
    fun onSaveClick(task: Task) {
        // ..
    }
}
```

```xml
<layout>
    <data>
        <variable 
            name="task" 
            type="com.example.Task"/>
        <variable 
            name="presenter" 
            type="com.example.Presenter"/>
    </data>

    <TextView
        android:onClick="@{() -> presenter.onSaveClick(task)}" />
</layout>
```