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
    + R클래스는 앱을 빌드하면 생성되는 클래스로 rec디렉터리를 포함한 앱의 모든 정보를 담고 있다. R클래스를 이용해 앱의 많은 리소스(이미지, 문자열, 레이아웃 파일의 요소 등)를 참조할 수 있다.