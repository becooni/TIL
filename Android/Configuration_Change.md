# Configuration Changed


>landscape, portrait 모드에 맞는 각각 두 벌의 dimens.xml 파일안에 width, height 값이 지정되어 있고, 화면 회전시 마다 그에 맞는 리소스를 적용 하려고한다.

- android:configChanges 속성을 설정하지 않았다면?
  - activity가 재시작되고 화면 회전 상태에 맞는 리소스를 가져와 화면을 그림
  - 별도의 작업이 필요없음

- android:configChanges="orientation" 플래그 속성 설정을 했다면?
  - activity가 재시작 되지 않음
  - Activity#onConfigurationChanged() 오버라이드 하여 회전 상태에 맞는 리소스로 변경을 해줘야한다
    - 왜 그래야할까?
      - -> Activity#setContentView()의 LayoutInflater#inflate() 를 통해서 View를 inflate할때 이미 configuration 리소스들이 대입 되었기 때문
      - View의 생성자에 Configuration 리소스를 대입함
      - android:layout_width, android:layout_height 속성들은 View가 아닌 상위 ViewGroup의 속성임 -> 이건 잘 이해가 안되네요

> activity가 재시작 되면 setContentView() -> inflate() 로 인해서 configuration 리소스가 재설정되기 때문이네요.

이러한 이유로 "configChanges 속성 사용시에는 layout_width같은 속성을 변경해야 할 경우 Activity#onConfigurationChanged() 오버라이드해서 처리해야한다" 가 중점 인것 같네요.
inflate() 프레임웤 소스 View가 resource를 참조하는 부분도 함께 첨부합니다.