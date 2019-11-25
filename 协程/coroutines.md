# 协程

## 为什么需要协程

异步编程中最为常见的场景：在后台线程执行一个复杂的任务，下一个任务依赖于上一个任务的执行结果，所以必须等待上一个任务执行完成后才能开始执行。

协程是什么？一种组织代码运行的方式。
协程和线程，进程不同，它通常不是由操作系统底层直接提供支持，而是通过应用层的库实现，Kotlin的协程，其实就是依赖java/android的线程/线程池再加上一些对上下文的控制逻辑来实现的。

1. 因为某些协程库的实现使用了任务分发（比如Kotlin），于是可以在协程函数（也就是Kotlin coroutine中的suspend方法）中无限递归调用自身而不会栈溢出，当然这依赖具体实现，不能保证全部。
2. 如上文所说，协程是一种组织代码的方式，因此可以将异步调用组织成顺序调用的书写形式，因而免除了回调地狱问题。
3. 因为协程本质上是一种用户态线程，在线程基础上再加了一层自己的调度，它的创建和delay延迟调用都开销很小。

举例场景，代码中三个函数，后一个函数都依赖前一个函数的执行结果：

```kt
fun requestToken(): Token {
    // makes request for a token & waits
    return token;
}

fun createPost(token: Token, item: Item): Post {
    // sends item to the server & waits
    return post
}

fun processPost(post: Post) {
  // does some local processing of result
}
```

三个函数都是耗时操作，因此不能再ui线程中运行，而且后两个函数都依赖于前一个函数的执行结果，三个任务不能并行运行

### 回调

常见的做法是使用回调

```kt
fun requestTokeAsync(cb: (Token) -> Unit) {..}

fun createPostAsync(token: Token, item: Item, cb: (Post) -> Unit) {..}

fun postItem(item: Item) {
    requestTokenAsync { token ->
        createPostAsync(token, item) { post ->
            processPost(post)
        }

    }
}
```

### Future

Java 8 引入的CompletableFuture可以将多个任务串联起来，可以避免多层嵌套的问题

### Rx编程

### 协程方式

```kt
suspend fun requestToken(): Token { ... } //挂起函数
suspend fun createPost(token: Token, item: Item): Post { ... } //挂起函数
fun processPost(post: Post) { ... }

fun postItem(item: Item) {
    GlobalScope.launch {
        val token = requestToken()
        val post = createPost(token, item)
        processPost(post)
        //需要异常处理，直接加上try/catch，语句即可
    }
}
```

## 定义

协程通过将复杂性放入库来简化异步编程，程序的逻辑可以在协程中顺序地表达，而底层库会为我们解决其异步性。该库可以将用户代码的相关部分包装为回调、订阅相关事件、在不同线程上调度执行，而代码则保持如同顺序执行一样简单

协程就像非常轻量级的线程。线程是由系统调度的，线程切换或线程阻塞的开销都比较大。而协程依赖于线程，但是协程挂起时不需要阻塞线程，几乎是无代价的，协程是由开发者控制的。所以协程也像用户态的线程，非常轻量级，一个线程中可以创建任意个协程。

协程可以简化异步编程，可以顺序地表达程序，协程也提供了一种避免阻塞线程并用更廉价、更可控的操作替代线程阻塞的方法 – 协程挂起

## 基本概念

### 挂起函数

```kt
suspend fun requestToke(): Token {..}//挂起函数
suspend fun createPost(token: Token, item: Item): Post {..}//挂起函数
fun processPost(post: Post {..}
```

`suspend`修饰符标记，这表示两个函数都是挂起函数。
挂起函数能够与普通函数相同方式获取参数和返回值，但是调用函数可能挂起协程，**挂起函数挂起协程时，不会阻塞协程所在的线程**。挂起函数执行完成后会恢复协程，后面的代码才会继续执行。但是挂起函数只能在协程中或其他挂起函数中调用。

## 在Kotlin使用

```kt
suspend fun doSomething(foo: Foo): Bar {..}
```

suspend关键字，就告诉编译器，这个方法是一个可中断方法。对于这样的方法，无法在常规方法中直接调用，只能在声明了接收suspend lambda原型的方法中才能调用，如内置的coroutine-builder:launch, async，当然也可以在另一个suspend方法中调用

```kt
public fun CoroutineScope.launch(.., block: suspend CoroutineScope.() -> Unit): Job {

}
```

唯一特别之处就是最后一个lambda参数前，多了一个suspend限定

### CoroutineScope和CoroutineContext

CoroutineScope可以理解为协程本身，包含了CoroutineContext。
CoroutineContex协程上下文，是一些元素的集合，主要包括Job和CoroutineDispather元素，可以代表一个协程场景

### CoroutineDispatcher

协程调度器，决定协程所在的线程或线程池，它可以指定协程运行于特定的一个线程、一个线程池或者不指定任何线程。
coroutines-core中 CoroutineDispatcher 有三种标准实现Dispatchers.Default、Dispatchers.IO，Dispatchers.Main和Dispatchers.Unconfined，Unconfined 就是不指定线程。

launch函数定义如果不指定CoroutineDispatcher或者没有其他的ContinuationInterceptor，默认协程调度器就是Dispatcher.Default。

### Job & Deferred

Job，任务，封装了协程中需要执行的代码逻辑，Job可以取消并且有简单的生命周期
Job完成时是没有返回值的，如果需要返回值的话。应该使用Deferred

### Coroutine builders

CoroutineScope.launch函数属于协程构建器，Kotlin中还有其他几种builders，负责创建协程

#### launch

CoroutineScope.launch是最常用的Coroutine builders，不阻塞当前线程，在后台创建一个新协程，也可以指定协程调度器

#### runBlocking

CoroutineScope.runBlocking是创建一个新的协程同时阻塞当前线程，直到协程结束。这个不应该在协程中使用，主要是为main函数和测试设计的

#### withContext

不会创建新的协程，在指定协程上运行挂起代码块，并挂起该协程直至代码块运行完成

#### async

CoroutineScope.async可以实现与launch builder一样的效果，在后台创建一个新协程，唯一的区别是它有返回值，因为CoroutineScope.async返回值是Derferred类型

```java
fun main(args: Array<String>) = runBlocking { // start main coroutine
    val time = measureTimeMillis {
        val one = async { doSomethingUsefulOne() }  // start async one coroutine without suspend main coroutine
        val two = async { doSomethingUsefulTwo() }  // start async two coroutine without suspend main coroutine
        println("The answer is ${one.await() + two.await()}") // suspend main coroutine for waiting two async coroutines to finish
    }
    println("Completed in $time ms")
}
```

获取CoroutineScope.async {}的返回值需要通过await()函数，它也是是个挂起函数，调用时会挂起当前协程直到 async 中代码执行完并返回某个值。

[深入理解协程的调度](深入理解协程的调度.md)
