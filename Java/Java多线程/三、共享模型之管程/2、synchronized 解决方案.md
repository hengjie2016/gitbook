1）解决手段

为了避免临界区中的竞态条件发生，由多种手段可以达到。



阻塞式解决方案：synchronized ，Lock

非阻塞式解决方案：原子变量

现在讨论使用 synchronized 来进行解决，即俗称的对象锁，它采用互斥的方式让同一时刻至多只有一个线程持有对象锁，其他线程如果想获取这个锁就会阻塞住，这样就能保证拥有锁的线程可以安全的执行临界区内的代码，不用担心线程上下文切换。



2）synchronized 语法

```java
synchronized(对象) {
	//临界区
}
```

3）synchronized 加在方法上

加在成员方法上，锁住的是对象

```java
public class Test {
	// 在方法上加上synchronized关键字
	public synchronized void test() {
	
	}
	// 等价于
	public void test() {
		synchronized(this) { // 锁住的是对象
		
		}
	}
}
```

加在静态方法上，锁住的是类

```java
public class Test {
	// 在静态方法上加上 synchronized 关键字
	public synchronized static void test() {
	
	}
	//等价于
	public void test() {
		synchronized(Test.class) { // 锁住的是类
		
		}
	}
}
```