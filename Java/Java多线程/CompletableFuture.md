，创建线程的方式只有两种：继承Thread或者实现Runnable接口。 但是这两种方法都存在一个缺陷，没有返回值，



有两种格式，一种是supply开头的方法，一种是run开头的方法

supply开头：这种方法，可以返回异步线程执行之后的结果
run开头：这种不会返回结果，就只是执行线程任务
或者可以通过一个简单的无参构造器

CompletableFuture<String> completableFuture = new CompletableFuture<String>();
小贴士：我们注意到，在实例化方法中，我们是可以指定Executor参数的，当我们不指定的试话，我们所开的并行线程使用的是默认系统及公共线程池ForkJoinPool，而且这些线程都是守护线程。我们在编程的时候需要谨慎使用守护线程，如果将我们普通的用户线程设置成守护线程，当我们的程序主线程结束，JVM中不存在其余用户线程，那么CompletableFuture的守护线程会直接退出，造成任务无法完成的问题，其余的包括守护线程阻塞问题我就不在本篇赘述。

Java8实战：

其中supplyAsync用于有返回值的任务，runAsync则用于没有返回值的任务。Executor参数可以手动指定线程池，否则默认ForkJoinPool.commonPool()系统级公共线程池，注意：这些线程都是Daemon线程，主线程结束Daemon线程不结束，只有JVM关闭时，生命周期终止。

![在这里插入图片描述](https://xue-online.oss-cn-hangzhou.aliyuncs.com/uPic/20221019/1666149410_Ksl6hu.png)



# 一、创建异步任务

`CompletableFuture`提供了四个静态方法来创建一个异步操作：

```java
// 不带返回值的异步请求，默认线程池
public static CompletableFuture<Void> runAsync(Runnable runnable)
// 不带返回值的异步请求，可以自定义线程池
public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor)

// 带返回值异步请求，默认线程池
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier)
// 带返回值的异步请求，可以自定义线程池
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier, Executor executor)
```

supplyAsync是创建带有返回值的异步任务。它有如下两个方法，一个是使用默认线程池（ForkJoinPool.commonPool()）的方法，一个是带有自定义线程池的重载方法

runAsync是创建没有返回值的异步任务。它有如下两个方法，一个是使用默认线程池（ForkJoinPool.commonPool()）的方法，一个是带有自定义线程池的重载方法

### 3.获取任务结果的方法

```java

// 如果完成则返回结果，否则就抛出具体的异常
public T get() throws InterruptedException, ExecutionException 
 
// 最大时间等待返回结果，否则就抛出具体异常
public T get(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException
 
// 完成时返回结果值，否则抛出unchecked异常。为了更好地符合通用函数形式的使用，如果完成此 CompletableFuture所涉及的计算引发异常，则此方法将引发unchecked异常并将底层异常作为其原因
public T join()
 
// 如果完成则返回结果值（或抛出任何遇到的异常），否则返回给定的 valueIfAbsent。
public T getNow(T valueIfAbsent)
 
// 如果任务没有完成，返回的值设置为给定值
public boolean complete(T value)
 
// 如果任务没有完成，就抛出给定异常
public boolean completeExceptionally(Throwable ex) 
 

```

# 二、异步回调处理

### 1.thenApply和thenApplyAsync

 thenApply 表示某个任务执行完成后执行的动作，即回调方法，会将该任务的执行结果即方法返回值作为入参传递到回调方法中，带有返回值。

从上面代码和测试结果我们发现thenApply和thenApplyAsync区别在于，使用thenApply方法时子任务与父任务使用的是同一个线程，而thenApplyAsync在子任务中是另起一个线程执行任务，并且thenApplyAsync可以自定义线程池，默认的使用ForkJoinPool.commonPool()线程池。



### 2.thenAccept和thenAcceptAsync

 thenAccep表示某个任务执行完成后执行的动作，即回调方法，会将该任务的执行结果即方法返回值作为入参传递到回调方法中，无返回值。

测试结果我们发现thenAccep和thenAccepAsync区别在于，使用thenAccep方法时子任务与父任务使用的是同一个线程，而thenAccepAsync在子任务中可能是另起一个线程执行任务，并且thenAccepAsync可以自定义线程池，默认的使用ForkJoinPool.commonPool()线程池。



### 2.thenRun和thenRunAsync

 thenRun表示某个任务执行完成后执行的动作，即回调方法，无入参，无返回值。

从上面代码和测试结果我们发现thenRun和thenRunAsync区别在于，使用thenRun方法时子任务与父任务使用的是同一个线程，而thenRunAsync在子任务中可能是另起一个线程执行任务，并且thenRunAsync可以自定义线程池，默认的使用ForkJoinPool.commonPool()线程池。

### 3.whenComplete和whenCompleteAsync

 whenComplete是当某个任务执行完成后执行的回调方法，会将执行结果或者执行期间抛出的异常传递给回调方法，如果是正常执行则异常为null，回调方法对应的CompletableFuture的result和该任务一致，如果该任务正常执行，则get方法返回执行结果，如果是执行异常，则get方法抛出异常。

 whenCompleteAsync和whenComplete区别也是whenCompleteAsync可能会另起一个线程执行任务，并且thenRunAsync可以自定义线程池，默认的使用ForkJoinPool.commonPool()线程池。

### 4.handle和handleAsync

 跟whenComplete基本一致，区别在于handle的回调方法有返回值。





# 三、多任务组合处理 

#### 1.thenCombine、thenAcceptBoth 和runAfterBoth

这三个方法都是将两个CompletableFuture组合起来处理，只有两个任务都正常完成时，才进行下阶段任务。

thenCombine会将两个任务的执行结果作为所提供函数的参数，且该方法有返回值；

thenAcceptBoth同样将两个任务的执行结果作为方法入参，但是无返回值；

runAfterBoth没有入参，也没有返回值。注意两个任务中只要有一个执行异常，则将该异常信息作为指定任务的执行结果。



### 2.applyToEither、acceptEither和runAfterEither

这三个方法和上面一样也是将两个CompletableFuture组合起来处理，当有一个任务正常完成时，就会进行下阶段任务。

applyToEither会将已经完成任务的执行结果作为所提供函数的参数，且该方法有返回值；

acceptEither同样将已经完成任务的执行结果作为方法入参，但是无返回值；

runAfterEither没有入参，也没有返回值。



上面可以看出cf1任务完成需要2秒，cf2任务完成需要5秒，使用applyToEither组合两个任务时，只要有其中一个任务完成时，就会执行cf3任务，显然cf1任务先完成了并且将自己任务的结果传值给了cf3任务，cf3任务中打印了接收到cf1任务完成，接着完成自己的任务，并返回cf3任务完成；acceptEither和runAfterEither类似，acceptEither会将cf1任务的结果作为cf3任务的入参，但cf3任务完成时并无返回值；runAfterEither不会将cf1任务的结果作为cf3任务的入参，它是没有任务入参，执行完自己的任务后也并无返回值。



### 3.allOf / anyOf 

allOf：CompletableFuture是多个任务都执行完成后才会执行，只有有一个任务执行异常，则返回的CompletableFuture执行get方法时会抛出异常，如果都是正常执行，则get返回null。

anyOf ：CompletableFuture是多个任务只要有一个任务执行完成，则返回的CompletableFuture执行get方法时会抛出异常，如果都是正常执行，则get返回执行完成任务的结果。



| 分类                  | 方法                                                         | 说明                                                         | 返回值                                                       |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 异步执行一个线程      | CompletableFuture.runAsvnc(Runnable)<br/>(CompletableFuture.supplyAsync(Supplier) | 默认使用ForkloinPool.commonPoo线程池<br />也可指定Executor参数指定线程池 | runAsvnc返回CompletableFuture< Void>:无返回值<br/>supplyAsync返回CompletableFuture<T>:有返回值<br/>调用get0.joinQ返回阻塞等待线程结束 |
| 2个线程依次执行       | thenApply                                                    | 获取前一个线程的结果，转换<br />thenApplySync:可指定线程池，其他方法类似 | 有返回值                                                     |
|                       | thenAccept                                                   | 获取前一个线程的结果，消费                                   | 无返回值                                                     |
|                       | thenRun                                                      | 忽略前一个线程的结果，执行额外的逻辑                         | 无返回值                                                     |
|                       | whenComplete                                                 | 获取前一个线程的结果或异常，消费                             | 不影响上一线程返回值                                         |
|                       | exceptionallv                                                | 前面线程异常时，执行，一般跟whencomplete配合使用。<br/>捕获异常范围跟包括前面所有异生线程<br/>如thenApply.thenAccepto.exceptionally0 | 有返回值                                                     |
|                       | handle                                                       | 相当干whencompletetexceptionallv<br/>根据是否产生异常内部if else分支处理业务逻辑 | 有返回值                                                     |
| 等待2个线程都执行完   | thenCombine                                                  | 12个线程都要有返回值，等待都结束．结果合并转换。             | 有返回值                                                     |
|                       | thenAcceptBoth                                               | 2个线程都要有返回值， 等待都结束，结果合并消费。             | 无返回值                                                     |
|                       | runAfterBoth                                                 | 2个线程无需要有返回值，等待都结束．执行其他逻辑。            | 无返回值                                                     |
| 等待2个线程任一执行完 | lapplyToEither                                               | 12个线程都要有返回值，等待任一先结束，转换其结果。           | 有返回值                                                     |
|                       | accentToEither                                               | 2个线程都要有返回值，等待任一先结束，消费其结果。            | 无返回值                                                     |
|                       | runAfterEither                                               | 2个线程无需要有返回值，等待任一结束．执行其他逻辑。          | 无返回值                                                     |
| 名个线程等待          | CompletableFuture.anvOf(cf1.cf2.cf3).join0                   | 名个线程任一执行完即返回。                                   | 有返回值 Obiect                                              |
|                       | CompletableFuture allOficf1 cf2 cf3) ioin                    | 多个线程全部执行完返回                                       | 无返回值                                                     |







