---
title: JDK8新特性
date: 2023/6/28
---

# 01介绍

随着java的发展，越来越多的企业开始使用 java8 版本。Java8 是自 java5之后最重要的版本，这个版本包含语言、编译器、库、工具、JVM等方面的十多个新特性。本次课程将着重学习其中的一些重点特性。  

Jdk8新增的特性如下：

 **Lambda\*\*\*\*表达式** **类似于ES6\*\*\*\*中的箭头函数**

 新的日期API Datetime

 引入Optional 防止空指针异常

 使用Base64

 **接口的默认方法和静态方法**

 **新增方法引用格式**

 **新增Stream\*\*\*\*类**

 注解相关的改变

 支持并行（parallel）数组

 对并发类（Concurrency）的扩展。

 JavaFX

# 02接口新特性

## 接口默认方法

当我们去实现某个框架提供的一个接口时，需要实现其所有的抽象方法，当该框架更新版本，在这个接口中加入了新的抽象方法时，我们就需要对项目重新编译，并且实现其新增的方法。

当实现类太多时，操作起来很麻烦。

JDK之前是使用开闭设计模式：对扩展开放，对修改关闭。即：创建一个新的接口，继承原有的接口，定义新的方法。

但是这样的话，原本的那些实现类并没有新的方法

这时候可以使用接口默认方法

关键字使用default进行修饰， 方法需要方法体。这样的方法所有的子类会默认实现（不用自己写），如果想要覆盖重写，也可以在实现类中覆盖重写

```java
 /**
 * 从java8开始，接口当中允许定义default默认方法
 * 修饰符：public default(public可以省略，default不能省略)
 */
  public interface MyInterface {
    void method1();
    void method2();
    default void methodNew() {
        System.out.println("接口默认方法执行");
    }
}
```

这里需要注意的是：这里的default是jdk8新增的关键字，和访问限定修饰符“default”不是一个概念，与switch中的default功能完全不同.

与抽象类的不同：抽象类更多的是提供一个模板，子类之间的某个流程大致相同，仅仅是某个步骤可能不一样（模板方法设计模式），这个时候使用抽象类，该步骤定义为抽象方法。而default关键字是用于扩展

## 接口静态方法

```java
/**
 * 从java8开始，接口当中允许定义静态方法
 * 修饰符：static xxx
  * 一般类的静态方法用法相同
 */
  public interface Animal {
    void eat();
    static Animal getAnimal() {
        return new Cat();
    }
}
```

接口的静态方法不会被实现类所继承

# 03函数式接口

# 函数式接口

## 概念

函数式接口在Java中是指：**有且仅有**一个**抽象方法**的接口。

函数式接口，即适用于函数式编程场景的接口。而Java中的函数式编程体现就是Lambda，所以函数式接口就是可以适用于Lambda使用的接口。只有确保接口中有且仅有一个抽象方法，Java中的Lambda才能顺利地进行推导。

## 格式

确保接口中有且只有一个抽象方法即可

| Public interface 接口名称 {<br>返回值 方法名称();<br>} |
| ------------------------------------------------------ |

## @FunctionalInterface注解

有的注解是在编译期起作用，如@Override注解。而@FunctionalInterface也是在编译期起作用。该注解是java8专门为函数式接口引入的新的注解，作用于一个接口上。

一旦使用该注解来定义接口，编译期会强制检查该接口是否符合函数式接口的条件，不符合则会报错。需要注意的是：即使不使用该注解，只要满足函数式接口的定义，这就是一个函数式接口。

```java
@FunctionalInterface
  public interface MyFunctionalInterface {
    void myMethod();
  }
```

## 自定义函数式接口

```java
public class DemoFunctionalInterface {
    // 使用自定义的函数式接口作为方法参数
    private static void doSomething(MyFunctionalInterface inter) {
        inter.myMethod();
    }
    public static void main(String[] args) {
        // 调用使用函数式接口的方法
        doSomething(() -> System.out.println("乌鸦坐飞机"));
    }
}
```

# 04Lambda表达式

在面向对象的基础上，java8 通过Lambda表达式与方法引用等，为开发者打开了函数式编程的大门。Lambda表达式不是语法糖，而是新的语法  

## 语法

三要素：参数、箭头、代码

(参数类型参数1, 参数类型 参数2....) -> {代码}

1. 如果参数有多个，那么使用逗号分隔。如果参数没有，则留空

2. 箭头是固定写法

3. 大括号相当于方法体。

使用Lambda表达式的必要前提：必须是函数式接口

## Lambda 省略规则

1. 参数类型可以省略。但是只能同时省略所有参数的类型，或者干脆都不省略。

2. 如果参数有且仅有一个，那么小括号可以省略。

3. 如果大括号内的语句有且仅有一条，那么无论是否有返回值，return、大括号、分号都可以省略

## Lambda的延迟执行

有些场景的代码执行后，结果不一定会被使用，从而造成性能浪费。而Lambda表达式是延迟执行的，这正好可以作为解决方案，提升性能。

### 性能浪费的案例

```java
public class Demo01Logger {
    private static void log(int level, String msg) {
        if (level == 1) {
            System.out.println(msg);
        }
    }
    public static void main(String[] args) {
        String msgA = "Hello";
        String msgB = "World";
        String msgC = "Java";
        log(1, msgA + msgB + msgC);
    }
}
```

这段代码存在问题：无论级别是否满足要求，作为 log 方法的第二个参数，三个字符串一定会首先被拼接并传入方法内，然后才会进行级别判断。如果级别不符合要求，那么字符串的拼接操作就白做了，存在性能浪费。

### Lambda的优化写法

```java
@FunctionalInterface
  public interface MessageBuilder {
    String buildMessage();
  }
```

这样一来，只有当满足条件的时候才会进行三个字符串的拼接。否则不会拼接。

### 证明Lambda的延迟

```java
public class Demo03LoggerDelay {
    private static void log(int level, MessageBuilder builder) {
        if (level == 1) {
            System.out.println(builder.buildMessage());
        }
    }
    public static void main(String[] args) {
        String msgA = "Hello";
        String msgB = "World";
        String msgC = "Java";
        log(2, () -> {
            System.out.println("Lambda执行！");
            return msgA + msgB + msgC;
        });
    }
}
```

从结果可以看出，在不符合要求的情况下，lambda将不会执行

## 使用Lambda作为参数和返回值

如果抛开实现原理不说，Java中的Lambda表达式可以被当作是匿名内部类的替代品。如果方法的参数是一个函数式接口类型，那么就可以使用Lambda表达式进行替代。使用Lambda表达式作为方法参数，其实就是使用函数式接口作为方法参数。

例如 java.lang.Runnable 接口就是一个函数式接口，假设有一个startThread 方法使用该接口作为参数，那么就可以使用Lambda进行传参。这种情况其实和Thread 类的构造方法参数为Runnable 没有本质区别。

```java
public class Demo04Runnable {
    private static void startThread(Runnable task) {
        new Thread(task).start();
    }
    public static void main(String[] args) {
        startThread(() -> System.out.println("线程任务执行！"));
    }
}
```

类似地，如果一个方法的返回值类型是一个函数式接口，那么就可以直接返回一个Lambda表达式。当需要通过一个方法来获取一个 java.util.Comparator 接口类型的对象作为排序器时,就可以调该方法获取

```java
public class Demo06Comparator {
    private static Comparator<String> newComparator() {
        return (a, b) -> b.length() - a.length();
    }
    public static void main(String[] args) {
        String[] array = {"abc", "ab", "abcd"};
        System.out.println(Arrays.toString(array));
        Arrays.sort(array, newComparator());
        System.out.println(Arrays.toString(array));
    }
}
```

# 05常用函数式接口

JDK提供了大量的函数式接口以及丰富的Lambda应用场景。下面是最简单的几个接口以及使用实例。

![image](https://picgo.xingenhi.cn//typoraclip_image002eb7ff5d1-15be-4776-bbf9-68921772ff42.gif)

## Supplier

java.util.function.Supplier 接口仅包含一个无参的方法： T get() 。用来获取一个泛型参数指定类型的对象数据。由于这是一个函数式接口，这也就意味着对应的Lambda表达式需要“对外提供”一个符合泛型类型的对象数据。

```java
public class Demo08Supplier {
    private static String getString(Supplier<String> function) {
        return function.get();
    }
    public static void main(String[] args) {
        String msgA = "Hello";
        String msgB = "World";
        System.out.println(getString(() -> msgA + msgB));
    }
}
```

练习：求数组元素的最小值

```java
public class Demo02Test {
    //定一个方法,方法的参数传递Supplier,泛型使用Integer 
    public static int getMax(Supplier<Integer> sup) {
        return sup.get();
    }
    public static void main(String[] args) {
        int arr[] = {2, 3, 4, 52, 333, 23};
        //调用getMax方法,参数传递Lambda 
        int maxNum = getMax(() -> {
            //计算数组的最大值 
            int max = arr[0];
            for (int i : arr) {
                if (i > max) {
                    max = i;
                }
            }
            return max;
        });
        System.out.println(maxNum);
    }
}
```

## Consumer接口

java.util.function.Consumer 接口则正好与Supplier接口相反，它不是生产一个数据，而是消费一个数据，

其数据类型由泛型决定。

**抽象方法：accept**，意为消费一个指定泛型的数据

```java
public class Demo09Consumer {
    private static void consumeString(Consumer<String> function) {
        function.accept("Hello");
    }
    public static void main(String[] args) {
        consumeString(s -> System.out.println(s));
    }
}
```

**默认方法：andThen**

如果一个方法的参数和返回值全都是 Consumer 类型，那么就可以实现效果：消费数据的时候，首先做一个操作，然后再做一个操作，实现组合。而这个方法就是Consumer 接口中的default方法 andThen

要想实现组合，需要两个或多个Lambda表达式即可，而 andThen 的语义正是“一步接一步”操作。例如两个步骤组合的情况

```java
public class Demo10ConsumerAndThen {
    private static void consumeString(Consumer<String> one, Consumer<String> two) {
        one.andThen(two).accept("Hello");
    }
    public static void main(String[] args) {
        consumeString(s -> System.out.println(s.toUpperCase()), s -> System.out.println(s.toLowerCase()));
    }
}
```

练习：格式化打印信息

下面的字符串数组当中存有多条信息，请按照格式“姓名：XX。性别：XX。”的格式将信息打印出来。要求将打印姓名的动作作为第一个Consumer 接口的Lambda实例，将打印性别的动作作为第二个Consumer 接口的Lambda实例，将两个 Consumer 接口按照顺序“拼接”到一起。

| public class DemoConsumer {<br> public static void main(String\[\] args) {<br> String\[\] array = {"迪丽热巴,女", "古力娜扎,女", "马尔扎哈,男"};<br> *printInfo*(s -> System.*out*.print("姓名：" + s.split(",")\[0\]), s -><br> System.*out*.println("。性别：" + s.split(",")\[1\] + "。"), array);<br> }<br> private static void printInfo(Consumer one, Consumer two, String\[\] array) {<br> for (String info : array) {<br> one.andThen(two).accept(info); // 姓名：迪丽热巴。性别：女。 <br> }<br> }<br>} |
| ------------------------------------------------------------ |

## Predicate接口

有时候我们需要对某种类型的数据进行判断，从而得到一个boolean值结果。这时可以使用java.util.function.Predicate 接口。

**抽象方法：test**

Predicate 接口中包含一个抽象方法：boolean test(T t) 。用于条件判断的场景：

```java
public class Demo15PredicateTest {
    private static void method(Predicate<String> predicate) {
        boolean veryLong = predicate.test("HelloWorld");
        System.out.println("字符串很长吗：" + veryLong);
    }
    public static void main(String[] args) {
        method(s -> s.length() > 5);
    }
}
```

条件判断的标准是传入lambda表达式逻辑

**默认方法：and**

既然是条件判断，就会存在与、或、非三种常见的逻辑关系。其中将两个 Predicate 条件使用“与”逻辑连接起来实现“并且”的效果时，可以使用default方法 and

如果要判断一个字符串既要包含大写“H”，又要包含大写“W”

```java
public class Demo16PredicateAnd {
    private static void method(Predicate<String> one, Predicate<String> two) {
        boolean isValid = one.and(two).test("Helloworld");
        System.out.println("字符串符合要求吗：" + isValid);
    }
    public static void main(String[] args) {
        method(s -> s.contains("H"), s -> s.contains("W"));
    }
}
```

**默认方法：or**

如果希望实现逻辑“字符串包含大写H或者包含大写W”，那么代码只需要将“and”修改为“or”名称即可，其他都不变：

**默认方法：negate**

表示取反

```java
public class Demo17PredicateNegate {
    private static void method(Predicate<String> predicate) {
        boolean veryLong = predicate.negate().test("HelloWorld");
        System.out.println("字符串很长吗：" + veryLong);
    }
    public static void main(String[] args) {
        method(s -> s.length() < 5);
    }
}
```

## 练习：集合信息筛选

数组当中有多条“姓名+性别”的信息如下，请通过 Predicate 接口的拼装将符合要求的字符串筛选到集合ArrayList 中，需要同时满足两个条件：

1\. 必须为女生；

2\. 姓名为4个字。

```java
public class DemoPredicate {
    public static void main(String[] args) {
        String[] array = {"迪丽热巴,女", "古力娜扎,女", "马尔扎哈,男", "赵丽颖,女"};
        List<String> list = filter(array, s -> "女".equals(s.split(",")[1]), s -> s.split(",")[0].length() == 4);
        System.out.println(list);
    }
    private static List<String> filter(String[] array, Predicate<String> one, Predicate<String> two) {
        List<String> list = new ArrayList<>();
        for (String info : array) {
            if (one.and(two).test(info)) {
                list.add(info);
            }
        }
        return list;
    }
}
```

## Function接口

java.util.function.Function<T,R> 接口用来根据一个类型的数据得到另一个类型的数据，前者称为前置条件，后者称为后置条件。

**抽象方法：apply**

Function 接口中最主要的抽象方法为：R apply(T t) ，根据类型T的参数获取类型R的结果。使用的场景例如：将String 类型转换为 Integer 类型

```java
public class Demo11FunctionApply {
    private static void method(Function<String, Integer> function) {
        int num = function.apply("10");
        System.out.println(num + 20);
    }
    public static void main(String[] args) {
        method(s -> Integer.parseInt(s));
    }
}
```

**默认方法：andThen**

```java
public class Demo12FunctionAndThen {
    private static void method(Function<String, Integer> one, Function<Integer, Integer> two) {
        int num = one.andThen(two).apply("10");
        System.out.println(num + 20);
    }
    public static void main(String[] args) {
        method(str -> Integer.parseInt(str) + 10, i -> i *= 10);
    }
}
```

## 练习：自定义函数模型拼接

请使用 Function 进行函数模型的拼接，按照顺序需要执行的多个函数操作为：

String str = "赵丽颖,20";

1\. 将字符串截取数字年龄部分，得到字符串；

2\. 将上一步的字符串转换成为int类型的数字；

3\. 将上一步的int数字累加100，得到结果int数字。

```java
public class DemoFunction {
    public static void main(String[] args) {
        String str = "赵丽颖,20";
        int age = getAgeNum(str, s -> s.split(",")[1], s -> Integer.parseInt(s), n -> n += 100);
        System.out.println(age);
    }
    private static int getAgeNum(String str, Function<String, String> one, Function<String, Integer> two, Function<Integer, Integer> three) {
        return one.andThen(two).andThen(three).apply(str);
    }
}
```

# 06方法引用

冗余的Lambda场景  

\==============

在使用Lambda表达式的时候，我们实际上传递进去的代码就是一种解决方案：拿什么参数做什么操作。那么考虑一种情况：如果我们在Lambda中所指定的操作方案，已经有地方存在相同方案，那是否还有必要再写重复逻辑？

先看一个简单的函数式接口

```java
@FunctionalInterface
  public interface Printable {
    void print(String str);
  }
```

```java
public class Demo01PrintSimple {
    private static void printString(Printable data) {
        data.print("Hello, World!");
    }
    public static void main(String[] args) {
        printString(s -> System.out.println(s));
    }
}
```

其中 printString 方法只管调用 Printable 接口的 print 方法，而并不管print 方法的具体实现逻辑会将字符串打印到什么地方去。而main 方法通过Lambda表达式指定了函数式接口Printable 的具体操作方案为：拿到String（类型可推导，所以可省略）数据后，在控制台中输出它。  

## 问题分析

这段代码的问题在于，对字符串进行控制台打印输出的操作方案，明明已经有了现成的实现，那就是 System.out对象中的 println(String) 方法。既然Lambda希望做的事情就是调用println(String) 方法，那何必自己手动调用呢？

能否省去Lambda的语法格式（尽管它已经相当简洁）呢？只要“引用”过去就好了：

```java
public class Demo02PrintRef {
    private static void printString(Printable data) {
        data.print("Hello, World!");
    }
    public static void main(String[] args) {
        printString(System.out::println);
    }
}
```

请注意其中的双冒号 :: 写法，这被称为“方法引用”，而双冒号是一种新的语法。

## 方法引用符

双冒号 :: 为引用运算符，而它所在的表达式被称为方法引用。如果Lambda要表达的函数方案已经存在于某个方法的实现中，那么则可以通过双冒号来引用该方法作为Lambda的替代者。

**语义分析**

例如上例中， System.out 对象中有一个重载的println(String) 方法恰好就是我们所需要的。那么对printString 方法的函数式接口参数，对比下面两种写法，完全等效：

 Lambda表达式写法： s -> System.out.println(s);

 方法引用写法： System.out::println

第一种语义是指：拿到参数之后经Lambda之手，继而传递给System.out.println 方法去处理。

第二种等效写法的语义是指：直接让System.out 中的 println 方法来取代Lambda。两种写法的执行效果完全一样，而第二种方法引用的写法复用了已有方案，更加简洁。

注:Lambda 中 传递的参数 一定是方法引用中的那个方法可以接收的类型,否则会抛出异常

**推导与省略**

如果使用Lambda，那么根据“可推导就是可省略”的原则，无需指定参数类型，也无需指定的重载形式——它们都将被自动推导。而如果使用方法引用，也是同样可以根据上下文进行推导。

函数式接口是Lambda的基础，而方法引用是Lambda的孪生兄弟。

下面这段代码将会调用println 方法的不同重载形式，将函数式接口改为int类型的参数：

```java
@FunctionalInterface
  public interface PrintableInteger {
    void print(int str);
  }
```

由于上下文变了之后可以自动推导出唯一对应的匹配重载，所以方法引用没有任何变化

```java
public class Demo03PrintOverload {
    private static void printInteger(PrintableInteger data) {
        data.print(1024);
    }
    public static void main(String[] args) {
        printInteger(System.out::println);
    }
}
```

## 通过对象名引用成员方法

这是最常见的一种用法，与上例相同。如果一个类中已经存在了一个成员方法：

```java
public class MethodRefObject {
    public void printUpperCase(String str) {
        System.out.println(str.toUpperCase());
    }
}
```

那么当需要使用这个 printUpperCase 成员方法来替代 Printable 接口的Lambda的时候，已经具有了MethodRefObject 类的对象实例，则可以通过对象名引用成员方法，代码为：

```java
public class Demo04MethodRef {
    private static void printString(Printable lambda) {
        lambda.print("Hello");
    }
    public static void main(String[] args) {
        MethodRefObject obj = new MethodRefObject();
        printString(obj::printUpperCase);
    }
}
```

## 通过类名称引用静态方法

由于在 java.lang.Math 类中已经存在了静态方法abs ，所以当我们需要通过Lambda来调用该方法时，有两种写法。首先是函数式接口：

```java
@FunctionalInterface
  public interface Calcable {
    int calc(int num);
  }
```

第一种写法使用Lambda

```java
public class Demo05Lambda {
    private static void method(int num, Calcable lambda) {
        System.out.println(lambda.calc(num));
    }
    public static void main(String[] args) {
        method(-10, n -> Math.abs(n));
    }
}
```

第二种使用方法引用

```java
public class Demo06MethodRef {
    private static void method(int num, Calcable lambda) {
        System.out.println(lambda.calc(num));
    }
    public static void main(String[] args) {
        method(-10, Math::abs);
    }
}
```

两种方式等价

## 通过super引用成员方法

如果存在继承关系，当Lambda中需要出现super调用时，也可以使用方法引用进行替代。首先是函数式接口：

```java
@FunctionalInterface
  public interface Greetable {
    void greet();
  }
```

父类Human的内容

```java
public class Human {
    public void sayHello() {
        System.out.println("Hello!");
    }
}
```

子类Man的内容

```java
public class Man extends Human {
    @Override
    public void sayHello() {
        System.out.println("大家好,我是Man!");
    }
    //定义方法method,参数传递Greetable接口 
    public void method(Greetable g) {
        g.greet();
    }
    public void show() {
        //调用method方法,使用Lambda表达式 
        method(() -> {
            //创建Human对象,调用sayHello方法 
            new Human().sayHello();
        });
        //简化Lambda 
        method(() -> new Human().sayHello());
        //使用super关键字代替父类对象 
        method(() -> super.sayHello());
    }
}
```

但是如果使用方法引用会更好

```java
public class Woman extends Human {
    @Override
    public void sayHello() {
        System.out.println("大家好,我是Man!");
    }
    public void method(Greetable g) {
        g.greet();
    }
    public void show() {
        method(super::sayHello);
    }
}
```

## 通过this引用成员方法

this代表当前对象，如果需要引用的方法就是当前类中的成员方法，那么可以使用“this::成员方法”的格式来使用方法引用。首先是简单的函数式接口：

```java
@FunctionalInterface
  public interface Richable {
    void buy();
  }
```

```java
public class Husband {
    private void marry(Richable lambda) {
        lambda.buy();
    }
    public void beHappy() {
        marry(() -> System.out.println("买套房子"));
    }
}
```

开心方法 beHappy 调用了结婚方法 marry ，后者的参数为函数式接口 Richable ，所以需要一个Lambda表达式。但是如果这个Lambda表达式的内容已经在本类当中存在了，则可以对 Husband 丈夫类进行修改：

```java
public class Husband {
    private void buyHouse() {
        System.out.println("买套房子");
    }
    private void marry(Richable lambda) {
        lambda.buy();
    }
    public void beHappy() {
        marry(this::buyHouse);
    }
}
```

# 07Stream流

说到Stream便容易想到I/O Stream，而实际上，在Java 8中，得益于Lambda所带来的函数式编程，引入了一个全新的Stream概念，用于解决已有集合类库既有的弊端。  

Stream流式操作性能比传统的For循环要低，就性能而言，传统的for循环最高

### 传统集合的遍历代码

几乎所有的集合（如 Collection 接口或 Map 接口等）都支持直接或间接的遍历操作。而当我们需要对集合中的元素进行操作的时候，除了必需的添加、删除、获取外，最典型的就是集合遍历。例如：

```java
public class Demo01ForEach {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("张无忌");
        list.add("周芷若");
        list.add("赵敏");
        list.add("张强");
        list.add("张三丰");
        for (String name : list) {
            System.out.println(name);
        }
    }
}
```

### 循环遍历的弊端

Java 8的Lambda让我们可以更加专注于做什么（What），而不是怎么做（How），这点此前已经结合内部类进行了对比说明。现在，我们仔细体会一下上例代码，可以发现：

 for循环的语法就是“怎么做”

 for循环的循环体才是“做什么”

为什么使用循环？因为要进行遍历。但循环是遍历的唯一方式吗？遍历是指每一个元素逐一进行处理，而并不是从第一个到最后一个顺次处理的循环。前者是目的，后者是方式。

试想一下，如果希望对集合中的元素进行筛选过滤：

1.将集合A根据条件一过滤为子集B；

2.然后再根据条件二过滤为子集C。

那怎么办？在Java 8之前的做法可能为：

```java
public class Demo02NormalFilter {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("张无忌");
        list.add("周芷若");
        list.add("赵敏");
        list.add("张强");
        list.add("张三丰");
        List<String> zhangList = new ArrayList<>();
        for (String name : list) {
            if (name.startsWith("张")) {
                zhangList.add(name);
            }
        }
        List<String> shortList = new ArrayList<>();
        for (String name : zhangList) {
            if (name.length() == 3) {
                shortList.add(name);
            }
        }
        for (String name : shortList) {
            System.out.println(name);
        }
    }
}
```

这段代码中含有三个循环，每一个作用不同：

1\. 首先筛选所有姓张的人；

2\. 然后筛选名字有三个字的人；

3\. 最后进行对结果进行打印输出。

每当我们需要对集合中的元素进行操作的时候，总是需要进行循环、循环、再循环。这是理所当然的么？不是。循环是做事情的方式，而不是目的。另一方面，使用线性循环就意味着只能遍历一次。如果希望再次遍历，只能再使用另一个循环从头开始。

那，Lambda的衍生物Stream能给我们带来怎样更加优雅的写法呢？

### Stream更优写法

```java
public class Demo03StreamFilter {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("张无忌");
        list.add("周芷若");
        list.add("赵敏");
        list.add("张强");
        list.add("张三丰");
        list.stream().filter(s -> s.startsWith("张"))
                .filter(s -> s.length() == 3)
                .forEach(System.out::println);
    }
}
```

直接阅读代码的字面意思即可完美展示无关逻辑方式的语义：获取流、过滤姓张、过滤长度为3、逐一打印。代码中并没有体现使用线性循环或是其他任何算法进行遍历，我们真正要做的事情内容被更好地体现在代码中。

## 获取流

java.util.stream.Stream 是Java 8新加入的最常用的流接口。（这并不是一个函数式接口。）

获取一个流非常简单，有以下几种常用的方式：

 所有的 Collection 集合都可以通过 stream 默认方法获取流；

 Stream 接口的静态方法 of 可以获取数组对应的流。

### 根据Collection获取流

首先， java.util.Collection 接口中加入了default方法 stream 用来获取流，所以其所有实现类均可获取流。

```java
public class Demo04GetStream {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        Stream<String> stream1 = list.stream();
        Set<String> set = new HashSet<>();
        Stream<String> stream2 = set.stream();
    }
}
```

### 根据Map获取流

java.util.Map 接口不是 Collection 的子接口，且其K-V数据结构不符合流元素的单一特征，所以获取对应的流

需要分key、value或entry等情况：

```java
public class Demo05GetStream {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        Stream<String> keyStream = map.keySet().stream();
        Stream<String> valueStream = map.values().stream();
        Stream<Map.Entry<String, String>> entryStream = map.entrySet().stream();
    }
}
```

### 根据数组获取流

如果使用的不是集合或映射而是数组，由于数组对象不可能添加默认方法，所以 Stream 接口中提供了静态方法

of ，使用很简单：

```java
public class Demo06GetStream {
    public static void main(String[] args) {
        String[] array = {"张无忌", "张翠山", "张三丰", "张一元"};
        Stream<String> stream = Stream.of(array);
    }
}
```

## 常用方法

### 逐一处理：forEach

虽然方法名字叫forEach，但是与for循环不同

基本使用

```java
public class Demo12StreamForEach {
    public static void main(String[] args) {
        Stream<String> stream = Stream.of("张无忌", "张三丰", "周芷若");
        stream.forEach(name -> System.out.println(name));
    }
}
```

### 过滤：filter

可以通过 filter 方法将一个流转换成另一个子集流

```java
public class Demo07StreamFilter {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> result = original.filter(s -> s.startsWith("张"));
    }
}
```

在这里通过Lambda表达式来指定了筛选的条件：必须姓张。

### 映射：map

如果需要将流中的元素映射到另一个流中，可以使用 map 方法。方法签名：

```java
public class Demo08StreamMap {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("10", "12", "18");
        Stream<Integer> result = original.map(str -> Integer.parseInt(str));
    }
}
```

这段代码中， map 方法的参数通过方法引用，将字符串类型转换成为了int类型（并自动装箱为 Integer 类对象）。

### 统计个数：count

```java
public class Demo09StreamCount {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> result = original.filter(s -> s.startsWith("张"));
        System.out.println(result.count());
    }
}
```

### 取用前几个：limit

```java
public class Demo10StreamLimit {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> result = original.limit(2);
        System.out.println(result.count());
    }
}
```

### 跳过前几个：skip

```java
public class Demo11StreamSkip {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> result = original.skip(2);
        System.out.println(result.count());
    }
}
```

### 组合：concat

如果有两个流，希望合并成为一个流，那么可以使用 Stream 接口的静态方法 concat

```java
public class Demo12StreamConcat {
    public static void main(String[] args) {
        Stream<String> streamA = Stream.of("张无忌");
        Stream<String> streamB = Stream.of("张翠山");
        Stream<String> result = Stream.concat(streamA, streamB);
    }
}
```

## 练习：集合元素处理（传统方式）

现在有两个 ArrayList 集合存储队伍当中的多个成员姓名，要求使用传统的for循环（或增强for循环）依次进行以下若干操作步骤：

1\. 第一个队伍只要名字为3个字的成员姓名；存储到一个新集合中。

2\. 第一个队伍筛选之后只要前3个人；存储到一个新集合中。

3\. 第二个队伍只要姓张的成员姓名；存储到一个新集合中。

4\. 第二个队伍筛选之后不要前2个人；存储到一个新集合中。

5\. 将两个队伍合并为一个队伍；存储到一个新集合中。

6\. 根据姓名创建Person 对象；存储到一个新集合中。

7\. 打印整个队伍的Person对象信息。

代码如下：

```java
public class DemoArrayListNames {
    public static void main(String[] args) {
        ArrayList<String> one = new ArrayList<>();
        one.add("迪丽热巴");
        one.add("宋远桥");
        one.add("苏星河");
        one.add("石破天");
        one.add("石中玉");
        one.add("老子");
        one.add("庄子");
        one.add("洪七公");
        ArrayList<String> two = new ArrayList<>();
        two.add("古力娜扎");
        two.add("张无忌");
        two.add("赵丽颖");
        two.add("张三丰");
        two.add("尼古拉斯赵四");
        two.add("张天爱");
        two.add("张二狗");
        // 第一个队伍只要名字为3个字的成员姓名；
        List<String> oneA = new ArrayList<>();
        for (String name : one) {
            if (name.length() == 3) {
                oneA.add(name);
            }
        }
        // 第一个队伍筛选之后只要前3个人； 
        List<String> oneB = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            oneB.add(oneA.get(i));
        }
        // 第二个队伍只要姓张的成员姓名； 
        List<String> twoA = new ArrayList<>();
        for (String name : two) {
            if (name.startsWith("张")) {
                twoA.add(name);
            }
        }
        // 第二个队伍筛选之后不要前2个人； 
        List<String> twoB = new ArrayList<>();
        for (int i = 2; i < twoA.size(); i++) {
            twoB.add(twoA.get(i));
        }
        // 将两个队伍合并为一个队伍； 
        List<String> totalNames = new ArrayList<>();
        totalNames.addAll(oneB);
        totalNames.addAll(twoB);
        // 根据姓名创建Person对象； 
        List<Person> totalPersonList = new ArrayList<>();
        for (String name : totalNames) {
            totalPersonList.add(new Person(name));
        }
    }
}
public class Person {
    private String name;
    public Person() {
    }
    public Person(String name) {
        this.name = name;
    }
}
```

## 练习：集合元素处理（Stream方式）

```java
public class DemoStreamNames {
    public static void main(String[] args) {
        List<String> one = new ArrayList<>();
        List<String> two = new ArrayList<>();
        // 第一个队伍只要名字为3个字的成员姓名；
        // 第一个队伍筛选之后只要前3个人；
        Stream<String> streamOne = one.stream().filter(s -> s.length() == 3).limit(3);
        // 第二个队伍只要姓张的成员姓名；
        // 第二个队伍筛选之后不要前2个人；
        Stream<String> streamTwo = two.stream().filter(s -> s.startsWith("张")).skip(2);
        // 将两个队伍合并为一个队伍；
        // 根据姓名创建Person对象；
        // 打印整个队伍的Person对象信息。
        Stream.concat(streamOne, streamTwo).map(Person::new).forEach(System.out::println);
    }
}
```