# Layouts

## View Groups
+ ViewGroup은 하위 view 또는 viewGroup을 포함하고 있는 view
+ LinearLayout은 하위 view를 수직 또는 수평으로 정렬할 수 있다.
	```xml
	android:orientation="vertical" // 수직 정렬
	android:orientation="horizontal" // 수평 정렬
	```
   
## Layout Editor
+ xml파일을 android에 내장되어 있는 editor를 이용해 작성할 수 있다.
	+ Palette : view 및 viewgroup의 목록
	+ Attributes : 현재 선택한 view의 특성을 설정할 수 있다.
	+ Component Tree : view계층을 표시 크기가 작거나 숨겨진, 겹쳐진 view를 선택 및 추가하는데 유용하다.
   
## Style
+ style은 view의 모양 및 속성의 모음으로 resource로 만들어 관리할 수 있다.
+ view의 기능(?)에 따라 style을 재사용해서 앱의 일관성을 유지할 수 있다.

## ScrollView
+ scrollview는 viewGroup으로 오직 하나의 하위 view 또는 viewGroup을 포함할 수 있다.
+ 여러 view를 scrollview에 넣고 싶다면 LinearLayout에 포함시키는 방법이 있음

## CoonstraintLayout
+ view를 유연하게 편집할 수 있다. 따라서 크고 복잡한 배치를 작성하는데 유용하다. (API 9레벨 이상 지원)
+ Design-time attributes : constraintLayout에서 view의 수평 또는 수직 constraint를 적용하지 않았을 경우 design중에서만 view를 적절한 위치에 나타내기 위해 사용(runtime에는 무시)
+ baseline constraint - 다른 view와 text의 기순선을 일치시켜 일관된 text를 제공한다.
	+ baseline constraint와 bottom constraint는 서로 배타적이기 때문에 bottom을 지정하고 baseline을 지정하면 bottom은 삭제된다.

## chain
+ chain은 수직 또는 수평으로 서로 연결된 view들의 그룹이다. chain의 첫 번째 view가 해당 chain의 head로 chain의 위치와 배치를 제어한다.
+ style : chain은 정렬되는 방식에 따라 스타일이 나뉜다.

	<img src="https://developer.android.com/codelabs/kotlin-android-training-constraint-layout/img/d57e8cdbe225181f.png">

	+ spread : 기본 스타일으로 모든 view가 같은 여백을 가진다.

	<img src="https://developer.android.com/codelabs/kotlin-android-training-constraint-layout/img/8ee14c6b5164afef.png">

	+ spread inside : 양 끝의 view들이 부모 view와 붙어 있고 나머지 view가 같은 여백을 가진다.

	<img src="https://developer.android.com/codelabs/kotlin-android-training-constraint-layout/img/16bb057b065865c6.png">

	<img src="https://developer.android.com/codelabs/kotlin-android-training-constraint-layout/img/c893437f3a9c3f06.png">

	+ packed : view들이 뭉쳐있고 margin을 계산한 후 haed view의 위치에 따라 chain의 위치를 조정할 수 있다.

	<img src="https://developer.android.com/codelabs/kotlin-android-training-constraint-layout/img/91ca5b204a0141ed.png">
	
	+ weighted : view가 layout_constraint(Horizontal/Vertical)_weight 속성에 설성된 값에 따라 모두 채운다.

## Data binding
+ binding객체를 통해 view와 데이터에 접근할 수 있다.
	+ 코드가 짧아지고 가독성, 유지보수가 쉽다.
	+ data와 view가 명확하게 구분된다.
	+ 사용자가 앱을 사용할 때가 아니라 앱을 시작할 때 뷰를 한번만 탐색해서 정보를 가져온다.
+ layout과 activity연결
	```gradle
	buildFeatures {
		dataBinding true
	}
	```
	+ `build.gradle(Module:app)`에 위의 코드를 추가 후 동기화한다.
	`<layout>`태그를 xml의 root view로 사용
	```
	lateinit var binding: ActivityMainBinding
	override fun onCreate(savedInstanceState: Bundle?) {
		super.onCreate(savedInstanceState)
		binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
	}
	```
	+ binding변수를 선언하고 `setcontentview()`를 `binding = DataBindingUtil.setContentView(this, R.layout.activity_main)`으로 변경한다.
+ view에서 data를 사용
	```
	<data>
		<variable
			name="myName"
			type="com.example.android.aboutme.MyName" />
	</data>
	```
	+ `<layout>`태그 안에 `<data>`태그를 추가하고 `<variable>`의 name과 type(data class의 경로)을 지정한다.
	```
	android:text="@={myName.name}"
	```
	+ `"@={}"`표기법을 통해 데이터를 지정할 수 있다.
	```binding.apply {
            myName?.nickname = nicknameEdit.text.toString()
            invalidateAll()
        }
	```
	+ 사용자와 상호작용 중 data class의 data가 변경되어 이를 적용하고자 할 때는 `invalidateAll()`(binding expression)을 사용해 이전의 데이터를 무효화하고 새로운 데이터를 적용한다.