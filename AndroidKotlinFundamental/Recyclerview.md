# Recyclerview

## Room error in M1
```gradle
kapt "org.xerial:sqlite-jdbc:3.34.0"
```
+ **M1 mac에서 Room을 사용한 프로젝트 빌드에 error발생**
+ **dependencies추가로 빌드 성공**

## RecyclerView의 개념과 동작
### RecyclerView?
+ **데이터 리스트는 간단한것 부터 복잡한것 까지 종류가 다양하다.**
+ **`RecyclerView`는 기본적으로 현재 표시된 view를 처리한다.**

<p align="center">
	<img src="https://developer.android.com/codelabs/kotlin-android-training-recyclerview-fundamentals/img/dcf4599789b9c2a1.png">
</p>

+ **`RecyclerView`는 화면에 표시할 item의 view를 삭제해고 새로 생성하는 동작 대신 재사용 한다.**
+ **사용자가 스크롤하면 RecyclerView는 새로운 item을 표시하기 위해 작업을 수행한다.**
+ **item이 화면에서 사라지면 해당 item의 view는 재사용 된다. -> 새로운 item의 view로 사용된다.**
+ **`RecyclerView`의 작업은 많은 processing time을 절약하고 스크롤을 부드럽게 해준다. 따라서 복잡하고 긴 리스트를 표시하는데 효율적이다.**

### adapter pattern
+ **`adapter pattern`은 다른 전압을 사용하는 나라에서 adapter로 전압을 변환해서 사용하는 것과 비슷한 개념이다. 한 클래스의 API를 다른 API로 사용할 수 있게 된다.**
+ **`RecyclerView`는 이 패턴을 사용해서 앱의 data를 `RecyclerView`가 표시할 수 있는 데이터로 변환한다.**
	+ 변환시 앱이 data를 저장하고 처리하는 방법은 변환하지 않는다.

### RecyclerView의 구성
1. **view의 컨테이너로 동작할 `RecyclerView`를 layout파일에 정의한다.**
2. **data를 표시할 item을 layout파일에 정의한다.** 
	+ 한 layout파일에 한 개의 item을 정의한다.(필수사항은 아님)
	+ item은 `RecyclerView`안에서 사용하기 때문에 따로 `ViewGroup`에 넣을 필요 없다.
	+ data를 표시할 item은 한 번에 한 개의 item을 생성하기 위해 fragment의 layout과 분리되어야 한다.(필수)
3. **view의 UI구조를 관리하는 layout manager를 정의 한다.**
4. **item에 표시할 data에 정보를 담은 view holder를 정의한다.**
	+ `ViewHolder` 클래스를 상속받는다.
	+ `viewHolder`는 화면 주변 view(직전/직후 표시될 item)를 효율적으로 이동시키기 위한 정보를 포함한다.
5. **`adapter`를 정의한다.**
	+ `adapter`는 data와 `RecyclerView`를 연결한다.
	+ data를 화면에 표시될 수 있도록 (viewHolder 내부에서)변환한다.
	+ `RecyclerView`는 `adapter`를 이용해서 data를 화면에 표시할 방법을 알아낸다.

## adapter
+ **getItemCount() : 표시할 item의 개수를 `RecyclerView`에 알려준다**
+ **onBindViewHolder() : `RecyclerView`에 의해 호출되어 item리스트에서 position에 해당하는 data를 화면에 나타낸다.**
+ **onCreateViewHolder() : `RecyclerView`가 `ViewHolder`가 필요할 때 호출한다.**
	+ 두 개(parent, viewType)의 파라미터를 받고 `ViewHolder`를 리턴한다.
	+ parent는 `ViewHolder`를 가지고 있는 RecyclerView
	+ viewType은 같은 `RecyclerView`에서 여러 view가 있을 때 view를 구분하기 위해 사용된다.

## ViewHolder
+ **`ViewHolder`는 `RecyclerView`안에서 item의 view와 data의 위치를 정의하고 있다. 이러한 기능을 이용해서 RecyclerView는 view의 위치를 설정하고 여러 기능을 추가할 수 있다.**
+ **RecyclerView가 ViewHolder에 저장된 view에 접근하기위해서 ViewHolder의 itemView 프로퍼티를 사용할 수 있다.**
	+ 화면에 item을 나타내기 위해 바인딩 할 때
	+ view에 decoration을 넣을 때 (ex : border)
	+ 접근성을 구현하기 위해서
+ **`ViewHolder`와 관련된 코드는 모두 `ViewHolder`내부에 작성해야 복잡한 구조도 관리하기 좋다.**

## DiffUtil
+ **`RecyclerView`는 item의 list를 비교하는 `DiffUtil`클래스를 제공한다. 이 클래스는 알고리즘으로 현재 list를 변경된 list로 변환하기 위한 최소 개수를 계산해서 `RecyclerView`가 최소한의 동작으로 list를 수정하도록 한다.**
```kotlin
class SleepNightDiffCallback : DiffUtil.ItemCallback<SleepNight>() {
    override fun areItemsTheSame(oldItem: SleepNight, newItem: SleepNight): Boolean {
        return oldItem.nightId == newItem.nightId
    }

    override fun areContentsTheSame(oldItem: SleepNight, newItem: SleepNight): Boolean {
        return oldItem == newItem
    }
}
```
+ **`areItemsTheSame()`는 oldItem과 newItem이 같은지 다른지 확인한다.**
+ **`areContentsTheSame()`는 두 item이 가지고 있는 data를 비교한다. data가 다르다면 `DiffUtil`에 item이 수정되었음을 알린다.**

## ListAdapter
+ **`RecyclerView.Adapter`대신 `ListAdapter`를 사용해서 item의 수정을 관찰할 수 있다.**
+ **`ListAdapter`는 list를 관찰/추적 하고 list가 수정되면 이를 알린다. observer?**

## binding adapter
+ **`@BindingAdapter`를 사용해서 itemView에서 `eventListener`처럼 사용할 수 있다.**
+ **함수를 정의하고 `@BindingAdapter` annotation을 달면 된다.**
+ **binding adapter는 클래스 내부에서 함수를 정의할 경우 실행시 error가 발생한다. 따라서 클래스 외부에 함수를 선언해야 한다.**

## GridLayout / Interacting with RecyclerView items
### GridLayout
+ **`GridLayoutManager`을 사용해서 `RecyclerView`에서 item의 배치를 설정할 수 있다.**
```kotlin
val manager = GridLayoutManager(activity, 3, GridLayoutManager.VERTICAL, false)
binding.sleepList.layoutManager = manager
```
+ **`GridLayoutManager`는 4개의 파라미터를 받는다.**
	1. activity
	2. span의 수
	3. (스크롤)방향
	4. 역배치 여부
### Interacting with RecyclerView items
+ **`RecyclerView`의 item과 상호작용에 대한 처리는 보통 `ViewModel`에서 한다. 이 외에도 여러가지 방법이 있으며 각각 장단점이 있다.**

## Headers
+ **header는 일반적으로 list의 넓이에 맞춰서 사용되는 하나의 item이다.**
+ **header는 하나만 사용해서 list의 title로 사용하거나, 분리자 역할을 하는 여러개의 header를 사용할 수 있다.**
+ **`RecyclerView`는 item을 구분할 수 없기 때문에 header는 adapter에서 적절하게 핸들링해 줘야 한다.**
+ **header를 추가하는 방법**
	+ header가 위치할 index를 확인하고 adpater가 header를 추적하는 것
	+ data gride의 백업을 수정하는 것
