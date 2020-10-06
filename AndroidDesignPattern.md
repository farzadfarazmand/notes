## Android Design Patterns

A Design Pattern is a general, reusable solution to a commonly occurring problem within a given context.

**Benefits:**

- **Understandable code**: By using Design Pattern, you can make your code understandable to everyone.

- **Code reusability**: By following the Design Pattern, you can make your code reusable

- **Cleaner code:** You can make your code cleaner by decoupling the code and it will make the code easier to understand by using Design Pattern.

  

There are a number of Design Patterns that can be used in Software Development and all these can be classified into the following three categories:

- **Creational Pattern**
- **Structural Pattern**
- **Behavioural Pattern**



### Creational Pattern

The Creational Pattern is used to create some object without showing the logic or the steps that are involved in creating the object. So, every time you want an object, **you need not instantiate the object by using the new operator**. So, this makes the creation of object easier and can be easily created again and again. Some of the examples of Creational Pattern are Builder, Singleton, and Dependency Injection.

- **Builder**

  ```kotlin
  class Laptop(builder: Builder) {
      private val processor: String = builder.processor
      private val ram: String = builder.ram
      private val battery: String = builder.battery
      private val screenSize: String = builder.screenSize
  
      // Builder class
      class Builder(processor: String) {
          var processor: String = processor // this is necessary
  
          // optional features
          var ram: String = "2GB"
          var battery: String = "5000MAH"
          var screenSize: String = "15inch"
  
          fun setRam(ram: String): Builder {
              this.ram = ram
              return this
          }
  
          fun setBattery(battery: String): Builder {
              this.battery = battery
              return this
          }
  
          fun setScreenSize(screenSize: String): Builder {
              this.screenSize = screenSize
              return this
          }
  
          fun create(): Laptop {
              return Laptop(this)
          }
      }
  }
  ```

  Now, **you need not create the object every time**. Just use the below code

  ```kotlin
  Laptop.Builder("i7") // processor is compulsory
      .setRam("8GB")            // this is optional
      .setBattery("6000MAH")    // this is optional
      .create()
  ```

  

- **Singleton**

  There are cases when you need **only one instance of a class**. So, whenever you call the object of the class, then the new object should not be created(only one-time object creation will be there).

  ```kotlin
  object DataManager{
      init {
          println("Singleton class invoked.")
      }
      fun getUserData(): User{
          // some code
      }
  
  }
  // to user singleton class
  fun main(args: Array<String>) {     
      DataManager.getUserData()
      // more code
  }
  ```

  *In Kotlin, there is no need for using private construction or static method to create a singleton class*



- **Dependency Injection**

  in the Dependency Injection pattern, we provide the dependency of a class from outside the class and no dependency will be provided in the same class.

  ```kotlin
  class DataManager(databaseHelper: DatabaseHelper, networkHelper: NetworkHelper) {
      private val databaseHelper: DatabaseHelper = databaseHelper
      private val networkHelper: NetworkHelper = networkHelper
  
      fun someTask() {
           // do some operation with DatabaseHelper and NetworkHelper
      }
  }
  ```

  *So by using the DependencyInjection pattern, you will get the ability to test each class in isolation. But every time you want to use the DatabaseHelper and NetworkHelper, you need to write all these codes. So, to make things easier, we make use of a framework called Dagger*



### Structural Pattern

In this Design Pattern, we are concerned about the structure of the code. Here we follow some particular structural pattern that will help in understanding the code and the working of code just by looking at the structure of the code. Some of the common Structural Pattern used are Adapter, Bridge, Facade, Proxy.



- **Adapter**

  An Adapter is something like a connector that is used to connect two or more incompatible interface.

  ```kotlin
  interface FlightDetailAdapter {
      // get price in indian rupees
      fun getPriceInINR()
      
      // get time in Indian Standard Time
      fun getTimeInIST()
  }
  
  class AirIndiaFlightDetailsAdapter: FlightDetailAdapter {
  
      // some AirIndiaApi that gives the time and price according to Indian standards
      var api:AirIndianApi = AirIndiaAPI()
  
      override fun getPriceInINR(): Int {
          // returns the price in INR
          return api.price
      }
  
      override fun getTimeInIST(): String {
          //returns the time in IST
          return api.time
      }
  }
  
  class UnitedAirlinesFlightDetailAdapter: FlightDetailAdapter {
      // some UnitedAirlinesAPI that gives the time and price according to American standards
      var api:UnitedAirlinesAPI = UnitedAirlinesAPI()
  
      override fun getPriceInINR(): Int {
          // returns the price in INR
          return convertUSDToINR(api.price)
      }
  
      override fun getTimeInIST(): String {
          //returns the time in IST
          return convertESTToIST(api.time)
      }
  
      fun convertUSDToINR(price: Int): Int {
          // some logic
      }
  
      fun convertESTToIST(time: String): String {
          // some logic
      }
  }
  
  class FlightView(context: Context) : View(context) {
      // showing flight details according to Indian time and price
      fun showFlightData(fda: FlightDetailAdapter) {
          priceTextView.text = fda.getPriceInINR()
          timeTextView.text = fda.getTimeInIST()
      }
  }
  
  val flightView: FlightView = FlightView()
  
  // for showing AirIndia flight details
  flightView.showFlightData(AirIndiaFlightDetailsAdapter())
  
  val flightView: FlightView = FlightView()
  
  // for showing UnitedAirlines flight details
  flightView.showFlightData(UnitedAirlinesFlightDetailAdapter())
  ```

  *In Android, RecyclerView and PagerAdapter are some of the good examples of the Adapter pattern.*

- **Facade**

  In the Facade pattern, a complicated system is wrapped into a simpler system that will help us in getting the values from the complicated system without having knowledge of how the data is being fetched and returned to the view or the presenter.

  ***For example***, We have one Presenter or View that shows us the users that are stored in the Database. So, the presenter demands the list of users by some DataManager class. The DataManager class deals with various data related things. It can have one NetworkHelper and one DatabaseHelper class. Internally, the DataManager may use the NetworkHelper or DatabaseHelper class to fetch the details, but our view or presenter is less bothered about that. It only needs the list of available users and nothing else. Even the view or presenter is unaware of the fact that there is some NerworkHelper or DatabaseHelper class.



### Behavioural Pattern

Behavioural Pattern mainly tells how the objects of the classes will communicate with each other. These patterns help us in understanding the code in a better way because by viewing the code we can identify the pattern and then we can understand the code in a better way. Some of the behavioural patterns are Chain of Responsibility, Interpreter, Mediator, Observer, Command, Model View Controler, Model View ViewModel, etc.

- **Observer**

  The observer pattern is like one-to-many(or one) dependencies between objects. So, in Android also, when there is a change in some object, then the dependents of that object will be notified about the change and the possible update will be made accordingly in the dependents. This is called an Observer pattern because here we are observing for some change in the object.

  ***For example***, In Android, LiveData is an observable data holder class that enables the use of an observer patte

- **Command**

  In the Command pattern, we give commands and we want our output and nothing else. We are not bothered about who will do our operation to give the desired result. All we want is our things to be done at the right time.

  ***For example***, You have an "Execute" interface and there are two classes that are implementing this "Execute" interface. One class is responsible for writing something in the file and the other class is responsible for appending the contents of a file. So, you can call any of these two classes i.e. the "WriteFile" and "AppendClass" with the help of "Execute" interface.

- **Model View Controller**

  Model View Controller or MVC is an Architectural Design pattern this is used to write an organised code for Android applications. Code following the MVC pattern is divided into three parts:

  - **Model:** It is the place where you write all your Business logic and data state. So, all the data classes of your application are considered as Model.
  - **View:** It is the user interface that a user sees. In Android, the user interface is built with the help of XML. So, all the views that the user sees come under this category.
  - **Controller:** A controller is simply a communication medium between the Model and the View. It takes the user input from the view. It processes the request and it sends the data from the Model to the view.

  Nowadays, we don't use this pattern because it is declared as "**Massive View Controller**" for Android. Instead of MVC, we use MVP and MVVM.

- **Model View Presenter**

  Model View Presenter or MVP is a set of guidelines that are followed to make the code reusable and testable because it decouples the whole code. MVP divides the application into three parts:

  - **Model:** It is the place where you write all your Business logic and data state. So, all the data classes of your application are considered as Model.
  - **View:** It is the user interface that a user sees. In Android, the user interface is built with the help of XML. So, all the views that the user sees come under this category.
  - **Presenter:** The presenter is responsible for giving the data to the view. If view demands some data from the Model, then it is the presenter that will fetch the data from the Model and provide the data to the view to display the required details.

- **Model View ViewModel**

  The Model View ViewModel or MVVC is similar to that of MVC but here the controller is replaced by the ViewModel. The three components of the Model View ViewModel are:

  - **Model:** It is the place where you write all your Business logic and data state. So, all the data classes of your application are considered as Model.
  - **View:** It is the user interface that a user sees. In Android, the user interface is built with the help of XML. So, all the views that the user sees come under this category.
  - **ViewModel:** It is a bridge between the Model and the View. Here most of the user interface logic is stored. This is mainly used to store and manage the UI-related and it resolves one of the biggest problems of data loss that was faced due to screen rotation.