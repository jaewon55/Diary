# Build your first app

## AndroidManifest.xml
+ 안드로이드 앱은 main메서드가 없다. 대신 AndroidManifest.xml파일이 사용자가 앱을 실행 시
    수행하는 동작을 나타낸다. (MainActivity를 실행하는 등)

## Activity & Rayout
+ 각 activity는 관련된 layout파일(xml)이 있고 activity와 layout파일은 layout inflation으로 알려진 프로세스로 연결된다. activity가 실행되면 layout파일은 kotlin view객체로 메모리에 저장된다. 이러한 작업이 실행된 후 객체(kotlin view)를 화면에 나타내거나 동적 동작을 수행할 수 있다.
```kotlin
class MainActivity : AppCompatActivity() { ...
```
+ AppCompatActivity는 최신 Android 기능을 지원하는 activity의 하위 클래스로 이전 버전의 안드로이드와 호환된다. (이는 Android Jetpack에 의해 가능하다.)
```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
   super.onCreate(savedInstanceState)
   setContentView(R.layout.activity_main)
}
```
+ setContentView(R.layout.activity_main)
    + R.layout.activity_main을 참조한다는 의미(실제로는 정수값)
        + R.layout.activity_main은 R클래스의 layout폴더의 activity_main(확장자 제외)파일을 의미한다.
    + R클래스는 앱을 빌드하면 생성되는 클래스로 res디렉터리를 포함한 앱의 모든 정보를 담고 있다. R클래스를 이용해 앱의 많은 리소스(이미지, 문자열, 레이아웃 파일의 요소 등)를 참조할 수 있다.

```
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout   
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    tools:context=".MainActivity" >

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!" />

</LinearLayout>
```
+ View와 ViewGroup
    + 모든 view(group)는 최상위 view를 root로 해서 계층 구조로 형성된다. 앱이 실행되면 view의 계층 구조는 객체의 계층 구조로 전환된다.
    + LinearLayout은 하위 view를 하나씩 선형(수직 또는 수평)으로 구성
    + ConstraintLayout은 디자인 편집기와 호환이 잘되는 view container
```
android:id="@+id/roll_button"
```
+ "@+id"접두사는 컴파일러에게 ID상수를 R클래스에 추가하라고 지시한다.

## API levels
+ Gradle Scripts의 build.gradle(mdule:app)에서 해당 모듈이 지원하는 Android API수준을 확인할 수 있다.
    + compileSdkVersin - 앱이 지원할 수 있는 최신 버전의 안드로이드
    + targetSdkVersion - 최근 앱을 테스트 한 API (대부분 compileSdkVersion과 동일)
    + minSdkVersion - 앱이 지원할 수 있는 가장 오래된 버전
+ Android Jetpack은 구글이 개발한 라이브러리 모음으로 이전 버전의 안드로이드를 지원하는데 도움이 되는 하위 클래스 및 기능을 제공한다.
    + vector drawables	xml로 작성된 verctor drawable은 일반적인 PNG파일보다 훨씬 적은 크기를 가진다. 하지만 vector drawables는 API level 21이상의 장치에서만 지원이 되기 때문에 그 이하의 장치에서는 vector파일을PNG파일로 변환되어 사용되기 때문에 앱의 크기가 커지는 단점이 있다.
    + 이러한 문제를 Android X 호환 라이브러리를 이용해 API레벨 7까지 vector drawable을 지원할 수 있다.
    + build.gradle (Module:app)에서 defaultCnfig에 `vectorDrawables.useSupportLibrary = true`을 추가후 동기화
    + activity의 layout폴더의 root view 에 `xmlns:app="http://schemas.android.com/apk/res-auto"`을 추가
    + `<ImageView>`의 android:src를 app:srcCompat로 변경한다.

## View Binding
+ View Binding은 activity나 fragment에서 view와 상호 작용을 보다 편리하게 할 수 있도록 해주는 기능이다.
+ 사용
    ```kotlin
    android {
        ...
        buildFeatures {
            viewBinding = true
        }
    }
    ```
    + `build.gradle(Module:app)`에 위의 코드를 추가 후 동기화한다. 이후 각 XML레이아웃 파일에 대한 바인딩 클래스가 자동으로 생성된다.
        + 예) `activity_main.xml`파일은 `ActivityMainBindig`이라는 바인딩 클래스가 생성됨
    ```kotlin
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        val view = binding.root
        setContentView(view)
    }
    ```
    + binding변수를 `ActivityMainBinding`클래스로 선언하고 `setContentView()`에 해당 layout의 root를 넣는다.
    ```kotlin
    binding.name.text = "test"
    binding.button.setOnClickListener { viewModel.userClicked() }
    ```
    + 이제 binding변수를 통해 layout의 id에 접근할 수 있게 된다.
+ findViewById()와 차이점
    + 잘못된 view ID로 인해 발생할 수 있는 NPE에 대해 안전하고 잘못된 type을 사용할 때 발생할 수 있는 문제에 안전하다.
    + -> layout과 activity의 잘못된 호환을 runtime에서의 에러가 아닌 compile단계에서 에러를 찾을 수 있어 에러를 보다 빠르게 찾을 수 있다.