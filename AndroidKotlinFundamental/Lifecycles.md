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

## Lifecycle method
### logging API
+ **`Logcat`콘솔에 메세지를 띄우는 API**
+ **콘솔의 메세지를 통해 어디서 버그가 발생하는지 찾아낼 수 있다.**
+ **`Log.i()` : informational message**
+ **`Log.e()` : error message**
+ **`Log.w()` : warning message**
+ **`Logcat`콘솔에서 `I/tag`를 검색해서 `Log.i()`로 생성된 message를 검색할 수 있다.**

### onCreate()
+ **모든 activity에서 포함해야하는 lifecycle 메서드**
+ **activity에서 한번 초기화하는 메서드**
+ **activity가 메모리에서 생성된 후 한번 호출된다. 이후 activity는 실행중인 것으로 간주된다.**

### onStart()
+ **`onStart()`메서드는 `onCreate()`메서드가 실행된 직후 실행되고 activity가 화면에 나타난다.**
+ **`onCreate()`는 수명주기동안 한번 호출되지만 `onStart()`는 여러번 호출될 수 있다.**
+ **`onStart()`메서드는 `onStop()`메서드와 한 쌍을 이룬다. app을 실행해서 activity가 화면에 표시되고 home으로 돌아가면 `onstop()`이 호출돼서 activity가 화면에 나타나지 않게 된다.**