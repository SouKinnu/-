# 标准函数
## let
- 作用：通常用于处理非空对象，避免空指针异常（可以和安全调用操作符 ?. 搭配使用）。
- 接收者对象：作为 it 传入 lambda 表达式中。
- 返回值：返回 lambda 表达式的执行结果。
### 使用场景
- 对一个非空对象执行操作。
- 修改对象后返回一个新的结果，而不是修改对象本身。
```
val name = "Kotlin"
val length = name?.let {
    println("Name is $it")
    it.length
}
println(length) // 输出: 6
```
## with
- 作用：用于调用同一个对象的多个方法，可以更简洁地对该对象执行一系列操作。
- 接收者对象：直接作为 lambda 的上下文对象。
- 返回值：返回 lambda 表达式的执行结果。
### 使用场景
- 多次调用同一对象的方法或属性。
- 适合需要对对象操作但不返回自身的场景。
```
val person = Person("Alice", 25)
val bio = with(person) {
    println(name)
    println(age)
    "Name: $name, Age: $age" // 返回字符串
}
println(bio) // 输出: Name: Alice, Age: 25
```
## run
- 作用：类似于 let 和 with 的结合。常用于在一个对象的作用域内执行代码块，返回结果。
- 接收者对象：作为 this 引用。
- 返回值：返回 lambda 表达式的执行结果。
### 使用场景
- 在对象的上下文中初始化和计算一个结果。
- 适合需要对对象操作并返回计算结果的场景。
```
val result = person.run {
    println(name)
    println(age)
    age + 5 // 返回年龄+5
}
println(result) // 输出: 30
```
## apply
- 作用：用于在对象上执行一系列操作后返回该对象本身，通常用于初始化对象。
- 接收者对象：作为 this 引用。
- 返回值：返回接收者对象本身。
### 使用场景
- 配置对象属性，通常用于对象的初始化。
- 链式调用，配置完属性后直接返回对象本身。
```
val person = Person().apply {
    name = "Alice"
    age = 25
}
println(person) // 输出: Person(name=Alice, age=25)
```
## also
- 作用：通常用于在对象上执行一些附加操作（如日志、调试），返回对象本身。
- 接收者对象：作为 it 引用。
- 返回值：返回接收者对象本身。
### 使用场景
- 对对象做额外操作（如打印日志、验证等）而不改变其本身。
- 可以在链式调用中插入调试代码。
```
val person = Person("Alice", 25).also {
    println("Created new person: $it")
}
println(person) // 输出: Created new person: Person(name=Alice, age=25)
```
## 总结表格
| 函数   | 作用                      | 接收者对象引用 | 返回值                   | 适用场景                       |
|--------|---------------------------|----------------|--------------------------|--------------------------------|
| `let`  | 操作非空对象并返回结果     | `it`          | Lambda 表达式的结果      | 对象非空操作、转换结果         |
| `with` | 访问同一对象的多个方法     | `this`        | Lambda 表达式的结果      | 对同一对象执行多操作           |
| `run`  | 对象的作用域中计算并返回结果 | `this`        | Lambda 表达式的结果      | 初始化和计算返回值             |
| `apply`| 配置对象属性并返回对象本身  | `this`        | 对象本身                 | 初始化或修改对象属性           |
| `also` | 执行附加操作并返回对象本身  | `it`          | 对象本身                 | 调试、记录日志，不影响对象本身 |
# 单例模式
## 使用 object 关键字实现单例
在 Kotlin 中，object 声明即是一个单例。它会在首次访问时初始化，并且在应用生命周期中只存在一个实例。这种方式线程安全，适用于大多数场景。
- 优点：object 本身就是线程安全的，且不会重复创建实例。
- 适用场景：适用于无需延迟初始化的简单单例。
```
object DatabaseHelper {
    fun connect() {
        // 数据库连接逻辑
    }
}

fun main() {
    DatabaseHelper.connect() // 使用单例
}
```
## 伴生对象 companion object
在某些情况下，我们可能不希望整个类是单例的，而是希望类中某些特定功能是单例。这时可以使用 companion object，为类提供一个共享的静态实例。每个类只能有一个 companion object。
- 优点：companion object 提供了一种在普通类中实现单例的方式。适合需要懒加载的情况。
- 适用场景：适用于某些类中需要静态的功能或实例。
```
class NetworkManager private constructor() {
    companion object {
        val instance: NetworkManager by lazy { NetworkManager() }
    }

    fun makeRequest() {
        // 网络请求逻辑
    }
}

fun main() {
    NetworkManager.instance.makeRequest() // 使用伴生对象
}
```
## 使用 by lazy 延迟初始化单例
Kotlin 提供了 by lazy 委托属性来实现延迟加载，只有在首次调用时才会初始化。可以与 object 和伴生对象结合使用来实现懒加载单例。
- 优点：仅在需要时才创建实例，减少不必要的资源消耗。
- 适用场景：适用于需要懒加载和在首次使用时初始化的单例。
```
object ApiClient {
    val instance by lazy { ApiClient() }

    fun fetchData() {
        // 网络请求
    }
}

fun main() {
    ApiClient.instance.fetchData() // 延迟初始化
}
```
## 双重检查锁定（Double-Checked Locking）
在 Kotlin 中，一般无需手动实现双重检查锁定，因为 Kotlin 自带的单例实现已经是线程安全的。但若需要在 Java 中与 Kotlin 兼容，可能会使用这种方式。
- 优点：适用于需要手动管理线程安全的场景。
- 适用场景：一般仅在需要与 Java 兼容或自定义线程安全时使用。
```
class AnalyticsManager private constructor() {
    companion object {
        @Volatile
        private var instance: AnalyticsManager? = null

        fun getInstance(): AnalyticsManager {
            return instance ?: synchronized(this) {
                instance ?: AnalyticsManager().also { instance = it }
            }
        }
    }
}
```
# 泛型
泛型是一种强大的工具，用于在类、接口和函数中创建类型安全、灵活的代码结构。泛型允许我们在编写代码时不必指定具体的类型，而是使用类型参数，这样可以使代码在不同类型上重用，且在编译时进行类型检查。
## 泛型基础
泛型通常在尖括号 < > 中定义类型参数，例如 <`T`>，T 是一个占位符，表示某个具体的类型。这些类型参数在实际使用时会被具体的类型替换。
## 泛型函数
泛型函数允许我们在函数中使用类型参数，这在处理类型未知或多种类型时非常有用。
## 泛型约束（Constraints）
有时候需要对泛型类型进行限制，例如希望泛型类型必须是某个特定类或实现某个接口，可以通过 where 子句或使用类型上界限制类型参数。
### 类型上界（Upper Bound）
使用 : 类型 定义泛型上界，表示泛型类型必须是指定类型的子类。
```
fun <T : Number> sum(a: T, b: T): Double {
    return a.toDouble() + b.toDouble()
}

fun main() {
    println(sum(5, 10))      // 输出: 15.0
    // println(sum("5", "10")) // 错误，String 不是 Number 的子类
}
```
在这个例子中，<`T : Number`> 表示 T 必须是 Number 类的子类。因此，sum 函数可以接受 Int、Double 等数值类型。
## 泛型的型变（Variance）
Kotlin 的泛型支持型变，即如何在继承关系中使用不同的泛型类型。Kotlin 中使用 in 和 out 关键字来表示协变和逆变。
- 协变 (out T)：使泛型只读，允许子类型泛型对象赋值给父类型泛型对象。
- 逆变 (in T)：使泛型只写，允许父类型泛型对象赋值给子类型泛型对象。
### 协变（out）
out 关键字表示泛型是协变的，用于只读的场景。
```
open class Animal
class Dog : Animal()

class Cage<out T>(val animal: T)

fun main() {
    val dogCage: Cage<Dog> = Cage(Dog())
    val animalCage: Cage<Animal> = dogCage  // 协变，允许子类型泛型赋值给父类型泛型
}
```
### 逆变（in）
in 关键字表示泛型是逆变的，用于只写的场景。
```
class Box<in T> {
    fun put(item: T) {
        // 将 item 放入 Box
    }
}

fun main() {
    val animalBox: Box<Animal> = Box<Dog>()  // 逆变，允许父类型泛型赋值给子类型泛型
}
```
## 星投影（Star Projection）
在不知道泛型的具体类型或不关心具体类型时，可以使用星号 * 来代替泛型类型。星投影是通配符的一种，表示该泛型类型可以是任何类型。
```
fun printBoxContents(box: Box<*>) {
    println(box.item)
}

fun main() {
    val intBox = Box(5)
    val stringBox = Box("Hello")
    
    printBoxContents(intBox)       // 输出: 5
    printBoxContents(stringBox)     // 输出: Hello
}
```
在这个例子中，Box<*> 表示 Box 可以包含任何类型的数据。



