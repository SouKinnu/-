# 四大组件
## Activity
### 什么是 Activity？Activity 的生命周期是什么样的？
- onCreate()：Activity 首次创建时调用，用于初始化组件。
- onStart()：Activity 变得对用户可见时调用。
- onResume()：Activity 开始和用户交互。
- onPause()：Activity 正在停止，通常用于保存未保存的数据或释放资源。
- onStop()：Activity 不再可见。
- onDestroy()：Activity 被销毁前调用。
- onRestart()：Activity 从停止状态回到启动状态。
### Activity 的启动模式有哪些？分别适用的场景是什么？
- standard：默认模式，每次启动都会创建新的实例。适合普通场景。
- singleTop：栈顶已有实例时复用，避免重复创建。适合点击通知栏启动的场景。
- singleTask：整个任务栈中有实例时复用，适合独立页面，如主页。
- singleInstance：始终单实例在独立任务栈，适合工具类或独立任务页面。
### onSaveInstanceState() 和 onRestoreInstanceState() 的作用是什么？
- onSaveInstanceState()：在 Activity 被销毁前（如屏幕旋转）保存数据。
- onRestoreInstanceState()：在 Activity 被重新创建后恢复数据。
### 如何防止 Activity 重复创建？
- 使用 singleTop 或 singleTask 启动模式。
- 在 AndroidManifest.xml 中配置 launchMode。
- 检查 savedInstanceState，以确保 Activity 是全新创建的。
### 如何在 Activity 中处理屏幕旋转？
- 可以在 AndroidManifest.xml 中配置 android:configChanges 属性来阻止重新创建。
- 也可以重写 onConfigurationChanged 方法手动处理配置变化。
- 使用 ViewModel 等存储数据的方式避免数据丢失。
### 什么是 Intent 和 Intent Filter？它们的区别是什么？
- Intent 是一个消息对象，用于在组件之间传递信息。
- Intent Filter 是对 Intent 的过滤器，决定哪些 Intent 可以启动 Activity。
## Service
### 什么是 Service？Service 的作用是什么？
Service 是 Android 四大组件之一，专为长时间在后台执行任务设计的。与 Activity 不同，Service 没有用户界面，一般用于执行后台处理、网络请求、播放音乐等任务，即便用户离开应用界面也能保持运行。
### Service 的生命周期有哪些方法？
- onCreate()：Service 首次创建时调用，用于初始化资源。
- onStartCommand(Intent intent, int flags, int startId)：每次通过 startService() 启动服务时调用，处理任务逻辑。
- onBind(Intent intent)：绑定服务时调用，返回一个 IBinder 对象用于客户端和服务端通信。
- onUnbind(Intent intent)：解绑服务时调用。
- onRebind(Intent intent)：解绑后再次绑定时调用。
- onDestroy()：Service 被停止或销毁时调用，用于释放资源。
### Service 有哪几种类型？它们的区别是什么？
- Started Service：通过 startService() 启动，独立运行，与调用者无关联。调用 stopSelf() 或 stopService() 停止。
- Bound Service：通过 bindService() 绑定，提供客户端和服务端的交互。与绑定组件的生命周期一致，当没有绑定者时会自动停止。
### IntentService 与 Service 的区别？
- IntentService 是 Service 的子类，适合处理异步任务。它的特点是：
    - 自动在工作线程中处理任务，不需要手动创建新线程。
    - 按照队列顺序处理任务，处理完成后自动停止服务。
    - 主要用于短时间后台任务，比如网络请求。
- Service 则需要手动管理线程，一般用于需要持续运行的后台任务。
### Service 与 Thread 的区别？
- Service 适合执行长期后台任务，它在主线程运行，若任务需要异步处理，需手动创建线程。
- Thread 是轻量的执行单位，但它在应用进程结束时会被杀死，适合短时间任务。要保持后台任务的持续性，需结合 Service。
### 如何在 Service 和 Activity 之间进行数据通信？
- 通过广播：使用 BroadcastReceiver 发送和接收广播。
- 通过 Messenger：Messenger 类提供了消息传递的方式，通过 Handler 来处理消息。
- 通过 AIDL（Android Interface Definition Language）：适合跨进程通信，定义接口后，生成代理类来调用方法。
- 通过绑定服务的 Binder：实现 onBind() 方法返回自定义 Binder 对象，直接调用服务方法。
### 如何优雅地停止一个 Service？
- Started Service：调用 stopSelf() 或 stopService() 结束。
- Bound Service：当最后一个绑定者调用 unbindService() 后自动停止，或调用 stopSelf()。
- IntentService：处理完所有任务后自动停止。
## BroadcastReceiver
### 什么是 BroadcastReceiver？有什么作用？
BroadcastReceiver 是 Android 四大组件之一，用于接收并处理系统或应用发出的广播消息。它可以在不同组件之间传递信息或事件。常见的广播事件有：电池电量变化、网络状态变化、应用安装和删除等。广播接收器可以在应用中侦听和响应这些事件。
