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
### 广播的类型有哪些？
- 普通广播 (Normal Broadcast)：异步发送，多个接收器同时接收，按系统决定的顺序执行。
- 有序广播 (Ordered Broadcast)：同步广播，按优先级顺序接收，前一个接收器可中止广播传播。
- 粘性广播 (Sticky Broadcast)：即便广播发送后也会被存储，可供后续注册的接收器获取，但已在 API 21 中被废弃。
- 本地广播 (Local Broadcast)：仅限应用内部发送和接收，安全性更高，效率更高。
### 如何注册 BroadcastReceiver？注册方式有哪些？
- 静态注册：在 AndroidManifest.xml 文件中声明 <receiver>，当系统检测到指定广播时会自动触发。适合系统广播或需要在应用关闭时接收的广播。
- 动态注册：在代码中使用 registerReceiver() 方法注册，通常在 Activity 或 Service 中进行，适合只在应用运行期间接收的广播。记得在 onDestroy() 中调用 unregisterReceiver() 取消注册。
### 如何发送广播？
- sendBroadcast(Intent intent)：发送普通广播。
- sendOrderedBroadcast(Intent intent, String receiverPermission)：发送有序广播，并指定接收权限。
- LocalBroadcastManager.getInstance(context).sendBroadcast(intent)：发送本地广播，适用于应用内部通信，安全性更高。
### 有序广播与普通广播的区别是什么？
- 普通广播：广播是异步的，所有接收器几乎同时接收，接收顺序不确定。
- 有序广播：广播是同步的，按优先级顺序逐个接收，优先级高的先接收，接收器可以在 onReceive() 方法中调用 abortBroadcast() 中止后续传播。
### 如何实现广播的安全性？
- 使用 本地广播 (LocalBroadcast) 限制广播的接收范围，仅限应用内部。
- 在发送广播时，通过设置权限 sendBroadcast(Intent intent, String receiverPermission) 来限制接收器的访问权限。
- 使用 动态注册，避免应用关闭后广播被监听。
### BroadcastReceiver 的生命周期是什么样的？
BroadcastReceiver 的生命周期很短，只有在 onReceive() 方法中运行。当 onReceive() 方法结束后，BroadcastReceiver 就会被销毁。因此，不能在 onReceive() 中执行耗时操作，推荐使用 IntentService 等来处理耗时任务。
### 如何在 BroadcastReceiver 中进行耗时操作？
- BroadcastReceiver 的 onReceive() 运行在主线程中，耗时操作可能会导致 ANR（应用无响应）。可以通过以下几种方式进行异步处理：
    - 在 onReceive() 中启动一个 IntentService 或 Service 处理耗时任务。
    - 使用 HandlerThread 或 AsyncTask 执行耗时操作，但这些方式需小心内存泄漏。    
### 动态注册的 BroadcastReceiver 为什么需要手动取消注册？
动态注册的 BroadcastReceiver 是在 Activity 或 Service 的生命周期中注册的，需要在 onDestroy() 或 onStop() 中调用 unregisterReceiver() 取消注册，否则会导致内存泄漏或系统异常。
### 为什么不推荐在 AndroidManifest 中注册隐式广播？
从 Android 8.0 (API 级别 26) 开始，应用不再允许在 AndroidManifest.xml 中注册大多数隐式广播，以减少电量消耗。隐式广播通常会唤醒多个应用并导致资源浪费。可以使用 JobScheduler 或 WorkManager 替代隐式广播来实现延迟任务或后台处理。
### 如何在 BroadcastReceiver 中获取网络状态变化？
- 可以注册一个监听网络变化的 BroadcastReceiver，例如：
    ```
    IntentFilter filter = new IntentFilter();
    filter.addAction(ConnectivityManager.CONNECTIVITY_ACTION);
    registerReceiver(new NetworkChangeReceiver(), filter);
    ```
    在 onReceive() 中，通过 ConnectivityManager 检查网络状态。需要注意的是，在 Android 7.0 之后，无法通过静态注册方式接收网络变化广播，需使用动态注册。
## Content Provider
### 什么是 ContentProvider？它的作用是什么？
ContentProvider 是 Android 四大组件之一，用于在不同应用之间共享数据。通过 ContentProvider，一个应用可以提供数据访问接口给其他应用，从而实现数据共享。它通常用于操作数据库、文件、联系人、媒体等数据资源。系统自带的例如联系人、日历等数据都通过 ContentProvider 来访问。
### ContentProvider 的工作原理是什么？
ContentProvider 通过统一资源标识符 (URI) 提供对数据的访问，每个 ContentProvider 会定义一个唯一的 URI 作为入口。使用 ContentResolver 访问 ContentProvider，通过 insert()、query()、update()、delete() 等方法来操作数据。同时，ContentProvider 可以使用权限保护数据，保证安全。
### ContentProvider 的主要方法有哪些？它们的作用是什么？
- onCreate()：初始化 ContentProvider 时调用，通常用于数据库或资源的初始化。
- insert(Uri uri, ContentValues values)：用于插入数据。
- query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)：用于查询数据。
- update(Uri uri, ContentValues values, String selection, String[] selectionArgs)：用于更新数据。
- delete(Uri uri, String selection, String[] selectionArgs)：用于删除数据。
- getType(Uri uri)：返回数据的 MIME 类型，便于其他应用识别。
### ContentResolver 的作用是什么？
ContentResolver 是 ContentProvider 的访问接口，用于在应用之间访问和操作数据。ContentResolver 提供了一组方法，例如 insert()、query()、update()、delete() 等，来与 ContentProvider 进行交互，通过 URI 定位 ContentProvider 并进行操作。
### ContentProvider 的 URI 结构是什么？
```
content://authority/path/id
```
- content://：表示数据类型。
- authority：唯一标识 ContentProvider 的授权名称，通常是应用包名。
- path：指定要操作的数据表或路径。
- id：可选，用于表示某一行数据。 例如 content://com.example.app.provider/table_name/1 表示访问 com.example.app 应用中 table_name 表的第 1 行数据。
### 如何自定义一个 ContentProvider？
- 创建一个类继承 ContentProvider 并实现其方法（onCreate()、insert()、query() 等）。
- 在 AndroidManifest.xml 文件中注册 provider 标签，设置 authorities 属性以唯一标识。
- 在各方法中实现数据的具体增删查改逻辑，通常通过数据库或文件实现数据存储。
### ContentProvider 是运行在主线程还是子线程？如何避免 ANR？
- ContentProvider 默认运行在主线程中，如果操作数据库或执行耗时操作，可能导致 ANR（应用无响应）。避免 ANR 的方法包括：
    - 在 ContentProvider 的方法中创建子线程或使用异步任务来处理数据。
    - 使用 Loader 或 AsyncQueryHandler 来执行数据库查询或增删操作。
### 如何实现 ContentProvider 数据的监听？
- 可以使用 ContentObserver 监听 ContentProvider 数据的变化：
    -  使用 ContentResolver.registerContentObserver() 方法注册一个 ContentObserver。
    - 当 ContentProvider 中的数据发生改变时，onChange() 方法会被回调，应用可以在其中实现数据刷新或更新逻辑。
### 如何确保 ContentProvider 数据的安全性？
- 权限保护：在 AndroidManifest.xml 中使用 android:permission 属性，为 ContentProvider 设置读写权限。例如，可以使用 android:readPermission 和 android:writePermission 限制读写权限。
- URI 访问控制：对 URI 进行权限检查，通过 URI 限制访问的路径或表，避免数据泄露。
- 自定义权限：定义自定义权限，以确保只有授权应用可以访问 ContentProvider 的数据。
### 如何访问系统自带的 ContentProvider（如联系人、日历）？
- 系统提供了一些常用的 ContentProvider，例如联系人和日历。可以通过 ContentResolver 和相应的 URI 来访问。例如：
    ```
    Uri uri = ContactsContract.Contacts.CONTENT_URI;
    Cursor cursor = getContentResolver().query(uri, null, null, null, null);
    ```
    访问这些数据时需要在 AndroidManifest.xml 中声明相应权限，例如 READ_CONTACTS。
### ContentProvider 和 SQLite 的区别是什么？
- ContentProvider：用于在应用之间共享数据，提供标准的接口和 URI 格式，适合跨应用数据访问。
- SQLite：是 Android 中的嵌入式数据库，适合应用内部的数据存储，不具备跨应用共享功能。ContentProvider 可以将 SQLite 数据库封装后提供给其他应用访问。
### ContentProvider 中如何获取 MIME 类型？
- 可以通过重写 getType(Uri uri) 方法获取 MIME 类型，用于标识不同的数据。常见的 MIME 类型格式如下：
    - ```vnd.android.cursor.dir/vnd.<authority>.<path>```：用于返回多行数据。
    - ```vnd.android.cursor.item/vnd.<authority>.<path>```：用于返回单行数据。
## Fragment 
### 什么是 Fragment？与 Activity 的区别是什么？
- Fragment 是一种 UI 组件，可以在 Activity 的布局中插入，可以在运行时添加、移除或替换。它可以包含自己的布局、生命周期和事件处理。
- 区别：
    - 生命周期：Fragment 的生命周期依赖于其所附加的 Activity，Activity 的生命周期会影响 Fragment。例如，当 Activity 被销毁时，附加的 Fragment 也会被销毁。
    - 独立性：Activity 是独立的组件，通常用于表示一个应用的单独页面；而 Fragment 通常是 UI 的一部分，可以组合、复用。
    - 复用性：Fragment 允许在不同的 Activity 中复用其代码和布局，从而创建灵活的用户界面。
### Fragment 的生命周期？
- onAttach()：Fragment 被附加到 Activity 时调用。
- onCreate()：初始化 Fragment。可以用来保存非 UI 的状态。
- onCreateView()：创建 Fragment 的 UI 视图。
- onActivityCreated()：Activity 完成 onCreate() 之后调用，可以执行和 Activity 有关的 UI 操作。
- onStart()：Fragment 对用户可见时调用。
- onResume()：Fragment 开始与用户交互时调用。
- onPause()：Fragment 暂停时调用。
- onStop()：Fragment 停止时调用。
- onDestroyView()：销毁 Fragment 的视图。
- onDestroy()：销毁 Fragment。
- onDetach()：将 Fragment 从 Activity 中分离。
### 如何在 Fragment 和 Activity 之间通信？
- 接口回调：在 Fragment 中定义接口，让 Activity 实现接口以接收事件通知。
- ViewModel：使用 ViewModel 实现数据共享，以便 Activity 和 Fragment 能够观察相同的数据变化。
- Fragment Result API：从 Fragment 向其他 Fragment 传递数据，Android 提供了 setFragmentResult 和 setFragmentResultListener。
### FragmentTransaction 是什么？有哪些常用操作？
- FragmentTransaction 是 FragmentManager 提供的一个类，用于执行 Fragment 的添加、删除、替换等事务操作。常用方法包括：
    - add()：将 Fragment 添加到 Activity 中。
    - replace()：替换指定的 Fragment。
    - remove()：移除指定的 Fragment。
    - hide()：隐藏 Fragment。
    - show()：显示 Fragment。
    - addToBackStack()：将 Fragment 添加到返回栈，用户可以按返回键返回到之前的 Fragment。
    - commit()：提交事务。
### Fragment 的 onSaveInstanceState() 方法的作用是什么？
- onSaveInstanceState() 用于在 Fragment 被销毁之前保存临时状态数据。系统在配置变化（如屏幕旋转）或内存不足时会调用此方法。
- 可以使用 Bundle 保存需要恢复的数据，稍后通过 onCreateView() 或 onActivityCreated() 方法中的 savedInstanceState 恢复数据。
### Fragment 为什么需要一个空的构造函数？
- Fragment 需要一个空构造函数，以便在配置更改（例如屏幕旋转）或进程恢复时，系统可以重新创建 Fragment 实例。
- 如果 Fragment 没有空构造函数，系统在重新创建 Fragment 时会出现错误。
### Fragment 中为什么推荐使用 getActivity() 和 requireActivity()？
- getActivity()：返回附加的 Activity，可能为空，因此需要检查返回值是否为 null。
- requireActivity()：返回附加的 Activity，如果 Fragment 尚未附加到 Activity 会抛出 IllegalStateException。requireActivity() 更安全，因为它确保 Fragment 已附加到 Activity。



