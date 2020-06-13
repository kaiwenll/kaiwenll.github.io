## Android四大组件（整理相关知识点）
        Android 开发的四大组件分别是：
	活动（activity），用于表现功能；
	服务（service），后台运行服务，不提供界面呈现；
	广播接受者（Broadcast Receive），勇于接收广播；
	内容提供者（Content Provider），支持多个应用中存储和读取数据，相当于数据库。

### 1.活动（activity）

（1）定义：Activity是Android的四大组件之一。是用户操作的可视化界面；它为用户提供了一个完成操作指令的窗口。当我们创建完毕Activity之后，需要调用setContentView()方法来完成界面的显示；以此来为用户提供交互的入口。在Android App 中只要能看见的几乎都要依托于Activity，所以Activity是在开发中使用最频繁的一种组件。


（2）一个Activity通常就是一个单独的屏幕（窗口）。

（3）Activity之间通过Intent进行通信。

（4）android应用中每一个Activity都必须要在AndroidManifest.xml配置文件中声明，否则系统将不识别也不执行该Activity。在android stdio会自动生成，但eclipse需要自己手动添加

（5）Activity的生命周期
      在Android中会维持一个Activity Stack（Activity栈），当一个新的Activity创建时，它就会放到栈顶，这个Activity就处于运行状态。当再有一个新的Activity被创建后，会重新压人栈顶，而之前的Activity则会在这个新的Activity底下，就像枪梭压入子弹一样。而且之前的Activity就会进入后台。
一个Activity实质上有四种状态：
a.运行中(Running/Active):这时Activity位于栈顶，是可见的，并且可以用户交互。
b.暂停(Paused):当Activity失去焦点，不能跟用户交互了，但依然可见，就处于暂停状态。当一个新的非全屏的Activity或者一个透明的Activity放置在栈顶，Activity就处于暂停状态；这个时候Activity的各种数据还被保持着；只有在系统内存在极低的状态下，系统才会自动的去销毁Activity。
c.停止(Stoped):当一个Activity被另一个Activity完全覆盖，或者点击HOME键退入了后台，这时候Activity处于停止状态。这里有些是跟暂停状态相似的：这个时候Activity的各种数据还被保持着；当系统的别的地方需要用到内容时，系统会自动的去销毁Activity。
d.销毁(Detroyed):当我们点击返回键或者系统在内存不够用的情况下就会把Activity从栈里移除销毁，被系统回收，这时候，Activity处于销毁状态。



### 2.服务(Service)


service（服务）是安卓中的四大组件之一，它通常用作在后台处理耗时的逻辑，与Activity一样，它存在自己的生命周期，也需要在AndroidManifest.xml配置相关信息。



服务（Service)是Android中实现程序后台运行的解决方案，它非常适合去执行那些不需要和用户交互而且还要求长期运行的任务。服务的运行不依赖于任何用户界面，即使程序被切换到后台，或者用户打开了另外一个应用程序，服务仍然能够保持正常运行。

不过需要注意的是，服务并不是运行在一个独立的进程当中的，而是依赖于创建服务时所在的应用程序进程。与某个应用程序进程被杀掉时，所有依赖于该进程的服务也会停止运行。另外.也不要被服务的后台概念所迷惑，实际上服务并不会自动开启线程，所有的代码都是默认运行在主线程当中的。也就是说，我们需要在服务的内部手动创建子线程，并在这里执行具体的任务，否则就有可能出现主线程被阻塞住的情况。

**（1）service用于在后台完成用户指定的操作。**
	service分为两种：
	（a）started（启动）：当应用程序组件（如activity）调用startService()方法启动服务时，服务处于started状态。
	（b）bound（绑定）：当应用程序组件调用bindService()方法绑定到服务时，服务处于bound状态。
**(2)startService()与bindService()区别：**
	(a)started service（启动服务）是由其他组件调用startService()方法启动的，这导致服务的onStartCommand()方法被调用。当服务是started状态时，其生命周期与启动它的组件无关，并且可以在后台无限期运行，即使启动服务的组件已经被销毁。因此，服务需要在完成任务后调用stopSelf()方法停止，或者由其他组件调用stopService()方法停止。
	(b)使用bindService()方法启用服务，调用者与服务绑定在了一起，调用者一旦退出，服务也就终止，大有“不求同时生，必须同时死”的特点。
**(3)开发人员需要在应用程序配置文件中声明全部的service，使用<service></service>标签。**

**(4)Service通常位于后台运行，它一般不需要与用户交互，因此Service组件没有图形用户界面。Service组件需要继承Service基类。Service组件通常用于为其他组件提供后台服务或监控其他组件的运行状态。**

定义：Service是一个专门在后台处理长时间任务的Android组件，它没有UI。它有两种启动方式，startService和bindService。

**这两种启动方式的区别：**
startService只是启动Service，启动它的组件（如Activity）和Service并没有关联，只有当Service调用stopSelf或者其他组件调用stopService服务才会终止。
bindService方法启动Service，其他组件可以通过回调获取Service的代理对象和Service交互，而这两方也进行了绑定，当启动方销毁时，Service也会自动进行unBind操作，当发现所有绑定都进行了unBind时才会销毁Service。

**Service的onCreate回调函数可以做耗时的操作吗？**
不可以，Service的onCreate是在主线程（ActivityThread）中调用的，耗时操作会阻塞UI

**如果需要做耗时的操作，你会怎么做？**
线程和Handler方式
是否知道IntentService，在什么场景下使用IntentService？

 IntentService相比父类Service而言，最大特点是其回调函数onHandleIntent中可以直接进行耗时操作，不必再开线程。其原理是IntentService的成员变量 Handler在初始化时已属于工作线程，之后handleMessage，包括onHandleIntent等函数都运行在工作线程中。
如果对IntentService的了解仅限于此，会有种IntentService很鸡肋的观点，因为在Service中开线程进行耗时操作也不麻烦。我当初也是这个观点，所以很少用IntentService。

    但是IntentService还有一个特点，就是多次调用onHandleIntent函数（也就是有多个耗时任务要执行），多个耗时任务会按顺序依次执行。原理是其内置的Handler关联了任务队列，Handler通过looper取任务执行是顺序执行的。
    
    这个特点就能解决多个耗时任务需要顺序依次执行的问题。而如果仅用service，开多个线程去执行耗时操作，就很难管理。

### 3.广播接受者（Broadcast Receive）


在Android中，广播是一种广泛运用的在应用程序之间传输信息的机制。而广播接收器是对发送出来的广播进行过滤接受并响应的一类组件。可以使用广播接收器来让应用对一个外部时间做出响应。例如，当电话呼入这个外部事件到来时，可以利用广播接收器进行处理。当下载一个程序成功完成时，仍然可以利用广播接收器进行处理。广播接收器不NotificationManager来通知用户这些事情发生了。广播接收器既可以在AndroidManifest.xml中注册，也可以在运行时的代码中使用Context.registerReceive（）进行注册。只要是注册了，当事件来临时，即使程序没有启动，系统也在需要的时候启动程序。各种应用还可以通过使用Context.sendBroadcast（）将它们自己的Intent广播给其他应用程序。

（1）你的应用可以使用它对外部事件进行过滤，只对感兴趣的外部事件(如当电话呼入时，或者数据网络可用时)进行接收并做出响应。广播接收器没有用户界面。然而，它们可以启动一个activity或serice来响应它们收到的信息，或者用NotificationManager来通知用户。通知可以用很多种方式来吸引用户的注意力，例如闪动背灯、震动、播放声音等。一般来说是在状态栏上放一个持久的图标，用户可以打开它并获取消息。

（2）广播接收者的注册有两种方法，分别是程序动态注册（在运行时的代码中使用Context.registerReceive（）进行注册）和AndroidManifest文件中进行静态注册。

（3）动态注册广播接收器特点是当用来注册的Activity关掉后，广播也就失效了。静态注册无需担忧广播接收器是否被关闭，只要设备是开启状态，广播接收器也是打开着的。也就是说哪怕app本身未启动，该app订阅的广播在触发时也会对它起作用。

### 4.内容提供者（Content Provider）

（1）android平台提供了Content Provider使一个应用程序的指定数据集提供给其他应用程序。其他应用可以通过ContentResolver类从该内容提供者中获取或存入数据。
（2）只有需要在多个应用程序间共享数据是才需要内容提供者。例如，通讯录数据被多个应用程序使用，且必须存储在一个内容提供者中。它的好处是统一数据访问方式。
（3）ContentProvider实现数据共享。ContentProvider用于保存和获取数据，并使其对所有应用程序可见。这是不同应用程序间共享数据的唯一方式，因为android没有提供所有应用共同访问的公共存储区。
（4）开发人员不会直接使用ContentProvider类的对象，大多数是通过ContentResolver对象实现对ContentProvider的操作。

（5）ContentProvider使用URI来唯一标识其数据集，这里的URI以content://作为前缀，表示该数据由ContentProvider来管理。


emmm看到就收集我也忘记哪是哪个网的了


面试题：有使用过ContentProvider码？能说说Android为什么要设计ContentProvider这个组件吗？
ContentProvider应用程序间非常通用的共享数据的一种方式，也是Android官方推荐的方式。Android中许多系统应用都使用该方式实现数据共享，比如通讯录、短信等。但我遇到很多做Android开发的人都不怎么使用它，觉得直接读取数据库会更简单方便。

那么Android搞一个内容提供者在数据和应用之间，只是为了装高大上，故弄玄虚？我认为其设计用意在于：
**封装**。对数据进行封装，提供统一的接口，使用者完全不必关心这些数据是在DB，XML、Preferences或者网络请求来的。当项目需求要改变数据来源时，使用我们的地方完全不需要修改。
提供一种跨进程数据共享的方式。
应用程序间的数据共享还有另外的一个重要话题，就是数据更新通知机制了。因为数据是在多个应用程序中共享的，当其中一个应用程序改变了这些共享数据的时候，它有责任通知其它应用程序，让它们知道共享数据被修改了，这样它们就可以作相应的处理。

ContentResolver接口的notifyChange函数来通知那些注册了监控特定URI的ContentObserver对象，使得它们可以相应地执行一些处理。ContentObserver可以通过registerContentObserver进行注册。

**既然是对外提供数据共享，那么如何限制对方的使用呢？**

android:exported属性非常重要。这个属性用于指示该服务是否能够被其他应用程序组件调用或跟它交互。如果设置为true，则能够被调用或交互，否则不能。设置为false时，只有同一个应用程序的组件或带有相同用户ID的应用程序才能启动或绑定该服务。

对于需要开放的组件应设置合理的权限，如果只需要对同一个签名的其它应用开放content provider，则可以设置signature级别的权限。大家可以参考一下系统自带应用的代码，自定义了signature级别的permission：
```java
        <permission android:name="com.android.gallery3d.filtershow.permission.READ"
                    android:protectionLevel="signature" />
 
        <permission android:name="com.android.gallery3d.filtershow.permission.WRITE"
                    android:protectionLevel="signature" />
 
        <provider
            android:name="com.android.gallery3d.filtershow.provider.SharedImageProvider"
            android:authorities="com.android.gallery3d.filtershow.provider.SharedImageProvider"
            android:grantUriPermissions="true"
            android:readPermission="com.android.gallery3d.filtershow.permission.READ"
            android:writePermission="com.android.gallery3d.filtershow.permission.WRITE" />
```

如果我们只需要开放部份的URI给其他的应用访问呢？可以参考Provider的URI权限设置，只允许访问部份URI，可以参考原生ContactsProvider2的相关代码（注意path-permission这个选项）：

```java
        <provider android:name="ContactsProvider2"
            android:authorities="contacts;com.android.contacts"
            android:label="@string/provider_label"
            android:multiprocess="false"
            android:exported="true"
            android:grantUriPermissions="true"
            android:readPermission="android.permission.READ_CONTACTS"
            android:writePermission="android.permission.WRITE_CONTACTS">
            <path-permission
                    android:pathPrefix="/search_suggest_query"
                    android:readPermission="android.permission.GLOBAL_SEARCH" />
            <path-permission
                    android:pathPrefix="/search_suggest_shortcut"
                    android:readPermission="android.permission.GLOBAL_SEARCH" />
            <path-permission
                    android:pathPattern="/contacts/.*/photo"
                    android:readPermission="android.permission.GLOBAL_SEARCH" />
            <grant-uri-permission android:pathPattern=".*" />
        </provider>
```
**ContentProvider接口方法运行在哪个线程中呢？**

ContentProvider可以在AndroidManifest.xml中配置一个叫做* android:multiprocess* 的属性，默认值是false，表示 **ContentProvider** 是单例的，无论哪个客户端应用的访问都将是同一个 **ContentProvider** 对象，如果设为true，系统会为每一个访问该 **ContentProvider** 的进程创建一个实例。

这点还是比较好理解的，那如果我要问每个ContentProvider的操作是在哪个线程中运行的呢（其实我们关心的是UI线程和工作线程）？比如我们在UI线程调用getContentResolver().query查询数据，而当数据量很大时（或者需要进行较长时间的计算）会不会阻塞UI线程呢？

要分两种情况回答这个问题：

ContentProvider和调用者在同一个进程，ContentProvider的方法（query/insert/update/delete等）和调用者在同一线程中；
ContentProvider和调用者在不同的进程，ContentProvider的方法会运行在它自身所在进程的一个Binder线程中。
但是，注意这两种方式在ContentProvider的方法没有执行完成前都会blocked调用者。所以你应该知道这个上面这个问题的答案了吧。

也可以看看CursorLoader这个类的源码，看Google自己是怎么使用getContentResolver().query的。

**ContentProvider是如何在不同应用程序之间传输数据的？**

这个问题点有些深入，大家要对Binder进程间通信机制比较了解。因为之前我们有提到过一个应用进程有16个Binder线程去和远程服务进行交互，而每个线程可占用的缓存空间是128KB这样，超过会报异常。ContentResolver虽然是通过Binder进程间通信机制打通了应用程序之间共享数据的通道，但Content Provider组件在不同应用程序之间传输数据是基于匿名共享内存机制来实现的。有兴趣的可以查看一下老罗的文章Android系统匿名共享内存Ashmem（Anonymous Shared Memory）简要介绍和学习计划。




*作者：goeasyway
链接：https://www.jianshu.com/p/380231307070
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。*
