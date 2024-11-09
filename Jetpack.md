# Jetpack
## MVC MVP MVVM
### MVC MVP MVVM的基本概念是什么？
- MVC（Model-View-Controller）: Model负责数据和业务逻辑，View负责展示，Controller负责协调Model和View。适用于小型应用，但随着业务复杂度增加，Controller容易变得臃肿。
- MVP（Model-View-Presenter）: Model负责数据和业务逻辑，View负责展示，Presenter作为中间人处理交互，解耦了View和Model的直接联系，使代码更易于测试。
- MVVM（Model-View-ViewModel）: Model负责数据和业务逻辑，View负责展示，ViewModel作为桥梁处理View和Model之间的交互。MVVM常用数据绑定（DataBinding），使得View和ViewModel之间可以自动同步。
### MVC MVP MVVM优缺点是什么？
- MVC:
    - 优点: 简单，适合小型应用。
    - 缺点: 随着代码量增加，Controller变得臃肿，不易维护。
- MVP:
    - 优点: 解耦了View和Model的联系，易于测试。
    - 缺点: Presenter可能也会变得臃肿，尤其是复杂的业务逻辑。
- MVVM:
    - 优点: 提供数据绑定机制，减少代码量，适合响应式UI开发。
    - 缺点: 数据绑定可能导致性能问题，调试也比较困难。
### 如何选择MVC、MVP或MVVM架构？
- MVC: 适合简单或小型应用，不需要过多复杂的交互逻辑。
- MVP: 适合对代码可测试性要求较高的项目，通常适合中小型项目。
- MVVM: 适合具有较多用户交互的应用，尤其是需要响应式UI的场景。推荐结合DataBinding或Jetpack Compose使用。
### 在MVP模式中，如何实现View和Presenter的通信？
通过接口定义View和Presenter之间的通信。View实现一个接口并传递给Presenter，Presenter通过接口调用View的相关方法实现数据展示和交互。
### MVVM中如何避免内存泄漏？
- 在Android中，避免内存泄漏可以通过以下方式：
    - 使用弱引用或绑定生命周期的LiveData避免Activity/Fragment的持有。
    - 使用ViewModel来持有数据，以便在界面销毁时自动回收资源。
    - 避免在ViewModel中直接持有Context对象。
### 在Android中，如何实现MVVM模式的数据绑定？
- 使用DataBinding库或LiveData和ViewModel组件结合。
- 在XML文件中绑定ViewModel中的数据，实现UI的自动更新。
### 为什么说MVVM比MVP更适合响应式编程？
MVVM模式中的ViewModel通过观察数据（LiveData或其他可观察对象）可以实时更新UI，减少了手动更新UI的工作量，更适合响应式编程。
### MVVM模式中的ViewModel和MVP模式中的Presenter有何区别？
- Presenter: 通常更依赖于View接口，直接与View进行交互。
- ViewModel: 不直接与View交互，通常与LiveData等可观察数据结合，通过数据更新间接通知View。
### 在Android Jetpack中，MVVM的推荐组合有哪些？
- Android Jetpack推荐使用ViewModel、LiveData和DataBinding结合实现MVVM。
- ViewModel管理数据和生命周期，LiveData实现数据观察，DataBinding实现UI自动更新。
## Navigation
Navigation是Android Jetpack库的一部分，主要用于管理应用内的Fragment或Activity之间的导航。它简化了复杂的导航逻辑，同时提供了可视化编辑器和类型安全的导航方式。
### Jetpack Navigation的主要组件有哪些？
- NavController: 管理应用内的导航，负责Fragment或Activity之间的导航动作。
- NavHost: 容器类，显示当前导航目标内容（通常是Fragment），最常用的是NavHostFragment。
- NavGraph: 表示应用的导航图，定义了所有可能的导航路径和节点。
- NavDestination: 导航目标，可以是一个Fragment、Activity或其他组件。
- NavArgs: 类型安全的方式传递参数，用于在导航时传递数据。
### 如何使用NavController进行导航？
- 可以在代码中使用findNavController()方法获取NavController实例，然后调用navigate()方法传递目标的ID进行导航。例如：
    ```
    findNavController().navigate(R.id.destination_fragment)
    ```
### Jetpack Navigation如何传递参数？
- 可以使用Safe Args插件生成类型安全的代码进行参数传递。在导航图（NavGraph）中为目的地定义参数，导航时使用生成的Directions类传递参数。例如：
    ```
    val action = CurrentFragmentDirections.actionToDestinationFragment(param1, param2)
    findNavController().navigate(action)
    ```
### 如何处理Jetpack Navigation中的返回栈？
- Jetpack Navigation会自动处理返回栈，可以通过findNavController().navigateUp()方法返回上一层。
- 也可以在XML中通过popUpTo属性手动配置返回栈行为，例如回到指定目的地并清除中间的Fragment。
### Jetpack Navigation如何处理深层链接（Deep Links）？
- 可以在导航图中为目标添加Deep Link的URI。导航组件会自动解析传入的URI并导航到指定的Fragment或Activity。例如：
    ```
    <fragment
        android:id="@+id/destination_fragment"
        android:name="com.example.DestinationFragment">
        <deepLink
            app:uri="example://app/destination" />
    </fragment>
    ```
### 什么是NavHostFragment，它的作用是什么？
- NavHostFragment是Jetpack Navigation框架中的容器类，用于显示当前的导航目标。它会动态切换当前显示的Fragment，实现了Fragment间的平滑导航。
## Lifecycle
### 什么是 Android 的生命周期？如何管理它？
- 生命周期指的是 Activity 和 Fragment 从创建到销毁的整个过程。每个生命周期阶段都有相应的回调方法（如 onCreate(), onStart(), onPause() 等），开发者需要在这些方法中编写代码来管理界面、资源等。
- 使用 Lifecycle 组件，可以简化生命周期管理，避免手动管理生命周期中的状态，减少内存泄漏和资源浪费。LifecycleObserver 让开发者能够将与生命周期相关的逻辑从 Activity 和 Fragment 中提取出来，使代码更加模块化。
### LifecycleObserver 是什么？如何使用它？
- LifecycleObserver 是一个接口，允许开发者观察 LifecycleOwner（如 Activity 和 Fragment）的生命周期变化。你可以通过实现 LifecycleObserver 接口，并使用注解（如 @OnLifecycleEvent）来指定生命周期事件。
- 使用步骤:
    - 创建一个类并实现 LifecycleObserver。
    - 使用注解（如 @OnLifecycleEvent(Lifecycle.Event.ON_START)）来指定需要处理的生命周期事件。
    - 在 Activity 或 Fragment 中注册这个观察者。
### 什么是 LifecycleOwner 和 LifecycleEventObserver？
- LifecycleOwner 是任何实现了 LifecycleOwner 接口的类（如 Activity、Fragment），它可以返回一个 Lifecycle 实例，用于跟踪其生命周期状态。
- LifecycleEventObserver 是一个新接口，继承自 LifecycleObserver，允许开发者使用 onStateChanged 方法监听生命周期事件。
### 如何使用 LiveData 和 Lifecycle 配合工作？
- LiveData 是一个可观察的数据持有者，它会遵循 Lifecycle 状态。只有在 LifecycleOwner 处于 活跃（如 onStart() 到 onStop()）状态时，LiveData 才会发送更新事件，避免了 UI 组件被销毁时的崩溃。
- 这使得 LiveData 能够自动管理观察者的生命周期，避免内存泄漏。
### ViewModel 和 Lifecycle 有何关系？
- ViewModel 是与生命周期相关的数据持有者，用于管理与 Activity 或 Fragment 相关的数据。ViewModel 在 Activity 或 Fragment 销毁时不会被销毁，可以有效防止数据丢失。
- ViewModel 与生命周期相关联，通常与 LiveData 一起使用，以便在 UI 更新时管理生命周期。
## Room
Room 是 Android 提供的数据库持久化库，它在 SQLite 的基础上构建，通过注解和编译时验证的方式简化了数据库操作。
- Room 的三大核心组件：
    - Entity：表示数据库中的表，通过注解 @Entity 定义，每个实体类的字段对应表的列。
    - DAO (Data Access Object)：包含用于操作数据库的方法，使用注解 @Dao 定义，通过 @Insert、@Delete、@Update、@Query 等注解来指定数据库操作。
    - Database：表示数据库的入口点，使用 @Database 注解定义，包含数据库配置和 DAOs 的实例。
### 如何定义一个 Room Entity？
- 使用 @Entity 注解定义一个类作为数据库表，@PrimaryKey 定义主键，@ColumnInfo 可以指定列名。
    ```
    @Entity(tableName = "users")
    data class User(
        @PrimaryKey(autoGenerate = true) val id: Int,
        @ColumnInfo(name = "first_name") val firstName: String,
        @ColumnInfo(name = "last_name") val lastName: String
    )
    ```
### 什么是 DAO？如何使用它？
- DAO 是用于定义数据库操作的接口或抽象类。DAO 通过 @Dao 注解，并使用 @Insert、@Delete、@Update、@Query 等注解定义方法来执行数据库操作。
    ```
    @Dao
    interface UserDao {
        @Insert
        suspend fun insertUser(user: User)

        @Query("SELECT * FROM users WHERE id = :userId")
        fun getUserById(userId: Int): LiveData<User>

        @Delete
        suspend fun deleteUser(user: User)
    }
    ```
### 如何定义一个 Room Database 类？
- Room Database 是抽象类，通过 @Database 注解定义，继承 RoomDatabase 并包含 DAOs 的实例。
    ```
    @Database(entities = [User::class], version = 1)
    abstract class AppDatabase : RoomDatabase() {
        abstract fun userDao(): UserDao
    }
    ```
### Room 如何处理数据库版本升级？
- Room 通过 @Database 注解中的 version 参数指定数据库版本。
- 数据库升级使用 Migration 类进行，定义从旧版本到新版本的迁移逻辑，并在构建数据库时调用 addMigrations() 注册迁移。
    ```
    val MIGRATION_1_2 = object : Migration(1, 2) {
        override fun migrate(database: SupportSQLiteDatabase) {
            database.execSQL("ALTER TABLE users ADD COLUMN age INTEGER")
        }
    }

    val db = Room.databaseBuilder(context, AppDatabase::class.java, "database-name")
        .addMigrations(MIGRATION_1_2)
        .build()
    ```
## ViewModel 
- ViewModel 是一个架构组件，专门用于管理与 UI 相关的数据，确保数据在生命周期事件（如屏幕旋转、配置更改）中保持不变。
- 它的主要作用是将业务逻辑和数据与 UI 分离，避免 Activity 和 Fragment 承担过多的业务逻辑。
### ViewModel 的生命周期是如何管理的？
- ViewModel 的生命周期是由 ViewModelProvider 管理的。
- 它的生命周期范围是在 Activity 或 Fragment 的生命周期内。在 Activity 或 Fragment 被销毁时，ViewModel 实例会自动释放，除非配置更改时 Activity 重建（如屏幕旋转）导致的销毁，此时 ViewModel 实例会被保留。
### 什么是 ViewModelFactory？何时需要它？
- ViewModelFactory 是一个工厂类，用于在创建 ViewModel 时传递自定义的参数。
- 默认情况下，ViewModelProvider 会使用无参构造函数创建 ViewModel，如果 ViewModel 有参数，则需要通过 ViewModelFactory 实现自定义的 ViewModel 实例化。
```
class MyViewModelFactory(private val userId: String) : ViewModelProvider.Factory {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(MyViewModel::class.java)) {
            return MyViewModel(userId) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class")
    }
}
```
### viewModelScope 和 GlobalScope 的区别是什么？
- viewModelScope：与 ViewModel 的生命周期绑定，ViewModel 清除时会自动取消协程任务，避免内存泄漏。
- GlobalScope：是应用级别的全局协程，不与任何生命周期组件绑定，可能导致内存泄漏。
###  什么是 AndroidViewModel？和普通 ViewModel 有什么区别？
- AndroidViewModel 是 ViewModel 的子类，提供 Application 的 Context，适合需要 Context 的 ViewModel。
- 普通 ViewModel 不提供 Context，通常用于不需要 Context 的场景。
## LiveData 
- LiveData 是 Android Jetpack 提供的生命周期感知型数据持有类，用于管理和观察数据变化。
- 特点：
    - 生命周期感知：LiveData 会自动遵循 Activity 和 Fragment 的生命周期，避免内存泄漏。
    - 数据自动更新：LiveData 中的数据变化会自动通知观察者。
    - 数据持久性：适合用于配置更改（如屏幕旋转）时持久化数据。
### 如何在 ViewModel 中定义和使用 LiveData？
- 在 ViewModel 中定义一个 MutableLiveData，然后在 Activity 或 Fragment 中通过观察 LiveData 来接收数据更新。
```
class MyViewModel : ViewModel() {
    private val _data = MutableLiveData<String>()
    val data: LiveData<String> get() = _data

    fun updateData(newData: String) {
        _data.value = newData
    }
}

// 在 Activity 中观察 LiveData
viewModel.data.observe(this, Observer { newData ->
    // 更新 UI
})
```
### 什么是 MutableLiveData？与 LiveData 有什么区别？
- MutableLiveData 是 LiveData 的子类，允许更新和设置值，适合在 ViewModel 中使用。
- LiveData 只允许读取，不允许修改，适合在 Activity 或 Fragment 中观察数据变化。
### postValue 和 setValue 有什么区别？
- setValue：在主线程调用，用于同步更新数据。
- postValue：可以在子线程调用，适合异步操作更新数据。调用 postValue 后会将更新排入主线程队列并在主线程应用。
### SingleLiveEvent 是什么？为什么要使用它？
- SingleLiveEvent 是一种自定义的 LiveData，确保事件只被消费一次。
- 避免配置更改时，数据再次触发并导致重复事件，例如导航或 Toast 消息。
### LiveData 和 ObservableField 有什么区别？
- LiveData：生命周期感知，适合与 ViewModel 和 Activity/Fragment 交互，自动管理生命周期。
- ObservableField：没有生命周期感知，常用于 DataBinding，适合简化 UI 数据的单向绑定。
### LiveData 和 Flow 的区别是什么？
- LiveData：生命周期感知，适合 UI 层数据管理。
- Flow：更适合多层数据流，支持更多异步操作和数据转换。通常 Flow 用于 Repository 或 Data Layer，LiveData 用于 UI 层。
### 为什么建议在 ViewModel 中使用 LiveData，而不是直接在 Activity 中操作数据？
- ViewModel 负责管理数据的业务逻辑，避免 Activity 和 Fragment 直接操作数据，有助于分离关注点，简化代码。
- 使用 ViewModel + LiveData 也可以确保在配置更改时数据持久化，避免重复加载数据。
## DataBinding
- DataBinding 是一种用于将 XML 布局与数据模型绑定的框架，可以在 XML 中直接访问数据源，减少样板代码。
- 优点：
    - 减少代码量：通过双向绑定减少手动编写 UI 更新代码。
    - 增强代码可读性：可以在 XML 中直接看到绑定的数据。
    - 提高性能：比 findViewById 更高效，避免过多的 UI 操作代码。
    - 实现 MVVM 架构：可将数据和逻辑分离，符合 MVVM 设计模式。
### DataBinding 和 ViewBinding 有什么区别？
- DataBinding：功能强大，可以双向绑定数据，支持数据观察、BindingAdapter、双向绑定。
- ViewBinding：仅生成视图绑定对象，不支持双向绑定和自定义绑定。适合轻量化的项目或只需简单视图绑定的场景。
