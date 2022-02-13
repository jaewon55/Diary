# Lifecycles
## Lifecycles
+ **`lifecycle`은 activity또는 fagment의 lifetime에 나타날 수 있는 상태의 집합**
+ **activity가 처음 생성될 때부터 시작해서 소멸될 때 system은 activity의 resourse를 회수한다.**
+ **사용자가 화면을 전환할 때 activity는 각각 다른 상태에서 전환된다.**
+ **앱이 `lifecycle`에 잘못된 응답을 한다면 버그 발생, 자원 낭비 그리고 사용자를 혼란스럽게 할 수 있다.**
+ **사용자가 앱을 시작하거나, 화면을 전환하거나, 앱을 들어갔다가 나올때 마다 activity의 상태는 바뀐다.**

<p align="center">
<img src="https://developer.android.com/codelabs/kotlin-android-training-lifecycles-logging/img/f6b25a71cec4e401.png" width="400px" height="500px">
<img src="https://developer.android.com/codelabs/kotlin-android-training-lifecycles-logging/img/8494ec955ce0c49d.png" width="400px" height="500px">
</p>

+ **lifecycle의 상태에 따라 다른 동작을 구현하기 위해 activity와 하위 클래스(AppCompatActivity 등)는 lifecycle callback 메서드를 구현한다.**
+ **activity에서 이러한 lifecycle callback 메서드를 재정(override)의 할 수 있다.**
+ **fragment또한 activity의 lifecycle과 비슷한 lifecycle을 가진다.**
****
## Timber
### logging API
+ **`Logcat`콘솔에 메세지를 띄우는 API**
+ **콘솔의 메세지를 통해 어디서 버그가 발생하는지 찾아낼 수 있다.**
+ **`Log.i()` : informational message**
+ **`Log.e()` : error message**
+ **`Log.w()` : warning message**
+ **`Logcat`콘솔에서 `I/tag`를 검색해서 `Log.i()`로 생성된 message를 검색할 수 있다.**

### Timber
+ **`Timber`는 logging 라이브러리로 안드로이드에 내장된 `Log`클래스보다 많은 이점이 있다.**

### Application Class
+ **`Application` Class는 앱의 전체적인 상태를 포함하고 있고, OS와 소통하는 main객체이다.**
+ **특별히 하나를 만들지 않는 이상 Android는 default `Application` Class를 사용한다.**
+ **`Application` Class를 작성했다면 `Manifest`파일에 추기해야 적용된다.**
+ **`Application` Class에 작성한 코드는 앱 전체에서 사용할 수 있기 때문에 유용할 수 있지만 그만큼 오류가 발생하기 쉽다. 따라서 꼭 필요한 경우가 아니라면 코드를 삽입하지 않는 것이 좋다.**
+ **`Timber`를 앱전체에서 사용하고 앱이 set up되기 이전에 라이브러리를 한번 정의해야 하기 때문에 `Application` Class를 사용한다.**

### Application Class를 사용해 Timber정의
```kotlin
class ClickerApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        Timber.plant(Timber.DebugTree())
    }
}
```
+ **package에 새로운  class file을 생성하고 subclass를 `Application()`로 지정한다.**
+ **`onCreate()`를 override해서 `Timber`라이브러리를 초기화 한다.**
```xml
 <application
        ...
        android:name=".ClickerApplication"
	...
```
+ **`AndroidManifest.xml`파일에 작성한 `Application` Class를 추가한다.**
+ **`Manifest`file에 class를 추가하지 않는다면 앱이 문제없이 실행되지만 class에서 정의한 내용은 사용할 수 없다.(적용되지 않음)**
```kotlin
Log.i("MainActivity", "onCreate Called")
Timber.i("onCreate called")
```
+ **이제 `Timber`를 사용해 로그를 찍어볼 수 있다.**
****
## Lifecycle method
### onCreate()
+ **모든 activity에서 포함해야하는 lifecycle 메서드로 activity에서 한번 호출 된다.**
+ **activity가 메모리에서 생성된 후 한번 호출된다. 이후 activity는 실행중인 것으로 간주된다.**
+ **layout또는 variable을 처음으로 초기화(생성)하는 메서드**

### onRestart()
+ **`onRestart()`메서드는 `onCreate()`메서드와 매우 비슷하다. 두 메서드 모두 activity가 화면에 나타나기 이전에 호출된다.**
+ **하지만 한 lifecycle에서 `onCreate()`메서드는 activity를 실행할 때 딱 한번 호출되지만 `onRestart()`메서드는 여러번 호출될 수 있다.**
+ **앱이 처음 실행되는 이후 `onCreate()`대신 `onRestart()`가 호출되는 것이다. 따라서 `onRestart()`메서드 에서는 activity가 처음 시작될 때에는 작동하지 않는 코드를 정의하는 곳이다.**

### onStart()
+ **`onStart()`메서드는 `onCreate()`메서드가 실행된 직후 실행되고 activity가 화면에 나타난다.**
+ **`onCreate()`는 수명주기동안 한번 호출되지만 `onStart()`는 여러번 호출될 수 있다.**
+ **`onStart()`메서드는 `onStop()`메서드와 한 쌍을 이룬다. app을 실행해서 activity가 화면에 표시되고 home으로 돌아가면 `onstop()`이 호출돼서 activity가 화면에 나타나지 않게 된다.**

### onResume()
+ **activity에 focus를 맞추고 사용자와 소통할 준비를 한다.**

### onPause()
+ **activity에 대한 focus가 해제된다.**

### onStop()
+ **activity가 화면에 나타나지 않는다.**

### onDestroy()
+ **activity가 완전히 종료되었음을 의미한다.**
+ **`onDestroy()`가 실행되면 activity가 완전히 종료되었고 garbage-colection을 실행해도 좋다는 의미이다.**
+ **`onDestroy()`메서드가 호출된 이후에 OS는 이 resourse는 폐기가 가능하다고 인지하고 메모리에서 삭제한다.**
+ **`onCreate()`, `onDestroy()`메서드는 하나의 activity의 수명주기에서 한번만 호출된다.**
+ **앱이 완전히 종료되는 경우**
	+ `finish()`메서드를 정의한 경우
	+ 사용자가 강제종료한 경우
	+ 장시간 화면이 나타나지 않는경우 (Android system은 스스로 activity를 종료)

## Background
+ **mobile device의 처리 성능과 배터리는 제한적이기 때문에 Android runtime system은 resource를 균형적으로 사용할 필요가 있다. 따라서 background에 있는 앱들에 대해서 resource사용을 중지한다.**
+ **`onStop()`가 호출되면 앱이 화면에서 나타나지 않는다. 즉 bacground에 들어있게 된다. 하지만 `onDestroy()`가 호출되기 전까지 메모리에 resourse가 저장되어 있기 때문에 다시 foreground로 돌아올 때 `onCreate()`를 실행하지 않는다.**

### Visiable lifecycle vs Interactive lifecycle
+ **Visiable lifecycle은 앱이 화면에 나타나는지 여부를 결정하는 lifecycle 메서드를 의미한다.**
	+ `onStart()`메서드가 실행되면 앱이 화면에 나타나고 반대로 `onStop()`메서드가 실행되면 앱이 화면에서 사라진다.
+ **Interactive lifecycle은 focus를 관리하는 lifecycle 메서드를 의미한다.**
	+ `onResume()`메서드는 activity에 focus를 맞추고 `onPause()`메서드는 focus를 해제한다.
+ **activiy를 부분적으로 보이게 할 수 있기 때문에(partially visible) focus와 visiable의 차이점을 알아야 한다.**

<p align="center">
<img src="https://developer.android.com/codelabs/kotlin-android-training-lifecycles-logging/img/9ddc8b1dc79b1bff.png" width="400px" height="550px">
</p>

+ **위의 사진에서 화면에는 2개의 activity가 나타나고 있다. 하지만 focus는 공유할 앱을 선택하는 activity에 맞춰 있기 때문에 앱의 위에 보여지고 있는 activity는 사용자와 상호작용을 할 수 없는 즉 focus가 해제된 상태이다.**
+ **이 때에 앱의 위에 보여지는 activity는 `onPause()`메서드가 호출되어 focus가 해제되었지만 `onStop()`메서드는 호출되지 않았기 때문에 화면에 나타난다.**