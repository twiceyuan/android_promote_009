android 4.x 集训计划 11.12
===================

## Service概述

> A Service is an application component that can perform long-running operations in the background and does not provide a user interface. Another application component can start a service and it will continue to run in the background even if the user switches to another application. Additionally, a component can bind to a service to interact with it and even perform interprocess communication (IPC). For example, a service might handle network transactions, play music, perform file I/O, or interact with a content provider, all from the background.

服务是一种应用组件，它能在后台执行长时间的操作，可以不提供一个用户界面。另一个应用组件可以启动一个服务，该服务将继续运行在后台，即使用户切换到另一个应用。
此外，一个应用组件可以绑定到一个服务上和该服务交互，甚至进行进程间的通信。例如一个服务可以处理网络事务处理、播放音乐、执行文件I/O操作或与一个内容提供者进行交互，以上所有这一切都在后台进行。

##Service两种形式

* started（已启动）

> * 定义：A service is "started" when an application component (such as an activity) starts it by calling startService(). Once started, a service can run in the background indefinitely, even if the component that started it is destroyed. 

    当一个应用组件(例如一个activity)通过调用startService()启动一个服务，该服务就是"已启动"状态。服务一旦启动后，它能长期运行在后台，即使调用它的应用组件已经被销毁了，它还能在后台继续进行。

> * 应用场景：a started service performs a single operation and does not return a result to the caller. For example, it might download or upload a file over the network. When the operation is done, the service should stop itself.

    一般来说，一个已启动的服务执行一个单一的操作，并不返回一个结果给调用者，例如，该服务可以通过网络下载或上传文件，当这些操作完成后，服务就应当自行停止。

* bound（被绑定）

> * 定义：A service is "bound" when an application component binds to it by calling bindService(). 

    当一个应用组件通过调用bindService()将自己绑定到一个服务上，该服务就处于"已绑定"的状态。

> * 应用场景: A bound service offers a client-server interface that allows components to interact with the service, send requests, get results, and even do so across processes with interprocess communication (IPC). A bound service runs only as long as another application component is bound to it. Multiple components can bind to the service at once, but when all of them unbind, the service is destroyed.

    一个"已绑定"状态的服务可以提供一个C/S的接口，允许应用组件与服务进行交互，发送请求和获取结果，
甚至可以通过IPC技术，跨进程完成这些操作。只要另一个应用组件绑定到该服务，该"已绑定"服务就能运行。多个应用组件可以一次绑定到该服务，但当所有的应用组件与该服务解除绑定后，该服务就被销毁了。

* both started and bound

#### Note:Service也运行在主线程（UI线程）里面，所以为了防止ANR（应用程序无响应），对于一些费时的阻塞操作（如网络，IO等）要开启一个子线程来完成。

## Service的基本使用

> To create a service, you must create a subclass of Service (or one of its existing subclasses). In your implementation, you need to override some callback methods that handle key aspects of the service lifecycle and provide a mechanism for components to bind to the service.

    要创建一个服务，我们必须创建一个Service的子类(或者已经存在的子类的子类)。在我们的具体实现中，我们需要覆盖重写一些回调方法来适当处理服务生命周期的重要信息，为应用组件提供一套机制来绑定到该服务上。需要重写的回调方法有以下几个最重要:

+ onStartCommand()

The system calls this method when another component, such as an activity, requests that the service be started, by calling startService(). Once this method executes, the service is started and can run in the background indefinitely. If you implement this, it is your responsibility to stop the service when its work is done, by calling stopSelf() or stopService(). (If you only want to provide binding, you don't need to implement this method.)

当另一个组件（例如一个Activity）通过调用startService()请求服务应该启动了，系统会调用onStartCommand()方法。一旦该方法被执行，服务就处于"已启动"的状态，能长期在后台运行。如果我们实现了该方法，当服务的工作已经完成，我们就必须调用stopSelf()或stopService()来停止服务。(如果我们只想提供绑定功能，我们就不必实现覆写该方法。)

+ onBind()
The system calls this method when another component wants to bind with the service (such as to perform RPC), by calling bindService(). In your implementation of this method, you must provide an interface that clients use to communicate with the service, by returning an IBinder. You must always implement this method, but if you don't want to allow binding, then you should return null.

当另一个应用组件通过调用bindService()方法，想与服务实现绑定（例如实现RPC的功能），系统就会调用该方法。在实现该方法中，我们必须提供一个接口返回一个IBinder接口，这样客户端可以使用这个接口与服务进行通信。我们经常需要实现该方法，但如果我们不运行绑定，那么我们就返回null即可。

+ onCreate()
The system calls this method when the service is first created, to perform one-time setup procedures (before it calls either onStartCommand() or onBind()). If the service is already running, this method is not called.

当服务第一次被创建，执行一次性的安装程序(在该服务调用onStartCommand()或onBind()之前)，系统会调用该方法。如果服务已经处于运行状态，那么该方法就不会被调用。

+ onDestroy()
The system calls this method when the service is no longer used and is being destroyed. Your service should implement this to clean up any resources such as threads, registered listeners, receivers, etc. This is the last call the service receives.

当服务不在使用并且即将被销毁，系统会调用该方法。我们的服务应该首先这个方法，去清理任何资源，例如线程、注册的监听器、接收者等。这个方式是该服务接收到的最后一个调用方法。

If a component starts the service by calling startService() (which results in a call to onStartCommand()), then the service remains running until it stops itself with stopSelf() or another component stops it by calling stopService().

如果一个组件通过调用startService()启动服务(会导致去调用onStartCommand())，然后服务就一直处于运行状态，知道它自己调用stopSelf()停止自己，或者另一个方法调用stopService()停止掉该服务。

Android Promote Plan by TcXiaoyi Co. Ltd
