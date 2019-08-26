# 协程

## 为什么需要协程

异步编程中最为常见的场景：在后台线程执行一个复杂的任务，下一个任务依赖于上一个任务的执行结果，所以必须等待上一个任务执行完成后才能开始执行。

## 定义

协程通过将复杂性放入库来简化异步编程，程序的逻辑可以在协程中顺序地表达，而底层库会为我们解决其异步性。该库可以将用户代码的相关部分包装为回调、订阅相关事件、在不同线程上调度执行，而代码则保持如同顺序执行一样简单

协程就像非常轻量级的线程。线程是由系统调度的，线程切换或线程阻塞的开销都比较大。而协程依赖于线程，但是协程挂起时不需要阻塞线程，几乎是无代价的，协程是由开发者控制的。所以协程也像用户态的线程，非常轻量级，一个线程中可以创建任意个协程。

协程可以简化异步编程，可以顺序地表达程序，协程也提供了一种避免阻塞线程并用更廉价、更可控的操作替代线程阻塞的方法 – 协程挂起

### CoroutineScope和CoroutineContext

CoroutineScope可以理解为协程本身，包含了CoroutineContext
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