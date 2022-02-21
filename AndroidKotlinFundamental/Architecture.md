# Architecture
## UI controller, ViewModel, ViewModelFactory
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
****
## LiveData, LiveData Observer
### LiveData
+ **LiveData는 lifecycle을 식별하는 data holder 클래스이다.**
+ **LiveData는 어떤 타입의 데이터든 보관할 수 있다.**
+ **LiveData객체를 observer와 연결해서 data가 변경될 때 처리해야될 행동을 자동적으로 처리하게 만들 수 있다.**

### LiveData생성(Encapsulation)
```kotlin
// GameViewModel.kt
 private val _word = MutableLiveData<String>()
val word: LiveData<String> get() = _word

private val _score = MutableLiveData<Int>()
val score: LiveData<Int> get() = _score
init {
	_word.value = ""
	_score.value = 0
}
```
+ **LiveData는 ViewModel에서 처리하도록 설계하는 것이 좋다. 따라서 ViewModel에선 수정가능하게 하고 UI controller에서는 읽기만 가능하도록 해야한다. -> Encapsulation**
+ **Encapsulation은 객체필드에 직접 접근을 제한하는 방법으로 private와는 다르게 객체필드의 데이터를 읽는것은 가능하지만 수정은 불가능하게 만든다. 이를 위해 kotlin의 `backing property`를 사용한다.**
	+ backing property : 다른 객체(getter)가 현재의 행동에 영향을 미친다?
	+ 관례상 backing property에서 getter는 `_[변수명]`으로 선언한다.
+ **LiveData의 Encapsulation : 수정가능한 MutableLiveData는 private으로 접근을 제한하고 수정불가능한 LiveData변수는 `get()`메서드로 MutableLiveData의 return을 가져온다.**
+ **LiveData의 초기화는 `setValue()`메서드로 한다. -> kotlin의 `value`프로퍼티를 사용한다.**

### LiveData Observer
```kotlin
// GameFragment.kt
viewModel.score.observe(viewLifecycleOwner, Observer { newScore ->
	binding.scoreText.text = newScore.toString()
})
viewModel.word.observe(viewLifecycleOwner, Observer { newWord ->
	binding.wordText.text = newWord
})
```
+ **fragment에서의 observer생성**
	+ `onCreateView()`에서 생성한다.
	+ `viewLifecycleOwner`를 `LifecylceOwner`로서 전달한다.
+ **LiveData를 observer와 연결한 경우 observer는 active-lifecycle에서만 변경된다.**

### Observer Pattern
+ **Observer Pattern : observer와 observable(관찰대상) 사이의 소통을 명시하는 소프트웨어 설계 패턴**
	+ observer : fragment의 `observe()`메서드
	+ observable : LiveData
+ **일반적으로 observable이 변경되면 observer에게 변경된 내용이 전달된다. 하지만 예외로 observable이 변경되지 않고 observer가 inactive상태에서 active상태로 전환될 때에도 데이터가 전달된다.**
	+ fragment가 재생성 되면 observer은 기존의 ViewModel과 재연결(inactive -> active) 하면서 현재의 데이터를 전달받게 된다.
+ **따라서 inactive->active의 전환에 대한 처리를 염두하지 않으면 버그가 발생할 수 있다.**

## binding data directly to the view objects
### app architecture
<p align="center">
	<img src="https://developer.android.com/codelabs/kotlin-android-training-live-data-data-binding/img/3f68038d95411119.png" width="800px" height="150px">
</p>

+ **현재 view는 UI controller을 통해서 ViewModel에 저장된 데이터를 전달받고 있다.**
+ **data binding을 이용하면 view를 ViewModel에 직접 연결해서 데이터를 받아올 수 있다.**
<p align="center">
	<img src="https://developer.android.com/codelabs/kotlin-android-training-live-data-data-binding/img/7f26738df2266dd6.png" width="800px" height="150px">
</p>

+ **수정후 app architecture**

### view와 ViewModel직접 연결
```xml
<layout...>
 <data>
	<variable
		name="gameViewModel"
		type="com.example.android.guesstheword.screens.game.GameViewModel" />
</data>
```
+ **xml파일에 data binding variable(ViewModel)을 생성한다.**
```kotlin
// fragment
viewModel = ViewModelProvider(this).get(GameViewModel::class.java)
binding.gameViewModel = viewModel
```
+ **fragment에서 binding.gameViewModel(xml의 변수)에 ViewModel을 할당한다.**
```xml
<Button
   android:id="@+id/skip_button"
   ...
   android:onClick="@{() -> gameViewModel.onSkip()}"
   ... />
```
+ **Listener binding을 이용해서 이벤트를 정의한다.**
+ **Listener binding은 onClick(), onZoomIn(), onZoomOut()등의 이벤트가 발생하면 실행되는 바인딩식이다.(람다식으로 정의)**
	+ Listener binding은 android gradle plugin 2.0버전 이상에서 작동한다.
```kotlin
// fragment
binding.skipButton.setOnClickListener { onSkip() }
private fun onSkip() {
   viewModel.onSkip()
}
```
+ **UI controller를 거쳐 처리되던 click event가 이제 view와 viewmodle이 직접 연결되어 Listener binding으로 처리가 된다.**

### data-binding error message
```
// adroidstudio build pane
error: cannot find symbol
import com.example.android.guesstheword.databinding.GameFragmentBindingImpl"

symbol:   class GameFragmentBindingImpl
location: package com.example.android.guesstheword.databinding
```
+ **data binding을 사용하면 컴파일 프로세스는 data binding을 사용하기 위한 중간 클래스를 생성한다.**
+ **이러한 중간 클래스에서 발생하는 error는 코드를 작성중에는 나타나지 않고 compile time에 발생한다.**
+ **compile time에 발생하는 에러는 찾아내기 힘들기 때문에 변수명에 대한 오타, 람다식을 작성하는데 주의해야 한다.**

### LiveData data binding
+ **data binding을 사용하면 LiveData observer를 사용하지 않고 변화한 데이터를 UI에 바로 적용할 수 있다.**
```xml
<TextView
   android:id="@+id/word_text"
   ...
   android:text="@{gameViewModel.word}"
   ... />
```
+ **xml파일에서 data binding variable(ViewModel)을 사용해서 LiveData를 적용할 객체를 지정한다.**
```kotlin
// fragment
viewModel = ViewModelProvider(this).get(GameViewModel::class.java)
binding.gameViewModel = viewModel
binding.lifecycleOwner = viewLifecycleOwner
```
+ **fragment에서 binding의 `lifecycleOwner`를 `viewLifecycleOwner`로 할당해서 LiveData의 범위를 지정한다.**
```kotlin
viewModel.word.observe(viewLifecycleOwner, Observer { newWord ->
   binding.wordText.text = newWord
})
```
+ **이제 위의 observe메서드를 사용해서 UI controller에서 변환해 주던 LiveData를 view객체에서 변환할 수 있게 된다.**

```xml
// strings.xml
<resources>
	...
	<string name="score_format">Current Score: %d</string>
	...
</resources>
```
```xml
// game_fragment.xml
<TextView
	android:id="@+id/score_text"
	...
	android:text="@{@string/score_format(gameViewModel.score)}"
	...
        />
```
+ **서식지정자 사용**

## LiveData Transformations
### Transformations.map()
+ **LiveData의 value를 변환해서 전달하고 싶다면 `Transformations.map()`메서드를 사용할 수 있다.**
+ **`Transformations.map()`메서드는 LiveData의 value를 변경하고 변경한 LiveData객체를 반환한다.**
+ **observer가 없는 LiveData는 `Transformations.map()`메서드로 변환할 수 없다.**
+ **`Transformations.map()`메서드는 변환할 LiveData와 변환 함수를 인자로 받는다.**
	+ 인자로 받는 변환 함수는 메인 쓰레드에서 실행되기 때문에 오래 걸리는 작업은 포함하면 안된다.
```kotlin
val currentWordLetter = Transformations.map(word) { word ->
        word.length
} // word의 길이를 반환

val currentWordposition = Transformations.map(currentWordLetter) { len ->
	(1..len).random()
} // word의 1~len 중 하나의 정수를 반환

val currentWordChar = Transformations.map(word) { word ->
	word.get(currentWordposition.value!!.minus(1)).toUpperCase()
} // word의 (랜덤 정수 - 1)인덱스에 해당하는 문자를 대문자로 반환
```
+ **`Transformations.map()`메서드 사용예제**
