# Android2
## 内存管理  
### 内存管理的基本概念
- 堆内存（Heap Memory）：用于存储对象（Java 对象）。当对象被创建时，它们会存储在堆中。Java 的垃圾回收机制会负责管理堆内存的分配和回收。
- 栈内存（Stack Memory）：用于存储方法的局部变量和调用栈信息。栈内存由操作系统自动管理，当方法执行完毕后，栈上的数据会自动销毁。
- 内存泄漏（Memory Leak）：是指应用中某些对象在不再使用时仍然被引用，导致垃圾回收器无法回收这些对象的内存，造成内存消耗逐渐增多，甚至可能导致应用崩溃。
### 垃圾回收机制（Garbage Collection）
- Android 运行时（ART 或 Dalvik）使用 垃圾回收机制（GC）来自动管理内存。GC 会定期检查哪些对象不再被引用，并回收这些对象占用的内存。以下是垃圾回收的相关细节：
    - GC 的目标：自动回收不再使用的对象，释放内存。
    - GC 的触发：当堆内存不足时，垃圾回收器会自动运行。
    - 垃圾回收的代际（Generational）策略：
        - 年轻代（Young Generation）：存储新创建的对象。GC 在年轻代频繁发生。
        - 老年代（Old Generation）：存储存活时间较长的对象。GC 在老年代发生的频率较低。
        - 永久代（Permanent Generation）：存储类的元数据，已经在 Android 5.0 及之后版本被 Metaspace 取代。
### 内存优化和管理技巧
#### 避免内存泄漏
- 内存泄漏的原因：
    - 对象被不再需要的引用保持。
    - Activity、Fragment 等 UI 组件的引用未释放，导致它们无法被回收。
    - 使用静态变量（例如单例）不当，导致对象无法被垃圾回收。
    - 内部类和匿名类持有外部类的引用，导致外部类不能被回收。
- 避免内存泄漏的解决方案：
    - Context 的正确使用：避免持有 Activity 或 Context 的长时间引用，尤其是静态引用。使用 ApplicationContext 代替 Activity 的 Context，除非需要与 UI 交互。
    - 弱引用（WeakReference）：对于不需要强引用的对象，可以使用 WeakReference 来避免内存泄漏。
    - 避免长时间持有 Activity 引用：Activity 和 Fragment 的生命周期管理很重要，避免在它们被销毁后仍持有其引用。
#### 优化 Bitmap 内存使用
- Bitmap 是内存消耗大户，尤其是在加载大图时。对于大图片，直接加载可能导致 OutOfMemoryError（OOM）错误。
- 优化 Bitmap 的方法：
    - Bitmap 缩放：在加载 Bitmap 时，通过 BitmapFactory.Options 来设置合适的 inSampleSize 来缩小图片尺寸，避免加载过大的图片。
    - 缓存机制：使用内存缓存（如 LRUCache）来缓存图片，避免重复加载和解码图片。
    - 回收 Bitmap：在不需要时，及时调用 bitmap.recycle() 回收 Bitmap 使用的内存，并将其设为 null，防止内存泄漏。
    - 使用 BitmapDrawable 和 BitmapFactory：通过合理使用这些类的功能，减少 Bitmap 的内存占用。
#### 使用内存缓存
- LRUCache（Least Recently Used Cache）：Android 提供的缓存机制，用于缓存最近使用的对象。LRU 缓存会在内存空间不足时自动清理最少使用的缓存项。
- 内存缓存的使用：通过 LruCache 对图片或数据进行缓存，能够显著减少内存的消耗。
#### 内存分析工具
- Android Profiler：Android Studio 提供的 Profiler 工具，可以实时监控应用的内存使用情况。它提供了 Memory 面板，可以查看内存的分配情况、对象的数量、堆内存的大小等。
- Heap Dump：可以通过 Android Studio 提供的 Heap Dump 分析工具，生成堆快照，查看哪些对象占用了大量的内存，帮助分析内存泄漏。
- LeakCanary：这是一个开源库，用于自动检测 Android 应用中的内存泄漏。它在应用中集成后，会定期扫描并报告可能的内存泄漏。
#### 内存优化的其他技巧
- 避免过度使用 View 层级：层级过深的布局会增加内存消耗，建议尽量使用平坦的布局结构，如 ConstraintLayout。
- 避免不必要的线程和对象：线程和对象的创建会消耗内存，在应用中应尽量避免无用线程或对象的创建。
- 压缩资源：对于图片、音频等资源，使用合适的格式和压缩比，避免资源文件过大。
### 内存溢出（OutOfMemoryError）
- OutOfMemoryError 通常发生在尝试加载过多的内存资源（如 Bitmap、视频文件等）时，超过了应用的内存限制。
- 避免 OOM 错误的方法：
    - Bitmap 缩放：避免加载过大的图片。
    - 按需加载资源：根据屏幕尺寸和分辨率按需加载资源，不加载不必要的资源。
    - 使用内存优化库：如 Glide、Picasso 等图像加载库，这些库会自动进行内存管理。
## 自定义 View
自定义 View 是指开发者根据需求创建的自定义界面组件，通常是继承现有的 View 类（如 View、TextView、ImageView 等）并重写其中的方法。自定义 View 可以提供独特的外观和功能，满足默认控件无法实现的需求。
### 自定义 View 的类型有哪些？
- 完全自定义 View：从头开始绘制，继承自 View 或 ViewGroup，适合从零开始创建的控件。
- 组合控件：将多个现有的 View 组合在一起形成新的控件，通常继承自 LinearLayout、FrameLayout 等布局类，适合将多个控件组合成一个新的 UI 组件。
### 如何在自定义 View 中绘制内容？
- 重写 onDraw(Canvas canvas) 方法，并通过 Canvas 对象进行绘制操作：
    - 使用 Paint 设置绘制样式、颜色、粗细等。
    - 使用 Canvas 的各种方法绘制图形（如 drawRect、drawCircle、drawText 等）。
### 如何在自定义 View 中处理布局？
- 重写 onMeasure() 方法来指定 View 的宽高。
    ```
    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int width = MeasureSpec.getSize(widthMeasureSpec);
        int height = MeasureSpec.getSize(heightMeasureSpec);
        setMeasuredDimension(width, height); // 设置测量的宽高
    }
    ```
- 可以根据需要自行计算宽高，也可以使用 MeasureSpec 获取父布局的建议尺寸。
### 如何在自定义 View 中实现交互（触摸事件）？
重写 onTouchEvent(MotionEvent event) 方法来处理触摸事件。
### 在自定义 View 中如何进行性能优化？
- 避免频繁创建对象：在 onDraw() 中尽量避免创建新的对象，如 Paint、Rect 等可在构造方法中初始化。
- 合理使用硬件加速：对于复杂的自定义绘制，使用硬件加速可以提升性能。
- 减少 onDraw 的调用：减少 invalidate() 的调用次数，避免不必要的重绘。
### 自定义 View 和 ViewGroup 的区别是什么？
- View：通常用于绘制内容并处理触摸事件，主要重写 onDraw() 和 onMeasure() 方法来实现自定义效果。
- ViewGroup：用于容纳子 View，需要重写 onLayout() 来安排子 View 的位置，可以用来创建复杂的布局结构。
## 事件分发机制
### 事件分发的三个关键方法
- dispatchTouchEvent(MotionEvent event)：负责事件的分发，决定是否将事件传递给子 View，或者自己处理。通常是 ViewGroup 的入口方法。
- onInterceptTouchEvent(MotionEvent event)：ViewGroup 用来拦截事件的方法，如果返回 true，表示拦截事件，不传递给子 View，而直接由该 ViewGroup 自己处理。
- onTouchEvent(MotionEvent event)：用于处理事件的方法，如果返回 true，表示当前 View 已处理该事件，事件不会继续传递。
### 事件分发的整体流程
- 事件从顶层的 Activity 开始，通过 Activity.dispatchTouchEvent() 进入根 View。
- 根 View 将事件传递给其子 View，直到找到能够处理事件的具体 View。
- 具体流程：
    - Activity.dispatchTouchEvent()：首先将事件交给根布局 View 处理。
    - ViewGroup.dispatchTouchEvent()：ViewGroup 判断是否需要将事件分发给子 View，还是拦截事件由自己处理。
    - ViewGroup.onInterceptTouchEvent()：在 ViewGroup 中可以重写此方法，决定是否拦截事件。
    - View.dispatchTouchEvent()：如果事件未被拦截，则事件传递到子 View 的 dispatchTouchEvent()，然后由 onTouchEvent() 进行处理。
### dispatchTouchEvent 方法
- dispatchTouchEvent() 是事件分发的核心，决定事件是由当前 View 处理，还是传递给子 View。
- 如果 dispatchTouchEvent() 返回 true，表示当前 View 完成了事件处理，事件到此为止。
- 如果 dispatchTouchEvent() 返回 false，则事件会继续传递，或被父 View 捕获。
### onInterceptTouchEvent 方法
- onInterceptTouchEvent() 只存在于 ViewGroup 中，用于决定是否拦截事件。
- 返回 true：表示拦截事件，当前 ViewGroup 自己处理事件，不再传递给子 View。
- 返回 false：表示不拦截，事件继续传递给子 View。
- 常见用例：如 ScrollView 等滑动控件，在某些条件下可能会拦截子 View 的触摸事件。
### onTouchEvent 方法
- onTouchEvent() 方法是事件处理的最终环节，用于处理触摸事件。
- onTouchEvent() 根据不同的事件类型执行相应的操作：
    - ACTION_DOWN：手指按下屏幕。
    - ACTION_MOVE：手指在屏幕上滑动。
    - ACTION_UP：手指离开屏幕。
- onTouchEvent() 返回 true，表示事件已被消费，不再传递。
- 如果返回 false，则事件会返回到父 View，由父 View 的 onTouchEvent() 进行处理。
### 事件分发的优先级
-  onTouchListener > onTouchEvent：如果为 View 设置了 OnTouchListener，并在 onTouch() 中返回 true，事件不会继续传递到 onTouchEvent()。
-  onTouchEvent 返回值的影响：onTouchEvent() 返回 true 表示事件处理完毕，不再向上层传递。如果返回 false，事件会继续向父 View 传递。
### 事件分发机制的常见问题
-  如何让 ViewGroup 拦截所有事件？
    - 重写 onInterceptTouchEvent()，始终返回 true。
-  如何让子 View 始终接收事件？
    - 重写父 View 的 onInterceptTouchEvent()，始终返回 false。
-  在 onTouchEvent 中未消费事件时会发生什么？
    - 事件将返回给上层的 View 或 ViewGroup，直到找到一个消费事件的 onTouchEvent()。
## RecyclerView 
- RecyclerView 是一个高性能的、灵活的视图组件，用于显示大量数据的列表或网格视图。
- 推荐使用原因：
    - 性能更高：采用了 ViewHolder 机制和视图回收重用技术，减少了内存消耗。
    - 灵活的布局管理：通过 LayoutManager 可以实现线性、网格、瀑布流等多种布局。
    - 动画效果：内置的动画支持，方便实现视图的插入、删除、移动动画。
    - 分离布局和数据：通过适配器（Adapter）来管理数据，代码更清晰易维护。
### RecyclerView 和 ListView 的区别是什么？
- ViewHolder：RecyclerView 强制使用 ViewHolder 模式，而 ListView 可以不使用。
- Item 动画：RecyclerView 支持默认的动画效果；ListView 不支持内置动画。
- 布局管理：RecyclerView 的布局是可定制的，支持不同的 LayoutManager；而 ListView 只能实现线性垂直列表。
- 数据更新：RecyclerView 通过 notifyItemChanged() 等精确控制单个数据项的刷新；ListView 只能 notifyDataSetChanged() 刷新整个列表。
### 如何优化 RecyclerView 性能？
- 复用 ViewHolder：通过 ViewHolder 缓存视图，避免重复加载。
- 使用 DiffUtil：在 Adapter 中使用 DiffUtil 可以高效地计算数据差异，并更新列表。
- 分页加载：对于大数据集，使用 Paging 3 组件或分页逻辑，避免一次性加载所有数据。
- 设置固定大小：当列表项大小固定时调用 setHasFixedSize(true)，减少布局调整时间。
- 避免嵌套：避免在 RecyclerView 中嵌套 ScrollView 或另一个 RecyclerView。
- 减少复杂布局：优化列表项的布局层级，尽量简化布局。
## ViewPager2 
### 什么是 ViewPager2？与 ViewPager 的区别是什么？
- ViewPager2 是 Android 推出的新的页面滑动组件，取代了 ViewPager。
- 主要区别：
    - 基于 RecyclerView 实现：ViewPager2 基于 RecyclerView 实现，因此更灵活，支持大数据集和列表优化。
    - 支持垂直滑动：ViewPager2 支持水平和垂直滑动，而 ViewPager 只能水平滑动。
    - 数据更新：ViewPager2 更加高效，可使用 notifyDataSetChanged() 刷新数据，和 RecyclerView 一样，还支持 ListAdapter 和 DiffUtil。
    - 反向布局：ViewPager2 支持 RTL（Right-to-Left）布局。
    - 嵌套滚动：ViewPager2 通过 RecyclerView 解决了嵌套滚动的问题，更好地支持与其他 RecyclerView 的嵌套使用。
## ANR（Application Not Responding）
ANR（Application Not Responding）是 Android 应用无响应的异常。发生 ANR 时，系统会弹出对话框，提示用户选择“等待”或“关闭应用”。ANR 通常是由主线程被长时间阻塞引起的，比如进行耗时操作、死锁、UI 渲染过慢等。
### 常见的导致 ANR 的原因有哪些？
- 主线程中进行耗时操作：比如数据库操作、文件读写、网络请求等应在子线程中完成。
- 死锁或线程锁定：如果多个线程间的锁操作互相等待，则会导致死锁，阻塞主线程。
- UI 操作耗时：主线程中进行复杂的 UI 渲染、解析大量数据等，会导致 UI 卡顿。
- 资源争用：主线程等待资源而被阻塞，比如 IO 资源、网络资源或数据库资源。
- BroadcastReceiver 或 Service 超时：BroadcastReceiver 或 Service 执行时间过长，导致超时。
### 如何避免 ANR？
- 将耗时操作放在子线程中：比如网络请求、数据库操作等。
- 避免主线程持有锁：主线程不应持有锁，避免因等待锁资源而阻塞。
- 优化 UI 操作：UI 渲染应简化，避免在主线程加载大量图片或数据。
- 合理设置超时：对于网络请求或耗时操作，设置合理的超时时间。
- BroadcastReceiver 中的操作：在 BroadcastReceiver 中避免耗时操作，必要时启动 IntentService 来处理。
- Service 中的操作：在 Service 中处理长时间任务时，启动独立的线程来执行，避免阻塞。
### 如何调试和定位 ANR 问题？
- Systrace 和 Profiler：使用 Systrace 和 Android Profiler 检查线程状态、方法调用时间等性能问题。
- 查看 Logcat：Logcat 中也会记录 ANR 相关信息，检查 ActivityManager 的相关日志。
- 查看 ANR 日志：ANR 日志记录在 /data/anr/traces.txt 文件中，通过 adb pull /data/anr/traces.txt 导出日志，定位问题。
### 如何优化 Android 应用以减少 ANR 发生的可能？
- 使用异步处理：尽可能在子线程中完成耗时任务。
- 缩减主线程任务：主线程中的任务尽可能轻量，避免执行繁重的计算或 IO 操作。
- 网络优化：通过缓存数据、压缩请求等方式，优化网络请求时间。
- 数据库优化：批量插入或查询时使用事务，减少数据库操作耗时。
- Bitmap 优化：加载图片时，使用压缩、缓存等手段，避免阻塞 UI 渲染。
- 减少 UI 布局层级：复杂布局会增加主线程负担，简化布局有助于减少 UI 卡顿。
### 如何在 BroadcastReceiver 中避免 ANR？
- 避免耗时操作：BroadcastReceiver 应快速返回，避免执行耗时任务。
- 使用 IntentService：如果需要执行较长的任务，可以启动 IntentService 或 JobIntentService 来处理耗时操作。
- 缩短任务时间：尽量在 BroadcastReceiver 中执行简单操作，如启动 Activity、通知用户等。
### 如何在 Service 中避免 ANR？
- 将耗时任务放入子线程：避免在主线程中执行任务，使用 HandlerThread、AsyncTask 或 ExecutorService 等工具。
- 设置超时时间：对于网络请求、文件 IO 等任务设置合理的超时，避免无期限等待。
- 使用 IntentService：如果任务是一次性的，使用 IntentService 处理，该服务在任务完成后会自动销毁。




