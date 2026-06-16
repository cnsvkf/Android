
# Room DB

## 개념

- Android에서 SQLite 쉽게 사용

- 어노테이션 기반 라이브러리

- 앱에서 테이블 형태 데이터를 저장해야할 때

-> __예시__

    앱을 껐다 켜도 데이터가 남아야 할 때

    인터넷이 없어도 이전 데이터를 보여줘야 할 때

    서버 요청을 줄이고 싶을 때

    즐겨찾기, 읽음 여부, 임시저장 같은 앱 내부 상태가 필요할 때

    채팅 메시지, 알림, 검색 기록처럼 로컬 보관이 필요한 데이터가 있을 때 등

- Data Store와의 차이

| 구분    | DataStore                                 | Room                                                   |
| ----- | ----------------------------------------- | ------------------------------------------------------ |
| 목적    | 작은 값 저장                                   | 구조화된 데이터 저장                                            |
| 저장 형태 | key-value 또는 Proto 객체                     | SQLite 테이블                                             |
| 주 사용처 | 토큰, 로그인 상태, 설정값                           | 게시글 목록, 채팅 메시지, 알림 목록, 즐겨찾기                            |
| 예시    | `accessToken`, `isAutoLogin`, `themeMode` | `ProjectEntity`, `MessageEntity`, `NotificationEntity` |
| 대체 대상 | SharedPreferences 대체                      | SQLite 직접 사용 대체                                        |

---
# 기능(어노테이션)

| 어노테이션       | 주로 붙는 형태                        | 이유                                              |
| ----------- | ------------------------------- | ----------------------------------------------- |
| `@Dao`      | `interface` 또는 `abstract class` | SQL 메소드를 Room이 대신 구현해야 해서                       |
| `@Entity`   | `data class`                    | DB 테이블의 “데이터 한 줄”을 표현하기 좋아서                     |
| `@Database` | `abstract class`                | RoomDatabase를 상속하고, DAO를 꺼내는 메소드를 Room이 구현해야 해서 |


| 기능                                 | 역할                                | 자주 쓰는 정도 |
| ---------------------------------- | --------------------------------- | -------: |
| `@Entity`                          | Kotlin data class를 DB 테이블로 만듦     |    매우 높음 |
| `@PrimaryKey`                      | 테이블의 고유 ID 컬럼 지정                  |    매우 높음 |
| `@ColumnInfo`                      | DB 컬럼 이름, 기본값 등을 지정               |       높음 |
| `@Dao`                             | DB 접근 메소드들을 모아두는 인터페이스            |    매우 높음 |
| `@Database`                        | Room DB의 중심 클래스(어떤 테이블을 DB에 넣을 지) 지정                |    매우 높음 |
| `Room.databaseBuilder()`           | 실제 DB 객체 생성                       |    매우 높음 |
| `@Insert`                          | 데이터 추가                            |    매우 높음 |
| `@Update`                          | 데이터 수정                            |       높음 |
| `@Delete`                          | 데이터 삭제                            |       높음 |
| `@Query`                           | 직접 SQL로 조회/삭제/수정 실행               |    매우 높음 |
| `OnConflictStrategy`               | 같은 PrimaryKey 데이터 충돌 시 처리 방식 지정   |       높음 |
| `@Upsert`                          | 없으면 insert, 있으면 update            |       높음 |
| `suspend DAO`                      | 코루틴으로 DB 작업 실행                    |    매우 높음 |
| `Flow` 조회                          | DB 데이터가 바뀌면 UI 자동 갱신              |    매우 높음 |
| `@Transaction`                     | 여러 DB 작업을 하나의 작업처럼 묶음             |       높음 |
| `@Relation`                        | 테이블 간 관계 조회                       |       중간 |
| `@Embedded`                        | 객체 안의 값을 컬럼처럼 펼침                  |       중간 |
| `@Junction`                        | 다대다 관계 연결 테이블 표현                  |       중간 |
| `ForeignKey`                       | 테이블 간 참조 규칙 지정                    |       중간 |
| `Index`                            | 특정 컬럼 검색 성능 향상                    |       중간 |
| `@TypeConverter`                   | Room이 저장 못 하는 타입을 저장 가능 타입으로 변환   |       높음 |
| `Migration`                        | DB 구조 변경 시 기존 데이터 유지              |       높음 |
| `AutoMigration`                    | 간단한 DB 변경을 자동 마이그레이션              |       중간 |
| `fallbackToDestructiveMigration()` | 마이그레이션 실패 시 DB 삭제 후 재생성           |    낮음~중간 |
| `Database Callback`                | DB 생성/열림 시 초기 작업 실행               |       중간 |
| `PagingSource`                     | 많은 데이터를 페이지 단위로 조회                |    중간~높음 |
| `@RawQuery`                        | 직접 만든 SQL 쿼리 실행                   |       낮음 |
| `@DatabaseView`                    | 복잡한 SELECT 결과를 가상의 읽기 전용 테이블처럼 사용 |    낮음~중간 |
| `@Fts4`                            | 전문 검색, 검색어 기반 빠른 검색 지원            |       낮음 |
| `inMemoryDatabaseBuilder()`        | 테스트용 메모리 DB 생성                    |       중간 |
| `exportSchema`                     | DB 스키마 파일 저장 여부 설정                |       중간 |
| `clearAllTables()`                 | 모든 테이블 데이터 삭제                     |       중간 |

---

### @Database

- abstract로 강제하는 기능

    1. @Dao를 반드시 제공 
    2. 직접 DB를 만들지 못하게 한다.
        - abstract은 직접 객체 생성 불가 -> __DB은 많은 작접이 필요하기 때문__ -> 그래서 _Room_ 을 통해 DB 생성

    3. RoomDatabase 전용 기능 상속하게 강제

        -> _예시_

            DB 열기
            DB 닫기
            트랜잭션 실행
            쿼리 실행
            내부 SQLite 관리

- 앱에서 사용할 _____로컬 DB의 중심 클래스_____


| 어노테이션       | 역할                              |
| ----------- | ------------------------------- |
| `@Entity`   | DB 테이블을 만든다                     |
| `@Dao`      | 테이블에 접근하는 메소드를 만든다              |
| `@Database` | Entity와 Dao를 연결해서 실제 DB 객체를 만든다 |

---

### @Entity

- DB 테이블 생성 시 사용

- 생성된 테이블 보는 방법

    1. Android Studio의 Database Inspector로 실제 DB 테이블을 보는 방법

    2. 앱 코드에서 조회해서 로그/UI로 확인하는 방법


            @Entity 작성
            ↓
            @Database의 entities에 등록
            ↓
            앱 실행
            ↓
            RoomDatabase 생성
            ↓
            DB가 실제로 열림
            ↓
            Room이 테이블 생성
            ↓
            Database Inspector에서 확인 가능
-> Entity 파일만 만들면 끝이 아니라 ____@Database에 등록____ 되어 있어야 한다.

-> ___예시___

```kotlin
@Entity(tableName = "github_profiles")
data class GitHubProfileEntity(

    // DB의 기본키로 사용할 값
    @PrimaryKey
    val id: Long,

    // DB 컬럼 이름을 github_id로 지정한다.
    @ColumnInfo(name = "github_id")
    val githubId: String
)
```



---

### @PrimaryKey

- 테이블 기본키 지정

- Room DB 테이블에서 “이 한 줄(row)을 구분하는 고유한 값” 을 지정

```kotlin
@Entity(tableName = "users")
data class UserEntity(

    // 이 값이 users 테이블의 기본키가 된다.
    // 이름은 중복 가능하기에 id를 PrimaryKey로 지정
    @PrimaryKey
    val userId: Long,

    val name: String
)
```

-> _만들어지는 데이터 테이블_

### users 테이블
| userId | name |
| -----: | ---- |
|      1 | 철수   |
|      2 | 영희   |
|      3 | 민수   |


---

### @ColumInfo

- DB 컬럼 이름을 지정한다. 

- @SerializedName와 __차이__

```kotlin
    @SerializedName → 서버 JSON 이름과 Kotlin 변수 이름을 연결
    
    @ColumnInfo     → Room DB 테이블 컬럼 이름과 Kotlin 변수 이름을 연결
```

-> 예시

```kotlin
@Entity(tableName = "users")
data class UserEntity(

    // 이 값은 users 테이블의 기본키가 된다.
    @PrimaryKey
    val userId: Long,

    // DB 컬럼 이름은 "user_name"으로 저장된다.
    // Kotlin에서는 name이라는 변수명으로 사용한다.
    @ColumnInfo(name = "user_name")
    val name: String
)
```
: 코드 내 사용

```kotlin
//객체변수.객체안의값
// user라는 객체 안에 들어있는 name 값을 꺼낸다
// user 객체는 나중에 따로 만든 것
val userName = user.name
```

-> ____되는 이유____ : Kotlin 코드에서는 ____DB 컬럼명이 아니라 data class의 변수명으로 접근____ 하기 때문

-> __중요 부분__

```kotlin
@ColumnInfo(name = "user_name")
val name: String
```

| 위치            | 이름          |
| ------------- | ----------- |
| Room DB 컬럼 이름 | `user_name` |
| Kotlin 변수 이름  | `name`      |


---

### @Dao

- Room에서 ____DB 테이블에 접근하는 메소드들을 모아두는 곳____

- DB 접근 메소드들을 모아두는 인터페이스에 붙힌다. 

-> _구조 예시_

```kotlin
@Entity(tableName = "users")
data class UserEntity(
    @PrimaryKey
    val userId: Long,

    val name: String
)
```
### users 테이블
| userId | name |
| -----: | ---- |
|      1 | 철수   |
|      2 | 영희   |
|      3 | 민수   |


### 여기에 접근하는 메소드 묶음이 @Dao

-> _예시_

```kotlin
@Dao
interface UserDao {

    // users 테이블에 UserEntity 데이터를 추가한다.
    @Insert
    suspend fun insertUser(user: UserEntity)

    // users 테이블의 모든 데이터를 조회한다.
    @Query("SELECT * FROM users")
    fun getUsers(): Flow<List<UserEntity>>

    // userId가 같은 행을 찾아서 데이터를 수정한다.
    @Update
    suspend fun updateUser(user: UserEntity)

    // userId가 같은 행을 찾아서 데이터를 삭제한다.
    @Delete
    suspend fun deleteUser(user: UserEntity)
}
```

---

### @Insert

- Room DB에서 데이터를 테이블에 추가할 때 사용하는 DAO 어노테이션

기본

| userId | name |
| -----: | ---- |

-> 코드 실헹

```kotlin
userDao.insertUser(
    UserEntity(
        userId = 1,
        name = "철수"
    )
)
```

추가

| userId | name |
| -----: | ---- |
|      1 | 철수   |


--- 

### @Query (Room에서)


    결과 생성 시, 
    DB 데이터가 새로 생성되는 게 아니라,
    Room이 DB에 이미 저장된 행(row)을 읽어서 UserEntity 객체 리스트로 만들어서 반환

- Room에서 SQL 문장을 직접 작성해서 DB에 요청하는 어노테이션

- @Insert 와의 차이

    - @Insert : 객체를 테이블에 _____추가_____
    - @Query : 직접 SQL로 원하는 데이터를 _____가져오거나 수정하거나 삭제_____

 #### @Query 내 지원 SQL 문장

| 들어올 수 있는 SQL | 역할     | Room에서 자주 쓰는 정도       |
| ------------ | ------ | --------------------- |
| `SELECT`     | 데이터 조회 | 매우 자주 사용              |
| `INSERT`     | 데이터 추가 | 가능하지만 보통 `@Insert` 사용 |
| `UPDATE`     | 데이터 수정 | 조건 수정할 때 자주 사용        |
| `DELETE`     | 데이터 삭제 | 조건 삭제할 때 자주 사용        |

\+ 세부 조건 조절 메소드 등이 있음

---

### OnConflicStrategy

- __같은 PrimaryKey를 가진 데이터__ 가 이미 있을 때 어떻게 할지 정하는 기능

-> 예시

```kotlin
@Dao
interface UserDao {

    // users 테이블에 사용자 1명을 추가한다.
    // 같은 userId 데이터가 이미 있으면 기존 데이터를 새 데이터로 교체한다.
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertUser(user: UserEntity)
}
```



| 옵션        | 의미                   |
| --------- | -------------------- |
| `REPLACE` | 기존 데이터 삭제 후 새 데이터 저장 |
| `IGNORE`  | 이미 있으면 새 데이터 무시      |
| `ABORT`   | 충돌 나면 작업 중단          |

---

### Upsert

= insert + update 기능


1. 데이터가 없으면 _추가_
2. 데이터가 있으면 _수정_

-> 예시

```kotlin
@Dao
interface UserDao {

    // userId가 없는 새 사용자면 insert 한다.
    // userId가 이미 존재하는 사용자면 update 한다.
    @Upsert
    suspend fun upsertUser(user: UserEntity)
}
```

: ___user 객체 전체 추가___

\+ 기존 방식

```kotlin
if (이미 있음) {
    update()
} else {
    insert()
}
```

---

### Flow DB 변경 자동 감지

Flow<List<UserEntity>>가 이 기능

---

### @Transaction

- DB 작업을 하나의 작업으로 묶는 기능
    - 기능이 하나라도 실패 시, 안한 것으로 되돌림

-> _예시_

```kotlin
@Dao
interface UserDao {

    // userId에 해당하는 사용자를 삭제한다.
    @Query("DELETE FROM users WHERE userId = :userId")
    suspend fun deleteUserById(userId: Long)

    // userId에 해당하는 사용자의 게시글을 삭제한다.
    @Query("DELETE FROM posts WHERE writerId = :userId")
    suspend fun deletePostsByUserId(userId: Long)

    // 사용자 삭제와 게시글 삭제를 하나의 작업으로 묶는다.
    // 중간에 하나라도 실패하면 전체 작업을 되돌릴 수 있다.
    @Transaction
    suspend fun deleteUserAndPosts(userId: Long) {
        deletePostsByUserId(userId)
        deleteUserById(userId)
    }
}
```

: 게시글 삭제는 성공했는데 사용자 삭제가 실패하면 DB가 이상한 상태가 될 수 있다. 

@Transaction은 이런 상황을 줄여준다.

---

### @Relation(테이블 관계)

- 기능 : 1:N 관계
    - 객체끼리 연결되는 게 아니라, ___테이블의 컬럼 값끼리 비교___ 해서 연결


            UserEntity 1명
            ↓
            PostEntity 여러 개

-> 예시

#### user 테이블
| userId | name |
| -----: | ---- |
|      1 | 철수   |
|      2 | 영희   |

#### post 테이블

| postId | writerId | title  |
| -----: | -------: | ------ |
|    101 |        1 | 철수 글 1 |
|    102 |        1 | 철수 글 2 |
|    103 |        2 | 영희 글 1 |

```kotlin
import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "users")
data class UserEntity(

    // users 테이블의 기본키이다.
    // 각 사용자를 구분하는 고유한 값이다.
    @PrimaryKey
    val userId: Long,

    // 사용자 이름 컬럼이다.
    val name: String
)

------
import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "posts")
data class PostEntity(

    // posts 테이블의 기본키이다.
    // 각 게시글을 구분하는 고유한 값이다.
    @PrimaryKey
    val postId: Long,

    // 이 게시글을 작성한 사용자의 userId이다.
    // users.userId와 연결되는 값이다.
    val writerId: Long,

    // 게시글 제목이다.
    val title: String
)

------
import androidx.room.Embedded
import androidx.room.Relation

data class UserWithPosts(

    // 부모 객체를 그대로 포함한다.
    // UserEntity의 컬럼들이 이 객체 안에 펼쳐져 들어온다.
    @Embedded
    val user: UserEntity,

    // user.userId와 post.writerId가 같은 PostEntity들을 자동으로 찾아서 리스트로 묶는다.
    @Relation(
        parentColumn = "userId",
        entityColumn = "writerId"
    )
    val posts: List<PostEntity>
)
```
| 속성                          | 의미                           |
| --------------------------- | ---------------------------- |
| `parentColumn = "userId"`   | 부모 테이블 `users`에서 기준이 되는 컬럼   |
| `entityColumn = "writerId"` | 자식 테이블 `posts`에서 부모와 연결되는 컬럼 |

    User 조회 후 → 반환 타입이 UserWithPosts인 걸 Room이 확인 → UserWithPosts 안의 @Relation을 확인 → posts 테이블을 추가 조회 → UserWithPosts(user, posts)로 조립해서 반환

---

### @Embedded

- 객체 안의 값을 테이블 컬럼처럼 펼쳐서 저장할 때 쓴다.

-> 예시

```kotlin
data class Address(

    // 도시 이름이다.
    val city: String,

    // 상세 주소다.
    val detail: String
)
```

```kotlin
@Entity(tableName = "users")
data class UserEntity(

    // users 테이블의 기본키다.
    @PrimaryKey
    val userId: Long,

    // 사용자 이름이다.
    val name: String,

    // Address 객체의 city, detail 값을 users 테이블 컬럼으로 펼친다.
    @Embedded
    val address: Address
)
```

: ___users 테이블___ 결과

| userId | name | city | detail |
| -----: | ---- | ---- | ------ |
|      1 | 철수   | 광주   | 첨단동    |

---

###  @TypeCoverter

- Room은 기본적으로 String, Int, Long, Boolean 같은 단순 타입은 저장할 수 있다.

    - 이런 타입은 바로 저장 불가능

            LocalDateTime
            List<String>
            Enum
            객체 타입

-> 예시

```kotlin
class StringListConverter {

    // List<String>을 DB에 저장 가능한 String으로 바꾼다.
    @TypeConverter
    fun fromList(value: List<String>): String {
        return value.joinToString(",")
    }

    // DB에 저장된 String을 다시 List<String>으로 바꾼다.
    @TypeConverter
    fun toList(value: String): List<String> {
        return value.split(",")
    }
}
```

---

### Migration
- 구조
    - __@Entity 테이블을 Migration로 수정 후 @Entity를 관리 중인 @DataBase에도 반영__

- Migration은 기존 DB를 새 구조로 바꾸는 코드


        테이블 추가 = 무엇을 바꿀 건지
        Migration = 기존 사용자 DB에 그 변경을 어떻게 적용할 건지

1. Entity 추가만 함
→ 새로 설치한 사람은 정상
→ 기존 설치자는 DB 구조 불일치 문제 발생 가능

2. Migration 추가함
→ 기존 설치자의 DB도 안전하게 새 구조로 변경됨

-> ____예시____

```kotlin
// DB 버전 1에서 2로 올라갈 때 실행할 마이그레이션이다.

// 여기서 1은 이전 DB 버전, 2는 새 DB 버전

// val에 Migration을 저장하는 이유는 Room에게 나중에 넘겨줄 “마이그레이션 작업 객체”를 이름 붙여 보관하기 위해서
val MIGRATION_1_2 = object : Migration(1, 2) {

    // 기존 users 테이블에 age 컬럼을 추가한다.

    // db은 Room이 넘겨준 SQLite DB 객체
    override fun migrate(db: SupportSQLiteDatabase) {
        db.execSQL("ALTER TABLE users ADD COLUMN age INTEGER NOT NULL DEFAULT 0")
    }
}
```

```kotlin
// DB version 업그레이드 반영(일일히 설정해야함)
@Database(
    entities = [UserEntity::class],
    version = 1
)
abstract class AppDatabase : RoomDatabase()
```


__SupportSQLiteDatabase__ 는 Room이 넘겨주는 실제 SQLite 데이터베이스 조작 객체

    execSQL()로 Room이 넘겨준 기존 DB 객체에 SQL문을 실행해서,
    버전 1 구조의 DB를 버전 2 구조에 맞게 수정

---

### ForeignKey

- ___테이블 간 연결 규칙을 강제___ 하는 기능

        @Entity의 foreignKeys 설정
                ↓
        Room이 SQLite 테이블 생성 SQL을 만듦
                ↓
        posts.writerId는 users.userId에 있어야 한다는 규칙이 DB에 저장됨
                ↓
        insert/update/delete 할 때 SQLite가 자동 검사함
                ↓
        규칙 위반이면 저장 실패

-> _예시_

```kotlin
// foreginKeys : 이 테이블의 컬럼이 다른 테이블 컴럼을 반드시 가르키도록 강제하는 규칙
@Entity(
    tableName = "posts",

    // posts 테이블이 다른 테이블을 참조한다는 규칙을 설정한다.
    foreignKeys = [
        ForeignKey(

            // 부모 테이블이다.
            // 여기서는 UserEntity, 즉 users 테이블을 참조한다.
            entity = UserEntity::class,

            // 부모 테이블에서 기준이 되는 컬럼이다.
            // users 테이블의 userId를 기준으로 삼는다.
            parentColumns = ["userId"],

            // 자식 테이블에서 부모를 가리키는 컬럼이다.
            // posts 테이블의 writerId가 users.userId를 참조한다.
            childColumns = ["writerId"],

            // 부모 데이터가 삭제될 때 자식 데이터를 어떻게 처리할지 정한다.
            // CASCADE는 사용자가 삭제되면 그 사용자의 게시글도 같이 삭제한다.
            onDelete = ForeignKey.CASCADE
        )
    ]
)
data class PostEntity(

    // posts 테이블의 기본키다.
    // 게시글 하나를 구분하는 고유 id다.
    @PrimaryKey
    val postId: Long,

    // 게시글 작성자의 id다.
    // foreignKey 설정 때문에 users.userId와 연결된다.
    val writerId: Long,

    // 게시글 제목이다.
    val title: String
)
```

#### 없으면 생기는 문제

1. ___사용자가 삭제됐지만, 게시물은 남으면 안되므로___ foreginKeys로 규칙을 만들어 사용자 삭제 시 게시물도 삭제

2. 임시 코드 or 서버에서 이상한 값

-> 예시
#### user 테이블
| userId | name |
| -----: | ---- |
|      1 | 철수   |
|      2 | 영희   |

-> 이상한 값을 post에 넣음 
-> 하지만 DB입장에서는 저장 가능

| postId | writerId | title |
| -----: | -------: | ----- |
|     10 |      999 | 이상한 글 |

-> ___posts.writerId 값은 users.userId에 실제로 존재해야 한다.___ 라고 ___foreginKeys___ 로 막음

---

### Paging 연동
#### 개념

- ____데이터를 한 번에 많이 가져오면 안좋으므로 사용____

        LazyColumn 화면
            ↓
        collectAsLazyPagingItems()
            ↓
        ViewModel의 Flow<PagingData<Post>>
            ↓
        Repository의 Pager
            ↓
        PagingSource.load()
            ↓
        서버 API 또는 Room DB

Paging 3는 데이터를 한 번에 전부 가져오는 대신, ___PagingSource가 필요한 페이지 단위로 데이터___ 를 가져오고, ___Pager가 그 데이터를 Flow<PagingData<T>> 형태로 흘려보낸다.___ Compose에서는 collectAsLazyPagingItems()로 받아서 LazyColumn에 연결한다. 공식 구조도 Repository layer → ViewModel layer → UI layer로 나뉜다.

\+ Paging이 Repository를 감싸는 게 아니라, ___Repository 안에서 Pager가 PagingSource를 감싼다.___

    Repository
    - Pager를 만든다.
    - PagingSource를 Paging 시스템에 등록한다.
    - ViewModel에 Flow<PagingData<Post>>를 넘긴다.

    PagingSource
    - 실제 서버 요청 방법을 정의한다.
    - load() 안에서 postApi.getPosts(page, size)를 호출한다.

#### 구조

| 요소                           | 의미                   |
| ---------------------------- | -------------------- |
| `params.key`                 | 가져올 페이지 번호 또는 cursor |
| `params.loadSize`            | 이번에 가져오라고 요청된 데이터 개수 |
| `params.placeholdersEnabled` | placeholder 사용 여부    |
| `LoadResult.Page`            | 성공 결과                |
| `LoadResult.Error`           | 실패 결과                |
| `prevKey`                    | 이전 페이지 key           |
| `nextKey`                    | 다음 페이지 key           |


| 매개변수                 | 의미                                        |
| -------------------- | ----------------------------------------- |
| `pageSize`           | 한 번에 가져올 기본 데이터 개수                        |
| `prefetchDistance`   | 끝에서 몇 개 남았을 때 다음 페이지를 미리 가져올지             |
| `enablePlaceholders` | 아직 안 가져온 데이터 자리에 `null` placeholder를 둘지   |
| `initialLoadSize`    | 처음 로딩할 때 가져올 데이터 개수                       |
| `maxSize`            | 메모리에 유지할 최대 아이템 수                         |
| `jumpThreshold`      | 너무 멀리 점프했을 때 기존 페이지를 이어서 로딩하지 않고 새로고침할 기준 |

| 클래스                        | 역할                                                            |
| -------------------------- | ------------------------------------------------------------- |
| `PagingSource<Key, Value>` | 데이터를 실제로 가져오는 클래스                                             |
| `PagingConfig`             | 한 번에 몇 개 가져올지, 언제 다음 페이지를 미리 가져올지 설정                          |
| `Pager`                    | `PagingSource`와 `PagingConfig`를 묶어서 `Flow<PagingData<T>>`를 만듦 |
| `PagingData<T>`            | 페이징된 데이터 묶음                                                   |
| `LazyPagingItems<T>`       | Compose `LazyColumn`에서 쓰기 좋은 형태로 변환된 데이터                      |
| `RemoteMediator`           | 서버 + Room 캐시를 같이 쓸 때 사용                                       |

    Paging 라이브러리
            ↓
    PostPagingSource.load()
            ↓
    postApi.getPosts(page, size)
            ↓
    서버에서 게시글 목록 가져옴
            ↓
    LoadResult.Page(data = posts)
            ↓
    화면 LazyColumn에 표시
#### ___예시___

```kotlin
// 한번에 데이터 가져옴
SELECT * FROM messages
```

-> Paging 사용

```kotlin
import androidx.paging.PagingSource
import androidx.paging.PagingState

// PostPagingSource는 서버에서 게시글을 페이지 단위로 가져오는 클래스다.
//
// Int:
// - 페이지를 구분하는 key 타입이다.
// - 여기서는 1페이지, 2페이지처럼 Int를 사용한다.
//
// Post:
// - 실제로 화면에 보여줄 데이터 타입이다.
class PostPagingSource(
    private val postApi: PostApi
) : PagingSource<Int, Post>() {

    // load()는 Paging 라이브러리가 데이터가 필요할 때 자동으로 호출하는 메소드다.
    //
    // 예:
    // - 처음 화면에 들어왔을 때 호출된다.
    // - 사용자가 아래로 스크롤해서 다음 데이터가 필요할 때 다시 호출된다.
    //
    // params:
    // - Paging이 넘겨주는 로드 요청 정보다.
    // - params.key: 지금 가져올 페이지 번호다.
    // - params.loadSize: 이번에 가져오라고 요청된 데이터 개수다.
    //
    // 반환 타입:
    // - LoadResult<Int, Post>
    // - 성공하면 LoadResult.Page를 반환한다.
    // - 실패하면 LoadResult.Error를 반환한다.
    override suspend fun load(
        params: LoadParams<Int>
    ): LoadResult<Int, Post> {
        return try {
            // params.key가 null이면 첫 로딩이라는 뜻이다.
            // 그래서 기본값으로 1페이지를 사용한다.
            val page = params.key ?: 1

            // PagingConfig에서 정한 pageSize 또는 initialLoadSize가 params.loadSize로 들어온다.
            val size = params.loadSize

            // 서버에 page와 size를 넘겨서 게시글 목록을 가져온다.
            val response = postApi.getPosts(
                page = page,
                size = size
            )

            val posts = response.posts

            // LoadResult.Page는 성공적으로 가져온 페이지 결과다.
            LoadResult.Page(
                // 실제 데이터 목록이다.
                data = posts,

                // 이전 페이지 key다.
                // 현재 page가 1이면 이전 페이지가 없으므로 null이다.
                prevKey = if (page == 1) null else page - 1,

                // 다음 페이지 key다.
                // posts가 비어 있으면 더 이상 가져올 데이터가 없다고 보고 null을 넣는다.
                nextKey = if (posts.isEmpty()) null else page + 1
            )
        } catch (e: Exception) {
            // 서버 에러, 네트워크 에러 등이 발생하면 Error를 반환한다.
            LoadResult.Error(e)
        }
    }

    // getRefreshKey()는 새로고침할 때 어느 페이지부터 다시 가져올지 정하는 메소드다.
    //
    // state:
    // - 현재까지 로드된 페이지 상태다.
    // - 사용자가 현재 어느 위치 근처를 보고 있는지 알 수 있다.
    //
    // 반환 타입:
    // - Int?
    // - 다시 시작할 page key를 반환한다.
    override fun getRefreshKey(
        state: PagingState<Int, Post>
    ): Int? {
        // anchorPosition은 사용자가 최근에 보고 있던 아이템 위치다.
        val anchorPosition = state.anchorPosition ?: return null

        // anchorPosition에 가장 가까운 페이지를 찾는다.
        val closestPage = state.closestPageToPosition(anchorPosition)

        // prevKey가 있으면 prevKey + 1이 현재 페이지다.
        // nextKey가 있으면 nextKey - 1이 현재 페이지다.
        return closestPage?.prevKey?.plus(1)
            ?: closestPage?.nextKey?.minus(1)
    }
}
```
### Repository에서 Pager 만들기
```kotlin
import androidx.paging.Pager
import androidx.paging.PagingConfig
import androidx.paging.PagingData
import kotlinx.coroutines.flow.Flow

class PostRepository(
    private val postApi: PostApi
) {

    // getPagedPosts()는 화면에 보여줄 게시글 페이징 스트림을 만드는 메소드다.
    //
    // 반환 타입:
    // - Flow<PagingData<Post>>
    // - 게시글 데이터가 페이지 단위로 흘러가는 Flow다.
    fun getPagedPosts(): Flow<PagingData<Post>> {
        return Pager(
            // PagingConfig는 페이징 설정이다.
            config = PagingConfig(
                // 한 번에 가져올 기본 데이터 개수다.
                pageSize = 20,

                // 사용자가 끝에 도달하기 전에 미리 다음 페이지를 가져오는 거리다.
                prefetchDistance = 5,

                // 처음 로딩할 때 가져올 데이터 개수다.
                // 설정하지 않으면 기본적으로 pageSize보다 크게 잡힌다.
                initialLoadSize = 40,

                // 아직 로드되지 않은 아이템 자리에 null placeholder를 둘지 여부다.
                enablePlaceholders = false
            ),

            // PagingSource를 새로 만들어주는 함수다.
            // PagingSource는 새로고침이나 invalidate가 발생하면 다시 만들어질 수 있다.
            pagingSourceFactory = {
                PostPagingSource(postApi)
            }
        ).flow
    }
}

```


### viewModel

```Kotlin
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import androidx.paging.PagingData
import androidx.paging.cachedIn
import kotlinx.coroutines.flow.Flow

class PostViewModel(
    private val postRepository: PostRepository
) : ViewModel() {

    // posts는 화면에서 구독할 페이징 데이터 흐름이다.
    //
    // cachedIn(viewModelScope):
    // - ViewModel 범위 안에서 PagingData를 캐싱한다.
    // - 화면 회전 같은 상황에서 같은 데이터를 다시 불필요하게 요청하는 것을 줄인다.
    val posts: Flow<PagingData<Post>> =
        postRepository.getPagedPosts()
            .cachedIn(viewModelScope)
}
```

--- 

### Database Callback

- DB가 처음 만들어졌을 때 초기 데이터를 넣고 싶을 수 있다.

    - 예를 들어 앱 첫 실행 시 기본 카테고리를 넣는 경우

-> _코드_

```kt
val database = Room.databaseBuilder(
    context,
    AppDatabase::class.java,
    "app_database"
)
    // DB가 생성되거나 열릴 때 특정 작업을 실행할 수 있다.
    .addCallback(object : RoomDatabase.Callback() {

        // D가 처음 생성될 때 호출된다.
        override fun onCreate(db: SupportSQLiteDatabase) {
            super.onCreate(db)

            // 초기 데이터 insert 같은 작업을 여기서 실행할 수 있다.
        }
    })
    .build()
```

| 메소드                        | 호출 시점                | 자주 쓰는 용도      |
| -------------------------- | -------------------- | ------------- |
| `onCreate()`               | DB가 **처음 생성될 때 한 번** | 초기 데이터 넣기     |
| `onOpen()`                 | DB가 **열릴 때마다**       | 로그, DB 상태 확인  |
| `onDestructiveMigration()` | DB를 삭제 후 재생성했을 때     | 초기 데이터 복구, 로그 |

--- 

### In-Memory Database

- 테스트 시 실제 DB 파일을 만들지 않고 _____메모리 안에서만 DB를 만들 수 있다._____

1. Kotlin 코드 파일
   - AppDatabase.kt
   - UserEntity.kt
   - UserDao.kt

2. 실제 DB 데이터 파일
   - app_database
   - app_database-shm
   - app_database-wal
: 코드파일은 있지만 ___앱 내에서 DB파일을 저장 안함___

#### 예시
```kt
// 이 DB는 앱 프로세스가 죽으면 사라진다.
// 1. Room에게 "메모리 DB를 만들 Builder를 줘"라고 요청한다.
val builder = Room.inMemoryDatabaseBuilder(
    context,
    AppDatabase::class.java
)

// 2. Builder에 설정을 붙인다.
// 예: addCallback(), allowMainThreadQueries(), setQueryCallback() 등

// 3. build()로 실제 AppDatabase 객체를 만든다.
val database = builder.build()
```

| 부분                          | 역할                         |
| --------------------------- | -------------------------- |
| `Room`                      | Room DB를 생성하는 진입점 객체       |
| `inMemoryDatabaseBuilder()` | 메모리 전용 DB Builder를 만든다     |
| `context`                   | DB 생성에 필요한 Android Context |
| `AppDatabase::class.java`   | 만들 RoomDatabase 클래스        |
| `.build()`                  | 실제 DB 인스턴스를 생성             |
