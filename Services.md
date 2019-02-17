# Services
* one of the application components.
* for long running operations.
* no support for UI.
* it continues to run in the background even if the user switches to another application.
* a component can bind to a service to interact with it.
* it can even perform IPC.
* runs in the main thread of its hosting process.
* does not create its own thread and does not run in a separate process unless you specify otherwise.  

**What if we CPU intensive task or blocking operations with services?**  
We should create a new thread dedicated for services and ANR may be thrown frequently.

</br>

#### Where do we use them?
  * network transaction.
  * play music.
  * file I/O access etc.

</br>

## Different types of services
|Type|Description|
|:--|:--|
|Foreground Services|<ul><li>performs some operation that is noticeable to the user.</li><li>It should show a **notification**.</li><li>continues running even when the user isn't interacting with the app. </li><li>e.g. playing music.</li><ul>|
|Background Services|<ul><li>performs an operation that isn't directly noticed by the user.</li><li>e.g. service to compact its storage</li></ul>|
|Bound Services|<ul><li>service that can be bound by an application component.</li><li>```bindService()``` is used to bind to a service.</li><li>offers a client-server interface to send request and get results.</li><li>enables IPC: can send requests from another process.</li><li>Multiple components can bind to the service at once</li><li>servide is destroyed when all components unbind.</li><ul>|

> **Started Services** :  
  Another variant of services which are made to run indefinitely in the background. These are **STARTED** by some app components and continues to run and does not get killed like **Bound services**.

</br>

**Do we choose a **THREAD** or **SERVICE** to do something? Both runs in background, right?**

|Thread|Service|
|:--|:--|
|If we want **UI interaction** while doing a task.|If we want no UI interaction|
|Highly coupled with UI and its lifecycle. If activity killes, thread runs in this activity gets killed too.|No coupling with UI and its lifecycle.|
|To run tasks for short time.|For long running tasks.|
|Used for blocking operations. (User waits for something to be finished)|Not meant for blocking operations|
|e.g. playing a music in the background while an activity is running.|e.g. if we want to play music without an activity or even when the app is in the background.|


</br>

**It's confused! But still Service runs within a thread right?**  
Ya. Everything runs in thread. Activity runs in a main Thread. By default, Service runs in main thread. But **Service provides us provisions to do long running operations using thread**. 

</br>
</br>

## I want to make a Service Now. 
* Create a class and extend it with ```Service``` or ```IntentService``` class or its subclasses available.
* Override some callback methods to handle its lifecycle.
* Declare service in Manifest.

</br>

**Following are some callbacks.** Not all of them are mandatory to implement.  
1. ```onStartCommand()``` :  
   * Invoked when we call method ```startService()``` to start a service.
   * should return any of the following integer values to indicate what system  should do about services in case a service gets killed:
     * START_NOT_STICKY:
     * START_STICKY:
     * START_REDELIVER_INTENT:


2. ```onBind()``` :  
   * Invoked when we call ```bindService()``` to bind to a service running already.
   * When we bind to an activity we should in turn get an object to communicate with Service. This object is called **IBinder** and should returned in this callback.
   * **If we want to make a service to which no other components should be connected, return NULL**. That's how we make it restricted.

3. ```onCreate()``` :  
   * Invoked when the service is **initially** created.

4. ```onDestroy()``` :  
   * Invoked when the service is no longer used and is being destroyed.

</br>

**Following are some methods we use to manipulate Services.**
1. ```startService()```: 
   * to start a service.
   * If service is started with ```startService()```, service is said to be **STARTED SERVICE** and continues to run indefinitely util it is stopped explicitly.
   * it invokes callback ```onStartCommand()```.

2. ```bindService()``` :  
   * to bind to a service **running already**.
   * But it can be used **to start a service too and such services runs only as long as the component is bound to it.**

2. ```stopSelf()```:  
   * to stop a running service from within it.

3. ```stopService()``` :  
   * to stop a running service from another app component.

</br>


## Which classes to use extend to create Service?
1. ```Service``` : 
   * base class for all services.
   * performs task on main thread by default and it can slow the performance of activity.
   * create and use new thread to avoid this.
   * use it if we want to run multiple requests concurrently - multithreading.
   * it does not have any logic implemented with it to create a worker thread, queueing multiple requests etc and we need manage it.

2. ```IntentService``` :  
   * subclass of ```Service```.
   * it does not run on main thread and runs on a dedicated worker thread instead.
   * use it **if we want to execute requests one at a time**. Hence all the consecutive intents will go into the message queue for the worker thread and will execute sequentially and don't have to worry about multi threading..
   * ```Intent``` is used to start a service.
   * ```onHandleIntent()``` callback is invoked when each request ```Intent``` is received to start request.
   * gets stopped after all of the start requests are handled.
   * provides a default implementation of ```onBind()``` that returns ```null```.
   * Provides a default implementation of onStartCommand() that sends the intent to the work queue and then to your onHandleIntent() implementation.

</br>

## Now I want to create a service extending ```IntentService```. But how?
* Extend ```IntentService```
* provide a constructor.
* implement ```onHandleIntent()```.


</br>
</br>
</br>

## References
1. Services : [Android developer portal](https://developer.android.com/guide/components/services#java)
