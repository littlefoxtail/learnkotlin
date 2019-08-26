# Kotlin

[kotlin Android Extensions](./android_extensions.md)

## 基础概念

### Numbers

类型和位数差不多都与java相同

* 不同点
    chat在java中占2个字节且属于Number
    kotlin并不是

### 文字常量

1. java中没有二进制直接表示数字，只能用其他禁止间接表示，而kotlin表示也不支持8进制。
2. 小数默认为Double要转添加`f`或`F`，数字默认Int，要转添加`L`
3. kotlin可以用下划线使得文字容易阅读
4. 可空的数字引用或涉及泛型，数字被装箱，其他的都存储为JVM基础单元
    > `==`判断是两个数据的值是否相等；`===`则判定的两个数据是否是同一个对象，也就是说===的判断条件更加苛刻

    必须显示转换，才能将强转为更宽泛的类型

    ```kotlin
    val b: Byte = 1
    val i: Int = b.toInt()
    ```

5. 位运算没有特殊字符，但是有方法
6. 浮点字符串比较多了一个范围检查 `a..b`, `x in a..b`, `x !in a..b`

### Array

1. 创建可以使用`arrayOf`
2. `arrayOfNulls`可以创建含null元素的数组
3. Array构造函数可以是 根据index来生成 val asc = Array(5, {i -> (i * i).toString()})
4. []符号可以调用`get()`和`set()`方法
5. kotlin 中的arrays和java中的不一样，是不变,不允许`Array<String>`变成`Array<Any>`

### String

1. 可以使用for循环
2. 常规方式进行转义，原始字符串由三引号(""")分隔，不包含转义，可以包含换行符和其他任何字符
3. trimMargin用于去除空白 默认`|`用作边距前缀
4. 字符串可能包含模板表达式，并且其结果被串联到字符串中

### 定义包结构

* 大体上与java相符

```kotlin
import foo.Bar
```

```kotlin
import foo.*
```

* 不同点
  * 遇到类名冲突，可以用as修改冲突的实体
  
    ```kotlin
    import foo.Bar
    import bar.Bar as bBar 
    ```

  * 没有`import static`
    归类到`import`里面了

## Control Flow

### if

Kotlin中没有三元运算符

大体与java相同

1. 这是kotlin中的三元 if (a > b) a else b

### when

when 用来替换switch操作

when 也可以用来替换`if-else`

### Loops

```kotlin
for (item in arrar) {

}
```

1. While Loops和java一致
2. break continue与java一致

## 返回和跳转

默认的break、return、continue是对最近的封闭有效

### break和continue标签

kotlin中可以标记为label，写法`@$标识符`

```kotlin
loop@ for (item in 1..50) {
      println(item)
      if (item == 25) {
        println("执行了25")
        break@loop
      }
    }
```

### return标签

1. return默认返回给当前位置调用这个函数的调用者
2. return标签可以使得在函数内部返回

```kotlin
 listOf(1, 3, 5, 7, 9).forEach lit@ {
      if (it == 3) return@lit
      println(it)
    }

    println("隐式的标签")
```

## 类和继承

1. 构造函数的写法与java不一样
2. 初始化和属性初始化器是按照它们出现在类体中的顺序执行的
3. 与普通属性一样，主构造函数中生命的属性可以是可变的(var)或只读的(val)

### 次构造函数

类也可以声明前缀有`constructor`

```kotlin
class Person {
    construcor(parent: Person) {
        parent.children.add(this)
    }
}
```

次构造函数委托给主构造，可以直接委托或者间接委托

```kotlin
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

如果一个非抽象类没有声明任何的构造函数，它会有一个不带参数的主构造函数，构造函数的可见性是`public`，如果你不希望你的类有一个公有构造，需要声明一个带有非默认可见性的空的构造函数

### 创建实例

kotlin中没有`new`关键字
创建一个类的实例，跟普通函数一样调用构造函数

```kotlin
val invoice = Invoice()
```

### 继承

Kotlin中所有的类都有一个共同的超类`Any`，这对于没有超类型声明的类是默认超类：

> Any并不是java.lang.Object；尤其是，它除了equals()、hashCode()和toString()外没有任何成员

要声明一个显示的超类型，把类型放到类头的冒号之后

```kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

> open 标注正好与java中`final`相反，它允许其他类从这个类继承。默认Kotlin所有的类都是`final`

### 覆盖方法

Kotlin需要显示标注可覆盖的成员和覆盖后的成员

```kotlin
open class Base {
    open fun v() {}
    fun nv{}
}

class Derived() : Base() {
    override fun v() {}
}
```

### 覆盖属性

直接覆盖或者覆盖`get`方法

```kotlin
open class Foo {
    open val x: Int get() {
        return 1
    }
}

class Bar1 : Foo() {
    override val x: Int = 2
}
```

可以用一个`var`属性覆盖一个`val`，因为一个`val`本质上声明了一个`getter`方法，而将其覆盖为`var`只是在其子类中额外声明一个`setter`方法

### 派生类初始化顺序

先执行基类的初始化，再进行派生类初始化

### 调用超类

1. `super`关键字(super.)与java些许不同
2. 在一个内部类中访问外部类的超类， `super@Outer`

### 覆盖规则

如果一个类从直接超类继承相同的多个实现，它必须覆盖这个成员并提供自己的实现，使用`super<Base>`来表示从哪个超类型

### 伴生对象

与java不同，在Kotlin中没有静态方法。在大多数情况下，它建议简单地使用包级函数

## 属性和字段

### Getters与Setters

很多部分可以省略

```text
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```

### 幕后字段

在Kotlin类中不能直接声明字段。然而，当一个属性需要一个幕后字段时，Kotlin会自动提供`field`
只能在属性的访问器内

### 幕后属性

解决需求例如：

* 对外表现为val属性，只能读不能写；
* 在类内部表现为var尚需经，也就是说只能在类内部改变它的值

```java
private  var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap()
        }
        return _table ?: throw AssertionError("set to be null")
    }
```

### 编译器常量

已知值的属性可以用`const`修饰符标记为*编译器常量*

* 位于顶层或者是`object`的一个成员
* 用`String`或原生类型值初始化
* 没有自定义`getter`

### 延迟初始化属性与变量

`lateinit`

1. 该修饰符只能用于在类体中，顶层属性与局部变量
2. 该属性或变量必须为非空类型，并且不能是原生类型。
3. 该属性在初始化之前被访问，会抛出一个明确的异常

- 如果是值可修改的变量（即在之后的使用中可能被重新赋值），使用 lateInit 模式
- 如果变量的初始化取决于外部对象（例如需要一些外部变量参与初始化），使用 lateInit 模式。这种情况下，lazy 模式也可行但并不直接适用。
- 如果变量仅仅初始化一次并且全局共享，且更多的是内部使用（依赖于类内部的变量），请使用 lazy 模式。从实现的角度来看，lateinit 模式仍然可用，但 lazy 模式更有利于封装你的初始化代码。

## 接口

接口可以有属性但必须声明为抽象或提供访问器实现

```kotlin
interface MyInterface {
    fun bar()
    fun foo()
}
```

## 可见性修饰符

kotlin中有四个可见性修饰符`private`、`protected`、`internal`和`public`
如果没有显示指定修饰符的话，默认可见性是`public`

### 包

顶层声明：

```kotlin
package foo

fun baz() {

}

class Bar {

}
```

1. public 这意味着你的声明将随处可见
2. private，它会只在它的文件内可见
3. internal，它会在相同模块内随处可见
4. protected, 不适于顶层声明

### 类和接口

1. private 意味着只在这个类内部（包含其所有成员）可见；
2. protected—— 和 private一样 + 在子类中可见。
3. internal —— 能见到类声明的 本模块内 的任何客户端都可见其 internal 成员；
4. public —— 能见到类声明的任何客户端都可见其 public 成员。

### 构造函数

可以指定一个类的主构造函数的可见性，以下语法：

```kotlin
class C private constructor(a: Int) {...}
```

### 局部变量

局部变量、函数和类不能有可见性修饰符

### 模块

可见性修饰符`internal`意味着该成员只在相同模块内可见。一个模块是编译在一起的一套Kotlin文件：

* 一个intellij IDEA模块
* 一个Maven项目
* 一个Gradle源集
* 一次`<kotlin>`Ant任务执行所编译的一套文件

## 扩展

### 扩展函数

```kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    //this对应着被扩展类型的对象
    val tmp = this[index1]
    this[index1] = this[index2]
    this[index2] = tmp
}
```

1. 扩展不能真正的修改他们所扩展的类。通过定义一个扩展，仅仅是可以通过该类型的变量用点表达式去调用这个新函数
2. 调用扩展函数是由函数调用所在的表达式的类型来决定的，而不是运行时
3. 扩展函数接收者可以为空，这样的扩展在对象变量上调用，其值可以为null
4. 扩展属性不能有初始化器，因为没有幕后属性

### 扩展属性

```kotlin
val <T> List<T>.lastInde: Int
    get() = size - 1
```

## 数据类

只保存数据的类

```kotlin
data class User(val name: String, val age: Int)
```

* 主构造函数需要至少有一个参数
* 主构造函数的所有参数需要标记为`val`或`var`
* 数据不能是抽象、开放、密封或者内部

### 在类体重声明的属性

对于自动生成的函数，只能使用在主构造函数内部定义的属性

### 复制

```kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
```

### 数据类和解构声明

数据类生成的*Component*函数使它们可在解构声明中使用

## 密封类

密封类用来表示受限的类继承结构；当一个值为有限集中的类型、而不能有任何其他类型时。

1. 密封类也可以有子类，但是所有的子类都必须在于密封类自身相同的文件中声明
2. 密封类自身是抽象的，它不能直接实例化并可以有抽象(abstract)成员
3. 密封类不允许有非`private`构造函数(其构造函数默认为`private`)
4. 扩展密封子类的类(间接继承者)可以放在任何位置，而无需再同一个文件中

## 泛型

与java类似，但如果可以推断出来，运行省略

1. 在java中泛型是不型变的，`List<String>`并不是`List<Object>`的子类型，但是带`extends`限定的通配符类型使得类型是协变的(convariant)

`<? extends Object>` 对应 `<out Object>`
`<? super String>` 对应`<in String>`

### 型变

一个类`C`的类型参数`T`被声明为`out`时，它就只能出现在`C`的成员的**输出**-位置，但回报是`C<Base>`可以安全地作为`C<Derived>`的超类

`out`修饰符称为**型变注解**，并且由于它的类型参数声明处提供，所以讲声明处型变。这与java的使用处型变相反。

转换：消费者in,生产者out

### 星投影

1. 对于Foo `<out T : TUpper>`，其中`T`是一个具有上界`TUpper`的协变类型，`Foo<*>`等价于`Foo<out TUpper>`。这意味着当`T`未知时，可以安全地从`Foo <*>`读取`TUpper`
2. 对于Foo `<in T>`，其中`T`是一个逆变类型参数，`Foo <*>`等价于`Foo <in Nothing>`
3. 对于Foo `<T : TUpper>`，其中`T`是一个具有上界`TUpper`的不型变类型参数，`Foo<*>`对于读取值时等价于`Foo<out Tupper>`，而对于写值等价于`Foo<in Nothing>`

### 泛型函数

类可以有类型参数，函数也可以。放在函数名称之前

### 泛型约束

能够替换给定类型参数的所有可能类型的集合可以由泛型约束限制

#### 上界

```kotlin
fun <T : Comparable<T>> sort(list: List<T>) {
    //...
}
```

1. 冒号之后指定的类型是上界：只有`Comparable<T>`的子类型可以替代`T`
2. 默认的上届是`Any?`。在尖括号只能指定一个上界。

## 嵌套类与内部类

### 内部类

`inner`

### 匿名内部类

使用对象表达式创建匿名内部类实例：

```kotlin
window.addMouseListener(object: MouseAdapter()) {
    override fun mouseClicked(e: MouseEvent) {

    }

    override fun mouseEntered(e: MouseEvent) {

    }
}
```

如果对象是函数式java接口，可以使用带接口类型前缀的lambda表达式创建它

```kotlin
val listener = ActionListener {println("clicked")}
```

## 枚举类

```kotlin
enum class Direction {
    NORTH, SOURTH, WEST, EAST
}
```

### 匿名类

枚举常量可以声明自己的匿名类

```kotlin
enum class ProtocolState {
    WAITING {
        override fun signal() = TALKING
    },

    TALKING {
        override fun signal() = WAITING
    },

    abstract fun signal(): ProtocolState
}
```

### 枚举常量

```kotlin
EnumClass.valueOf(value: String): EnumClass
EnumClass.values(): Array<EnumClass>
```

## 对象

### 对象表达式

```kotlin
window.addMouseListener(object: MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        //...
    }

    override fun mouseEntered(e: MouseEvent) {
        //...
    }
})
```

构造函数与超类型

```kotlin
val ab: A = object : A(1), B {
    override val y = 15
}
```

如果不需要特殊超类

```kotlin
fun foo() {
    val adHoc = object {
        val x: Int = 0
        val y: Int = 0
    }
    print(abHoc.x + adHoc.y)
}
```

匿名对象可以用作只在本地和私有作用域中声明的类型。

```kotlin
class C {
    //私有函数，所以其返回类型是匿名函数对象类型
    private fun foo() = object {
        val x: String = "x"
    }

    //共有函数，所以其返回类型是Any
    fun publicfoo() = object {
        val x: String = "x"
    }

    fun bar() {
        val x1 = foo().x //没有问题
        val x2 = publicFoo().x //错误：未能解析的引用"x"
    }
}
```

与java匿名内部类一样，对象表达式可以访问来自包含它的作用域的变量。(不只是final)

### 对象声明

单例模式

```kotlin
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {

    }

    val allDataProviders: Collection<DataProvider>
        get() = // ...
}
```

这称为对象声明。并且总是在`object`关键字后跟一个名称。就像变量声明一样，对象声明不是一个表达式，不能用在赋值语句的右边:

1. 对象声明的初始化过程是线程安全的
2. 如需要引入该对象，直接使用其名称即可
3. 直接使用名称就行(跟java用静态似的)
4. 可以有超类型

### 伴生对象

类内部的对象声明可以用`companion`关键字标记

```kotlin
class MyClass {
    companion object Factory {
        fun create() : MyClass = MyClass()
    }
}
```

该伴生对象的成员可通过只使用类名作为限定符来调用

```kotlin
val instance = MyClass.create()
```

1. 可以省略伴生对象的名称，这种情况下使用名称: `Companion`:

```kotlin
class MyClass {
    companion object {

    }
}
val x = MyClass.Companion
```

#### 对象表达式和对象声明之间的语义差异

* 对象表达式是在使用他们的地方立即执行(及初始化)的
* 对象声明是在第一次被访问到时延迟初始化的
* 伴生对象的初始化是在相应的类被加载（解析）时，与java静态初始化器语义相匹配

## 委托

委托模式已经证明是实现继承的一个很好的替代方式
by-子句，编译器将生产转发给委托类的所有方法

## 委托属性

kotlin支持*委托属性*
语法：`val/var` <属性名>: <类型> by <表达式>
在by后面的表达式是该委托，因为属性对应的`get()`和`set()`会被委托给它的`getValue()`和`setValue()`方法。
属性的委托不必实现任何的接口，但是需要提供一个`getValue()`函数（和`setValue()`--对于`var`属性）

```kotlin
class Example {
    var p: String by Delegate()
}
```

委托属性的要求：

1. 对于只读属性(val)，委托必须提供一个名为`getValue`的函数，该函数接受以下参数：
    * thisRef 必须与属性所有者类型相同或者是它的超类型
    * property 必须是类型KProperty<*>及其超类型

2. 这个函数必须返回与属性相同的类型(或其子类型)
3. 两函数都需要用`operator`关键字来进行标记
4. 委托类可以实现包含所需`operator`方法的`ReadOnlyProperty` 或 `ReadWriteProperty` 接口之一。 这俩接口是在 Kotlin 标准库中声明的

翻译规则：
Kotlin 编译器都会生成辅助属性并委托给它

```kotlin
class C {
    var prop : Type by MyDelegate()
}

//编译器生成的相应代码
class C {
    private val prop$delegate = MyDelegate()
    val prop: Type
        get() = prop$delegate.getValue(this. this::prop)
        set(value: Type) = prop$delegate.setValue(this, this::prop, value) 
}
```

### 标准委托

#### 延迟属性Lazy

lazy是接受一个lambda并返回一个`Lazy<T>`实例的函数，返回的实例可以作为实现延迟属性的委托：
第一次调用`get()`会执行已传递给`lazy()`的lambda表达式并记录结果，后续调用`get()`只是返回记录的结果

#### 可观察性Observable

`Delegates.observable()`接受两个参数：初始值和修改时处理程序(handler)。每当给属性赋值时会调用该处理程序

#### 把属性存储在映射中

```kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int by map
}
```

```kotlin
val user = User(mapOf(
    "name" to "John Doe"
    "age" to 23
))
```

委托属性会从这个映射中取值(通过字符串键一一属性的)

```kotlin
println(user.name)
println(user.age)
```

## 函数

```kotlin
fun double(x: Int): Int {
    return 2 * x
}
```

### 参数

函数参数使用Pascal表示法定义，即*name:type*。参数用逗号隔开

可以通过使用星号操作符将可变数量参数，以命名形式传入:

```kotlin
fun foo(vararg string: String) {/** .. */}
foo(strings = *arrayOf("a", "b", "c"))
```

### 单表达式

当函数返回单个表达式时，可以省略花括号并且在=符号之后指定代码

```kotlin
fun double(x: Int): Int = x * 2
```

当返回值类型可由编译器推断时，显示声明返回类型是可选的

### 显示返回类型

Kotlin 不推断具有块代码体的函数的返回类型

### 可变数量的参数(Varargs)

函数的参数可以用`varargs`修饰符标记

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList()
    for (t in ts)
        result.add(t)
    return result
}
```

```kotlin
val list = asList(1, 2, 3)
```

### 中缀表示法

标有`infix`关键字的函数也可以使用中缀表示法。

* 它们必须是成员函数或扩展函数
* 它们必须只有一个参数
* 其参数不接受可变数量的参数且不能有默认值

```kotlin
infix fun Int.shl(x: Int): Int {
    // ...
}
```

### 函数作用域

#### 局部函数

Kotlin支持局部函数，即一个函数在另一个函数内部
局部函数可以访问外部函数(即闭包)的局部变量

#### 成员函数

成员函数是在类或对象内部定义的函数

#### 泛型函数

函数可以有泛型参数，通过在函数名前使用尖括号指定

```kotlin
fun <T> singletonList(item: T): List<T> {
    //
}
```

## 高阶函数和Lambda

### 高阶函数

高阶函数是将函数用作参数或返回值的函数。

### 函数类型

对于接受另一函数作为参数的函数，我们必须为该函数指定函数类型。

```kotlin
fun <T> max(collection: Collection<T>, less: (T, T) -> Boolean): T? {
    var max: T? = null
    for (it in collection)
        if (max == null || less(max, it))
            max = it
    return max
}
```

参数`less`的类型是`(T, T) -> Boolean`，即一个接受两个类型`T`的参数并返回一个布尔值的函数
less作为一个函数使用：通过传入的两个`T`类型的参数来调用

```kotlin
val compare: (x: T, y: T) -> Int = ...
```

如果声明一个函数类型的可空变量，请将整个函数类型括在括号中并在其后加上句号

```kotlin
val sum: ((Int, Int) -> Int)? = null
```

### Lambda表达式语法

Lambda表达式的完整语法形式，即函数类型的字面值如下：

```kotlin
val sum = { x: Int, y: Int -> x + y}
```

完整语法形式如下：

```kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y}
```

lambda表示式总是括在花括号中，完整语法形式的参数声明放在花括号内，并有可选的类型标注，
函数体跟在一个`->`符号之后，如果推断出该lambda的返回类型不是Unit，那么该lambda主体
中的最后一个(或可能是单个)表达式可视为返回值

一个lambda表达式只有一个参数是很常见的。如果Kotlin可以自己计算出签名，它允许我们不声明唯一的参数，
并且将隐含地位我们声明其名称为`it`：

1. lambda返回值

可以使用限定返回语法从lambda显示返回一个值。否则，将隐身返回最后一个表达式的值。因此，以下两个片段是等价：

```kotlin
ints.filter {
    val shouldFilter = it > 0
    shouldFilter
}
```

```kotlin
ints.filter {
    val shouldFilter = it > 0
    return@filter shouldFilter
}
```

### 匿名函数

lambda语法可以推断返回类型，但是如果要显示指定的话，可以使用匿名函数：

```kotlin
fun(x: Int, y: Int): Int = x + y
```

1. 参数和返回类型的指定凡是与常规函数相同，普通函数不能够从上下文推断的参数类型省略，但匿名函数可以
2. Lambda表达式与匿名函数之间的另一个区别是非局部返回的行为。一个不带标签的 return 语句总是在用 fun 关键字声明的函数中返回

### 闭包

Lambda表达式或匿名函数，可以访问其闭包。即在外部作用域中声明的变量。
与java不同的是可以修改闭包中捕获的变量:

```kotlin
val sum = 0
ints.filter { it > 0}
```

### 带接收者的函数字面值

Kotlin提供了使用指定的接收者对象调用函数字面值的功能。类似于扩展函数
带有接收者的函数类型的字面值可以在赋值或者传参中用于期待多出第一个参数为接收者类型的普通函数的地方

## 内联函数

使用高阶函数会带来一些运行时的效率损失：每个函数都是一个对象，并且会捕获一个闭包

使用`inline`修饰符标记`lock()`函数

```kotlin
inline fun <T> lock(lock: Lock, body: () -> T): T {

}
```

`inline`修饰符影响函数本身和传给它的lambda表达式：所有将这些都将内联到调用处

### 禁用内联

`noinline`

可内联的lambda表达式只能在内联函数内部调用或者作为可内联的参数传递，但是`noinline`的可以以任何方式操作:存储在字段中、传送它

## 其他

### 解构声明

有时候把一个对象*解构*成很多变量会很方便

```kotlin
val (name, age) = person
```

### 区间

区间表达式由具有操作符形式`..`的`rangeTo`函数辅以`in`和`!in`。区间是为任何可比较类型定义的，但对于整型原生类型，它有一个优化的实现

```kotlin
if (i in 1..10) {
    println(i)
}
```

整型区别(IntRange、LongRange、CharRange)有一个额外的特性：它们可以迭代。编译器负责将其转换为类似java的基于索引的`for`循环而无额外开销

### 类型的检查与转换"is"与"as"

#### is与!is操作符

在运行时通过使用`is`来检查对象是否符合给定对象

#### 智能转换

因为编译器跟中不可变值`is`检查以及显示转换，并在需要时自动插入转换

### 空安全

#### Elvis操作符

```kotlin
val l = b?.length ?: -1
```

`?:`左侧表达式非空，elvis操作符就返回其左侧表达式，否则返回右侧表达式。当且仅当左侧为空时，才会对右侧表达式求值
`throw`和`return`在Kotlin中都是表达式

#### !!操作符

非空断言运算符(!!)将任何值转换为非空类型，若该值为空则抛出异常。
`b!!`这会返回一个非空`b`值或者`b`为空，就会抛出`NPE`异常

#### 安全的类型转换

如果尝试转换不成功则返回null

```kotlin
val aInt: Int? = a as? Int
```

### This表达式

为了表示当前*接受者*使用`this`表达式

* 在类的成员中，this指的是该类的当前对象
* 在扩展函数或者带接接收者的函数字面值中，this表示在点左侧传递的接收者参数

### 反射

#### 函数引用

使用`::`操作符，可以把函数作为一个值传递。

```kotlin
fun isOdd(x: Int) = x %2 != 0
```

`::isOdd`是函数类型`(Int) -> Boolean`的一个值

```kotlin
val c = MyClass::class
```

该引用是`KClass`类型的值
Kotlin类引用于java类引用不同。要获得java类引用，在`KClass`实例上使用`.java`属性

String::toCharArray为类型`String`提供了一个扩展函数`String.() -> CharArray`

#### 绑定的类引用

通过使用对象作为接收者，可以用相同`::class`语法获取对象的类的引用

```kotlin
val ge = Generics()
println(ge::class)
```

#### 属性引用

1. 表达式`::x`求值为`KProperty<Int>`类型的属性对象，它允许我们使用`get()`读取它的值，或者使用`name`属性来获取属性名

2. 对于可变属性，`var y = 1`，`::y`返回KMutableProperty<Int>类型的一个值，该类型有一个`set()`方法

3. 属性引用可以用在不需要参数的函数处
    ```kotlin
    val strs = listOf("a", "bc", "def")
    println(strs.map(String::length))
    ```
4. 要访问类的成员属性
    ```kotlin
    class A(val p: Int)

    fun main(args: Array<String>) {
        val prop = A::p
        println(prop.get(A(1)))
    }
    ```

### 异常

`try`是一个表达式，即它可以有一个返回值：

```kotlin
val a: Int? = try {
    parseInt(input)
} catch (e: NumberFormatException) {
    Null
}
```

try-表达式的返回值是`try`块中最后一个表达式或者是（所有）`catch`块中的最后一个表达式。`finally`块中的内容不会影响表达式的结果。

#### 受检的异常

Kotlin没有受检的异常。

#### Nothing类型

在Kotlin中`throw`是表达式，所以你可以使用它作为Elvis表达式的一部分

```kotlin
val s = person.name ? throw IllegalArgumentException("Name required")
```

`throw`表达式的类型是特殊类型`Nothing`。该类型没有值，而是用于标记永远不能达到的代码位置。
