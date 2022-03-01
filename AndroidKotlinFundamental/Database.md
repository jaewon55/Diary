# Database

## Room database
#### 로컬에 저장되는 데이터
+ **로컬(디바이스)에 데이터를 저장하면 많은 이점을 가진다.**
	+ 오프라인 상태에서 데이터를 가져올 수 있다.
	+ 온라인으로 저장되면 연동된 서버에 수정된 데이터를 백그라운드에서 원할하게 동기화 할 수 있다.
+ **android에서는 로컬에 데이터를 저장할 때 SQLite를 사용한다.**
+ **하지만 SQLite를 직접사용해서 database를 구축하기는 어렵기 때문에 android jetpack이 제공하는 `Room`을 사용하는 것이 권장된다.**

#### Room
+ **`Room`은 SQLite를 보다 쉽게 사용할 수 있는 라이브러리다.**
+ **`Room`은 `RoomDatabase`, `DAO`, `Entity`로 구성되어 있다.**
<p align="center">
	<img src="https://blog.kakaocdn.net/dn/cu1a4e/btq1KAzkc4e/SGWx0e7YJkeycMQerJbLh1/img.png">
</p>

+ **application은 `Room database`에서 `DAO`를 가져온다.**
+ **가져온 `DAO`를 사용해서 `Entity`에 접근해서 `Entity`의 항목을 읽고 수정하는 등의 행동을 하는 구조**

#### Entity
+ **`Entity`는 database에서 `table`과 비슷하다.**
+ **`data class`로 선언되고 table의 이름, id, 여러 속성들을 정의할 수 있다.**
```kotlin
@Entity(tableName = "daily_sleep_quality_table")
data class SleepNight(
    @PrimaryKey(autoGenerate = true)
    var nightID: Long = 0L,

    @ColumnInfo(name = "start_time_milli")
    val startTimeMilli: Long = System.currentTimeMillis(),

    @ColumnInfo(name = "end_time_milli")
    var endTimeMilli: Long = startTimeMilli,

    @ColumnInfo(name = "quality_rating")
    var sleepQuality: Int = -1
)
```
+ **`@Entity` annotation을 이용해서 `entity`를 선언한다.**
	+ `@Entity`의 tableName인자로 table의 이름을 지정할 수 있다.
	+ tableName을 지정하지 않은 경우 class의 이름으로 지정된다.
+ **`@PrimaryKey`는 table의 기본 키를 지정한다. 기본 키는 유일한(Unique) 값이어야 하기 때문에 중복되어선 안된다.**
	+ 키 값을 직접 지정할 수 있지만 autoGenerate인자를 사용해서 `Room`이 자동으로 생성하게 할 수 있다.
+ **`@ColumInfo`는 각 속성의 이름을 지정할 수 있다.**
	+ 생략하면 맴버 변수의 변수명이 이름이 된다.

### DAO(Data Access Object)
```kotlin
@Dao
interface SleepDatabaseDao {
    @Insert
    fun insert(night: SleepNight)

    @Update
    fun update(night: SleepNight)

    @Query("SELECT * from daily_sleep_quality_table WHERE nightId = :key")
    fun get(key: Long): SleepNight?

    @Query("DELETE FROM daily_sleep_quality_table")
    fun clear()

    @Query("SELECT * FROM daily_sleep_quality_table ORDER BY nightId DESC LIMIT 1")
    fun getTonight(): SleepNight?

    @Query("SELECT * FROM daily_sleep_quality_table ORDER BY nightID DESC")
    fun getAllNights(): LiveData<List<SleepNight>>
}
```
+ **데이터에 접근할 수 있는 메서드를 정의한 interface로 `@Dao` annotation을 사용한다.**
+ **`DAO`interface에서 정의한 메서드는 `Room`라이브러리가 제공하는 `@Insert`, `@Update`, `@Delete`, `@Query`annotation을 사용할 수 있다.**
	+ `@Query`를 제외한 annotation은 `Room`이 자동으로 해당 기능의 `SQL query`를 실행한다.
+ **`@Insert` : entity생성**
+ **`@Update` : entity 업데이트**
+ **`@Delete` : 특정 entity를 삭제한다.**
+ **`@Query` : 메서드 호출시 실행할 `SQL query`를 custom할 수 있다.**
	+ query명령에 `:`(콜론)을 사용해서 메서드의 매개변수를 참조할 수 있다.(`:key`)

#### Room Database
```kotlin
@Database(entities = [SleepNight::class], version = 1, exportSchema = false)
abstract class SleepDatabase : RoomDatabase() {

    abstract val sleepDatabaseDao: SleepDatabaseDao

    companion object {
        @Volatile
        private var INSTANCE: SleepDatabase? = null

        fun getInstance(context: Context): SleepDatabase {
            synchronized(this) {
                var instance = INSTANCE
                if (instance == null) {
                    instance = Room.databaseBuilder(
                        context.applicationContext,
                        SleepDatabase::class.java,
                        "sleep_history_database")
                        .fallbackToDestructiveMigration()
                        .build()
                    INSTANCE = instance
                }
                return instance
            }
        }
    }
}
```
+ **`Room database`는 `RoomDatabase`클래스를 상속받는 추상 클래스로 생성한다.**
+ **`@Database` annotation으로 연관된 entity와 version을 지정한다.**
	+ `version`은 `schema`(entity의 구조)가 수정되었을 경우 이전과 구분하는 역할을 한다. `schema`가 수정되었는데 version이 그대로라면 error가 발생한다. 
	+ `exportSchema`는 `schema`의 버전 기록(history) 저장여부를 지정하는 인자
+ **`Room database`의 instance는 메모리를 굉장히 많이 사용한다. 그리고 단일 프로세스에서 여러개의 instance를 사용할 필요가 없다. 따라서 `Room database`는 싱글톤 패턴으로 구현한다.**
+ **싱글톤 페턴으로 구현한 database는 여러 쓰레드에서 접근할 수 있기 때문에 database의 읽기와 쓰기는 main쓰레드에서만 가능하도록 `@Volatile` annotation을 사용한다.**
+ **`getInstance()`메서드는 database를 return한다. 이 때 생성된 database가 없다면 `Room.databaseBuilder()`메서드를 통해서 database를 생성한다.**
	+ 규모가 큰 프로젝트의 경우 여러 쓰레드에서 동시에 같은 database를 요청할 수 도 있다. 이 때 같은 database가 둘 이상 생성될 수 있다.
	+ 따라서 database를 생성하는 메서드는 한 번에 하나의 쓰레드에서만 실행하도록 하는 `synchronized{}`로 감싸주는 것이 좋다.
+ **database를 빌드하기 전에 `schema`가 수정되었을 경우 이전의 데이터를 새로운 `schema`에 적용하는 방법을 정의한 `migration`을 지정한다.**

## Coroutine
#### Multi-threading & callback
+ **현재 대부분의 디바이스는 다중 하드웨어 프로세서를 가지고 있다. 이러한 프로세서는 동시에 실행될 수 있다. ->`multi-processing`**
+ **여러 프로세서를 효과적으로 사용하기 위해서 OS는 application이 프로세서에서 여러 쓰레드를 실행할 수 있도록 한다. ->`multi-threading`**
+ **일반적인 경우 application은 하나의 작업을 실행하면 작업이 완료될 때 까지 실행이 멈추게 된다. 따라서 긴 작업을 수행하는 경우 앱은 장시간 멈추게 된다. 이 경우 UX뿐만 아니라 hardware적으로도 효율적이지 않다.**
+ **이러한 문제를 `callback`을 사용해서 긴 시간이 걸리는 작업을 main쓰레드가 아닌 다른 쓰레드에서 처리하게 만들어 해결할 수 있다.**
+ **하지만 `callback`을 사용하는 코드는 순차적으로 실행되지 않기 때문에 가독성이 떨어지고 관리도 어렵다. 그리고 언어가 제공하는 몇몇 기능들을 사용할 수 없게 된다.**

#### Coroutine
+ **callback의 단점을 보완한 것이 `Coroutine`이다.**
+ **`Coroutine`은 프로그램의 기본적인 흐름과는 관계없이 실행된다. 따라서 main 쓰레드의 진행을 멈추거나 간섭하지 않는다.**
+ **`suspend`는 코틀린에서 함수를 `coroutine`에서 사용할 수 있다고 표시하는 keyword이다.**
	+ blocking : 작업이 끝날 때 까지 다른 모든 작업들이 중단된다.
	+ suspending : 작업의 결과가 필요한 작업은 일시중단 하고 결과와 관계없는 작업은 실행할 수 있다.
+ **Coroutine을 사용하는데 필요한 3가지 요소**
	+ Job : `Coroutine`을 취소하는데 사용된다. Job은 상하계층을 이룰 수 있고 상위 Job이 취소되면 하위 Job또한 즉시 취소된다.
	+ Dispatcher : `Coroutine`을 실행할 쓰레드를 보내는 역할을 한다.
	+ Scope : `Coroutine`이 실행될 때의 context를 정의하는 것으로 `Job`과 `Dispatcher`을 결합한다.
+ **`CoroutineScope`는 `Coroutine`을 추적하고 관리한다. 각각의 `Coroutine`은 특정한 `CoroutineScope`에서 실행된다.**
	+ Architecture components는 최고수준의 scope를 내장하고 있다.(`ViewModelScope`, `LifecycleScope`, `liveData`)
	+ 이러한 scope는 각각 적절한 dependencies를 설정해야 한다.

## button states
#### enabled
+ **`enabled`속성은 view의 활성화 여부를 결정한다. view의 가시성을 결정하는 `visibility`속성과는 다르다.**
+ **`enabled`속성으로 설정한 view가 비활성화 되면 사용자가 알아볼 수 있도록 default 스타일로 view가 설정된다.**
	+ 만약 `background` 또는 `textcolor`가 설정되어 있다면 view가 비활성화 되어도 default 스타일이 적용되지 않는다.
	+ 활성화/비활성화 상태에 대한 스타일을 설정하고 싶다면 background : `StateListDrawable`/ text : `ColorStateList` 속성을 사용하면 된다.

#### Snackbar
+ **database를 삭제하는 것과 같은 작업이 완료되었음을 사용자에게 알리기 위한 방법으로 `Snackbar`가 있다.**
+ **`Snackbar`는 화면의 아래에 메세지로 동작에 대한 짧은 feedback을 제공한다.**
+ **`Snackbar`는 시간이 끝나거나, 사용자가 다른 상호작용을 하면 사라진다.**
```kotlin
sleepTrackerViewModel.showSnackBarEvent.observe(this, Observer {
	if (it == true) {
	Snackbar.make(
		requireActivity().findViewById(android.R.id.content),
		getString(R.string.cleared_message),
		Snackbar.LENGTH_SHORT
	).show()
	sleepTrackerViewModel.doneShowingSnackbar()
	}
})
```
+ **`Snackbar`구현 코드**
+ **`Toast`와 비슷하다**
