android 4.x 集训计划 11.12
===================



## Service概述


> A Service is an application component that can perform long-running operations in the background and does not provide a user interface. Another application component can start a service and it will continue to run in the background even if the user switches to another application. Additionally, a component can bind to a service to interact with it and even perform interprocess communication (IPC). For example, a service might handle network transactions, play music, perform file I/O, or interact with a content provider, all from the background.

##Service两种形式

* started
> * 定义：A service is "started" when an application component (such as an activity) starts it by calling startService(). Once started, a service can run in the background indefinitely, even if the component that started it is destroyed. 
> * 应用场景：a started service performs a single operation and does not return a result to the caller. For example, it might download or upload a file over the network. When the operation is done, the service should stop itself.

* bound
> * 定义：A service is "bound" when an application component binds to it by calling bindService(). 
> * 应用场景:A bound service offers a client-server interface that allows components to interact with the service, send requests, get results, and even do so across processes with interprocess communication (IPC). A bound service runs only as long as another application component is bound to it. Multiple components can bind to the service at once, but when all of them unbind, the service is destroyed.

* both started and bound

####Note:Service也运行在主线程（UI线程）里面，所以为了防止ANR（应用程序无响应），对于一些费时的阻塞操作（如网络，IO等）要开启一个子线程来完成。

## Service的基本使用
> To create a service, you must create a subclass of Service (or one of its existing subclasses). In your implementation, you need to override some callback methods that handle key aspects of the service lifecycle and provide a mechanism for components to bind to the service.

+ onStartCommand()
The system calls this method when another component, such as an activity, requests that the service be started, by calling startService(). Once this method executes, the service is started and can run in the background indefinitely. If you implement this, it is your responsibility to stop the service when its work is done, by calling stopSelf() or stopService(). (If you only want to provide binding, you don't need to implement this method.)

+ onBind()
The system calls this method when another component wants to bind with the service (such as to perform RPC), by calling bindService(). In your implementation of this method, you must provide an interface that clients use to communicate with the service, by returning an IBinder. You must always implement this method, but if you don't want to allow binding, then you should return null.

+ onCreate()
The system calls this method when the service is first created, to perform one-time setup procedures (before it calls either onStartCommand() or onBind()). If the service is already running, this method is not called.

+ onDestroy()
The system calls this method when the service is no longer used and is being destroyed. Your service should implement this to clean up any resources such as threads, registered listeners, receivers, etc. This is the last call the service receives.



Android Promote Plan by TcXiaoyi Co. Ltd
