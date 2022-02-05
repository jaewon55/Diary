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
