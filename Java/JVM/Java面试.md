# 基本数据类型



2、short：16位



3、int：32位



4、long：64位



5、float：32位



6、double：64位



7、boolean：1位



1、byte：8位、一个字节



8、char：16位



# 引用数据类型



接口、类、数组



# 访问修饰符

|           | 同一个类 | 同一个包 | 不同包子类 | 不同包非子类 |
| --------- | -------- | -------- | ---------- | ------------ |
| private   | √        |          |            |              |
| default   | √        | √        |            |              |
| protected | √        | √        | √          |              |
| public    | √        | √        | √          | √            |



# 继承



子类可以继承父类的非private变量、方法



子类可以对父类进行扩展，子类可以重写父类的方法



构造方法不能被继承，可以通过super访问，super只能在子类构造方法的第一行



# 多态



Java可以描述一个事物的多种形态，Student类继承了Person类



Java实现多态有三个必要条件：继承、重写、向上转型



静态的多态:重载



动态的多态:重写



**上转型** :自动转换：父类声明子类实例化



1、父类引用变量调用方法时，会调用子类重写后的方法



2、父类引用变量无法调用子类特有的方法



**下转型** :强制转换



# 重写、重载区别



**重写**



子类的方法名、参数列表、返回值类型跟父类一样



**重载**



本类中方法名相同、参数列表不同



重写和重载可以改变返回值类型、异常、访问修饰符



# 普通类、抽象类区别



1、普通类可以实例化，抽象类不能实例化



2、如果子类只实现了部分抽象方法，那么该子类还是一个抽象类



如果子类只实现了部分抽象方法，那么该子类还是一个抽象类



3、抽象方法不能用static修饰



# 接口、类区别



1、接口不能用于实例化对象



2、接口没有构造方法，main方法



3、接口中的成员变量只能是public static final类型



4、接口支持多继承



# 抽象类、接口区别



1、抽象类用abstract修饰，接口用interface修饰



2、抽象类不能实例化，接口不能被实例化



3、继承抽象类使用extends，子类只能继承一个类，实现接口使用implements，子类可以实现多个接口



4、抽象类可以包含非抽象方法，接口中方法为抽象方法，默认public abstract修饰，Java8接口中可以有default方法



5、抽象类中可以有构造方法，接口中不能有构造方法



6、抽象类中可以包含静态方法，接口中不能包含静态方法，Java8接口中可以有静态方法



7、抽象类中可以有普通成员变量，也可以有静态成员变量，接口中成员变量只能是常量，默认public static final修饰



8、抽象类中抽象方法的访问类型可以是public、protected和default，接口中的抽象方法访问类型只能是public



# 标记接口



没有任何方法的接口被称为标记接口



Serializable：标记类是否支持序列化的



Cloneable：标记该对象是否拥有克隆的能力



RandomAccess：判断集合是否能快速访问



# 关键字



### 1、this、super



this表示当前对象，可以在本类的任何地方使用



super表示当前对象的父类对象，super方法只能在子类构造方法的第一行



### 2、instanceof



用来判断某个对象是否属于某种数据类型



### 3、final



修饰类不可被继承，修饰方法不可被重写，但可以重载，修饰变量一经赋值，值不可变



### 4、abstract



abstract不能与final、static、private共存



### 5、goto、const



保留字



# ==、equals区别



==：基本数据类型比较值，引用数据类型比较地址



equals：基本类型不能使用equals方法，引用数据类型默认比较地址，重写equals方法后比较值（String、Integer、Date比较值，StringBuffer、StringBuilder比较地址）



# &、&&区别



&运算符：按位与、逻辑与



&&运算符：短路与



# Java是值传递还是引用传递



基本数据类型属于值传递，引用数据类型属于引用传递



String及其包装类，属于引用传递的定义，表现为值传递



# int、Integer区别



1、Integer是int的包装类，int是一种基本数据类型



2、Integer必须实例化后才能使用，int不需要



3、Integer默认值是null，int默认值是0



4、Integer有缓存



# Integer 的缓存机制



在Integer类的内部有一个**IntegerCache**的私有内部类，它缓存了从-128到127之间的所有的整数对象



```java
public abstract class Test {
    public static void main(String[] args) {
        Integer a = 5;
        Integer b = 5;
        System.out.println(a == b);//true

        Integer c = new Integer(5);
        Integer d = new Integer(5);
        System.out.println(c == d);//false
        //非new生成的Integer变量指向的是java常量池中的对象，而new Integer()生成的变量指向堆中新建的对象，两者在内存中的地址不同
        System.out.println(a == c);//false

        Integer e = 0;
        // + 不适用于Integer对象，c、d、e进行自动拆箱操作，5 == 5 + 0
        System.out.println(a == b + e);//true
        System.out.println(c == d + e);//true
    }
}
```



Byte、Short、Integer、Long、Character都具有缓存机制。缓存工作都是在静态块中完成，在类生命周期的初始化阶段执行。



Byte，Short，Integer，Long为 -128 到 127



Character范围为 0 到 127，除了Integer可以指定缓存范围，其它类都不行



Float，Double没有缓存机制



# String



`String str1 = "A";`如果常量池中存在”s”,那么str1直接引用,如果常量池中不存在”s”,在常量池中创建”s”字符串,再引用.会在常量池中添加一个新的ABCD字符，str1指向常量池的ABCD



```
String str2 = new String("A");
```



new会在堆中开辟一块空间来存储String对象，检查常量池中是否有A字符，如果有的话堆中的String对象指向常量池中的ABCD，而str2则指向堆空间中的String对象



```java
public class Test {
    public static void main(String[] args) {
        String s1 = "abcd";
        String s2 = new String("abcd");
        System.out.println(s1 == s2);//false

        String str = "str";
        String ing = "ing";
        String string1 = "str" + "ing";
        String string2 = str + ing;
        //只有使用引号包含文本的方式创建的String对象之间使用“+”连接产生的新对象才会被加入字符串池中。
        //对于所有包含new方式新建对象（包括null）的“+”连接表达式，它所产生的新对象都不会被加入字符串池中。
        System.out.println(string1 == string2);//false

        String string = "string";
        System.out.println(string1 == string);//true

        // abc在常量池中
        String a = "abc";
        //栈中b指向常量池中的abc
        String b = "abc";
        // 在堆内存中重新开辟了一个abc的空间
        String c = new String("abc");
        //检查字符串池里是否存在"abc"这么一个字符串，如果存在，就返回池里的字符串；
        // 如果不存在，该方法会 把"abc"添加到字符串池中，然后再返回它的引用
        //该方法是返回当前字符串在常量池中的引用，如果当前字符串在常量池中没有引用，则会把堆里面的引用复制到常量池中，然后再返回引用；
        String d = c.intern();

        System.out.println(a==b);//true
        System.out.println(a==c);//false
        System.out.println(a.equals(c));//true
        System.out.println(a==d);//true
    }
}
```



String s1 = "a";
String s2 = "b";
String s3 = "a" + "b";
String s4 = s1 + s2; //new stringbuilder().append("a").append("b").tostring()
new string("ab")//堆里面
String s5 = "ab"; //串池里面
String s6 = s4.intern();
// 问
System.out.println(s3 == s4);
System.out.println(s3 == s5);
System.out.println(s3 == s6);
String x2 = new String("c") + new String("d");
String x1 = "cd";
x2.intern();
// 问，如果调换了【最后两行代码】的位置呢，如果是jdk1.6呢
System.out.println(x1 == x2);



# String、StringBuffer、StringBuilder



都是final类，不允许被继承



StringBuffer：早期版本，线程安全，效率低，StringBuffer在append方法上增加了sycronize线程锁



StringBuilder：后期版本，线程不安全，效率高



String底层是一个不可变的字符数组，StringBuilder和StringBuffer底层是可变的字符数组



三者在执行速度方面比较：StringBuilder>StringBuffer>String



String可以用常量、构造方法创建对象，StringBuffer、StringBuilder只能用构造方法



字符串的+操作其本质是创建了StringBuilder对象进行append操作，然后使用StringBuilder类的toString方法



# Object方法



#### 1、clone方法



调用clone方法需要实现Cloneable接口



浅克隆和深克隆的主要区别在于是否支持引用类型的成员变量的复制



#### 2、getClass方法



#### 3、equals方法



#### 4、hashCode方法



一般重写equals方法的时候都会重写hashCode方法



如果两个对象相同，hashCode值一定相同



如果两个对象的hashCode相同，它们并不一定相同



# final、finally、finalize区别



final：修饰类不可被继承，修饰方法不可被重写，修饰变量一经赋值，值不可变



finally：异常处理关键字，只能用在try/catch语句中，无论有没有异常，finally代码块总会执行



finalize：Object类的一个protected方法，它是在对象被垃圾回收之前由Java虚拟机来调用的



finally



1、无论有没有异常，finally代码块总会执行



2、当try/catch中有return时，finally代码块依然会执行



3、finally是在return后执行，方法的返回值是在finally之前确定的



4、如果return是引用数据类型，则finally可以改变（StringBuilder）



5、如果finally有return，则会覆盖try/catch里的return



# 对象克隆



1、 实现Cloneable接口并重写Object类中的clone()方法



2、实现Serializable接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深度克隆。



# 反射



反射就是把类中的各种成分映射成Java对象，成员变量、方法、构造方法



Class、Constructor、Filed、Method



setAccessible(true)



## 获取Class对象



1、通过已知对象获取，Object的getClass方法



2、通过类名.Class



3、通过Class的静态方法，Class.forName



## Class.forName、classloader区别



相同点：java中Class.forName()和classloader都可以用来对类进行加载
不同点：
class.forName除了将类的.class文件加载到jvm中之外，还会对类进行解释，执行类中的static块
而classloader只做一件事情，就是将.class文件加载到jvm中，不会执行static中的内容，只有在newinstance才会去执行static块。



# 创建对象的四种方式



1、使用new创建对象



2、使用反射的机制创建对象、使用Class类的newInstance方法



3、采用clone、要调用clone方法需要实现Cloneable接口



4、采用序列化机制要实现实现Serializable接口



86、如何通过反射创建对象？
答：



方法1：通过类对象调用newInstance()方法，例如：String.class.newInstance()
方法2：通过类对象的getConstructor()或getDeclaredConstructor()方法获得构造器（Constructor）对象并调用其newInstance()方法创建对象，例如：String.class.getConstructor(String.class).newInstance("Hello");



# 类加载顺序



1、先静态后普通



2、先父类后子类



静态字段 > 静态代码块 > 普通字段 > 构造代码块 > 构造函数



1、父类静态字段初始化



2、父类静态代码块



3、子类静态字段初始化



4、子类静态代码块



5、父类普通字段初始化



6、父类构造代码块（{//代码}） –优先于构造函数执行



7、父类构造函数



8、子类普通字段初始化



9、子类构造代码块



10、子类构造函数



# 异常



exception 和 error都是 Throwable 的子类



exception程序可以捕获的异常，error程序无法处理的错误，比如OutOfMemoryError



## throw、throws区别



1、throw用在方法实现中，throws用在方法声明中



2、throw只能抛出一种异常，throws可以抛出多个异常