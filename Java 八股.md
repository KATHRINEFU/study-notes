# Java 八股

## Java 基础

### OOP

**Abstraction.** Using simple things to represent complexity. 

**Encapsulation.** The practice of keeping fields within a class private, then providing access to those fields via public methods

**Inheritance.**Inheritance lets programmers create new classes that share some of the attributes of existing classes. 

**Polymorphism.** Allows programmers to use the same word in Java to mean different things in different contexts. Overload vs overwrite

### overload vs override

| o.   | Method Overloading                                           | Method Overriding                                            |
| :--- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 1)   | Method overloading is used *to increase the readability* of the program. | Method overriding is used *to provide the specific implementation* of the method that is already provided by its super class. |
| 2)   | Method overloading is performed *within class*.              | Method overriding occurs *in two classes* that have IS-A (inheritance) relationship. |
| 3)   | In case of method overloading, *parameter must be different*. 参数的类型、参数的个数、参数的顺序，只要有一个不同就叫做参数列表不同。 | In case of method overriding, *parameter must be same*.      |
| 4)   | Method overloading is the example of *compile time polymorphism*. | Method overriding is the example of *run time polymorphism*. |
| 5)   | In java, method overloading can't be performed by changing return type of the method only. *Return type can be same or different* in method overloading. But you must have to change the parameter. | *Return type must be same or covariant* in method overriding. |

| 区别点     | 重载方法 | 重写方法                                                     |
| ---------- | -------- | ------------------------------------------------------------ |
| 发生范围   | 同一个类 | 子类                                                         |
| 参数列表   | 必须修改 | 一定不能修改                                                 |
| 返回类型   | 可修改   | 子类方法返回值类型应比父类方法返回值类型更小或相等           |
| 异常       | 可修改   | 子类方法声明抛出的异常类应比父类方法声明抛出的异常类更小或相等； |
| 访问修饰符 | 可修改   | 一定不能做更严格的限制（可以降低限制）                       |
| 发生阶段   | 编译期   | 运行期                                                       |

1. 方法名、参数列表必须相同，子类方法返回值类型应比父类方法返回值类型更小或相等，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类。

2. 如果父类方法访问修饰符为 `private/final/static` 则子类就不能重写该方法，但是被 `static` 修饰的方法能够被再次声明。

3. 构造方法无法被重写

   **private and final methods can be overloaded** but they cannot be overridden.

### OOP vs Procedure

|               Procedural Oriented Programming                |                 Object-Oriented Programming                 |
| :----------------------------------------------------------: | :---------------------------------------------------------: |
| program is divided into small parts called ***functions***.  |  program is divided into small parts called ***objects***.  |
|                   ***top-down approach***.                   |                  ***bottom-up approach***.                  |
|                     no access specifier                      | has access specifiers like private, public, protected, etc. |
|          Adding new data and functions is not easy.          |            Adding new data and function is easy.            |
| does not have any proper way of hiding data so it is ***less secure***. |      provides data hiding so it is ***more secure***.       |
|                 overloading is not possible.                 |                   Overloading is possible                   |
|          no concept of data hiding and inheritance.          |                 data hiding and inheritance                 |
|                the function is more important                |            data is more important than function.            |
|               based on the ***unreal world***.               |               based on the ***real world***.                |
|                    medium-sized programs.                    |                 large and complex programs.                 |
|                    procedure abstraction.                    |                      data abstraction.                      |
|                   Code reusability absent                    |                      Code reusability                       |
|        **Examples:** C, FORTRAN, Pascal, Basic, etc.         |          **Examples:** C++, Java, Python, C#, etc.          |

### multiplatform

Java is cross platform because **a program's source code is compiled into an intermediate "bytecode" language** .class. The bytecode is then executed by a Java Virtual Machine (Java interpreter) that was written for that particular hardware platform. So Java program runs on all platforms for which there exists a JVM.

### JVM, JRE, JDK

JVM (Java Virtual Machine) is an abstract machine. It is called a virtual machine because it doesn't physically exist. It is a specification that provides a runtime environment in which Java bytecode can be executed.

JRE is an acronym for Java Runtime Environment. JRE is an acronym for Java Runtime Environment.

JDK is an acronym for Java Development Kitsoftware development environment which is used to develop Java applications and [applets](https://www.javatpoint.com/java-applet)

![JDK](https://static.javatpoint.com/images/jdk2.png)

### abstract class vs interface

| Abstract class                                               | Interface                                                    |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 1) Abstract class can **have abstract and non-abstract** methods. | Interface can have **only abstract** methods. Since Java 8, it can have **default and static methods** also. |
| 2) Abstract class **doesn't support multiple inheritance**.  | Interface **supports multiple inheritance**.                 |
| 3) Abstract class **can have final, non-final, static and non-static variables**. | Interface has **only static and final variables**.           |
| 4) Abstract class **can provide the implementation of interface**. | Interface **can't provide the implementation of abstract class**. |
| 5) The **abstract keyword** is used to declare abstract class. | The **interface keyword** is used to declare interface.      |
| 6) An **abstract class** can extend another Java class and implement multiple Java interfaces. | An **interface** can extend another Java interface only.     |
| 7) An **abstract class** can be extended using keyword "extends". | An **interface** can be implemented using keyword "implements". |
| 8) A Java **abstract class** can have class members like private, protected, etc. | Members of a Java interface are public by default.           |
| 9)**Example:** public abstract class Shape{ public abstract void draw(); } | **Example:** public interface Drawable{ void draw(); }       |

### Exception

`unchecked exception`包括`RuntimeException`和`Error`类，其他所有异常称为检查（checked）异常。

1. `RuntimeException`由程序错误导致，应该修正程序避免这类异常发生。
2. `checked Exception`由具体的环境（读取的文件不存在或文件为空或sql异常）导致的异常。必须进行处理，不然编译不通过，可以catch或者throws。

常见的RuntimeException：

1. `ClassCastException` //类型转换异常
2. `IndexOutOfBoundsException` //数组越界异常
3. `NullPointerException` //空指针
4. `ArrayStoreException` //数组存储异常
5. `NumberFormatException` //数字格式化异常
6. `ArithmeticException` //数学运算异常

checked Exception：

1. `NoSuchFieldException` //反射异常，没有对应的字段
2. `ClassNotFoundException` //类没有找到异常
3. `IllegalAccessException` //安全权限异常，可能是反射时调用了private方法

### final, finally, finalize

- final 用于修饰属性、方法和类, 分别表示属性不能被重新赋值，方法不可被覆盖，类不可被继承。
- finally 是异常处理语句结构的一部分，一般以`try-catch-finally`出现，`finally`代码块表示总是被执行。
- finalize 是Object类的一个方法，该方法一般由垃圾回收器来调用，当我们调用`System.gc()`方法的时候，由垃圾回收器调用`finalize()`方法，回收垃圾，JVM并不保证此方法总被调用。

### hashcode & equals

equals与hashcode的关系：

1. 如果两个对象调用equals比较返回true，那么它们的hashCode值一定要相同；
2. 如果两个对象的hashCode相同，它们并不一定相同。

hashcode method is mainly used to **improve the efficiency of object comparison**, first hashcode() comparison, if not the same, then there is no need to compare in equals, which greatly reduces the number of equals comparison, when the number of objects compared to a large number of efficiency can be improved.

The reason for rewriting `equals()` to rewrite `hashcode()` is to ensure that the `equals()` method returns true in the case of the hashcode value should also be consistent, if you rewrite `equals()` without rewriting `hashcode()`, there will be two objects equal but `hashcode()` is not equal The situation.

### new features in Java 8

1. forEach() method in Iterable interface
2. default and static methods in Interfaces
3. Functional Interfaces and Lambda Expressions
4. Java Stream API for Bulk Data Operations on Collections
5. Java Time API
6. Collection API improvements
7. Concurrency API improvements
8. Java IO improvements

## Java 多线程

### Daemon threads

User threads are high-priority threads. **The JVM will wait for any user thread to complete its task before terminating it.**

On the other hand, **daemon threads are low-priority threads whose only role is to provide services to user threads.**

A daemon is a special kind of process that runs in the background.
It is independent of the control terminal and periodically performs some task or waits to process some event that occurs.
In Java garbage collection threads are special daemons

```java
NewThread daemonThread = new NewThread();
daemonThread.setDaemon(true);
daemonThread.start();
```

### sync vs async

Synchronous: When a call is issued, the call does not return until the result is obtained.

Asynchronous: After a call is issued, the caller is notified after the callee returns the result, or the call is handled by a callback function.

### thread vs process

A process is a single execution of a program and is the basic unit of a system to run a program. A process can spawn multiple threads during its execution.

Multiple threads share the process's heap and method area, each thread has its own program counter, virtual machine stack and local method stack.

### thread lifecycle

1. New
2. Active: When a thread invokes the start() method
3. Blocked / Waiting
4. Timed Waiting
5. Terminated

### Sleep vs wait

Both can suspend the execution of a thread.

The main difference between the two is that the sleep() method does not release a lock, while the wait() method does.
wait() is usually used for inter-thread communication, and sleep() is usually used to suspend execution.
After the wait() method is called, the thread does not wake up automatically and requires another thread to call the notify() or notifyAll() method on the same object. sleep() method will wake up automatically when execution is complete. 

Alternatively, you can use wait(long timeout) to wake up the thread after a timeout.

### synchronized vs volatile

Synchronized keyword solves the problem of execution control, it will prevent other threads from obtaining the monitoring lock of the current object, so that the block of code in the current object protected by the synchronized keyword can not be accessed by other threads, and can not be executed concurrently.

The volatile keyword solves the memory visibility problem by making all reads and writes to volatile variables swipe directly to main memory

Volatile essentially tells jvm that the value of the current variable in the register (working memory) is indeterminate and needs to be read from main memory; synchronized locks the current variable so that only the current thread can access it and other threads are blocked.

- volatile can only be used at the variable level; synchronized can be used at the variable, method, and class levels
- volatile only enables variable modification visibility, not atomicity, while synchronized guarantees variable modification visibility and atomicity
- volatile does not cause thread blocking; synchronized may cause thread blocking.

volatile-tagged variables are not optimized by the compiler; synchronized-tagged variables can be optimized by the compiler

### Handling multi threads

As a first step, you need to implement a run() method provided by a **Runnable** interface.

```
public void run( )
```

As a second step, you will instantiate a **Thread** object using the following constructor −

```
Thread(Runnable threadObj, String threadName);
```

Once a Thread object is created, you can start it by calling **start()** method, which executes a call to run( ) method. 

```
void start();
```

### Deadlock

Multiple threads are blocked at the same time, and one or all of them are waiting for some resource to be released. 

1. 互斥条件：该资源任意一个时刻只由一个线程占用。
2. 请求与保持条件：一个线程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件:线程已获得的资源在未使用完之前不能被其他线程强行剥夺，只有自己使用完毕后才释放资源。
4. 循环等待条件:若干线程之间形成一种头尾相接的循环等待资源关系。

The four necessary conditions for a deadlock situation to occur are **mutual exclusion, hold and wait, no preemption and circular set**. We can prevent a deadlock by preventing any one of these conditions.

### Reflection

Reflection is an API that is used to examine or modify the behavior of methods, classes, and interfaces at runtime.

say you have an object of an unknown type in Java, and you would like to call a 'doSomething' method on it if one exists. Java's static typing system isn't really designed to support this unless the object conforms to a known interface, but using reflection, your code can look at the object and find out if it has a method called 'doSomething' and then call it if you want to.

get class for an object:

```
Class alunbarClass = TargetObject.class;
Class alunbarClass1 = Class.forName("cn.javaguide.TargetObject");
TargetObject o = new TargetObject();
Class alunbarClass2 = o.getClass();
lassLoader.getSystemClassLoader().loadClass("cn.javaguide.TargetObject");
```

## Java 集合

### list set queue map

List (good for dealing with order): The elements stored are ordered and repeatable.
Set (focus on the unique nature): storage of elements is unordered, non-repeatable.
Queue (queuing function to achieve the caller): according to the specific queuing rules to determine the order, the elements stored is ordered, repeatable.
Map (key to search experts): use key-value pairs (key-value) storage, similar to the mathematical function y = f (x), "x" represents key, "y" represents value, key is unordered, irreducible, value is unordered, repeatable, each key is mapped to at most one value.

### Hashmap

HashMap: Before JDK1.8, HashMap consists of array + chain table, array is the main body of HashMap, and chain table exists mainly for resolving hash conflicts ("zipper method" to resolve conflicts). 

Now, When the chain table length is greater than the threshold (default is 8), the chain table will be converted to red-black tree to reduce the search time.

before converting the chain table to red-black tree will determine, if the current array length is less than 64, then will choose to expand the array first, rather than converting to red-black tree

HashMap 的结构，是数组+链表+红黑树的结构，

链表长度超过 8 的时候，链表便会转为红黑树，另外，当链表长度小于 6 的时候，会从红黑树转为链表。

#### hash map, how to find index when put

1. Calculate the hash code of the key:
2. Map the hash code to an index:
3. Handle potential collisions:
   - In cases where multiple keys result in the same index (hash collision), the HashMap uses additional data structures to handle collisions, such as linked lists or binary trees.
4. Store the key-value pair:
   - Once the index is determined, the key-value pair is stored at that index within the HashMap's internal array.

### ArrayList 与 LinkedList 区别?

- **是否保证线程安全：** `ArrayList` 和 `LinkedList` 都是不同步的，也就是不保证线程安全；
- **底层数据结构：** The underlying `ArrayList` uses **`Object` arrays**; the underlying `LinkedList` uses **bidirectional chained tables** data structures
- 插入和删除是否受元素位置的影响：
  - `ArrayList` 采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响。 比如：执行`add(E e)`方法的时候， `ArrayList` 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是 O(1)。但是如果要在指定位置 i 插入和删除元素的话（`add(int index, E element)`）时间复杂度就为 O(n-i)。
  - `LinkedList` 采用链表存储，所以，如果是在头尾插入或者删除元素不受元素位置的影响（`add(E e)`、`addFirst(E e)`、`addLast(E e)`、`removeFirst()` 、 `removeLast()`），时间复杂度为 O(1)，如果是要在指定位置 `i` 插入和删除元素的话（`add(int index, E element)`，`remove(Object o)`）， 时间复杂度为 O(n) ，因为需要先移动到指定位置再插入。
- **是否支持快速随机访问get：** `LinkedList` does not support efficient access to random elements, while `ArrayList` (which implements the RandomAccess interface) does.
- **内存空间占用：** The space wastage of `ArrayList` is mainly due to the fact that a certain amount of space is reserved at the end of the list, while the space consumption of LinkedList is due to the fact that each element of it consumes more space than ArrayList

### Comparable vs Comparator

- `comparable` 接口实际上是出自`java.lang`包 它有一个 `compareTo(Object obj)`方法用来排序

  Comparable is the sorting interface. If a class implements the Comparable interface, it means that the class supports sorting. Lists or arrays of objects of classes that implement the Comparable interface can be automatically sorted by Collections.sort or Arrays.sort.

- `comparator`接口实际上是出自 java.util 包它有一个`compare(Object obj1, Object obj2)`方法用来排序

  Comparator is a comparison interface, if we need to control the order of a class, and the class itself does not support sorting (that is, does not implement the Comparable interface), then we can create a "comparator of the class" to sort, 

一般我们需要对一个集合使用自定义排序时，我们就要重写`compareTo()`方法或`compare()`方法，

```java
Collections.sort(arrayList, new Comparator<Integer>() {

            @Override
            public int compare(Integer o1, Integer o2) {
                return o2.compareTo(o1);
            }
        });

public int compareTo(Person o) {
        if (this.age > o.getAge()) {
            return 1;
        }
        if (this.age < o.getAge()) {
            return -1;
        }
        return 0;
    }
```

### 排序

常见的稳定排序算法有：
**冒泡排序**（Bubble Sort） — O(n²)**插入排序**（Insertion Sort）— O(n²)桶排序（Bucket Sort）— O(n); 需要 O(k) 额外空间计数排序 (Counting Sort) — O(n+k); 需要 O(n+k) 额外空间**合并排序**（Merge Sort）— O(nlogn); 需要 O(n) 额外空间**二叉排序树排序** （Binary tree sort） — O(n log n) 期望时间; O(n²)最坏时间; 需要 O(n) 额外空间基数排序（Radix sort）— O(n·k); 需要 O(n) 额外空间
常见的不稳定排序算法有：**选择排序**（Selection Sort）— O(n²)希尔排序（Shell Sort）— O(nlogn)**堆排序**（Heapsort）— O(nlogn)**快速排序**（Quicksort）— O(nlogn) 期望时间, O(n²) 最坏情况; 对于大的、乱数串行一般相信是最快的已知排序

### design pattern

1. Singleton Pattern: This pattern is used when we need to ensure that only one instance of a class is created and that it can be easily accessed by other objects. For example, a logger class in a large application may be a singleton so that only one instance of the logger is used throughout the application.
2. Factory Pattern: This pattern is used when we want to create objects without exposing the creation logic to the client. For example, a document editor might have a factory that creates different types of documents (PDF, Word, etc.) without the client having to know how each document is created.
3. Observer Pattern: This pattern is used when we want to notify a set of objects when the state of another object changes. For example, in a GUI application, we might want to update the display whenever a user interacts with a widget.
4. Strategy Pattern: This pattern is used when we want to define a family of algorithms, encapsulate each one as an object, and make them interchangeable. For example, a search engine might have multiple algorithms for ranking search results, and the strategy pattern can be used to swap between them easily.

### Spring & Springboot

Spring is a lightweight, non-invasive Inversion of Control (IoC) and Aspect Oriented Programming (AOP) framework.

### Spring bean

*In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container.*

### why spring

https://www.geeksforgeeks.org/10-reasons-to-use-spring-framework-in-projects/



### features

OC and DI support: The Spring factory is used to generate beans and manage their lifecycle to achieve a highly cohesive and low-coupling design philosophy.

AOP programming support: Spring provides tangent oriented programming, which makes it easy to implement tangent features such as permission interception and runtime monitoring of programs.

Declarative transaction support: Support for configuration to complete the management of transactions, rather than through the hard-coded way, some of the previous repeated transactions commit, rollback JDBC code, you can not write their own.

Quick test support: Spring provides support for Junit

Fast integration features: Easy to integrate a variety of good frameworks(such as: Struts, Hibernate, MyBatis, Quartz, etc.).

Complex API Template Wrapping

### module

1. **Spring Core**：Spring 核心，它是框架最基础的部分，提供 IOC 和依赖注入 DI 特性。
2. **Spring Context**：Spring 上下文容器，它是 BeanFactory 功能加强的一个子接口。
3. **Spring Web**：它提供 Web 应用开发的支持。
4. **Spring MVC**：它针对 Web 应用中 MVC 思想的实现。
5. **Spring DAO**：提供对 JDBC 抽象层，简化了 JDBC 编码，同时，编码更具有健壮性。
6. **Spring ORM**：它支持用于流行的 ORM 框架的整合，比如：Spring + Hibernate、Spring + iBatis、Spring + JDO 的整合等。
7. **Spring AOP**：即面向切面编程，它提供了与 AOP 联盟兼容的编程实现。

![Spring模块划分](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/spring-bb7c13ea-3174-4b32-84b8-821849ddc377.png)

###  commonly used annotations

![Spring常用注解](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/spring-8d0a1518-a425-4887-9735-45321095d927.png)

**Web**:

- @Controller：组合注解（组合了@Component 注解），应用在 MVC 层（控制层）。
- @RestController：该注解为一个组合注解，相当于@Controller 和@ResponseBody 的组合，注解在类上，意味着，该 Controller 的所有方法都默认加上了@ResponseBody。
- @RequestMapping：用于映射 Web 请求，包括访问路径和参数。如果是 Restful 风格接口，还可以根据请求类型使用不同的注解：
  - @GetMapping
  - @PostMapping
  - @PutMapping
  - @DeleteMapping
- @ResponseBody：支持将返回值放在 response 内，而不是一个页面，通常用户返回 json 数据。
- @RequestBody：允许 request 的参数在 request 体中，而不是在直接连接在地址后面。
- @PathVariable：用于接收路径参数，比如 `@RequestMapping(“/hello/{name}”)`申明的路径，将注解放在参数中前，即可获取该值，通常作为 Restful 的接口实现方法。
- @RestController：该注解为一个组合注解，相当于@Controller 和@ResponseBody 的组合，注解在类上，意味着，该 Controller 的所有方法都默认加上了@ResponseBody。

**容器**:

- @Component：表示一个带注释的类是一个“组件”，成为 Spring 管理的 Bean。当使用基于注解的配置和类路径扫描时，这些类被视为自动检测的候选对象。同时@Component 还是一个元注解。
- @Service：组合注解（组合了@Component 注解），应用在 service 层（业务逻辑层）。
- @Repository：组合注解（组合了@Component 注解），应用在 dao 层（数据访问层）。
- @Autowired：Spring 提供的工具（由 Spring 的依赖注入工具（BeanPostProcessor、BeanFactoryPostProcessor）自动注入）。
- @Qualifier：该注解通常跟 @Autowired 一起使用，当想对注入的过程做更多的控制，@Qualifier 可帮助配置，比如两个以上相同类型的 Bean 时 Spring 无法抉择，用到此注解
- @Configuration：声明当前类是一个配置类（相当于一个 Spring 配置的 xml 文件）
- @Value：可用在字段，构造器参数跟方法参数，指定一个默认值，支持 `#{} 跟 \${}` 两个方式。一般将 SpringbBoot 中的 application.properties 配置的属性值赋值给变量。
- @Bean：注解在方法上，声明当前方法的返回值为一个 Bean。返回的 Bean 对应的类中可以定义 init()方法和 destroy()方法，然后在`@Bean(initMethod=”init”,destroyMethod=”destroy”)`定义，在构造之后执行 init，在销毁之前执行 destroy。
- @Scope:定义我们采用什么模式去创建 Bean（方法上，得有@Bean） 其设置类型包括：Singleton 、Prototype、Request 、 Session、GlobalSession。

**AOP**:

- @Aspect:声明一个切面（类上） 使用@After、@Before、@Around 定义建言（advice），可直接将拦截规则（切点）作为参数。
  - `@After` ：在方法执行之后执行（方法上）。
  - `@Before`： 在方法执行之前执行（方法上）。
  - `@Around`： 在方法执行之前与之后执行（方法上）。
  - `@PointCut`： 声明切点 在 java 配置类中使用@EnableAspectJAutoProxy 注解开启 Spring 对 AspectJ 代理的支持（类上）。

**事务：**

- @Transactional：在要开启事务的方法上使用@Transactional 注解，即可声明式开启事务

### design pattern

1. **工厂模式** : Spring 容器本质是一个大工厂，使用工厂模式通过 BeanFactory、ApplicationContext 创建 bean 对象。factory 
2. **代理模式** : Spring AOP 功能功能就是通过代理模式来实现的，分为动态代理和静态代理。
3. **单例模式** : Spring 中的 Bean 默认都是单例的，这样有利于容器对 Bean 的管理。
4. **模板模式** : Spring 中 JdbcTemplate、RestTemplate 等以 Template 结尾的对数据库、网络等等进行操作的模板类，就使用到了模板模式。
5. **观察者模式**: Spring 事件驱动模型就是观察者模式很经典的一个应用。
6. **适配器模式** :Spring AOP 的增强或通知 (Advice) 使用到了适配器模式、Spring MVC 中也是用到了适配器模式适配 Controller。
7. **策略模式**：Spring 中有一个 Resource 接口，它的不同实现类，会根据不同的策略去访问资源。

### IoC

Inversion of Control is a principle in software engineering which transfers the control of objects or portions of a program to a container or framework. 

**ApplicationContext** context  = new **ClassPathXmlApplicationContext**("applicationContext.xml");

**AOP is a programming paradigm that aims to increase modularity by allowing the separation of cross-cutting concerns.**

An aspect is a modularization of a concern that cuts across multiple classes. Unified logging can be an example of such cross-cutting concern.

## 笔试

```java
static long swapCount(String s)
    {
        char[] chars = s.toCharArray();
         
        // stores total number of Left and Right
        // brackets encountered
        int countLeft = 0, countRight = 0;
                // swap stores the number of swaps required
        //imbalance maintains the number of imbalance pair
        int swap = 0 , imbalance = 0;
         
        for(int i =0; i< chars.length; i++)
        {
            if(chars[i] == '[')
            {
                // increment count of Left bracket
                countLeft++;
                if(imbalance > 0)
                {
                    // swaps count is last swap count + total
                    // number imbalanced brackets
                    swap += imbalance;
                    // imbalance decremented by 1 as it solved
                    // only one imbalance of Left and Right
                    imbalance--;    
                }
            } else if(chars[i] == ']' )
            {
                // increment count of Right bracket
                countRight++;
                // imbalance is reset to current difference
                // between Left and Right brackets
                imbalance = (countRight-countLeft);
            }
        }
        return swap;
    }
```

```java
public static String correctPath(String s) {
    Random random = new Random();
    while (true) {
        List<Character> route = new ArrayList<>();
        List<String> tracepos = new ArrayList<>();
        int x = 1, y = 5, answer = 1;
        for (char i : s.toCharArray()) {
            if (i == '?') {
                i = "lrud".charAt(random.nextInt(4));
            }
            if (i == 'u') {
                y += 1;
            } else if (i == 'd') {
                y -= 1;
            } else if (i == 'r') {
                x += 1;
            } else if (i == 'l') {
                x -= 1;
            }
            String currentPosition = x + "," + y;
            if (tracepos.contains(currentPosition)) {
                answer = 0;
                break;
            } else {
                tracepos.add(currentPosition);
            }
            route.add(i);
            if (x == 6 || x == 0 || y == 0 || y == 6) {
                answer = 0;
                break;
            }
        }
        if (x == 5 && y == 1 && answer == 1) {
            StringBuilder sb = new StringBuilder();
            for (char c : route) {
                sb.append(c);
            }
            return sb.toString();
        }
    }
}
```
