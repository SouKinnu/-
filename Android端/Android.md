# Android
## 动画
### Android 动画的种类有哪些？
- 视图动画（View Animation）：基于视图的简单动画类型，包含补间动画（Tween Animation）和逐帧动画（Frame Animation）。
    - 补间动画：实现平移、缩放、旋转和透明度变化，常用 TranslateAnimation、ScaleAnimation、RotateAnimation 和 AlphaAnimation。
    - 逐帧动画：一帧一帧地显示图片序列，类似 GIF 动画，通过 XML 文件定义一系列图片。
- 属性动画（Property Animation）：更为灵活，可以对任意属性进行动画操作，如 ObjectAnimator 和 ValueAnimator。
- 矢量动画（Vector Drawable Animation）：基于矢量图形的动画，用于绘制路径动画，适合图标动画等。
### 如何实现补间动画？补间动画有哪些类型？
补间动画包括平移、缩放、旋转和透明度变化，通过 XML 或代码实现。以下是实现示例：
- 补间动画包括平移、缩放、旋转和透明度变化，通过 XML 或代码实现。以下是实现示例：
    - XML 文件：在 res/anim 目录下创建 .xml 文件，如 alpha.xml。
    ```
    <alpha xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromAlpha="1.0"
    android:toAlpha="0.0"
    android:duration="1000" />
    ```
    - 代码方式：
    ```
    AlphaAnimation alphaAnimation = new AlphaAnimation(1.0f, 0.0f);
    alphaAnimation.setDuration(1000);
    view.startAnimation(alphaAnimation);
    ```
### 什么是属性动画？属性动画与视图动画的区别？
- 视图动画 只能作用于 View，只会改变 View 的视觉效果，不会真正改变属性值。
- 属性动画 可以作用于任何对象的任意属性，并且可以改变对象的实际属性值，效果更强大和灵活。
### 如何实现属性动画？
- ObjectAnimator：直接作用于属性。
    ```
    ObjectAnimator animator = ObjectAnimator.ofFloat(view, "translationX", 0f, 100f);
    animator.setDuration(1000);
    animator.start();
    ```
- ValueAnimator：通过监听器获取动画进度，并手动更新属性。
    ```
    ValueAnimator animator = ValueAnimator.ofFloat(0f, 100f);
    animator.setDuration(1000);
    animator.addUpdateListener(animation -> {
        float value = (float) animation.getAnimatedValue();
        view.setTranslationX(value);
    });
    animator.start();
    ```
### 如何使用矢量动画？矢量动画的优势是什么？
- 矢量动画基于矢量图形，使用 VectorDrawable 和 AnimatedVectorDrawable 实现。矢量图形具有放大不失真、文件小的优势，适合图标和简单图形的动画。
    - 定义矢量图形和动画文件。
    - 使用 AnimatedVectorDrawable 控制动画效果，例如图标变化和路径动画。
## 图片缓存
图片缓存是指在应用中将图片存储在内存或磁盘中，以便在需要使用时可以快速加载，而无需重新从网络或其他慢速存储中获取。这样可以显著提高应用性能，降低网络流量消耗。
### 图片缓存的类型有哪些？
- 内存缓存：将图片存储在内存中，访问速度非常快，但缓存大小受限于可用内存，适合频繁访问的图片。
- 磁盘缓存：将图片存储在磁盘上，存储空间更大，但访问速度相对较慢，适合不常访问的图片或大图片的长期缓存。
### 为什么需要图片缓存？
- 减少网络请求：缓存图片后，不必每次都从网络下载，减少流量消耗。
- 加快加载速度：直接从缓存加载图片，比从网络获取快得多。
- 降低资源消耗：减少 CPU 和网络的消耗，提升电池使用效率。
### 如何实现内存缓存和磁盘缓存
- 内存缓存：LruCache 使用最近最少使用（LRU）算法来管理缓存。当缓存大小达到上限时，会自动移除最近最少使用的项。
- 磁盘缓存：DiskLruCache 会将图片存储在指定的目录中，并按 LRU 策略来管理缓存空间。
## Glide
### Glide 的基本特性
- 高效的内存和磁盘缓存：Glide 自动缓存加载的图片，减少网络请求，提高应用性能。
- 支持多种图片格式：Glide 支持常见的图片格式，如 JPG、PNG、GIF、WebP 以及自定义的图片格式。
- 无缝的加载与显示：可以轻松地加载图片并显示到 ImageView 中，同时支持加载进度、占位符、错误图片等。
- 动画支持：支持 GIF 动画、图片渐变显示等特性。
- 跨平台支持：Glide 不仅支持普通的图片加载，还支持视频帧提取、资源文件、以及网络图片等。
- 优化内存使用：Glide 进行内存管理，避免内存溢出，特别是在处理大量图片时。
## OkHttp 
### 基本特性
- 支持 HTTP/1.1 和 HTTP/2：OkHttp 支持 HTTP/2 协议，允许多路复用、头压缩等特性，从而提高请求效率。
- 连接池：支持连接池技术，减少了多次请求的连接开销，提高性能。
- 缓存：OkHttp 内置支持 HTTP 响应缓存，可以在不需要重新请求的情况下直接从缓存中获取数据，从而减少网络流量和加快响应速度。
- 透明压缩：支持 GZIP 自动压缩响应体，减少带宽消耗。
- 拦截器：支持使用拦截器对请求和响应进行处理，例如，日志拦截器、身份验证、请求重试等。
- 支持 WebSocket：支持 WebSocket 协议，适用于需要实时双向通信的应用场景。
### 请求拦截器
- 应用拦截器：在请求和响应的全过程中进行拦截。
- 网络拦截器：只在实际的网络请求和响应过程中进行拦截。
## Retrofit 
### 基本特性
- 简化的 HTTP 请求：Retrofit 通过注解来声明 HTTP 请求，使得代码简洁、易读。
- 支持多种数据格式：Retrofit 支持 JSON、XML、Protobuf 等数据格式的转换。
- 基于注解：使用注解声明 HTTP 方法、请求 URL 和请求体等。
- 集成 OkHttp：底层通过 OkHttp 进行网络请求，支持连接池、缓存、请求拦截等特性。
- 支持同步和异步请求：支持同步调用和异步回调两种方式。
### 创建 API 接口
- Retrofit 通过定义接口来描述 HTTP 请求。每个方法使用注解来指示请求的类型（如 GET、POST 等）和 URL。
    ```
    public interface ApiService {
        // GET 请求，返回的 JSON 数据会自动转换为对应的 Java 对象
        @GET("users/{user}/repos")
        Call<List<Repo>> getRepos(@Path("user") String user);
    }
    ```
    - @GET 注解表示这是一个 GET 请求。
    - @Path 注解将 URL 路径中的 {user} 替换为实际的 user 参数。
- 请求头与请求体
    - 请求头：使用 @Header 或 @Headers 注解来添加请求头
    - 请求体：使用 @Body 注解向请求添加请求体
## WebSocket 
### 什么是 WebSocket？与 HTTP 的区别是什么？
- WebSocket 是一种双向通信协议，通过单个持久连接实现客户端和服务器之间的数据实时传输。
- 与 HTTP 的区别：
    - 连接方式：HTTP 是无状态的请求-响应协议，客户端发起请求，服务器返回响应；而 WebSocket 是持久化连接，建立后双方可自由发送数据。
    - 实时性：WebSocket 提供实时性，数据可以在两端直接传递；而 HTTP 每次都要创建和销毁连接，实时性不高。
    - 头部开销：WebSocket 连接建立后，消息头部非常小，节省带宽；HTTP 的每个请求头部都包含大量信息。
### WebSocket 的工作原理是什么？
- 握手阶段：客户端向服务器发送 HTTP 请求，其中包含 Upgrade 头部，要求将协议升级为 WebSocket。
- 响应阶段：服务器确认并同意协议升级。
- 数据传输：握手成功后，客户端和服务器可以通过已建立的连接进行双向通信。数据帧可以是文本、二进制数据等，帧格式比 HTTP 请求简单。
- 关闭连接：客户端或服务器可随时发送关闭帧来终止连接。
### WebSocket 的生命周期方法有哪些？
- onOpen()：当连接成功建立时调用，可以在此方法中初始化资源。
- onMessage()：当客户端接收到服务器发送的消息时调用，可以接收文本或二进制消息。
- onClosing()：当服务器发起关闭请求时调用，客户端可以决定是否关闭。
- onClosed()：当连接完全关闭时调用。
- onFailure()：连接失败时调用，处理网络异常或其他问题。
### 如何在 Android 中处理 WebSocket 连接断开与重连？
- 心跳检测：通过定期发送心跳消息检查连接是否有效，如定时发送 ping 请求，服务器响应 pong。
- 自动重连：在 onFailure() 和 onClosing() 中检测到连接关闭时，尝试延迟重连，可以使用 Handler 或 Coroutine 实现。
### 如何实现 WebSocket 的心跳机制？
- 心跳机制用于检测连接的活跃状态，防止连接意外中断。在 Android 中可以通过 Handler 或 Timer 定期发送心跳包实现。
## Handler
### 什么是 Handler？它的作用是什么？
- Handler 是 Android 提供的一种线程间通信机制。主要用于在工作线程中执行耗时操作后，将结果发送到主线程更新 UI。
- 作用：
    - 发送消息和可运行对象 (Runnable) 到 MessageQueue 中排队。
    - 从 MessageQueue 中取出消息并处理，使得不同线程之间的通信更加方便。
### Handler 的工作原理是什么？
- Handler 工作原理涉及 Looper、MessageQueue 和 Message 三个组件：
    - Looper：每个线程可以有一个 Looper，负责管理当前线程的消息循环。
    - MessageQueue：每个 Looper 持有一个 MessageQueue，它存储所有待处理的消息。
    - Handler：Handler 可以向 MessageQueue 中添加消息，Looper 循环检查 MessageQueue 中的消息并传递给 Handler 处理。
- 当 Handler 调用 sendMessage() 或 post() 方法时，消息被添加到 MessageQueue 中。Looper 会不断地从 MessageQueue 中提取消息并调用 Handler 的 handleMessage() 方法处理消息。
### Message、MessageQueue 和 Looper 的关系是什么？
- Message：表示一个消息对象，封装了要传递的消息内容。
- MessageQueue：消息队列，存储线程中等待处理的消息。
- Looper：用于管理线程的消息循环，不断从 MessageQueue 中取出消息并分发给 Handler 处理。
- Looper 负责创建消息循环，从 MessageQueue 中读取 Message，并由 Handler 调用 handleMessage() 处理。
### 如何避免 Handler 引起的内存泄漏？
- 静态内部类 + 弱引用：使用静态内部类创建 Handler，并用 WeakReference 持有 Activity 的引用，避免持有 Activity 的强引用。
- 移除未处理的消息：在 Activity 或 Fragment 销毁时调用 handler.removeCallbacksAndMessages(null); 清除消息队列中的消息，避免内存泄漏。
### Handler 中 post() 和 sendMessage() 的区别？
- sendMessage()：将 Message 对象放入 MessageQueue，Handler 在 handleMessage() 中处理。
- post()：将 Runnable 对象放入 MessageQueue，Handler 执行 Runnable 的 run() 方法。
- sendMessage() 更适合传递数据，而 post() 适合直接执行任务。
### HandlerThread 是什么？它的优势是什么？
- HandlerThread 是 Android 提供的一个包含 Looper 的线程，可以轻松在子线程中执行异步任务。它的优势包括：
    - 自动创建 Looper：HandlerThread 在 start() 时自动创建 Looper，可以直接在子线程中创建 Handler。
    - 便于管理：HandlerThread 在任务完成后可以直接退出线程，避免了线程资源的浪费。
    - 低成本多任务：可以通过单个 HandlerThread 处理多个任务，而不需要频繁创建新线程。
### Looper.loop() 为什么不会阻塞主线程？
- Looper.loop() 是一个无限循环，通常在主线程中运行，但它并不会阻塞主线程，因为 Looper 是一个 事件分发机制。它会检测 MessageQueue 中的消息：
    - 如果有消息，就处理消息；
    - 如果没有消息，就保持线程空闲。
- 当主线程空闲时，它不会消耗大量资源，而是在等待新的消息到达时再去执行对应的操作。
### 为什么主线程可以直接使用 Handler，而子线程需要手动创建 Looper？
- 主线程在启动时会自动创建 Looper 和 MessageQueue，因此可以直接在主线程中使用 Handler。
- 子线程默认没有 Looper，为了在子线程中使用 Handler，需要手动调用 Looper.prepare() 创建 Looper 和 MessageQueue，然后调用 Looper.loop() 开启消息循环。
### 为什么 Handler 是线程安全的？
- Handler 本身不是线程安全的，但它通过 MessageQueue 和 Looper 实现了线程间通信：
    - MessageQueue 是单线程消息队列，只能被一个 Looper 操作，确保了消息队列的顺序性和一致性。
    - 每个 Looper 只在一个线程中运行，Handler 发送的消息会按顺序排入 MessageQueue，保证了线程安全性。
### 屏幕适配
- 资源目录
    - drawable-mdpi：标准密度（160dpi）
    - drawable-hdpi：高密度（240dpi）
    - drawable-xhdpi：超高密度（320dpi）
    - drawable-xxhdpi：超超高密度（480dpi）
    - drawable-xxxhdpi：超超超高密度（640dpi）
    - layout-large、layout-xlarge：适用于大屏幕的布局
    - values-small、values-normal、values-large：适用于不同屏幕尺寸的资源

