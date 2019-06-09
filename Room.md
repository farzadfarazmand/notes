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
      fun insert(task: Task)

      @Update(onConflict = OnConflictStrategy.IGNORE)
      fun update(task: Task)

      @Query("DELETE FROM task_table")
      fun deleteAll()

      @Query("SELECT * FROM task_table ORDER BY task_timestamp ASC")
      fun getAllTasks(): MutableList<Task>

  }
  ```

* **@Database**

  We need to create an abstract class (Room class) which extends RoomDatabase. It is a database layer over the SQLite database; this helps us in all the work which we use to do in SQLiteOpenHelper class. Room helps us with the compile-time checking of SQL statements; we need only a single instance for the whole app.
  ```java
  @Database(entities = {Task.class}, version = 1)
  public abstract class DatabaseHelper extends RoomDatabase {

      private static final String DATABASE_NAME = "remindMe_database";
      private static DatabaseHelper instance;

      private DatabaseHelper() {
      }

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
  
  
  
**SQLScout is a plugin for Android Studio and IntelliJ IDEA that provides support for SQLite databases**
