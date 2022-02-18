# Architecture
## app architecture
### app architecture?
+ **app architecture은 앱의 class들의 관계를 설계하는 방법이다.**
+ **architecture로 설계된 코드는 체계적이고 뛰어난 성능을 발휘하고 관리하기 쉽다.**
+ **Android app architecture은 MVVM(model-view-viewmodel) architectural과 비슷하다.**

### UI controller
+ **activity와 fragment같은 UI기반 class로 UI처리 또는 OS상호작용 로직을 포함한다.**
	+ UI처리 : 화면에 UI를 띄우는 등과 같은 처리
	+ OS상호작용 : 사용자 입력을 받는 것과 같은 처리
+ **UI의 text와 같은 데이터를 결정하는 로직(decision-making)은 포함하면 안된다.**

### ViewModel
+ **activity와 fragment에 나타낼 데이터의 정보를 저장하는 class로 데이터에 대한 약간의 계산과 변화를 처리할 수 있다.**
+ **ViewModel은 configuration changes가 발생해도 남아있기 때문에 activity, fragment, view를 참조하는 코드가 포함되면 안된다.**
+ **ViewModel은 configuration change가 발생해도 삭제되지 않는다.**
+ **ViewModel은 관련된 fragment와 분리되거나 activity가 끝나면 파괴된다. 파괴되기전 `onCleared()`메서드가 호출되어 resourse를 지운다.**

### ViewModel생성 및 연결
```gradle
implementation 'androidx.lifecycle:lifecycle-viewmodel-ktx:2.2.0'
```
+ **module단위의 gradle파일에 viewmodel dependency를 추가한다.**
```kotlin
// GameViewModel.kt
class GameViewModel : ViewModel() {
	var score = 0
}
```
+ **ViewModel class를 생성한다.(추상클래스 ViewModel을 상속)**
```kotlin
// GameFragment
private lateinit var viewModel: GameViewModel
 override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        binding = DataBindingUtil.inflate(
            inflater,
            R.layout.game_fragment,
            container,
            false
        )
        viewModel = ViewModelProvider(this).get(GameViewModel::class.java)
        return binding.root

    }
```
+ **연결할 fragment에 ViewModel을 인스턴스화 한다. 이 때 ViewModel class로 직접 인스턴스화 하지 않고 `ViewModelProvider().get()`를 통해서 인스턴스를 해야한다.**
	+ ViewModel을 해당 class로 직접 인스턴스화 하면 configuration change가 발생할 때 마다 이전의 ViewModel이 없어지지 않고 새로운 ViewModel이 생성될 수 있다.
	+ `ViewModelProvider`는 현재 생성된 ViewModel이 있는지 확인하고 생성된 ViewModel이 있다면 해당 ViewModel을 return하고 없다면 새로 생성한 ViewModel을 return 한다.
```kotlin
class GameFragment : Fragment() {
	...
	private fun updateScoreText() {
		binding.scoreText.text = viewModle.score.toString()
   }
	...
}
```
+ **fragment에서 인스턴스화 한 ViewModel을 통해서 데이터에 접근이 가능하다.**

### ViewModelFactory
+ **ViewModel객체를 인스턴스화 하는데 생성자가 필요한 경우 factory method를 사용해서 생성자와 함께 초기화 할 수 있다.**
+ **factory method pattern은 creational design pattern의 일종으로 factory method로 객체를 생성하는 설계방식 이다.**
	+ creational design pattern : 상황에 맞는 방식의 객체를 생성하는 설계방식

### ViewModelFactory을 이용한 ViewModel초기화
```kotlin
class ScoreViewModel(finalScore: Int) : ViewModel() {
   var score = finalScore
}
```
+ **생성자(finalScore)와 함께 초기화할 ViewModel class**
```kotlin
class ScoreViewModelFactory(private val finalScore: Int) : ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(ScoreViewModel::class.java)) {
            return ScoreViewModel(finalScore) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class")
    }
}
```
+ **ViewModel을 초기화하는 ViewModelFactory class.**
+ **`create()`메서드를 재정의 해서 ViewModel을 생성하고 반환한다.**
```kotlin
class ScoreFragment : Fragment() {

    private lateinit var viewModel: ScoreViewModel
    private lateinit var viewModelFactory: ScoreViewModelFactory

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
	...
        viewModelFactory =
            ScoreViewModelFactory(ScoreFragmentArgs.fromBundle(requireArguments()).score)
        viewModel =
            ViewModelProvider(this, viewModelFactory).get(ScoreViewModel::class.java)
	...
    }
}
```
+ **ViewModel와 연결할 fragment**
+ **navigation에서 전달받은 argument를 생성자로 해서 ViewModelFactory를 인스턴스화 한다.**
+ **인스턴스화한 ViewModelFactory를 `ViewModelProvider()`의 인자로 넣어 ViewModel을 인스턴스화 한다.**

