android 4.x 集训计划 11.12
===================

## Service概述

服务是一种应用组件，它能在后台执行长时间的操作，可以不提供一个用户界面。另一个应用组件可以启动一个服务，该服务将继续运行在后台，即使用户切换到另一个应用。
此外，一个应用组件可以绑定到一个服务上和该服务交互，甚至进行进程间的通信。例如一个服务可以处理网络事务处理、播放音乐、执行文件I/O操作或与一个内容提供者进行交互，以上所有这一切都在后台进行。

##Service两种形式

* started（已启动）

> * 定义：

    当一个应用组件(例如一个activity)通过调用startService()启动一个服务，该服务就是"已启动"状态。服务一旦启动后，它能长期运行在后台，即使调用它的应用组件已经被销毁了，它还能在后台继续进行。

> * 应用场景：

    一般来说，一个已启动的服务执行一个单一的操作，并不返回一个结果给调用者，例如，该服务可以通过网络下载或上传文件，当这些操作完成后，服务就应当自行停止。

* bound（被绑定）

> * 定义：

    当一个应用组件通过调用bindService()将自己绑定到一个服务上，该服务就处于"已绑定"的状态。

> * 应用场景: 

    一个"已绑定"状态的服务可以提供一个C/S的接口，允许应用组件与服务进行交互，发送请求和获取结果，
甚至可以通过IPC技术，跨进程完成这些操作。只要另一个应用组件绑定到该服务，该"已绑定"服务就能运行。多个应用组件可以一次绑定到该服务，但当所有的应用组件与该服务解除绑定后，该服务就被销毁了。

* both started and bound

#### Note:Service也运行在主线程（UI线程）里面，所以为了防止ANR（应用程序无响应），对于一些费时的阻塞操作（如网络，IO等）要开启一个子线程来完成。

## Service的基本使用

要创建一个服务，我们必须创建一个Service的子类(或者已经存在的子类的子类)。在我们的具体实现中，我们需要覆盖重写一些回调方法来适当处理服务生命周期的重要信息，为应用组件提供一套机制来绑定到该服务上。需要重写的回调方法有以下几个最重要:

+ onStartCommand()

当另一个组件（例如一个Activity）通过调用startService()请求服务应该启动了，系统会调用onStartCommand()方法。一旦该方法被执行，服务就处于"已启动"的状态，能长期在后台运行。如果我们实现了该方法，当服务的工作已经完成，我们就必须调用stopSelf()或stopService()来停止服务。(如果我们只想提供绑定功能，我们就不必实现覆写该方法。)

+ onBind()

当另一个应用组件通过调用bindService()方法，想与服务实现绑定（例如实现RPC的功能），系统就会调用该方法。在实现该方法中，我们必须提供一个接口返回一个IBinder接口，这样客户端可以使用这个接口与服务进行通信。我们经常需要实现该方法，但如果我们不运行绑定，那么我们就返回null即可。

+ onCreate()

当服务第一次被创建，执行一次性的安装程序(在该服务调用onStartCommand()或onBind()之前)，系统会调用该方法。如果服务已经处于运行状态，那么该方法就不会被调用。

+ onDestroy()

当服务不在使用并且即将被销毁，系统会调用该方法。我们的服务应该首先这个方法，去清理任何资源，例如线程、注册的监听器、接收者等。这个方式是该服务接收到的最后一个调用方法。

如果一个组件通过调用startService()启动服务(会导致去调用onStartCommand())，然后服务就一直处于运行状态，知道它自己调用stopSelf()停止自己，或者另一个方法调用stopService()停止掉该服务。

Android Promote Plan by TcXiaoyi Co. Ltd
