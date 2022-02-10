# Lifecycles
## Lifecycles
+ **`lifecycle`은 activity또는 fagment의 lifetime에 나타날 수 있는 상태의 집합**
+ **activity가 처음 생성될 때부터 시작해서 소멸될 때 system은 activity의 resourse를 회수한다.**
+ **사용자가 화면을 전환할 때 activity는 각각 다른 상태에서 전환된다.**
+ **앱이 `lifecycle`에 잘못된 응답을 한다면 버그 발생, 자원 낭비 그리고 사용자를 혼란스럽게 할 수 있다.**
+ **사용자가 앱을 시작하거나, 화면을 전환하거나, 앱을 들어갔다가 나올때 마다 activity의 상태는 바뀐다.**

<img src="https://developer.android.com/codelabs/kotlin-android-training-lifecycles-logging/img/f6b25a71cec4e401.png" width="400px" height="500px"></br>

+ **lifecycle의 상태에 따라 다른 동작을 구현하기 위해 activity와 하위 클래스(AppCompatActivity 등)는 lifecycle callback 메서드를 구현한다.**
+ **activity에서 이러한 lifecycle callback 메서드를 재정(override)의 할 수 있다.**

<img src="https://developer.android.com/codelabs/kotlin-android-training-lifecycles-logging/img/8494ec955ce0c49d.png" width="400px" height="500px"></br>

+ **fragment또한 activity의 lifecycle과 비슷한 lifecycle을 가진다.**