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
	<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcu1a4e%2Fbtq1KAzkc4e%2FSGWx0e7YJkeycMQerJbLh1%2Fimg.png">
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