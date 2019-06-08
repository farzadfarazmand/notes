## RxJava2 in Android Development

###  What is Reactive Programming
Reactive Programming is basically event-based asynchronous programming.
Everything you see is an asynchronous data stream, which can be observed and an action will be taken place when it emits values.

_**RxJava is Java implementation of Reactive Extension**_

_**RxAndroid is specific to Android Platform with few added classes on top of RxJava. More specifically, Schedulers are introduced in RxAndroid**_

### Basic Definations
RxJava is all about two key components: Observable and Observer

* Observable: Observable is a data stream that do some work and emits data.

* Observer: Observer is the counter part of Observable. It receives the data emitted by Observable.

* Subscription: The bonding between Observable and Observer is called as Subscription. There can be multiple Observers subscribed to a single Observable.

* Operator / Transformation: Operators modifies the data emitted by Observable before an observer receives them.

* Schedulers: Schedulers decides the thread on which Observable should emit the data and on which Observer should receives the data i.e background thread, main thread etc.

### Add Dependencies
```
// RxJava
implementation 'io.reactivex.rxjava2:rxjava:2.1.9'
// RxAndroid
implementation 'io.reactivex.rxjava2:rxandroid:2.0.1'
```

### Disposable
Disposable is used to dispose the subscription when an Observer no longer wants to listen to Observable. In android disposable are very useful in avoiding memory leaks.

Let’s say you are making a long running network call and updating the UI. By the time network call completes its work, if the activity / fragment is already destroyed, as the Observer subscription is still alive, it tries to update already destroyed activity. In this case it can throw a memory leak. So using the Disposables, the un-subscription can be when the activity is destroyed.

_**CompositeDisposable**_ -> Can maintain list of subscriptions in a pool and can dispose them all at once.
Usually we call compositeDisposable.clear() in onDestroy() method, but you can call anywhere in the code.

### Operators
RxJava Operators allows you manipulate the data emitted by Observables. Basically, operators tells Observable, how to modify the data and when to emit the data. Using the operators you can modify, merge, filter or group the data streams

All the operators are categorized depending on the kind of work it do. Some operators are used to _**Create Observables**_. The operators like _create_, _just_, _fromArray_, _range_ creates an Observable.

Some operators such as _debounce_, _filter_, _skip_, _last_ are used to _**filter**_ the data emitted by an Observable.

The operators like _buffer_, _map_, _flatMap_, _switchMap_, _compose_ creates an Observable by _**transform**_ the data emitted by another Observable.

[You can find complete list of operators here!](http://reactivex.io/documentation/operators.html#categorized "Operators List")


example :
```java
Observable.range(1, 20)
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .filter(new Predicate<Integer>() {
                    @Override
                    public boolean test(Integer integer) throws Exception {
                        return integer % 2 == 0;
                    }
                })
                .map(new Function<Integer, String>() {
                    @Override
                    public String apply(Integer integer) throws Exception {
                        return integer + " is even number";
                    }
                })
                .subscribe(new Observer<String>() {
                    @Override
                    public void onSubscribe(Disposable d) {   
                    }
 
                    @Override
                    public void onNext(String s) {
                        Log.d(TAG, "onNext: " + s);
                    }
 
                    @Override
                    public void onError(Throwable e) {
                    }
 
                    @Override
                    public void onComplete() {
                        Log.d(TAG, "All numbers emitted!");
                    }
                });
```
output is :
```
onNext: 2 is even number
onNext: 4 is even number
onNext: 6 is even number
onNext: 8 is even number
onNext: 10 is even number
onNext: 12 is even number
onNext: 14 is even number
onNext: 16 is even number
onNext: 18 is even number
onNext: 20 is even number
All numbers emitted!
```
more examples [here](https://www.androidhive.info/RxJava/rxjava-operators-introduction/#all-operators)


### Types of Observables & Observers

1. Observable & Observer

   Observable is probably most used observable among all. Observable can emit one or more items.

2. Single & SingleObserver

   Single always emits only one value or throws an error. The same job can be done using Observable too with a single emission    but Single always makes sure there is an emission. A use case of Single would be making a network call to get response as      the response will be fetched at once.

3. Maybe & MaybeObserver

   Maybe observable may or may not emits a value. This observable can be used when you are expecting an item to be emitted        optionally.
 
 4. Completable & CompletableObserver
 
    Completable observable won’t emit any data instead it notifies the status of the task either success or failure. This         observable can be used when you want to perform some task and not expect any value. A use case would be updating some data     on the server by making PUT request.
   
 5. Flowable & Observer

    Flowable observable should be used when an Observable is generating huge amount of events/data than the Observer can           handle. As per doc, Flowable can be used when the source is generating 10k+ events and subscriber can’t consume it all.
 
 
### Transform Operators
In short, Map, FlatMap, ConcatMap and SwitchMap applies a function or modifies the data emitted by an Observable.

* Consider using Map operator where there is an offline operations needs to be done on emitted data. As explained in the article, we got something from server but that doesn’t fulfils our requirement. In that case, Map can be used to alter the emitted data.
* Choose FlatMap when the order is not important. Let’s say you are building a Airline Ticket Fair app that fetches the prices of each airline separately and display on the screen. For this both FlatMap and ConcatMap can be used. But if the order is not important and want to send all the network calls simultaneously, I would consider FlatMap over ConcatMap. If you consider ConcatMap in this scenario, the time takes to fetch the prices takes very longer time as the ConcatMap won’t make simultaneous calls in order to maintain item order.
* SwitchMap is best suited when you want to discard the response and consider the latest one. Let’s say you are writing an Instant Search app which sends search query to server each time user types something. In this case multiple requests will be sent to server with multiple queries, but we want to show the result of latest typed query only. For this case, SwitchMap is best operator to use.
* Another use case of SwitchMap is, you have a feed screen in which feed is refreshed each time user perform pulldown to refresh. In this scenario, SwitchMap is best suited as it can ignores the older feed response and consider only the latest request.

for more detail about each operator and examples, see [this](https://www.androidhive.info/RxJava/map-flatmap-switchmap-concatmap/)

### Filter Operators
* Buffer :
  gathers items emitted by an Observable into batches and emit the batch instead of emitting one item at a time.

* Debounce 
  operators emits items only when a specified timespan is passed. This operator is very useful when the Observable is rapidly   emitting items but you are only interested in receiving them in timely manner.

* Concat 
  operator combines output of two or more Observables into a single Observable. Concat operator always maintains the             sequential execution without interleaving the emissions. So the first Observables completes its emission before the second     starts and so forth if there are more observables.
  
* Merge
  also merges multiple Observables into a single Observable but it won’t maintain the sequential execution.
  
for more detail about each operator and examples, see [this](https://www.androidhive.info/RxJava/rxjava-operators-buffer-debounce/) and [this](https://www.androidhive.info/RxJava/rxjava-operators-concat-merge/)
