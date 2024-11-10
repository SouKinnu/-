# Class
## 普通类（Class）
普通类是 Kotlin 中最基本的类类型。普通类的结构灵活，可以包含属性、方法、构造函数等内容。普通类在 Android 中可以用于创建包含数据和行为的对象，适合用于定义业务逻辑、模型类、工具类等。
```
class Person(val name: String, var age: Int) {
    fun introduce() {
        println("Hi, my name is $name and I am $age years old.")
    }
}
```
- 构造函数：Kotlin 普通类可以包含主构造函数和一个或多个次构造函数。
- 访问控制：可以使用 private、protected、internal、public 等访问修饰符。
- 继承：类默认是 final 的，不能被继承；要允许继承，需要使用 open 关键字。
- 适用场景：适合表示具有行为的对象，例如 ViewModel、Repository、Service 类等。
## 密封类（Sealed Class）
密封类用于创建受限制的类层次结构。密封类的子类必须定义在同一个文件中，使得密封类特别适合用于表示有限的状态或结果，尤其是在处理 API 响应、UI 状态等场景中。
- 受限继承：密封类的所有子类必须定义在同一文件中，保证了类型的封闭性，避免意外扩展。
- 类型安全：在 when 表达式中使用密封类时，不需要添加 else 分支（如果已处理所有子类）。
- 适用场景：非常适合表示有限状态（如 UI 状态、网络请求结果等）。
```
// 定义一个密封类 NetworkResult，用于封装网络请求的状态
sealed class NetworkResult {
    // 表示请求成功的状态，带有泛型数据 T
    data class Success<T>(val data: T): NetworkResult()
    // 表示请求失败的状态，包含错误信息
    data class Failure(val error: Throwable): NetworkResult()
    // 表示加载中的状态，没有任何额外信息
    object Loading : NetworkResult()
}
// 定义一个函数，用于处理不同的网络请求状态
fun handleNetworkResult(result: NetworkResult) {
    when (result) {
        // 如果是成功状态，输出数据
        is NetworkResult.Success -> println("Data: ${result.data}")
        // 如果是失败状态，输出错误信息
        is NetworkResult.Failure -> println("Error: ${result.error.message}")
        // 如果是加载中状态，输出“Loading...”
        NetworkResult.Loading -> println("Loading...")
    }
}
```
## 数据类（Data Class）
数据类专门用于表示纯数据对象，它的目的是存储数据。数据类自动提供了 equals()、hashCode()、toString()、copy() 等方法，实现了值对象的标准功能。
- 自动生成方法：数据类自动生成 equals()、hashCode()、toString()、copy() 方法。
- 主要构造函数：数据类必须有至少一个参数，且参数不能使用 var 或 val。
- 适用场景：适合表示值对象、DTO（数据传输对象）、Entity 等。
```
data class ApiResponse(
    val status: String,
    val data: List<User>
)
data class User(
    val id: Int,
    val name: String,
    val email: String
)
fun printUserInfo(user: User) {
    println(user.toString())  // 自动生成的 toString() 方法
}
```
## 嵌套类（Nested Class）
嵌套类是一个定义在另一个类中的类，不会自动持有其外部类的引用。它相当于一个独立的类，只是定义在其他类的作用范围内。嵌套类不能直接访问外部类的成员。
- 嵌套类默认是 static 的，不会持有外部类的引用。
- 适用场景：适用于逻辑上属于外部类的一部分，但不需要访问外部类实例的成员。例如，适用于常量、工具方法等。
```
class NetworkManager {
    class Config {
        companion object {
            const val TIMEOUT = 30
            const val BASE_URL = "https://api.example.com"
        }
    }
}
```
## 内部类（Inner Class）
内部类是一个定义在另一个类中的类，声明时需要使用 inner 关键字。与嵌套类不同，内部类会持有外部类的引用，因此可以访问外部类的成员。
- 内部类使用 inner 关键字声明。
- 持有外部类的引用，可以访问外部类的属性和方法。
- 适用场景：当需要访问外部类成员时，适合使用内部类。尤其在 Android 开发中，经常用于回调处理和事件监听。
```
class MyAdapter(val items: List<String>) : RecyclerView.Adapter<MyAdapter.MyViewHolder>() {
    inner class MyViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        fun bind(position: Int) {
            // 可以访问外部类的成员变量 items
            val item = items[position]
            itemView.textView.text = item
        }
    }
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item_view, parent, false)
        return MyViewHolder(view)
    }
    override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        holder.bind(position)
    }
    override fun getItemCount(): Int = items.size
}
```
## 单例类（Object）
Kotlin 使用 object 关键字来创建单例类，无需手动编写单例模式代码。object 声明的类在整个应用程序中只会存在一个实例。
- 自动线程安全：Kotlin 的 object 声明的单例类在多线程环境中也是线程安全的，不需要额外的同步机制。
- 懒加载：单例类的实例在首次使用时才会被创建（懒加载）。
- 生命周期：单例类的实例会一直存在，直到整个应用程序结束。
### 单例类的应用场景
在 Android 中，单例类用于一些全局唯一的资源或工具类，比如网络请求管理、数据库实例、配置管理、日志工具等。
### 注意事项
- 资源释放：单例类的生命周期较长，如果持有大量资源（如数据库连接），应在不使用时及时释放。
- 不适合短生命周期对象：单例类适合存放应用全局共享资源，不适合存放生命周期较短的对象。
## 枚举类（enum class）
枚举类（enum class）用于定义一组常量集合。每个常量都是枚举类的一个实例。枚举类在 Android 开发中常用于表示固定的、可枚举的值，比如方向、状态、请求类型等。
### 枚举类的特性
- 每个枚举常量都是枚举类的实例：可以通过点操作符访问。
- 支持属性和方法：可以为枚举类定义属性和方法。
- 构造函数：枚举常量可以拥有构造参数，并且可以为枚举类定义构造函数。
### 枚举类的其他方法
- values()：返回所有枚举常量的数组。
- valueOf(name: String)：根据名称返回对应的枚举常量，如果没有找到则抛出异常。
## 伴生类（companion object）
在 Kotlin 中使用 companion object 声明一个伴生对象。每个类最多只能有一个伴生对象。
### 伴生对象的特性
- 类的静态成员：伴生对象的成员相当于类的静态成员，但 Kotlin 没有 static 关键字。
- 单例：每个类只能有一个伴生对象，且伴生对象本身是单例的。
- 名称可省略：伴生对象默认名称为 Companion，但可以为伴生对象指定自定义名称。
### 注意事项
- 伴生对象不是真正的静态成员：尽管可以通过类名访问伴生对象成员，但它们并不是真正的 Java 静态成员。在 JVM 字节码中，伴生对象成员其实是通过静态内部类实现的。
- @JvmStatic 注解：如果需要将伴生对象成员作为真正的静态方法或属性使用，可以用 @JvmStatic 注解。
## 内联类（Value Class）
内联类使用 value class 关键字定义，并且只能包含一个非空的属性。由于 Kotlin 的 value class 不会在运行时创建实际的对象，因此在处理简单数据结构（如 ID、值类型）时可以显著减少内存开销。
### 内联类的特性
- 仅包含一个非空的属性：内联类只能包含一个非空的属性。
- 内联性：在编译时，内联类的实例将会被替换为其中的值，以减少对象创建的开销。
- 不可继承：内联类不能被继承或扩展，因为它们的实现方式不支持多态。
- 类型安全：内联类可以为基本类型或普通类型提供额外的类型安全检查，避免错误传值。
### 内联类的限制
- 仅一个非空属性：内联类只能有一个非空属性，如果需要多个属性，可以使用 data class。
- 不能继承：内联类不能继承其他类，也不能被继承。
- 不能包含 init 代码块：内联类不支持包含复杂的初始化代码，通常只用于简单的值包装。
- 反射限制：由于内联类在运行时不创建实例，因此在使用反射时会遇到一些限制。
### JVM 内联（@JvmInline）注解
@JvmInline 注解用于确保 Kotlin 编译器会对该类进行内联处理，且在 JVM 上可以更好地与 Java 兼容。
### 内联类与数据类的区别
| 特性       | 内联类（Value Class）             | 数据类（Data Class）                          |
|------------|----------------------------------|----------------------------------------------|
| 属性数量   | 仅支持一个非空属性                 | 支持多个属性                                  |
| 对象创建   | 在运行时不会创建对象               | 每次创建新实例都会分配对象                     |
| 继承       | 不支持继承                        | 支持实现接口，但不支持类继承                   |
| 使用场景   | 用于包装简单值，提高性能           | 用于封装多个值，并自动生成 `copy()` 方法等      |

