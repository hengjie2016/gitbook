## 1、Annotation

Annotation 就是个接口，每一个 Annotation 对象，只能有一个 RetentionPolicy 属性，可以有多个 ElementType 属性

Annotation 有许多实现类，包括：Deprecated, Documented, Inherited, Override 等等

```plain
package java.lang.annotation;
public interface Annotation {
    boolean equals(Object obj);
    int hashCode();
    String toString();
    Class<? extends Annotation> annotationType();
}
```

## 2、ElementType

ElementType 是 Enum 枚举类型，它用来指定 Annotation 的类型，每一个 Annotation 对象，可以有多个 ElementType 属性

。当 Annotation 与某个 ElementType 关联时，就意味着：Annotation有了某种用途。例如，若一个 Annotation 对象是 METHOD 类型，则该 Annotation 只能用来修饰方法。

```plain
package java.lang.annotation;
public enum ElementType {
    /** 类、接口（包括注释类型）或枚举声明  */
    TYPE,
    /** 字段声明（包括枚举常量） */
    FIELD,
    /** 方法声明  */
    METHOD,
    /** 参数声明  */
    PARAMETER,
    /** 构造方法声明 */
    CONSTRUCTOR,
    /** 局部变量声明  */
    LOCAL_VARIABLE,
    /** 注释类型声明 */
    ANNOTATION_TYPE,
    /** 包声明  */
    PACKAGE,
    /**
     *类型参数声明（1.8新加入）
     */
    TYPE_PARAMETER,
    /**
    *类型使用声明（1.8新加入)
     */
    TYPE_USE
}
```

## 3、RetentionPolicy.java

RetentionPolicy 是 Enum 枚举类型，它用来指定 Annotation 的策略。通俗点说，就是不同 RetentionPolicy 类型的 Annotation 的作用域不同。**

"每 1 个 Annotation" 都与 "1 个 RetentionPolicy" 关联。

- a) 若 Annotation 的类型为 SOURCE，则意味着：Annotation 仅存在于编译器处理期间，编译器处理完之后，该 Annotation 就没用了。 例如，" @Override" 标志就是一个 Annotation。当它修饰一个方法的时候，就意味着该方法覆盖父类的方法；并且在编译期间会进行语法检查！编译器处理完后，"@Override" 就没有任何作用了。
- b) 若 Annotation 的类型为 CLASS，则意味着：编译器将 Annotation 存储于类对应的 .class 文件中，它是 Annotation 的默认行为。
- c) 若 Annotation 的类型为 RUNTIME，则意味着：编译器将 Annotation 存储于 class 文件中，并且可由JVM读入。

@Retention用来约束注解的生命周期，分别有三个值，源码级别（source），类文件级别（class）或者运行时级别（runtime），其含有如下：

- SOURCE：注解将被编译器丢弃（该类型的注解信息只会保留在源码里，源码经过编译后，注解信息会被丢弃，不会保留在编译好的class文件里）
- CLASS：注解在class文件中可用，但会被JVM丢弃（该类型的注解信息会保留在源码里和class文件里，在执行的时候，不会加载到虚拟机中），请注意，当注解未定义Retention值时，默认值是CLASS，如Java内置注解，@Override、@Deprecated、@SuppressWarnning等
- RUNTIME：注解信息将在运行期(JVM)也保留，因此可以通过反射机制读取注解的信息（源码、class文件和执行的时候都有注解的信息），如SpringMvc中的@Controller、@Autowired、@RequestMapping等。

```plain
package java.lang.annotation;
public enum RetentionPolicy {
    //Annotation信息仅存在于编译器处理期间，编译器处理完之后就没有该Annotation信息了
    SOURCE,
    //编译器将Annotation存储于类对应的.class文件中。默认行为
    CLASS,
    //编译器将Annotation存储于class文件中，并且可由JVM读入
    RUNTIME
}
```

# 2、内置注解

注解的语法比较简单，除了**@**符号的使用之外，它基本与Java固有的语法一致。Java内置了三种注解，定义在java.lang中的注解：

- **@Override**，表示当前的方法定义将覆盖超类中的方法。如果你不小心拼写错误，或者方法签名对不上被覆盖的方法，编译器就会发出错误提示。
- **@Deprecated**，如果程序员使用了注解为它的元素，那么编译器会发出警告信息。
- **@SuppressWarnings**，关闭不当的编译器警告信息。在java SE5之前的版本中，也可以使用该注解，不过会被忽略不起作用。

java内置注解：

@Override（重写方法）：被用于标注方法，用于说明所标注的方法是重写父类的方法

@Deprecated（过时方法）：用于说明所标注元素，因存在安全问题或有更好选择而不鼓励使用，如果强行使用，则编译器会发出警告

@SuppressWarnings（消除警告）：用于取消编辑器所显示的警告，有如下属性值

# 3、元注解

定义注解时，会需要一些**元注解**（meta-annotation），如**@Target**和**@Retention**。

@Target 用来定义你的注解将应用于什么地方（例如是一个方法或者一个域）。

@Retention 用来定义该注解在哪一个级别可用，在源代码中（SOURCE）、类文件中（CLASS）或者运行时（RUNTIME）。

Java除了内置了三种标准注解，还有四种元注解。

**@Target** 表示该注解用于什么地方，可能的值在枚举类 **ElemenetType** 中，包括：

ElemenetType.**CONSTRUCTOR**-----------------------------构造器声明

ElemenetType.**FIELD** ----------------------------------域声明（包括 enum 实例）

ElemenetType.**LOCAL_VARIABLE**------------------------- 局部变量声明

ElemenetType.**METHOD** ---------------------------------方法声明

ElemenetType.**PACKAGE** --------------------------------包声明

ElemenetType.**PARAMETER** ------------------------------参数声明

ElemenetType.**TYPE**----------------------------------- 类，接口（包括注解类型）或enum声明

**@Retention** 表示在什么级别保存该注解信息。可选的参数值在枚举类型 **RetentionPolicy** 中，包括：

RetentionPolicy.**SOURCE**-------------注解将被编译器丢弃

RetentionPolicy.**CLASS** -------------注解在class文件中可用，但会被VM丢弃

RetentionPolicy.**RUNTIME** ---------VM将在运行期也保留注释，因此可以通过反射机制读取注解的信息。

**@Documented** 将此注解包含在 javadoc 中 ，它代表着此注解会被javadoc工具提取成文档。在doc文档中的内容会因为此注解的信息内容不同而不同。相当与@see,@param 等。

**@Inherited** 允许子类继承父类中的注解。

元注解：java提供了四个用于修饰自定义注解的元注解

@Target：用于指定被修饰的自定义注解只能用于修饰程序中哪些元素，有四个常用属性

@Retention：用于指定被修饰的自定义注解可以保留多久，有三个常用属性

@Documented：执行javadoc命令时，被该元注解修饰的自定义注解也会生成在文档中

@Inherited：如果父类所使用的注解有此修饰，则子类可以继承该注解，否则不能

# 4、注解与反射

# 5、其他

### 内置的注解

Java 定义了一套注解，共有 7 个，3 个在 java.lang 中，剩下 4 个在 java.lang.annotation 中。

**作用在代码的注解是**

- @Override - 检查该方法是否是重写方法。如果发现其父类，或者是引用的接口中并没有该方法时，会报编译错误。
- @Deprecated - 标记过时方法。如果使用该方法，会报编译警告。
- @SuppressWarnings - 指示编译器去忽略注解中声明的警告。

作用在其他注解的注解(或者说 元注解)是:

- @Retention - 标识这个注解怎么保存，是只在代码中，还是编入class文件中，或者是在运行时可以通过反射访问。
- @Documented - 标记这些注解是否包含在用户文档中。
- @Target - 标记这个注解应该是哪种 Java 成员。
- @Inherited - 标记这个注解是继承于哪个注解类(默认 注解并没有继承于任何子类)

从 Java 7 开始，额外添加了 3 个注解:

- @SafeVarargs - Java 7 开始支持，忽略任何使用参数为泛型变量的方法或构造函数调用产生的警告。
- @FunctionalInterface - Java 8 开始支持，标识一个匿名函数或函数式接口。
- @Repeatable - Java 8 开始支持，标识某注解可以在同一个声明上使用多次。

## 3、java 自带的 Annotation

**(01) @interface**

使用 @interface 定义注解时，意味着它实现了 java.lang.annotation.Annotation 接口，即该注解就是一个Annotation。

定义 Annotation 时，@interface 是必须的。

注意：它和我们通常的 implemented 实现接口的方法不同。Annotation 接口的实现细节都由编译器完成。通过 @interface 定义注解后，该注解不能继承其他的注解或接口。

**(02) @Documented**

类和方法的 Annotation 在缺省情况下是不出现在 javadoc 中的。如果使用 @Documented 修饰该 Annotation，则表示它可以出现在 javadoc 中。

定义 Annotation 时，@Documented 可有可无；若没有定义，则 Annotation 不会出现在 javadoc 中。

**(03) @Target(ElementType.TYPE)**

前面我们说过，ElementType 是 Annotation 的类型属性。而 @Target 的作用，就是来指定 Annotation 的类型属性。

@Target(ElementType.TYPE) 的意思就是指定该 Annotation 的类型是 ElementType.TYPE。这就意味着，MyAnnotation1 是来修饰"类、接口（包括注释类型）或枚举声明"的注解。

定义 Annotation 时，@Target 可有可无。若有 @Target，则该 Annotation 只能用于它所指定的地方；若没有 @Target，则该 Annotation 可以用于任何地方。

**(04) @Retention(RetentionPolicy.RUNTIME)**

前面我们说过，RetentionPolicy 是 Annotation 的策略属性，而 @Retention 的作用，就是指定 Annotation 的策略属性。

@Retention(RetentionPolicy.RUNTIME) 的意思就是指定该 Annotation 的策略是 RetentionPolicy.RUNTIME。这就意味着，编译器会将该 Annotation 信息保留在 .class 文件中，并且能被虚拟机读取。

定义 Annotation 时，@Retention 可有可无。若没有 @Retention，则默认是 RetentionPolicy.CLASS。

**java 常用的 Annotation：**

@Deprecated -- @Deprecated 所标注内容，不再被建议使用。

@Override -- @Override 只能标注方法，表示该方法覆盖父类中的方法。

@Documented -- @Documented 所标注内容，可以出现在javadoc中。

@Inherited -- @Inherited只能被用来标注“Annotation类型”，它所标注的Annotation具有继承性。

@Retention -- @Retention只能被用来标注“Annotation类型”，而且它被用来指定Annotation的RetentionPolicy属性。

@Target -- @Target只能被用来标注“Annotation类型”，而且它被用来指定Annotation的ElementType属性。

@SuppressWarnings -- @SuppressWarnings 所标注内容产生的警告，编译器会对这些警告保持静默。