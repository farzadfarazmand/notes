## Room with RxJava2
The Room persistence library provides an abstraction layer over SQLite to allow for more robust database access while harnessing the full power of SQLite

### Add Dependencies
```
room_version = "2.1.0-rc01"

implementation "androidx.room:room-runtime:$room_version"
kapt "androidx.room:room-compiler:$room_version"
implementation "androidx.room:room-rxjava2:$room_version"

```

### Basic Definitions

* **@Entity**

  Representation of table and columns becomes very easy, you just have to annotate “@Entity” to a class and name of the class becomes table name and, data members becomes the name of the columns. “@Entity” class represent an entity in a table.
  ```kotlin
  @Entity(tableName = "tasks")
  data class Habit(@PrimaryKey
                  @ColumnInfo(name = "title") val title: String)
  ```
  
* **“@Dao” — Data Access Object**

  An Interface where we put all our SQL queries. We don’t require to write whole queries now, we just need to make a method and annotate with specific annotations like @Insert, @Delete, @Query(SELECT FROM *)
  ```kotlin
  @Dao
  interface TaskDao {

    @Insert(onConflict = OnConflictStrategy.IGNORE)
    fun insert(task: Task): Completable

    @Update(onConflict = OnConflictStrategy.IGNORE)
    fun update(task: Task): Completable

    @Query("SELECT * FROM task_table ORDER BY task_timestamp ASC")
    fun getAllTasks(): Flowable<List<Task>>

  }
  ```

* **@Database**

  We need to create an abstract class (Room class) which extends RoomDatabase. It is a database layer over the SQLite           database; this helps us in all the work which we use to do in SQLiteOpenHelper class. Room helps us with the compile-time     checking of SQL statements; we need only a single instance for the whole app.
  ```java
  @Database(entities = {Task.class}, version = 1)
  public abstract class DatabaseHelper extends RoomDatabase {

      private static final String DATABASE_NAME = "remindMe_database";
      private static DatabaseHelper instance;

      public abstract TaskDao taskDao();

      public synchronized static DatabaseHelper getInstance(Context context) {
          if (instance == null) {
              instance = Room.databaseBuilder(context.getApplicationContext(),
                      DatabaseHelper.class, DATABASE_NAME)
                      .build();
          }

          return instance;
      }
  }
  ```
  **we cant define a private constructor in Database class**

### Some Tips About Different RxJava Operator
**Maybe:**
* If there is not any data in your database and the query return no row Maybe will complete.
* When there is already an entry of ModelClass object in your database, Maybe will trigger onSuccess and it will complete.

**Single:**
* If there is not any data in your database and the query return no row Single will trigger onError(EmptyResultSetException.class).
* When there is already an entry of ModelClass object in your database, Single will trigger onSuccess.

**Flowable:**
* If there is not any data in your database and the query return no row the Flowable will not emit, neither onNext, nor onError.
* When there is already an entry of ModelClass object in your database, Flowable will trigger onNext.
* Now if again you add new data or update your previous inserted object then the Flowable object will emit automatically, allowing you to update the UI based on the latest data.

### Tips and Tricks

* _**Observable behavior is same as Flowable and Room supports Observable from`2.0.0-beta01`.**_

* _**you are not allowed to change the return type from List to anything else inside the Dao. I'd assume Room doesn't know how to deal with other return types. Take the List and cast/convert it into your desired type outside of the Dao.**_
  
* _**SQLScout is a plugin for Android Studio and IntelliJ IDEA that provides support for SQLite databases**_
