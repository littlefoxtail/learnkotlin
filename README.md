# learnkotlin
学業kotlin

## 基础概念

### Numbers
类型和位数差不多都与java相同

* 不同点
chat在java中占2个字节且属于Number
kotlin并不是

### 文字常量
1. java中没有二进制直接表示数字，只能用其他禁止间接表示，而kotlin表示也不支持8进制。
2. 小数默认为Double要转添加`f`或`F`，数字默认Int，要转添加`L`
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
    归类到`import`里面了


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
如果一个非抽象类没有声明任何的构造函数，它会有一个不带参数的主构造函数，构造函数的可见性是`public`，如果你不希望你的类有一个公有构造，需要声明一个带有非默认可见性的空的构造函数

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
```
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
- 位于顶层或者是`object`的一个成员
- 用`String`或原生类型值初始化
- 没有自定义`getter`

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

```
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
```
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

















