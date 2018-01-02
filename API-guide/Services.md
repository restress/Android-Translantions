### Services

Service(服务)是一个可以在后台长期运行的应用组件，并且并不提供用户界面。别的应用组件可以开启一个服务，即使用户切换了应用，这个服务仍然可以在后台持续运行。除此之外，绑定了服务的组件还可以与它相互交流甚至实现进程间通信（IPC）。例如，一个服务可以在后台处理网络请求，播放音乐，处理文件流，和Content provider交互。

有三种不同类型的服务：

- 前台服务Foreground 前台服务是用来对用户做一些用户可见的操作的。例如，一个音频app使用前台服务用来播放音轨。前台服务必须要有一个状态栏图标status bar icon。即使用户没有和app交互，前台服务也会持续运行。

- 后台服务Background 后台服务是用户看不到的操作。比如，一个app可以使用服务在后台压缩存储空间。注意：如果你的目标API高于26，那么你的系统会在你的app不在前台的时候，对其后台服务有所限制。在大部分情况，你最好使用一个[scheduled job](https://developer.android.com/topic/performance/scheduling.html)

- 绑定服务Bound 一个应用组件可以通过调用bindService()来绑定一个服务。一个绑定服务可以提供客户-服务端接口用于和服务交互，发送请求，接收请求，甚至实现进程间通信（IPC）。一个绑定的服务会一直运行，直到另一个组件和它绑定。一个服务可以同时绑定多个组件，但是一旦他们都解绑了，那么服务就会被销毁。

  ​

虽然这个文档在讨论如何开启服务（独立运行）和绑定服务，但是你的服务可以同时开启（独立运行）和绑定。这完全取决对于你是否实现了回调函数用于开启服务的onStartCommand()和用于绑定服务的onBind()。

如果不考虑你的应用是你的应用是启动、绑定还是都有，任何应用组件（甚至从一个单独的应用中）都可以使用这个服务，使用方法就像任何组件使用activity一样（通过使用Intent启动）。然而，你可以在manifest文件中声明服务是private以防止外部应用引用该服务。具体可以查看Declaring the sevice in the Manifest

注意：服务一般在其进程的主线程中运行。除非被单独定义，服务并不会创建它自己的线程，或者在单独的进程中运行。如果你的服务需要运行耗时操作或者占用大量CPU资源的操作，比如MP3的回放，网络连接，你应该在服务中创建一个新的线程来完成他们。通过使用新的线程，你可以降低ANR（Application Not Responding）错误，同时应用的主线程仍然是专用于用户和app交互的。



#### 基础

---

使用service还是使用新线程？

一个服务只是在后台运行的组件，即使用户没有和app交互，因此你可以在需要的时候创建一个service。

如果你想要在主线程之外操作，但是只想要在用户和app交互的时候运行，那么你就可以创建一个子线程。例如，如果你想要在activity运行的时候播放音乐，那么你可以在onCreate()方法中创建子线程，在onStart()方法中运行它，并且在onStop()方法中停止运行。同时你还可以考虑AsyncTask和HandlerThread来取代传统的线程。更多信息可以查看[Precessing and Thread](https://developer.android.com/guide/components/processes-and-threads.html#Threads)

需要注意的是，如果你开启了service，那么它默认在主线程中运行，因此如果你要在service中运行耗时操作或者占用大量CPU资源的操作你应该创建一个子线程，

---

要创建一个service，需要创建或者使用Service的子类。在执行时，需要覆盖一些回调函数并且处理service的主要生命周期，以及提供组件绑定service的机制。以下是最重要的需要被覆盖的回调方法：

onStartCommand():

系统会在调用了startService()之后调用它，当另一个组件要求service被启用的时候。当这个方法执行之后，service就会被启动并且在后台单独运行。如果你覆盖了这个，那么你就需要在完成任务后使用stopSelf()和stopService()来停止service。如果你只想绑定服务，那么就无需重写这个方法。

onBind():

系统会在组件想要绑定服务的时候调用了bindService()方法之后调用这个方法。如果你继承了这个方法，那么你就必须通过返回一个IBinder来提供客户端用户和service交流的接口。这个方法一般都要重写。但是如果你不希望被绑定，那么你可以返回一个null。

onCreate():

系统会在service建立之后立马调用这个方法（在onStartCommand()和onBind()方法之前），如果service已经运行了，那么这个方法不会被调用。

onDestroy():

当service不再使用或者被销毁之后会调用这个方法。你的service需要在这个方法中清理所有资源，比如线程，注册的listener或者receiver。这是service最后接收到的调用。

如果组件使用startService()来启动service，那么就需要使用stopSelf()（或者别的组件调用stopService()来停止这个service。

如果一个组件调用了bindService()来创建service，但是没有调用onStartCommand()，那么这个service只会在组件绑定它的时候运行。在service被解绑之后，系统会自动销毁它。

android系统只有在运存很少的时候才会销毁service，因为它得为用户聚焦的活动来节省资源。如果一个service绑定了一个有用户聚焦的activity，那么它就不太可能被销毁。如果是foreground前台服务，也基本不可能被销毁。如果service被启动且长时间运行，那么系统就会随着时间增长自动降低它在后台的地位，那么它就极有可能被销毁。如果系统把你的service销毁了，那么它会在资源足够的时候被重启，但是这仍然取决于onStartCommand()的返回值。更多信息，查看[Processes and Threading](https://developer.android.com/guide/components/processes-and-threads.html)

在接下来的部分中，你将会看到如何创建startService()和bindService()方法，以及如何在其他组件中调用它们。

#### 在Manifest中声明Service

---

必须要在manifest文件中声明service，就像声明activity和其他组件那样。

需要在<application>下面声明<service>

```xml
<manifest ... >
  ...
  <application ... >
      <service android:name=".ExampleService" />
      ...
  </application>
</manifest>
```

service更多属性查看[service](https://developer.android.com/guide/topics/manifest/service-element.html)

在service标签里面还可以添加其他属性，比如说开启service需要的权限，以及service在什么进程中运行，android:name是唯一一个强制属性，规定了service的类名。在你发布自己的应用之后，需要保持名字不变以防止因为明确的intent依赖的service操作导致代码崩溃。更多查看，[不能变的东西Things that Cannot Change](https://android-developers.googleblog.com/2011/06/things-that-cannot-change.html)

注意：为了保证app稳定，请确保在开启service的时候使用明确的intent，并且不要为你的service声明intent filter。使用不明确的intent开启service会导致稳定性比较危险，因为你不确定你的service是否一定会响应这个intent，用户也不知道这个service是否开启。在android5.0（API 21）以上，不明确的intent会抛出异常。

你可以通过使用android:exported属性来使你的service只在本应用中可用。这可以有效防止其他app调用你的service，即使使用了一个明确的intent也不行。

注意：用户可以看见他们设备商运行着什么service。如果他们看见了一个不信任的service，他们会停止service。为了防止你的service被用户停止，你最好添加android:description属性在manifest文件service标签里。在描述中，写一个简单的service介绍，告知用户service在干什么，有什么好处。

#### 创建已开启的service|Creating a started service

----

一个已开启的service是指被其他组件通过startService()方法开启的service，当然，这会调用service的onStartCommand()方法。

当一个service被启动之后，它就有独立于组件的生命周期。即使开启它的组件已经被销毁，service会在后台独立运行。因此，service可以调用stopSelf()方法来停止service，或者别的组件通过调用stopService()来停止service。

例如，假设一个activity需要在线上数据库获得一些数据。那么这个activity可以通过startService()开启一个伴随service将数据下载保存下来。service就会在onStartCommand()方法中接收到这个intent，进行联网，数据库操作。等到操作结束之后，service就会自己停止且被销毁。

注意：service默认是在当前应用进程的主线程中。如果你的service执行耗时或者耗资源的操作同时用户也要对当前应用进行交互，那么service就会影响应用的表现。为了防止这种事情发生，最好在service中重新开一个线程。

通常，在创建一个已启动的service，你可以继承两个类。

Service：

这是所有service的基类。当你继承了这个类之后，要记得给service创建一个新线程。因为默认在主线程工作，service会很影响app的表现。

IntentService:

这是Servie的一个子类，使用了一个工作线程一个一个地处理所有开启需求。如果你不要求你的service同时处理多个需求，那么这是最好的选择。重写onHandleIntent()方法，来处理开启需求。

接下来的部分是告诉你如何扩写上述class



#### 继承IntentService类|Extending the IntentService class

因为大部分的已开启service不会同时处理多个需求（有可能会导致比较危险的多线程的情况），因此最好继承IntentService类

IntentService类做以下这些事情：

- 首先创建一个处理所有onStartCommand()中带来的intents默认的工作线程，不同于应用的主线程。
- 创建一个工作队列，一次一个Intent，传给onHandleIntent()方法，因此你不必担心多个子线程的情况。
- 在所有开启需求被处理之后会自动停止service，因此你不需要调用stopService()
- 提供一个默认的返回null的onBind()方法
- 提供一个默认的onStartCommand()方法用于传递intent给工作队列，接着传送给onHandleIntent()方法

重写onHandleIntent()方法来完成用户需求。然而你还是需要提供一个service的小构造函数。

这是一个IntentService的继承实例：

```java
public class HelloIntentService extends IntentService {

  /**
   * A constructor is required, and must call the super IntentService(String)
   * constructor with a name for the worker thread.
   */
  public HelloIntentService() {
      super("HelloIntentService");
  }

  /**
   * The IntentService calls this method from the default worker thread with
   * the intent that started the service. When this method returns, IntentService
   * stops the service, as appropriate.
   */
  @Override
  protected void onHandleIntent(Intent intent) {
      // Normally we would do some work here, like download a file.
      // For our sample, we just sleep for 5 seconds.
      try {
          Thread.sleep(5000);
      } catch (InterruptedException e) {
          // Restore interrupt status.
          Thread.currentThread().interrupt();
      }
  }
}
```

唯一需要的就是一个构造函数和一个onHandleIntent()方法。

如果你还想要重写其他回调函数，例如onCreate(),onStartCommand()或者onDestroy()，要确认调用super方法以保证IntentService的正常运行，

例如，onStartCommand()一定要返回一个默认值，因为这决定了Intent怎么传递给onHandleInetnt()

```java
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
    Toast.makeText(this, "service starting", Toast.LENGTH_SHORT).show();
    return super.onStartCommand(intent,flags,startId);
}
```

除了onHandleIntent()之外,另外一个不需要调用super的函数就是onBind()。当然，你只有在这个service允许绑定的时候才需要继承这个函数。

在接下来的部分中，如果你想要同时处理多个需求，你可以选择继承Service。

#### 继承Service | Extending the Service class

---

使用IntentService会使你开启一个service很容易。但是如果你想要处理多线程的任务，那么你就需要继承Service了。

作为对比，接下来的代码会展示拥有相同功能的继承了Service的代码。就是对于每一个开启任务，都是在一个工作线程中逐个实现的。

```java
public class HelloService extends Service {
  private Looper mServiceLooper;
  private ServiceHandler mServiceHandler;

  // Handler that receives messages from the thread
  private final class ServiceHandler extends Handler {
      public ServiceHandler(Looper looper) {
          super(looper);
      }
      @Override
      public void handleMessage(Message msg) {
          // Normally we would do some work here, like download a file.
          // For our sample, we just sleep for 5 seconds.
          try {
              Thread.sleep(5000);
          } catch (InterruptedException e) {
              // Restore interrupt status.
              Thread.currentThread().interrupt();
          }
          // Stop the service using the startId, so that we don't stop
          // the service in the middle of handling another job
          stopSelf(msg.arg1);
      }
  }

  @Override
  public void onCreate() {
    // Start up the thread running the service.  Note that we create a
    // separate thread because the service normally runs in the process's
    // main thread, which we don't want to block.  We also make it
    // background priority so CPU-intensive work will not disrupt our UI.
    HandlerThread thread = new HandlerThread("ServiceStartArguments",
            Process.THREAD_PRIORITY_BACKGROUND);
    thread.start();

    // Get the HandlerThread's Looper and use it for our Handler
    mServiceLooper = thread.getLooper();
    mServiceHandler = new ServiceHandler(mServiceLooper);
  }

  @Override
  public int onStartCommand(Intent intent, int flags, int startId) {
      Toast.makeText(this, "service starting", Toast.LENGTH_SHORT).show();

      // For each start request, send a message to start a job and deliver the
      // start ID so we know which request we're stopping when we finish the job
      Message msg = mServiceHandler.obtainMessage();
      msg.arg1 = startId;
      mServiceHandler.sendMessage(msg);

      // If we get killed, after returning from here, restart
      return START_STICKY;
  }

  @Override
  public IBinder onBind(Intent intent) {
      // We don't provide binding, so return null
      return null;
  }

  @Override
  public void onDestroy() {
    Toast.makeText(this, "service done", Toast.LENGTH_SHORT).show();
  }
}
```

正如你所见，代码多了。

但是你却可以在onStartCommand()中处理你的每一个需求，也可以同时处理多个任务。虽然例子没有那么写，但是你完全可以为每一个任务写一个新的线程立即执行，而不需要等待之前的任务结束。

需要注意的是onStartCommand()必须要返回一个intergar，这个intergar是一个用来描述在系统销毁service之后系统该如何操作的。默认的IntentService会为你处理，但你可以修改它。onStartCommand()一定得是以下几个常数：

[START_NOT_STICKY](https://developer.android.com/reference/android/app/Service.html#START_NOT_STICKY) 

系统在onStartCommand()返回之后销毁service，除非这儿有等待的intent，否则不要重新创建service。这是最安全的选择，防止在不必要或者应用可以简单重启未完成工作的时候运行service。

[START_STICKY](https://developer.android.com/reference/android/app/Service.html#START_STICKY)

系统在onStartCommand()返回之后销毁service，会重新创建service并且调用onStartCommand()，但是并不会把上次的intent再次传送过来。而是使用一个空的intent来调用onStartCommand()方法除非有在等待的intent才会被传送过来。这适合那些不执行命令但是却独立运行等待任务的媒体播放器。

[START_REDELIVER_INTENT](https://developer.android.com/reference/android/app/Service.html#START_REDELIVER_INTENT)

系统在onStartCommand()返回之后销毁service，会使用上次的intent重新创建service并且调用onStartCommand(),等待的intent会依次执行。这很适合那些需要立刻重启的任务，比如是下载文件。

更多信息查看每个参数的相关文档。

#### 开启service|Starting Service

---

你可以通过在activity或者别的应用组件将Intent传递给startService()或者startForegroundService()来开启一个service。Android系统会调用service的onStartCommand()方法并且传递Intent。

注意：如果你的目标API在26以上，那么系统在创建后台服务有限制，除非当前应用在前台运行。如果app需要创建前台服务，那么app应该调用StartForegroundService()。这个方法创建的是后台服务，但是它会告诉系统这个service想要到前台来。一旦service被创建之后，service必须要在五秒内调用startForeground()方法。

例如，一个activity可以开启例子中的service通过使用startService()方法传递intent

```java
Intent intent = new Intent(this, HelloService.class);
startService(intent);
```



















