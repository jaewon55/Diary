# Navigation
## Fragment
### Fragment?
+ **Fragment는 Activity의 modular section으로 sub-activity로 생각하면 된다.**
+ **하나의 Activity가 여러 fragment를 사용할 수 있고, 반대로 하나의 fragment가 여러 Activity에서 사용될 수 있다.**
+ **각각의 lifecycle을 가지고 자체적으로 입력 이벤트를 받아들인다.**
+ **activity가 실행중에 Fragment를 추가 또는 제거 할 수 있다.**
+ **Fragment는 Kotlin class로 정의된다.**
+ **Fragment UI는 XML layout file로 정의된다.**

### Fragment Binding
```kotlin
override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                         savedInstanceState: Bundle?): View? {
   val binding = DataBindingUtil.inflate<FragmentTitleBinding>(inflater,
           R.layout.fragment_title,container,false)
   return binding.root
   }
```
+ **onCreateView()에 (fragment)binding객체를 추가한다.**
```
binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
```
```
val binding = DataBindingUtil.inflate<FragmentTitleBinding>(inflater,
           R.layout.fragment_title,container,false)
```
+ **activity data binding vs fragment data binding**
	+ [activity_doc](https://developer.android.com/reference/android/databinding/DataBindingUtil#setcontentview)
	+ [fragment_doc](https://developer.android.com/reference/android/databinding/DataBindingUtil#inflate_1)
	+ 공식문서에서 `DataBindingUtil.inflate()`는 layoutid를 미리 알 수 없을 때만 사용하라고 권고한다.
	+ `onCreateView()`는  Fragment View를 반환한다 즉, Fragment의 view를 생성하는 함수이고 `onCreateView()`가 실행되는 시점에는 아직 Fragment View가 생성되지 않아서 layoutid를 알 수 없는 상태??

### Activity에서 Fragment사용하기
```xml
<fragment
                android:id="@+id/titleFragment"
                android:name="com.example.android.navigation.TitleFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                />
```
+ **Activity의 xml file에 fragment 태그를 통해 fragment를 사용할 수 있다.**
+ **`android:name="fragment경로"`를 통해 fragment를 지정한다.**

## Navigation
### Navigation?
+ **Navigation은 앱의 화면이 전환될 때 화면과 화면 사이의 탐색, 전환 애니매이션, 딥링크, 컴파일시 확인되는 인자를 관리하는 라이브러리이다.**

### Navigation 사용을 위한 Gradle설정 및 resource file생성
```gradle
ext {
        ...
        navigationVersion = "2.3.0"
        ...
    }
```
+ **project-level의 gradle파일의 ext{}부분에 navigationVersion추가**
```
dependencies {
  ...
  implementation "androidx.navigation:navigation-fragment-ktx:$navigationVersion"
  implementation "androidx.navigation:navigation-ui-ktx:$navigationVersion"
  ...
}
```
+ **module-level의 gradle파일의 dependencies에 navigation-fragment-ktx 와 navigation-ui-ktx를 추가**

### Navigation 리소스파일 생성
+ **파일 이름을 지정하고 Resource type은 Navigation으로 지정**

### Navigation Host fragment
+ **navigation host fragment는 navigation에서 host역할을 한다. 보통 NavHostFragment이라는 이름을 가진다.**
+ **host는 user가 정의된 navigation으로 화면을 전환할 때 fragment를 필요에 따라 바꾸고 fragment back stack을 생성하고 관리한다.**
```xml
<fragment
                android:id="@+id/myNavHostFragment"
                android:name="androidx.navigation.fragment.NavHostFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                app:navGraph="@navigation/navigation"
                app:defaultNavHost="true" />
```
+ **정의한 navGraph를 지정 `app:navGraph="@navigation/navigation"`**
+ **fragment태그에 name을 `android:name="androidx.navigation.fragment.NavHostFragment"`지정해서 NavHostFragment로 지정할 수 있다.**
+ **`app:defaultNavHost="true"`를 추가하면 이 fragment host가 기본 host가 되고 system Back button(휴대전화의 뒤로가기 버튼)을 intercept해서 앱에서 동작을 정의할 수 있다.**

## Navigation graph그리기
### fragment추가
+ **navigation xml파일의 Design탭에서 `New Destination`버튼으로 fragment를 추가할 수 있다.**
+ **fragment의 미리보기가 보여지지 않는다면 Code탭에서 해당 fragment에 `tools:layout="[해당 fragment]"`를 추가해서 미리보기를 디자인시점에 확인할 수 있다.**

### fragment연결
+ **두 fragment를 연결하면 사용자가 어떠한 동작을 했을 때 연결된 fragment로 화면전환이 전환된다.**
+ **Design탭에서 connection point를 눌러 연결하고자 하는 fragment로 드래그해서 간편하게 연결이 가능하다.**
+ **생성된 화살표를 클릭해서 나오는 Attributes에서 해당 전환의 id와 화면전환시의 동작들을 정의할 수 있다.**

### .kt파일에서 화면전환 정의
```kotlin
binding.playButton.setOnClickListener { view : View ->
       view.findNavController().navigate(R.id.action_titleFragment_to_gameFragment)
}
```
+ **코틀린 fragment파일에서 위처럼 정의하면 playButton을 눌리면 navigation에 지정된 해당 id의 동작이 실행된다.**

### back stack management
+ **`popUpTo`속성은 지정한 대상이전까지 back stack을 pop한다.**
+ **`popUpToInclusive`속성을 true로 설정할 경우 `popUpTo`로 지정한 대상을 포함해서 back stack을 pop한다.**

## Safe Args plugin
### Safe Args?
+ **`Safe Args`는 데이터를 안전하게 전달하기 위해서 사용한다.**
+ **`NavDirection`은 `Safe Args`에 의해 생성되는 클래스**
	+ 다음 목적지로 향하는 `Actionid`와 `Arguments`들을 `Bundle`에 담아주는 형태
+ **`Bundle`은 전달할 데이터를 key-value방식으로 저장하는 클래스**
+ **`Bundle`을 사용한 데이터 전달방식은 오류가 발생할 수 있다.**
	+ Type mismatch errors
	+ Missing key errors
+ **이러한 `Bundle`오류를 컴파일시점에 바로잡기 위해 `Safe Args`를 사용한다.**

### Safe Args 추가 및 사용
```gradle
dependencies {
   ...
classpath "androidx.navigation:navigation-safe-args-gradle-plugin:$navigationVersion"

}
```
+ **project-level의 gradle파일에 `navigation-safe-args-gradle-plugin` dependency 추가**
```gradle
apply plugin: 'androidx.navigation.safeargs'
```
+ **moudle-level의 gradle파일에 plugin `androidx.navigation.safeargs`추가**
+ **gradle을 적용하면 각 fragment에 `NavDirection`이 생성된다.**
```kotlin
view.findNavController()
        .navigate(GameFragmentDirections.actionGameFragmentToGameWonFragment(numQuestions, questionIndex)))
```
+ **fragment의 navigate()의 파라미터로 `GameFragmentDirections`클래스의 `actionid`매서드를 사용한다.**
+ **`actionid`매서드의 파라미터에 값을 넣어 다음 fragment 전달할 수 있다.**

## Intent
+ **Android components간의 통신에 사용되는 간단한 메시지 객체로 두 개의 유형이 있다.**
	+ explicit : 정확한 타겟에게 메시지를 전달
	+ implicit : 정확한 타겟이 없는 상태에서 메시지 전달, 사용자가 요청을 처리할 앱을 선택하게 할 수 있다, 작업의 유형을 설명하는 ACTION을 가져야 한다.