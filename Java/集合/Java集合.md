# 1、Collection



Collection、Collections区别



1、Collection是一个接口，是List、Set、Queue的父接口，Collections是个一个工具类，提供了一系列的静态方法（搜索、排序、线程安全化）



2、java.util



Collection线程安全的类Vector，CopyOnWriteArrayList，Hashtable，Stack，Enumeration，ConcurrentHashMap



## 1、List



List：有序可重复



### 1、ArrayList



继承自AbstractList，实现了List，Cloneable，Serializable，RandomAccess接口



底层是Object数组，默认大小是10，可以存null，新容量是老容量的1.5倍，线程不安全



适合遍历，不适合插入删除



Array、ArrayList 区别



1、Array 可以包含基本类型和对象类型，ArrayList 只能包含对象类型



2、Array 大小是固定的，ArrayList 的大小是动态变化的



fail-fast（快速失败）机制，是Java集合中的一种错误检测机制，多线程下用迭代器遍历一个集合对象时，如果遍历过程中对集合对象进行了修改（增加、删除），就有可能会发生fail-fast



1、遍历过程中，涉及到改变modCount的地方加上synchronized



2、使用CopyOnWriteArrayList来替换ArrayList



### 2、Vector



ArrayList和Vector的区别



1、Vector早期版本，线程安全，ArrayList后期版本，线程不安全



2、Vector新容量是老容量的2倍，ArrayList新容量是老容量的1.5倍



3、Vector加载因子为1，ArrayList加载因子为0.5



### 3、LinkedList



继承于AbstractSequentialList，实现了List，Queue接口



底层是双向链表，适合插入或删除，不适合遍历，线程不安全



ArrayList、LinkedList区别



1、ArrayList底层是动态数组，LinkedList底层是双向链表



2、ArrayList适合遍历，不适合插入删除；LinkedList适合插入或删除，不适合遍历



3、LinkedList比ArrayList需要占用更多的内存



### 4、Stack



先进后出，继承自Vector类，只有一个无参构造方法



## 2、Set



Set：无序无重复，线程不安全



### 1、HashSet



HashSet底层是HashMap，添加元素调用的是HashMap的put方法，可以存null



初始容量为16，加载因子为0.75，新容量是老容量的2倍



无重复的原则：利用存入对象的equals方法、hashCode方法



Set集合发现重复的元素，拒绝存入



### 2、TreeSet



TreeSet底层是TreeMap（红黑树），TreeSet不允许存null



元素必须实现Comparable接口，并重写compareTo方法



TreeSet是SortedSet接口唯一的实现类，TreeSet可以确保集合元素处于排序状态



HashSet、TreeSet区别



1、HashSet底层是HashMap，用的是HashMap的key，TreeSet底层是TreeMap（红黑树），用的是TreeMap的key



2、HashSet可以存null，TreeSet不允许存null



3、HashSet不能保证元素的排序顺序，TreeSet中的数据是排好序的，元素必须实现Comparable接口，并重写compareTo方法



Comparable接口、Comparator接口区别



1、Comparable接口在java.lang包，方法是compareTo，Comparator接口在java.util包，方法是compare



2、Comparable在比较类里重写compareTo方法，Comparator是实现一个比较器



### 3、LinkedHashSet



## 3、Queue



子类LinkedList，ArrayQueue



# 2、Map



key：无序无重复



value：无序无重复



## 1、HashMap



继承自AbstractMap类，实现Map接口



初始容量16，加载因子0.75，最大初始容量2^30，扩容为原来的两倍，线程不安全（put方法）



当底层数组长度为2的n次幂时，哈希碰撞的概率小，查询效率比较高



#### HashMap1.7、1.8区别



1、1.7是数组+链表，1.8是数组+链表+红黑树



2、put方法，1.7是头插，1.8尾插



3、扩容时1.7会颠倒链表的顺序，1.8保持原链表的顺序



4、当桶中链表元素个数超过8时会转换成红黑树，红黑树中的元素个数小于6就会还原为链表



## 2、Hashtable



HashMap继承自AbstractMap类，Hashtable继承自Dictionary类，都实现了Map、Cloneable、Serializable接口



Hashtable比HashMap多提供了elements方法和contains方法



## 3、LinkedHashMap



继承自HashMap类



底层是哈希表、链表，链表是用来维护其元素的插入顺序，LinkedHashMap是插入有序的



## 4、TreeMap



有序的



底层是红黑树，继承自AbstractMap，实现了Cloneable、 Serializable接口



## 5、ConcurrentHashMap



底层是HashMap和链表，HashMap存储数据，链表存储锁，ConcurrentHashMap采用的是分段锁机制，将hash表分为16个桶



ConcurrentHashMap、HashMap、HashTable



1、都实现了Map接口，ConcurrentHashMap、HashMap继承自AbstractMap，HashTable继承自Dictionary



2、HashMap线程不安全，ConcurrentHashMap、Hashtable线程安全



3、HashMap允许键、值为null，ConcurrentHashMap、HashTable不允许键、值为null



4、Hashtable是key.hashCode取余，ConcurrentHashMap、HashMap先对hashCode进行再哈希，然后再与(桶数 - 1)进行取余运算



5、ConcurrentHashMap、HashTable在插入元素前扩容检查，HashMap在元素插入后扩容检查



6、HashTable初始容量为11，扩容2倍+1，HashMap初始容量16，扩容2倍



## 6、Properties