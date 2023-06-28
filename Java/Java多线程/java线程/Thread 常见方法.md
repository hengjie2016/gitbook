# join

**1. join() 的示例和作用**

```java
// 父线程
publicclassParent {
 publicstaticvoidmain(String[] args) {
  // 创建child对象，此时child表示的线程处于NEW状态
  Child child = newChild();
  // child表示的线程转换为RUNNABLE状态
  child.start();
  // 等待child线程运行完再继续运行
  child.join();
 }
}

/ 子线程
publicclassChild extendsThread {
 publicvoidrun() {
  // ...
 }
}
```

上面代码展示了两个类：Parent（父线程类），Child（子线程类）。

Parent.main()方法是程序的入口，通过Child child = new Child(); 新建child子线程（此时 child子线程处于NEW状态）；

然后调用child.start()（child子线程状态转换为RUNNABLE）；

再调用child.join()，此时，Parent父线程会等待child子线程运行完再继续运行。

下图是我总结的 Java 线程状态转换图：

![img](https://xue-online.oss-cn-hangzhou.aliyuncs.com/uPic/20220831/1661948853_EIzya0.png)

**1.2 join() 的作用**

让父线程等待子线程结束之后才能继续运行。



当我们调用某个线程的这个方法时，这个方法会挂起调用线程，直到被调用线程结束执行，调用线程才会继续执行。



join() 一共有三个重载版本，分别是无参、一个参数、两个参数：



```java
public final void join() throws InterruptedException;

publicfinalsynchronizedvoidjoin(longmillis) throwsInterruptedException;

publicfinalsynchronizedvoidjoin(longmillis, intnanos) throwsInterruptedException;
```

(1)三个方法都被final修饰，无法被子类重写。

(2)join(long),join(long, long) 是synchronized method，同步的对象是当前线程实例。

(2)无参版本和两个参数版本最终都调用了一个参数的版本。

(3) join() 和 join(0) 是等价的，表示一直等下去；join(非0)表示等待一段时间。

从源码可以看到 join(0)调用了Object.wait(0)，其中Object.wait(0)会一直等待，直到被notify/中断才返回。

while(isAlive())是为了防止子线程伪唤醒(spurious wakeup)，只要子线程没有TERMINATED的，父线程就需要继续等下去。

(4) join() 和 sleep() 一样，可以被中断（被中断时，会抛出 InterrupptedException 异常）；不同的是，join() 内部调用了 wait()，会出让锁，而 sleep() 会一直保持锁。





以本文开头的代码为例，我们分析一下代码逻辑：

调用链：Parent.main() -> child.join() -> child.join(0) -> child.wait(0)（此时 Parent线程会获得 child 实例作为锁，其他线程可以进入 child.join() ，但不可以进入 child.join(0)， 因为child.join(0)是同步方法）。

如果 child 线程是 Active，则调用 child.wait(0)（为了防止子线程 spurious wakeup, 需要将 wait(0) 放入while(isAlive())循环中。

一旦 child 线程不为 Active （状态为 TERMINATED）,child.notifyAll()会被调用-> child.wait(0)返回 -> child.join(0)返回 -> child.join()返回 -> Parent.main()继续执行, 子线程会调用this.notify()，child.wait(0)会返回到child.join(0) ，child.join(0)会返回到 child.join(), child.join() 会返回到 Parent 父线程，Parent 父线程就可以继续运行下去了。

**3. 对网上其他分析 join() 的文章提出疑问**



a. 子线程结束之后，"会唤醒主线程"，父线程重新获取cpu执行权，继续运行。

这里感谢kerwinX的留言，子线程结束后，子线程的this.notifyAll()会被调用，join()返回，父线程只要获取到锁和CPU，就可以继续运行下去了。

b. join() 将几个并行的线程"合并为一个单线程"执行。

我理解这个说法的意思，但是这样描述只会让读者更难理解。

在调用 join() 方法的程序中，原来的多个线程仍然多个线程，并没有发生“合并为一个单线程”。真正发生的是调用join() 的线程进入 TIMED_WAITING 状态，等待 join() 所属线程运行结束后再继续运行。











# 五、几个方法的比较

Thread.sleep(long millis)，一定是当前线程调用此方法，当前线程进入TIMED_WAITING状态，但不释放对象锁，millis后线程自动苏醒进入就绪状态。作用：给其它线程执行机会的最佳方式。

Thread.yield()，一定是当前线程调用此方法，当前线程放弃获取的CPU时间片，但不释放锁资源，由运行状态变为就绪状态，让OS再次选择线程。作用：让相同优先级的线程轮流执行，但并不保证一定会轮流执行。实际中无法保证yield()达到让步目的，因为让步的线程还有可能被线程调度程序再次选中。Thread.yield()不会导致阻塞。该方法与sleep()类似，只是不能由用户指定暂停多长时间。

thread.join()/thread.join(long millis)，当前线程里调用其它线程t的join方法，当前线程进入WAITING/TIMED_WAITING状态，当前线程不会释放已经持有的对象锁。线程t执行完毕或者millis时间到，当前线程一般情况下进入RUNNABLE状态，也有可能进入BLOCKED状态（因为join是基于wait实现的）。

obj.wait()，当前线程调用对象的wait()方法，当前线程释放对象锁，进入等待队列。依靠notify()/notifyAll()唤醒或者wait(long timeout) timeout时间到自动唤醒。

obj.notify()唤醒在此对象监视器上等待的单个线程，选择是任意性的。notifyAll()唤醒在此对象监视器上等待的所有线程。

LockSupport.park()/LockSupport.parkNanos(long nanos),LockSupport.parkUntil(long deadlines), 当前线程进入WAITING/TIMED_WAITING状态。对比wait方法,不需要获得锁就可以让线程进入WAITING/TIMED_WAITING状态，需要通过LockSupport.unpark(Thread thread)唤醒。

# 六、疑问

等待队列里许许多多的线程都wait()在一个对象上，此时某一线程调用了对象的notify()方法，那唤醒的到底是哪个线程？随机？队列FIFO？or sth else？Java文档就简单的写了句：选择是任意性的（The choice is arbitrary and occurs at the discretion of the implementation）。