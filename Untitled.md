# 一、Java反射机制是什么？

## 1.1 基本概念

`Java`反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象方法的功能称为`Java`语言的反射机制。简单来说，反射机制指的是程序在运行时能够获取自身的信息。

在`Java`中，只要给定类的名字，就可以通过反射机制来获得类的所有信息。`Java`反射机制主要提供了以下功能，这些功能都位于`java.lang.reflect`包。

- 在运行时判断任意一个对象所属的类。
- 在运行时构造任意一个类的对象。
- 在运行时判断任意一个类所具有的成员变量和方法。
- 在运行时调用任意一个对象的方法。
- 生成动态代理。

要想知道一个类的属性和方法，必须先获取到该类的字节码文件对象。获取类的信息时，使用的就是`Class`类中的方法。所以先要获取到每一个字节码文件（`.class`）对应的`Class`类型的对象。

## 1.2 优缺点

优点：

- 能够运行时动态获取类的实例，大大提高系统的灵活性和扩展性。
- 与`Java`动态编译相结合，可以实现无比强大的功能。
- 对于`Java`这种先编译再运行的语言，能够让我们很方便的创建灵活的代码，这些代码可以在运行时装配，无需在组件之间进行源代码的链接，更加容易实现面向对象。

缺点：

- 反射会消耗一定的系统资源，因此，如果不需要动态地创建一个对象，那么就不需要用反射；
- 反射调用方法时可以忽略权限检查，获取这个类的私有方法和属性，因此可能会破坏类的封装性而导致安全问题。

## 1.3 reflect包与核心类Class

实现`java`反射机制的类都位于`java.lang.reflect`包中，`java.lang.Class`类是`Java`反射机制`API`中的核心类。

`java.lang.reflect`包提供了反射中用到类，主要的类说明如下：

- `Constructor`类：提供类的构造方法信息。
- `Field`类：提供类或接口中成员变量信息。
- `Method`类：提供类或接口成员方法信息。
- `Array`类：提供了动态创建和访问`Java`数组的方法。
- `Modifier`类：提供类和成员访问修饰符信息。

# 二、获取class对象

`java.lang.Class`类是实现反射的关键所在，`Class`类的一个实例表示`Java`的一种数据类型，包括类、接口、枚举、注解(`Annotation`)、数组、基本数据类型和`void`。`Class`没有公有的构造方法，`Class`实例是由`JVM`在类加载时自动创建的。

**获取class对象的三种方式：**

- `Object`类的`getClass()`方法
- 静态属性`class`
- `Class`类中的静态方法`forName()`

在程序代码中获得`Class`实例可以通过如下代码实现：



```java
// 1. 类名.class：通过类名的class属性获取Class对象
Class clazz1 = String.class;

// 2. 类的对象.getClass()：创建类的对象，调用对象的getClass()方法获取Class对象
String str2 = "Hello";
Class clazz2 = str2.getClass();

// 3. Class.forName("类的路径")：先将类的字节码文件加载到内存，再返回Class对象
Class clazz3 = Class.forName("java.lang.String");
```

每一种类型包括类和接口等，都有一个`class`静态变量可以获得`Class`实例。另外，每一个对象都有`getClass()`方法可以获得`Class`实例，该方法是由`Object`类提供的实例方法。

# 三、访问构造方法

为了能够**动态获取**对象构造方法的信息，首先需要通过下列方法之一创建一个`Constructor`类型的对象或者数组。

- getConstructors()
- getConstructor(Class<?>… parameterTypes)
- getDeclaredConstructors()
- getDeclaredConstructor(Class<?>... parameterTypes)

创建的每个`Constructor`对象表示一个构造方法，然后利用`Constructor`对象的方法操作构造方法。

> getConstructors()和getDeclaredConstructors()区别
> getConstructors()：获得某个类的所有的公共(public)的构造方法，包括父类中的构造方法。
> getDeclaredConstructors()：获得某个类的所有声明的构造方法，即包括public、private和proteced，但是不包括父类的申明构造方法。
> 同样类似的还有getMethods()和getDeclaredMethods()。

`Constructor`类的常用方法

| 方法名称            | 说明                                                         |
| :------------------ | :----------------------------------------------------------- |
| isVarArgs()         | 查看该构造方法是否允许带可变数量的参数，如果允许，返回`true`，否则返回`false` |
| getParameterTypes() | 按照声明顺序以`Class`数组的形式获取该构造方法各个参数的类型  |

通过`java.lang.reflect.Modifier`类可以解析出`getMocMers()`方法的返回值所表示的修饰符信息。在该类中提供了一系列用来解析的静态方法，既可以查看是否被指定的修饰符修饰，还可以字符串的形式获得所有修饰符。

## 3.1 Modifier类的常用静态方法

| 静态方法名称         | 说明                                                 |
| :------------------- | :--------------------------------------------------- |
| isStatic(int mod)    | 如果使用static修饰符修饰则返回true，否则返回false    |
| isPublic(int mod)    | 如果使用public修饰符修饰则返回true，否则返回false    |
| isProtected(int mod) | 如果使用protected修饰符修饰则返回true，否则返回false |
| isPrivate(int mod)   | 如果使用private修饰符修饰则返回true，否则返回false   |
| isFinal(int mod)     | 如果使用final修饰符修饰则返回true，否则返回false     |
| toString(int mod)    | 以字符串形式返回所有修饰符                           |

## 3.2 获取Constructor对象

可以通过`Class`对象来获取`Constructor`类的实例：



```java
Class aClass = ...; //获取Class对象
Constructor[] constructors = aClass.getConstructors();
```

返回的`Constructor`数组包含每一个声明为公有的（`Public`）构造方法。如果知道要访问的构造方法的方法参数类型，可以用下面的方法获取指定的构造方法，这例子返回的构造方法的方法参数为`String`类型：



```java
Class aClass = ...; //获取Class对象
Constructor constructor = aClass.getConstructor(new Class[]{String.class});
```

如果没有指定的构造方法能满足匹配的方法参数则会抛出：`NoSuchMethodException`。

## 3.3 构造方法参数

通过如下方式获取指定构造方法的方法参数信息：



```java
Constructor constructor = ...; //获取Constructor对象
Class[] parameterTypes = constructor.getParameterTypes();
```

## 3.4 利用Constructor对象实例化一个类

通过如下方法实例化一个类：



```java
Constructor constructor = MyObject.class.getConstructor(String.class);
MyObject myObject = (MyObject)constructor.newInstance("constructor-arg1");
```

`constructor.newInstance()`方法的方法参数是一个可变参数列表，但是当调用构造方法的时候必须提供精确的参数，即形参与实参必须一一对应。在这个例子中构造方法需要一个`String`类型的参数，那我们在调用`newInstance`方法的时候就必须传入一个`String`类型的参数。

# 四、访问方法(获取方法)

要**动态获取**一个对象方法的信息，首先需要通过下列方法之一创建一个`Method`类型的对象或者数组。

- getMethods()
- getMethods(String name, Class<?>… parameterTypes)
- getDeclaredMethods()
- getDeclaredMethods(String name, Class<?>... parameterTypes)

## 4.1 Method类的常用方法

| 静态方法名称                       | 说明                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| getName()                          | 获取该方法的名称                                             |
| getParameterType()                 | 按照声明顺序以Class数组的形式返回该方法各个参数的类型        |
| getReturnType()                    | 以Class对象的形式获得该方法的返回值类型                      |
| getExceptionTypes()                | 以Class数组的形式获得该方法可能抛出的异常类型                |
| invoke(Object obj, Object... args) | 利用args参数执行指定对象obj中的该方法，返回值为Object类型    |
| isVarArgs()                        | 查看该方法是否允许带有可变数量的参数，如果允许返回true，否则返回false |
| getModifiers()                     | 获得可以解析出该方法所采用修饰符的整数                       |

## 4.2 获取Method对象

通过`Class`对象获取`Method`对象，如下例：



```java
Class aClass = ...; //获取Class对象
Method[] methods = aClass.getMethods();
```

返回的`Method`对象数组包含了指定类中声明为公有的(`public`)的所有变量集合。

如果知道要调用方法的具体参数类型，可以直接通过参数类型来获取指定的方法，下面这个例子中返回方法对象名称是“`doSomething`”，方法参数是`String`类型：



```java
Class  aClass = ...; //获取Class对象
Method method = aClass.getMethod("doSomething", new Class[]{String.class});
```

如果根据给定的方法名称以及参数类型无法匹配到相应的方法，则会抛出`NoSuchMethodException`。

如果想要获取的方法没有参数，那么在调用`getMethod()`方法时第二个参数传入`null`即可，就像这样：



```java
Class  aClass = ...; //获取Class对象
Method method = aClass.getMethod("doSomething", null);
```

## 4.3 方法参数以及返回类型

获取指定方法的方法参数是哪些：



```java
Method method = ...; //获取Class对象
Class[] parameterTypes = method.getParameterTypes();
```

获取指定方法的返回类型：



```java
Method method = ...; //获取Class对象
Class returnType = method.getReturnType();
```

## 4.4 通过Method对象调用方法

通过如下方式来调用一个方法：



```java
//获取一个方法名为doSomesthing，参数类型为String的方法
Method method = MyObject.class.getMethod("doSomething", String.class);
Object returnValue = method.invoke(null, "parameter-value1");
```

传入的`null`参数是要调用方法的对象，如果是一个静态方法调用的话则可以用`null`代替指定对象作为`invoke()`的参数，在上面这个例子中，如果`doSomething`不是静态方法的话，就要传入有效的`MyObject`实例而不是`null`。

Method.invoke(Object target, Object… parameters)方法的第二个参数是一个可变参数列表，但是必须要传入与要调用方法的形参一一对应的实参。就像上个例子那样，方法需要`String`类型的参数，那我们必须要传入一个字符串。

# 五、访问成员变量

通过下列任意一个方法访问成员变量时将返回`Field`类型的对象或数组。

- getFields()
- getField(String name)
- getDeclaredFields()
- getDeclaredField(String name)

## 5.1 Field类的常用方法

| 方法名称                          | 说明                                                         |
| :-------------------------------- | :----------------------------------------------------------- |
| getName()                         | 获得该成员变量的名称                                         |
| getType()                         | 获取表示该成员变量的Class对象                                |
| get(Object obj)                   | 获得指定对象obj中成员变量的值，返回值为Object类型            |
| set(Object obj, Object value)     | 将指定对象obj中成员变量的值设置为value                       |
| getInt(Object obj)                | 获得指定对象obj中成员类型为int的成员变量的值                 |
| setInt(Object obj, int i)         | 将指定对象obj中成员变量的值设置为i                           |
| setFloat(Object obj, float f)     | 将指定对象obj中成员变量的值设置为f                           |
| getBoolean(Object obj)            | 获得指定对象obj中成员类型为boolean的成员变量的值             |
| setBoolean(Object obj, boolean b) | 将指定对象obj中成员变量的值设置为b                           |
| getFloat(Object obj)              | 获得指定对象obj中成员类型为float的成员变量的值               |
| setAccessible(boolean flag)       | 此方法可以设置是否忽略权限直接访问private等私有权限的成员变量 |
| getModifiers()                    | 获得可以解析出该方法所采用修饰符的整数                       |

## 5.2 获取Field对象

通过`Class`对象获取`Field`对象，如下例：



```java
Class aClass = ...; //获取Class对象
Field[] methods = aClass.getFields();
```

返回的`Field`对象数组包含了指定类中声明为公有的(`public`)的所有变量集合。如果知道要访问的变量名称，可以通过如下的方式获取指定的变量：



```java
Class aClass = MyObject.class;
Field field = aClass.getField("someField");
```

上面的例子返回的`Field`类的实例对应的就是在`MyObject`类中声明的名为`someField`的成员变量，就是这样：



```java
public class MyObject {
    public String someField = null;
}
```

在调用`getField()`方法时，如果根据给定的方法参数没有找到对应的变量，那么就会抛出`NoSuchFieldException`。

## 5.3 变量名称

一旦获取了`Field`实例，可以通过调用`Field.getName()`方法获取他的变量名称，如下例：



```java
Field field = ...; //获取Field对象
String fieldName = field.getName();
```

## 5.4 变量类型

通过调用`Field.getType()`方法来获取一个变量的类型（如`String`, `int`等等）



```java
Field field = aClass.getField("someField");
Object fieldType = field.getType();
```

## 5.5 获取或设置(get/set)变量值

一旦获得了一个`Field`的引用，就可以通过调用`Field.get()`或`Field.set()`方法，获取或者设置变量的值，如下例：



```java
Class  aClass = MyObject.class;
Field field = aClass.getField("someField");
MyObject objectInstance = new MyObject();
Object value = field.get(objectInstance);
field.set(objetInstance, value);
```

传入`Field.get()`/`Field.set()`方法的参数`objetInstance`应该是拥有指定变量的类的实例。在上述的例子中传入的参数是`MyObject`类的实例，是因为`someField`是`MyObject`类的实例。

如果变量是静态变量的话(`public static`)那么在调用`Field.get()`/`Field.set()`方法的时候传入`null`做为参数而不用传递拥有该变量的类的实例。(译者注：如果传入拥有该变量的类的实例也可以得到相同的结果)

# 六、注解

通过下列任意一个方法访问注解时将返回`Annotation`类型的对象或数组。

- getAnnotations()
- getAnnotation(Class annotationClass)
- getDeclaredAnnotations()
- getDeclaredAnnotation(Class annotationClass)

注解是插入代码中的一种注释或者说是一种元数据(`meta data`)。这些注解信息可以在编译期使用预编译工具进行处理(`pre-compiler tools`)，也可以在运行期使用`Java`反射机制进行处理。下面是一个类注解的例子：



```java
@MyAnnotation(name="someName",  value = "Hello World")
public class TheClass {
}
```

在`TheClass`类定义的上面有一个`@MyAnnotation`的注解。注解的定义与接口的定义相似，下面是`MyAnnotation`注解的定义：



```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface MyAnnotation {
    public String name();
    public String value();
}
```

在interface前面的@符号表名这是一个注解，一旦定义了一个注解之后就可以将其应用到代码中。

在注解定义中的两个指示`@Retention(RetentionPolicy.RUNTIME)`和`@Target(ElementType.TYPE)`，说明了这个注解该如何使用。

- `@Retention(RetentionPolicy.RUNTIME)`表示这个注解可以在运行期通过反射访问。如果没有在注解定义的时候使用这个指示那么这个注解的信息不会保留到运行期，这样反射就无法获取它的信息。
- `@Target(ElementType.TYPE)`表示这个注解只能用在类型上面（比如类跟接口）。同样可以把`Type`改为`Field`或者`Method`，或者可以不用这个指示，这样的话注解在类，方法和变量上就都可以使用了。

## 6.1 类注解

在运行期访问类，方法或者变量的注解信息，下是一个访问类注解的例子：



```java
Class aClass = TheClass.class;
Annotation[] annotations = aClass.getAnnotations();
for (Annotation annotation : annotations) {
    if (annotation instanceof MyAnnotation) {
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("name: " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
}
```

还可以指定访问一个类的注解：



```java
Class aClass = TheClass.class;
Annotation annotation = aClass.getAnnotation(MyAnnotation.class);
if (annotation instanceof MyAnnotation) {
    MyAnnotation myAnnotation = (MyAnnotation) annotation;
    System.out.println("name: " + myAnnotation.name());
    System.out.println("value: " + myAnnotation.value());
}
```

## 6.2 方法注解

下面是一个方法注解的例子：



```java
public class TheClass {
    @MyAnnotation(name="someName",  value = "Hello World")
    public void doSomething(){}
}
```

访问方法注解：



```java
Method method = ...; //获取方法对象
Annotation[] annotations = method.getDeclaredAnnotations();
for (Annotation annotation : annotations) {
    if (annotation instanceof MyAnnotation) {
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("name: " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
}
```

访问指定的方法注解：



```java
Method method = ...; //获取方法对象
Annotation annotation = method.getAnnotation(MyAnnotation.class);
if (annotation instanceof MyAnnotation) {
    MyAnnotation myAnnotation = (MyAnnotation) annotation;
    System.out.println("name: " + myAnnotation.name());
    System.out.println("value: " + myAnnotation.value());
}
```

## 6.3 参数注解

方法参数也可以添加注解，就像下面这样：



```java
public class TheClass {
    public static void doSomethingElse(@MyAnnotation(name="aName", value="aValue") String parameter) {
    }
}
```

可以通过`Method`对象来访问方法参数注解：



```java
Method method = ...; //获取方法对象
Annotation[][] parameterAnnotations = method.getParameterAnnotations();
Class[] parameterTypes = method.getParameterTypes();
int i = 0;
for (Annotation[] annotations : parameterAnnotations) {
    Class parameterType = parameterTypes[i++];
    for (Annotation annotation : annotations) {
        if (annotation instanceof MyAnnotation) {
            MyAnnotation myAnnotation = (MyAnnotation) annotation;
            System.out.println("param: " + parameterType.getName());
            System.out.println("name : " + myAnnotation.name());
            System.out.println("value: " + myAnnotation.value());
        }
    }
}
```

需要注意的是`Method.getParameterAnnotations()`方法返回一个注解类型的二维数组，每一个方法的参数包含一个注解数组。

## 6.4 变量注解

下面是一个变量注解的例子：



```java
public class TheClass {
    @MyAnnotation(name="someName",  value = "Hello World")
    public String myField = null;
}
```

访问变量的注解：



```java
Field field = ...; //获取方法对象
Annotation[] annotations = field.getDeclaredAnnotations();
for (Annotation annotation : annotations) {
    if (annotation instanceof MyAnnotation) {
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("name: " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
}
```

访问指定的变量注解：



```java
Field field = ...; //获取方法对象
Annotation annotation = field.getAnnotation(MyAnnotation.class);
if (annotation instanceof MyAnnotation) {
    MyAnnotation myAnnotation = (MyAnnotation) annotation;
    System.out.println("name: " + myAnnotation.name());
    System.out.println("value: " + myAnnotation.value());
}
```

# 七、泛型

通过下列任意一个方法访问泛型时将返回`Type`类型的对象或数组。

- getGenericType()
- getGenericReturnType()
- getGenericParameterTypes()

## 7.1 运用泛型反射的经验法则

下面是两个典型的使用泛型的场景：

1. 声明一个需要被参数化（`parameterizable`）的类/接口。
2. 使用一个参数化类。

当声明一个类或者接口的时候可以指明这个类或接口可以被参数化，`java.util.List`接口就是典型的例子。你可以运用泛型机制创建一个标明存储的是`String`类型`list`，这样比你创建一个`Object`的`list`要更好。

当你想在运行期参数化类型本身，比如你想检查`java.util.List`类的参数化类型，你是没有办法能知道他具体的参数化类型是什么。这样一来这个类型就可以是一个应用中所有的类型。但是，当你检查一个使用了被参数化的类型的变量或者方法，你可以获得这个被参数化类型的具体参数。

总之：不能在运行期获知一个被参数化的类型的具体参数类型是什么，但是可以在用到这个被参数化类型的方法以及变量中找到他们，换句话说就是获知他们具体的参数化类型。

## 7.2 泛型方法返回类型

如果获得了`java.lang.reflect.Method`对象，那么就可以获取到这个方法的泛型返回类型信息。如果方法是在一个被参数化类型之中（译者注：如T fun()）那么无法获取他的具体类型，但是如果方法返回一个泛型类（译者注：如List fun()）那么就可以获得这个泛型类的具体参数化类型。

下面这个例子定义了一个类这个类中的方法返回类型是一个泛型类型：



```java
import java.util.ArrayList;

public class MyClass {
    protected List<String> stringList = new ArrayList<>();

    public List<String> getStringList() {
        return this.stringList;
    }
}
```

我们可以获取`getStringList()`方法的泛型返回类型，换句话说，我们可以检测到`getStringList()`方法返回的是`List`而不仅仅只是一个`List`。如下例：



```java
Method method = MyClass.class.getMethod("getStringList", null);
Type returnType = method.getGenericReturnType();
if (returnType instanceof ParameterizedType) {
    ParameterizedType type = (ParameterizedType) returnType;
    Type[] typeArguments = type.getActualTypeArguments();
    for (Type typeArgument : typeArguments) {
        Class typeArgClass = (Class) typeArgument;
        System.out.println("typeArgClass = " + typeArgClass);
    }
}
```

这段代码会打印出“typeArgClass = java.lang.String”。

## 7.3 泛型方法参数类型

可以通过反射来获取方法参数的泛型类型，下面这个例子定义了一个类，这个类中的方法的参数是一个被参数化的`List`：



```java
public class MyClass {

    protected List<String> stringList = new ArrayList<>();
    public void setStringList(List<String> list){
        this.stringList = list;
    }
}
```

获取方法的泛型参数：



```java
method = Myclass.class.getMethod("setStringList", List.class);
Type[] genericParameterTypes = method.getGenericParameterTypes();
for (Type genericParameterType : genericParameterTypes) {
    if (genericParameterType instanceof ParameterizedType) {
        ParameterizedType aType = (ParameterizedType) genericParameterType;
        Type[] parameterArgTypes = aType.getActualTypeArguments();
        for (Type parameterArgType : parameterArgTypes) {
            Class parameterArgClass = (Class) parameterArgType;
            System.out.println("parameterArgClass = " + parameterArgClass);
        }
    }
}
```

这段代码会打印出”parameterArgType = java.lang.String”。

## 7.4 泛型变量类型

同样可以通过反射来访问公有（`Public`）变量的泛型类型，无论这个变量是一个类的静态成员变量或是实例成员变量。你可以在“`Java Reflection`:`Fields`”中阅读到有关如何获取`Field`对象的相关内容。这是之前的一个例子，一个定义了一个名为`stringList`的成员变量的类。



```java
public class MyClass {
    public List<String> stringList = new ArrayList<>();
}
```



```java
Field field = MyClass.class.getField("stringList");
Type genericFieldType = field.getGenericType();
if (genericFieldType instanceof ParameterizedType) {
    ParameterizedType aType = (ParameterizedType) genericFieldType;
    Type[] fieldArgTypes = aType.getActualTypeArguments();
    for (Type fieldArgType : fieldArgTypes) {
        Class fieldArgClass = (Class) fieldArgType;
        System.out.println("fieldArgClass = " + fieldArgClass);
    }
}
```

这段代码会打印出”fieldArgClass = java.lang.String”。

# 八、数组

`Java`反射机制通过`java.lang.reflect.Array`这个类来处理数组。不要把这个类与`Java`集合套件(`Collections suite`)中的`java.util.Arrays`混淆，`java.util.Arrays`是一个提供了遍历数组，将数组转化为集合等工具方法的类。

## 8.1 创建数组

`Java`反射机制通过`java.lang.reflect.Array`类来创建数组。下面是一个如何创建数组的例子：



```java
int[] intArray = (int[]) Array.newInstance(int.class, 3);
```

这个例子创建一个`int`类型的数组。`Array.newInstance()`方法的第一个参数表示了我们要创建一个什么类型的数组。第二个参数表示了这个数组的空间是多大。

## 8.2 访问数组

通过`Java`反射机制同样可以访问数组中的元素。具体可以使用`Array.get(…)`和`Array.set(…)`方法来访问。下面是一个例子：



```java
int[] intArray = (int[]) Array.newInstance(int.class, 3);
Array.set(intArray, 0, 123);
Array.set(intArray, 1, 456);
Array.set(intArray, 2, 789);
System.out.println("intArray[0] = " + Array.get(intArray, 0));
System.out.println("intArray[1] = " + Array.get(intArray, 1));
System.out.println("intArray[2] = " + Array.get(intArray, 2));
```

这个例子会输出：



```text
intArray[0] = 123
intArray[1] = 456
intArray[2] = 789
```

## 8.3 获取数组的Class对象

在我编写`Butterfly DI Container`的脚本语言时，当我想通过反射获取数组的`Class`对象时遇到了一点麻烦。如果不通过反射的话你可以这样来获取数组的`Class`对象：



```java
Class stringArrayClass = String[].class;
```

如果使用`Class.forName()`方法来获取`Class`对象则不是那么简单。比如你可以像这样来获得一个原生数据类型（`primitive`）`int`数组的`Class`对象：



```java
Class intArray = Class.forName("[I");
```

在`JVM`中字母`I`代表`int`类型，左边的‘[’代表我想要的是一个`int`类型的数组，这个规则同样适用于其他的原生数据类型。对于普通对象类型的数组有一点细微的不同：



```java
Class stringArrayClass = Class.forName("[Ljava.lang.String;");
```

注意‘[`L`’的右边是类名，类名的右边是一个‘;’符号。这个的含义是一个指定类型的数组。需要注意的是，你不能通过`Class.forName()`方法获取一个原生数据类型的`Class`对象。下面这两个例子都会报`ClassNotFoundException`：



```java
Class intClass1 = Class.forName("I");
Class intClass2 = Class.forName("int");
```

我通常会用下面这个方法来获取普通对象以及原生对象的`Class`对象：



```java
public Class getClass(String className) {
    if("int" .equals(className)) return int .class;
    if("long".equals(className)) return long.class;
    ...
    return Class.forName(className);
}
```

一旦你获取了类型的`Class`对象，你就有办法轻松的获取到它的数组的`Class`对象，你可以通过指定的类型创建一个空的数组，然后通过这个空的数组来获取数组的`Class`对象。这样做有点讨巧，不过很有效。如下例：



```java
Class theClass = getClass(theClassName);
Class stringArrayClass = Array.newInstance(theClass, 0).getClass();
```

这是一个特别的方式来获取指定类型的指定数组的`Class`对象。无需使用类名或其他方式来获取这个`Class`对象。

为了确保`Class`对象是不是代表一个数组，你可以使用`Class.isArray()`方法来进行校验：



```java
Class stringArrayClass = Array.newInstance(String.class, 0).getClass();
System.out.println("is array: " + stringArrayClass.isArray());
```

## 8.4 获取数组的成员类型

一旦你获取了一个数组的`Class`对象，你就可以通过`Class.getComponentType()`方法获取这个数组的成员类型。成员类型就是数组存储的数据类型。例如，数组`int[]`的成员类型就是一个`Class`对象`int.class`。`String[]`的成员类型就是`java.lang.String`类的`Class`对象。

下面是一个访问数组成员类型的例子：



```java
String[] strings = new String[3];
Class stringArrayClass = strings.getClass();
Class stringArrayComponentType = stringArrayClass.getComponentType();
System.out.println(stringArrayComponentType);
```

# 九、反射工具类



```java
import org.apache.commons.lang3.StringUtils;
import org.apache.commons.lang3.Validate;

import java.lang.reflect.*;
import java.util.Date;

import lombok.extern.slf4j.Slf4j;
/**
 * 反射工具类
 * <p>
 * 1. 调用getter/setter方法，
 * 2. 访问私有变量，
 * 3. 调用私有方法
 * 4. 获取泛型类型Class
 * 5. 被AOP代理过的真实类
 *
 * @author
 */
@Slf4j
public class ReflectUtils {

    private static final String SETTER_PREFIX = "set";

    private static final String GETTER_PREFIX = "get";

    private static final String CGLIB_CLASS_SEPARATOR = "$$";

    /**
     * 调用Getter方法
     * 支持多级，如：对象名.对象名.方法
     *
     * @param obj
     * @param propertyName
     * @return
     */
    public static Object invokeGetter(Object obj, String propertyName) {
        Object object = obj;
        for (String name : StringUtils.split(propertyName, ".")) {
            String getterMethodName = GETTER_PREFIX + StringUtils.capitalize(name);
            object = invokeMethod(object, getterMethodName, new Class[]{}, new Object[]{});
        }
        return object;
    }

    /**
     * 调用Setter方法，仅匹配方法名。
     * 支持多级，如：对象名.对象名.方法
     *
     * @param obj
     * @param propertyName
     * @param value
     */
    public static void invokeSetter(Object obj, String propertyName, Object value) {
        Object object = obj;
        String[] names = StringUtils.split(propertyName, ".");
        for (int i = 0; i < names.length; i++) {
            if (i < names.length - 1) {
                String getterMethodName = GETTER_PREFIX + StringUtils.capitalize(names[i]);
                object = invokeMethod(object, getterMethodName, new Class[]{}, new Object[]{});
            } else {
                String setterMethodName = SETTER_PREFIX + StringUtils.capitalize(names[i]);
                invokeMethodByName(object, setterMethodName, new Object[]{value});
            }
        }
    }

    /**
     * 直接读取对象属性值，无视private/protected修饰符，不经过getter函数。
     *
     * @param obj
     * @param fieldName
     * @return
     */
    public static Object getFieldValue(final Object obj, final String fieldName) {
        Field field = getAccessibleField(obj, fieldName);
        if (field == null) {
            throw new IllegalArgumentException("Could not find field [" + fieldName + "] on target ["
                    + obj + "]");
        }
        Object result = null;
        try {
            result = field.get(obj);
        } catch (IllegalAccessException e) {
            log.error("不可能抛出的异常{}", e.getMessage());
        }
        return result;
    }

    /**
     * 直接设置对象属性值，无视private/protected修饰符，不经过setter函数。
     *
     * @param obj
     * @param fieldName
     * @param value
     */
    public static void setFieldValue(final Object obj, final String fieldName, final Object value) {
        Field field = getAccessibleField(obj, fieldName);
        if (field == null) {
            throw new IllegalArgumentException("Could not find field [" + fieldName + "] on target ["
                    + obj + "]");
        }
        try {
            field.set(obj, value);
        } catch (IllegalAccessException e) {
            log.error("不可能抛出的异常:{}", e.getMessage());
        }
    }

    /**
     * 直接调用对象方法，无视private/protected修饰符。
     * 用于一次性调用的情况，否则应使用getAccessibleMethod()函数获得Method后反复调用。
     * 同时匹配方法名+参数类型
     *
     * @param obj
     * @param methodName
     * @param parameterTypes
     * @param args
     * @return
     */
    public static Object invokeMethod(final Object obj, final String methodName,
                                      final Class<?>[] parameterTypes,
                                      final Object[] args) {
        Method method = getAccessibleMethod(obj, methodName, parameterTypes);
        if (method == null) {
            throw new IllegalArgumentException("Could not find method [" + methodName + "] on target ["
                    + obj + "]");
        }

        try {
            return method.invoke(obj, args);
        } catch (Exception e) {
            throw convertReflectionExceptionToUnchecked(e);
        }
    }

    /**
     * 直接调用对象方法，无视private/protected修饰符，直接调用对象方法，无视private/protected修饰符，
     * 用于一次性调用的情况，否则应使用getAccessibleMethodByName()函数获得Method后反复调用。
     * 只匹配函数名，如果有多个同名函数调用第一个。
     *
     * @param obj
     * @param methodName
     * @param args
     * @return
     */
    public static Object invokeMethodByName(final Object obj, final String methodName,
                                            final Object[] args) {
        Method method = getAccessibleMethodByName(obj, methodName);
        if (method == null) {
            throw new IllegalArgumentException("Could not find method [" + methodName
                    + "] on target [" + obj + "]");
        }

        try {
            return method.invoke(obj, args);
        } catch (Exception e) {
            throw convertReflectionExceptionToUnchecked(e);
        }
    }

    /**
     * 循环向上转型，获取对象的DeclaredField，并强制设置为可访问。
     * <p>
     * 如向上转型到Object仍无法找到，返回null。
     *
     * @param obj
     * @param fieldName
     * @return
     */
    public static Field getAccessibleField(final Object obj, final String fieldName) {
        Validate.notNull(obj, "object can't be null");
        Validate.notBlank(fieldName, "fieldName can't be blank");
        for (Class<?> superClass = obj.getClass(); superClass != Object.class;
             superClass = superClass.getSuperclass()) {
            try {
                Field field = superClass.getDeclaredField(fieldName);
                makeAccessible(field);
                return field;
            } catch (NoSuchFieldException e) {
                // Field不在当前类定义,继续向上转型
                continue;// new add
            }
        }
        return null;
    }

    /**
     * 循环向上转型, 获取对象的DeclaredMethod，并强制设置为可访问。
     * 如向上转型到Object仍无法找到，返回null。
     * 匹配函数名 + 参数类型。
     * <p>
     * 用于方法需要被多次调用的情况。
     * 先使用本函数先取得Method，然后调用Method.invoke(Object obj, Object... args)
     *
     * @param obj
     * @param methodName
     * @param parameterTypes
     * @return
     */
    public static Method getAccessibleMethod(final Object obj, final String methodName,
                                             final Class<?>... parameterTypes) {
        Validate.notNull(obj, "object can't be null");
        Validate.notBlank(methodName, "methodName can't be blank");

        for (Class<?> searchType = obj.getClass(); searchType != Object.class;
             searchType = searchType.getSuperclass()) {
            try {
                Method method = searchType.getDeclaredMethod(methodName, parameterTypes);
                makeAccessible(method);
                return method;
            } catch (NoSuchMethodException e) {
                // Method不在当前类定义,继续向上转型
                // new add
                continue;
            }
        }
        return null;
    }

    /**
     * 循环向上转型，获取对象的DeclaredMethod,并强制设置为可访问。
     * 如向上转型到Object仍无法找到，返回null。
     * 只匹配函数名。
     * <p>
     * 用于方法需要被多次调用的情况。
     * 先使用本函数先取得Method，然后调用Method.invoke(Object obj, Object... args)
     *
     * @param obj
     * @param methodName
     * @return
     */
    public static Method getAccessibleMethodByName(final Object obj, final String methodName) {
        Validate.notNull(obj, "object can't be null");
        Validate.notBlank(methodName, "methodName can't be blank");

        for (Class<?> searchType = obj.getClass(); searchType != Object.class;
             searchType = searchType.getSuperclass()) {
            Method[] methods = searchType.getDeclaredMethods();
            for (Method method : methods) {
                if (method.getName().equals(methodName)) {
                    makeAccessible(method);
                    return method;
                }
            }
        }
        return null;
    }

    /**
     * 改变private/protected的方法为public，尽量不调用实际改动的语句，避免JDK的SecurityManager抱怨。
     *
     * @param method
     */
    public static void makeAccessible(Method method) {
        if ((!Modifier.isPublic(method.getModifiers())
                || !Modifier.isPublic(method.getDeclaringClass().getModifiers()))
                && !method.isAccessible()) {
            method.setAccessible(true);
        }
    }

    /**
     * 改变private/protected的成员变量为public，尽量不调用实际改动的语句，避免JDK的SecurityManager抱怨。
     *
     * @param field
     */
    public static void makeAccessible(Field field) {
        if ((!Modifier.isPublic(field.getModifiers())
                || !Modifier.isPublic(field.getDeclaringClass().getModifiers())
                || Modifier.isFinal(field.getModifiers()))
                && !field.isAccessible()) {
            field.setAccessible(true);
        }
    }

    /**
     * 通过反射，获得Class定义中声明的泛型参数的类型，注意泛型必须定义在父类处
     * 如无法找到，返回Object.class
     * <p>
     * 如:public UserDao extends HibernateDao<User>
     *
     * @param clazz The class to introspect
     * @return the first generic declaration, or Object.class if cannot be determined
     */
    @SuppressWarnings("unchecked")
    public static <T> Class<T> getClassGenericType(final Class clazz) {
        return getClassGenericType(clazz, 0);
    }

    /**
     * 通过反射，获得Class定义中声明的父类的泛型参数的类型。
     * 如无法找到，返回Object.class
     * <p>
     * 如:public UserDao extends HibernateDao<User, Long>
     *
     * @param clazz clazz The class to introspect
     * @param index the Index of the generic ddeclaration,start from 0.
     * @return the index generic declaration, or Object.class if cannot be determined
     */
    public static Class getClassGenericType(final Class clazz, final int index) {
        Type genType = clazz.getGenericSuperclass();
        if (!(genType instanceof ParameterizedType)) {
            log.warn(clazz.getSimpleName() + "'s superclass not ParameterizedType");
            return Object.class;
        }
        Type[] params = ((ParameterizedType) genType).getActualTypeArguments();
        if (index >= params.length || index < 0) {
            log.warn("Index: " + index + ", Size of " + clazz.getSimpleName()
                    + "'s Parameterized Type: "
                    + params.length);
            return Object.class;
        }
        if (!(params[index] instanceof Class)) {
            log.warn(clazz.getSimpleName() +
                    " not set the actual class on superclass generic parameter");
            return Object.class;
        }
        return (Class) params[index];
    }

    public static Class<?> getUserClass(Object instance) {
        Validate.notNull(instance, "Instance must not be null");
        Class clazz = instance.getClass();
        if (clazz != null && clazz.getName().contains(CGLIB_CLASS_SEPARATOR)) {
            Class<?> superClass = clazz.getSuperclass();
            if (superClass != null && !Object.class.equals(superClass)) {
                return superClass;
            }
        }
        return clazz;
    }

    /**
     * 将反射时的checked exception转换为unchecked exception.
     */
    public static RuntimeException convertReflectionExceptionToUnchecked(Exception e) {
        if (e instanceof IllegalAccessException || e instanceof IllegalArgumentException
                || e instanceof NoSuchMethodException) {
            return new IllegalArgumentException(e);
        } else if (e instanceof InvocationTargetException) {
            return new RuntimeException(((InvocationTargetException) e).getTargetException());
        } else if (e instanceof RuntimeException) {
            return (RuntimeException) e;
        }
        return new RuntimeException("Unexpected Checked Exception.", e);
    }

    /**
     * 判断属性是否为日期类型
     *
     * @param clazz     数据类型
     * @param fieldName 属性名
     * @return 如果为日期类型返回true，否则返回false
     */
    public static <T> boolean isDateType(Class<T> clazz, String fieldName) {
        boolean flag = false;
        try {
            Field field = clazz.getDeclaredField(fieldName);
            Object typeObj = field.getType().newInstance();
            flag = typeObj instanceof Date;
        } catch (Exception e) {
            // 把异常吞掉直接返回false
        }
        return flag;
    }

    /**
     * 根据类型将指定参数转换成对应的类型
     *
     * @param value 指定参数
     * @param type  指定类型
     * @return 返回类型转换后的对象
     */
    public static <T> Object parseValueWithType(String value, Class<?> type) {
        Object result = null;
        try { // 根据属性的类型将内容转换成对应的类型
            if (Boolean.TYPE == type) {
                result = Boolean.parseBoolean(value);
            } else if (Byte.TYPE == type) {
                result = Byte.parseByte(value);
            } else if (Short.TYPE == type) {
                result = Short.parseShort(value);
            } else if (Integer.TYPE == type) {
                result = Integer.parseInt(value);
            } else if (Long.TYPE == type) {
                result = Long.parseLong(value);
            } else if (Float.TYPE == type) {
                result = Float.parseFloat(value);
            } else if (Double.TYPE == type) {
                result = Double.parseDouble(value);
            } else {
                result = (Object) value;
            }
        } catch (Exception e) {
            // 把异常吞掉直接返回null
        }
        return result;
    }
}
```