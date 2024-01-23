# 八股Perp

## 操作系统

### 进程和线程

- **进程（Process）** 是指计算机中正在运行的一个程序实例。举例：你打开的微信就是一个进程。
- **线程（Thread）** 也被称为轻量级进程，更加轻量。多个线程可以在同一个进程中同时执行

1.线程是程序执行的最小单位，而进程是操作系统分配资源的最小单位；

2.一个进程由一个或多个线程组成，线程是一个进程中代码的不同执行路线；

4.调度和切换：线程上下文切换比进程上下文切换要快得多。

一个进程中可以有多个线程，多个线程共享进程的**堆**和**方法区 (JDK1.8 之后的元空间)资源，但是每个线程有自己的程序计数器**、**虚拟机栈** 和 **本地方法栈**。

#### 线程同步的方式：

1. **互斥锁(Mutex)**：采用互斥对象机制，只有拥有互斥对象的线程才有访问公共资源的权限。因为互斥对象只有一个，所以可以保证公共资源不会被多个线程同时访问。比如 Java 中的 `synchronized` 关键词和各种 `Lock` 都是这种机制。
2. **读写锁（Read-Write Lock）**：允许多个线程同时读取共享资源，但只有一个线程可以对共享资源进行写操作。
3. **信号量(Semaphore)**：它允许同一时刻多个线程访问同一资源，但是需要控制同一时刻访问此资源的最大线程数量。
4. **屏障（Barrier）**：屏障是一种同步原语，用于等待多个线程到达某个点再一起继续执行。当一个线程到达屏障时，它会停止执行并等待其他线程到达屏障，直到所有线程都到达屏障后，它们才会一起继续执行。比如 Java 中的 `CyclicBarrier` 是这种机制。
5. **事件(Event)** :Wait/Notify：通过通知操作的方式来保持多线程同步，还可以方便的实现多线程优先级的比较操作。

#### 线程生命周期

![img](https://segmentfault.com/img/remote/1460000038387311)

#### 进程状态

我们一般把进程大致分为 5 种状态，这一点和线程很像！

- **创建状态(new)**：进程正在被创建，尚未到就绪状态。
- **就绪状态(ready)**：进程已处于准备运行状态，即进程获得了除了处理器之外的一切所需资源，一旦得到处理器资源(处理器分配的时间片)即可运行。
- **运行状态(running)**：进程正在处理器上运行(单核 CPU 下任意时刻只有一个进程处于运行状态)。
- **阻塞状态(waiting)**：又称为等待状态，进程正在等待某一事件而暂停运行如等待某资源为可用或等待 IO 操作完成。即使处理器空闲，该进程也不能运行。
- **结束状态(terminated)**：进程正在从系统中消失。可能是进程正常结束或其他原因中断退出运行。

#### 多线程 vs 多进程

多进程和[多线程](https://so.csdn.net/so/search?q=多线程&spm=1001.2101.3001.7020)的主要区别是：线程是进程的子集（部分），一个进程可能由多个线程组成。多进程的数据是分开的、共享复杂，需要用IPC；但同步简单。多线程共享进程数据，共享简单；但同步复杂。

多进程不受GIL的限制，**多个进程是并行执行，所以计算速度就比多线程快了很多**。

https://blog.csdn.net/qq_36287702/article/details/105612182

#### 进程通信方式

- 管道
- 有名管道
- 信号
- 消息队列
- 信号量 semaphores
- 共享内存
- sockets

#### 进程调度算法

**先到先服务调度算法(FCFS，First Come, First Served)** : 从就绪队列中选择一个最先进入该队列的进程为之分配资源，使它立即执行并一直执行到完成或发生某事件而被阻塞放弃占用 CPU 时再重新调度。

**短作业优先的调度算法(SJF，Shortest Job First)** : 从就绪队列中选出一个估计运行时间最短的进程为之分配资源，使它立即执行并一直执行到完成或发生某事件而被阻塞放弃占用 CPU 时再重新调度。

**时间片轮转调度算法（RR，Round-Robin）** : 时间片轮转调度是一种最古老，最简单，最公平且使用最广的算法。每个进程被分配一个时间段，称作它的时间片，即该进程允许运行的时间。

**多级反馈队列调度算法（MFQ，Multi-level Feedback Queue）**：前面介绍的几种进程调度的算法都有一定的局限性。如**短进程优先的调度算法，仅照顾了短进程而忽略了长进程** 。多级反馈队列调度算法既能使高优先级的作业得到响应又能使短作业（进程）迅速完成。，因而它是目前**被公认的一种较好的进程调度算法**，UNIX 操作系统采取的便是这种调度算法。

**优先级调度算法（Priority）**：为每个流程分配优先级，首先执行具有最高优先级的进程，依此类推。具有相同优先级的进程以 FCFS 方式执行。可以根据内存要求，时间要求或任何其他资源要求来确定优先级

#### 进程切换和线程切换的区别

- 操作系统为了控制进程的执行，必须有能力挂起正在CPU上运行的进程，并恢复以前挂起的某个进程的执行，这种行为被称为进程切换，任务切换或上下文切换。
  - 切换新的页表，然后使用新的虚拟地址空间
  - 切换内核栈，加入新的内容(PCB控制块，资源相关)，硬件上下文切换
- **线程的切换就只包括线程上下文的切换，就是替换线程放在处理器寄存器中的相关数据，但是同样需要从用户态转向内核态**

最主要的一个区别在于**进程切换涉及虚拟地址空间的切换而线程不会**。因为每个进程都有自己的虚拟地址空间，而线程是共享所在进程的虚拟地址空间的，因此同一个进程中的线程进行线程切换时不涉及虚拟地址空间的转换。

#### 线程的上下文切换

当出现如下情况的时候，线程会从占用 CPU 状态中退出。

- 主动让出 CPU，比如调用了 `sleep()`, `wait()` 等。
- 时间片用完，因为操作系统要防止一个线程或者进程长时间占用 CPU 导致其他线程或者进程饿死。
- 调用了阻塞类型的系统中断，比如请求 IO，线程被阻塞。
- 被终止或结束运行

#### 虚拟内存

**虚拟内存(Virtual Memory)** 是计算机系统内存管理非常重要的一个技术，本质上来说它只是逻辑存在的，是一个假想出来的内存空间，主要作用是作为进程访问主存（物理内存）的桥梁并简化内存管理。

是操作系统为每个进程提供的一种抽象，每个进程都有属于自己的、私有的、地址连续的虚拟内存，当然我们知道最终进程的数据及代码必然要放到物理内存上，那么必须有某种机制能记住虚拟地址空间中的某个数据被放到了哪个物理内存地址上，这就是所谓的地址空间映射，那么操作系统是如何记住这种映射关系的呢，答案就是页表。

总结来说，虚拟内存主要提供了下面这些能力：

- **隔离进程**：物理内存通过虚拟地址空间访问，虚拟地址空间与进程一一对应。每个进程都认为自己拥有了整个物理内存，进程之间彼此隔离，一个进程中的代码无法更改正在由另一进程或操作系统使用的物理内存。
- **提升物理内存利用率**：有了虚拟地址空间后，操作系统只需要将进程当前正在使用的部分数据或指令加载入物理内存。
- **简化内存管理**：进程都有一个一致且私有的虚拟地址空间，程序员不用和真正的物理内存打交道，而是借助虚拟地址空间访问物理内存，从而简化了内存管理。
- **多个进程共享物理内存**：进程在运行过程中，会加载许多操作系统的动态库。这些库对于每个进程而言都是公用的，它们在内存中实际只会加载一份，这部分称为共享内存。
- **提高内存使用安全性**：控制进程对物理内存的访问，隔离不同进程的访问权限，提高系统的安全性。
- **提供更大的可使用内存空间**：可以让程序拥有超过系统物理内存大小的可用内存空间。这

### 堆 vs 栈

- 栈stack的空间由操作系统自动分配/释放，heap上的空间手动分配/释放； 
- stack空间有限，heap是很大的自由内存区；
- Java程序创建的实例存在堆中，在stack中存一个引用

#### 内存管理

连续分配方式，是指为一个用户程序分配一个连续的内存空间，它主要包括单一连续分配、固定分区分配和动态分区分配。

非连续分配方式：分页存储，地址变换

### 内存泄漏 vs 内存溢出

内存溢出（out of memory）：是指程序在申请内存时，没有足够的内存空间供其使用，出现out of memory；比如申请了一个integer,但给它存了long才能存下的数，那就是内存溢出。

内存泄露（memory leak）：是指程序在申请内存后，无法释放已申请的内存空间，一次内存泄露危害可以忽略，但内存泄露堆积后果很严重，无论多少内存,迟早会被占光。

### 锁

#### 死锁必要条件

- **禁止[抢占](https://zh.wikipedia.org/wiki/抢占式多任务处理)**（no preemption）：系统资源不能被强制从一个进程中退出。

- **持有和等待**（hold and wait）：一个进程可以在等待时持有系统资源。

- **[互斥](https://zh.wikipedia.org/wiki/互斥锁)**（mutual exclusion）：资源只能同时分配给一个行程，无法多个行程共用。

- **循环等待**（circular waiting）：一系列进程互相持有其他进程所需要的资源。

**互斥**：资源必须处于非共享模式，即一次只有一个进程可以使用。如果另一进程申请该资源，那么必须等待直到该资源被释放为止。

**占有并等待**：一个进程至少应该占有一个资源，并等待另一资源，而该资源被其他进程所占有。

**非抢占**：资源不能被抢占。只能在持有资源的进程完成任务后，该资源才会被释放。

**循环等待**：有一组等待进程 `{P0, P1,..., Pn}`， `P0` 等待的资源被 `P1` 占有，`P1` 等待的资源被 `P2` 占有，……，`Pn-1` 等待的资源被 `Pn` 占有，`Pn` 等待的资源被 `P0` 占有。

#### 死锁的例子

```java
public class DeadLockDemo {
    private static Object resource1 = new Object();//资源 1
    private static Object resource2 = new Object();//资源 2

    public static void main(String[] args) {
        new Thread(() -> {
            synchronized (resource1) {
                System.out.println(Thread.currentThread() + "get resource1");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource2");
                synchronized (resource2) {
                    System.out.println(Thread.currentThread() + "get resource2");
                }
            }
        }, "线程 1").start();

        new Thread(() -> {
            synchronized (resource2) {
                System.out.println(Thread.currentThread() + "get resource2");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource1");
                synchronized (resource1) {
                    System.out.println(Thread.currentThread() + "get resource1");
                }
            }
        }, "线程 2").start();
    }
}

```

#### 解决死锁

是通过考虑破坏第二个条件和第四个条件

**1、静态分配策略**

静态分配策略可以破坏死锁产生的第二个条件（占有并等待）。所谓静态分配策略，就是指一个进程必须在执行前就申请到它所需要的全部资源，并且知道它所要的资源都得到满足之后才开始执行。进程要么占有所有的资源然后开始执行，要么不占有资源，不会出现占有一些资源等待一些资源的情况。

静态分配策略逻辑简单，实现也很容易，但这种策略 **严重地降低了资源利用率**，因为在每个进程所占有的资源中，有些资源是在比较靠后的执行时间里采用的，甚至有些资源是在额外的情况下才使用的，这样就可能造成一个进程占有了一些 **几乎不用的资源而使其他需要该资源的进程产生等待** 的情况。

**2、层次分配策略**

层次分配策略破坏了产生死锁的第四个条件(循环等待)。在层次分配策略下，所有的资源被分成了多个层次，一个进程得到某一次的一个资源后，它只能再申请较高一层的资源；当一个进程要释放某层的一个资源时，必须先释放所占用的较高层的资源，按这种策略，是不可能出现循环等待链的，因为那样的话，就出现了已经申请了较高层的资源，反而去申请了较低层的资源，不符合层次分配策略，证明略。

3. **设置事务等待锁的超时时间**。当一个事务的等待时间超过该值后，就对这个事务进行回滚，于是锁就释放了，另一个事务就可以继续执行了。
4. **开启主动死锁检测**。主动死锁检测在发现死锁后，主动回滚死锁链条中的某一个事务，让其他事务得以继续执行。

#### 乐观锁，悲观锁

悲观： **共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程**）。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。Java中`synchronized`和`ReentrantLock`等独占锁就是悲观锁思想的实现。

乐观：**乐观锁适用于多读的应用类型，这样可以提高吞吐量**，像数据库提供的类似于**write_condition机制**，其实都是提供的乐观锁。在Java中`java.util.concurrent.atomic`包下面的原子变量类就是使用了乐观锁的一种实现方式**CAS**实现的。

机制：版本号机制、CAS（Compare and Swap）算法。

问题：ABA问题、循环时间长开销大、只能保证一个共享变量的原子操作

#### **CAS与synchronized的使用情景**

> **简单的来说CAS适用于写比较少的情况下（多读场景，冲突一般较少），synchronized适用于写比较多的情况下（多写场景，冲突一般较多）**

1. 对于资源竞争较少（线程冲突较轻）的情况，使用synchronized同步锁进行线程阻塞和唤醒切换以及用户态内核态间的切换操作额外浪费消耗cpu资源；而CAS基于硬件实现，不需要进入内核，不需要切换线程，操作自旋几率较少，因此可以获得更高的性能。
2. 对于资源竞争严重（线程冲突严重）的情况，CAS自旋的概率会比较大，从而浪费更多的CPU资源，效率低于synchronized。

synchronized的底层实现主要依靠 **Lock-Free** 的队列，基本思路是 **自旋后阻塞**，**竞争切换后继续竞争锁**，**稍微牺牲了公平性，但获得了高吞吐量**。在线程冲突较少的情况下，可以获得和CAS类似的性能；而线程冲突严重的情况下，性能远高于CAS。

#### 分布式锁

一个最基本的分布式锁需要满足：

- **互斥**：任意一个时刻，锁只能被一个线程持有。
- **高可用**：锁服务是高可用的，当一个锁服务出现问题，能够自动切换到另外一个锁服务。并且，即使客户端的释放锁的代码逻辑出现问题，锁最终一定还是会被释放，不会影响其他线程对共享资源的访问。这一般是通过超时机制实现的。
- **可重入**：一个节点获取了锁之后，还可以再次获取锁。

除了上面这三个基本条件之外，一个好的分布式锁还需要满足下面这些条件：

- **高性能**：获取和释放锁的操作应该快速完成，并且不应该对整个系统的性能造成过大影响。
- **非阻塞**：如果获取不到锁，不能无限期等待，避免对系统正常运行造成影响

常见分布式锁实现方案如下：

- 基于关系型数据库比如 MySQL 实现分布式锁。
- 基于分布式协调服务 ZooKeeper 实现分布式锁。
- 基于分布式键值存储系统比如 Redis 、Etcd 实现分布式锁。

### 内核态 vs 用户态

**用户态(User Mode)** : 用户态运行的进程可以直接读取用户程序的数据，拥有较低的权限。当应用程序需要执行某些需要特殊权限的操作，例如读写磁盘、网络通信等，就需要向操作系统发起系统调用请求，进入内核态。

**内核态(Kernel Mode)**：内核态运行的进程几乎可以访问计算机的任何资源包括系统的内存空间、设备、驱动程序等，不受限制，拥有非常高的权限。当操作系统接收到进程的系统调用请求时，就会从用户态切换到内核态，执行相应的系统调用，并将结果返回给进程，最后再从内核态切换回用户态。

为什么分：

在 CPU 的所有指令中，有一些指令是比较危险的比如内存分配、设置时钟、IO 处理等，如果所有的程序都能使用这些指令的话，会对系统的正常运行造成灾难性地影响。因此，我们需要限制这些危险指令只能内核态运行。这些只能由操作系统内核态执行的指令也被叫做 **特权指令** 。

如果计算机系统中只有一个内核态，那么所有程序或进程都必须共享系统资源，例如内存、CPU、硬盘等，这将导致系统资源的竞争和冲突，从而影响系统性能和效率。并且，这样也会让系统的安全性降低，毕竟所有程序或进程都具有相同的特权级别和访问权限。

## 计算机网络

### OSI分层模型

![osi七层模型2](https://oss.javaguide.cn/github/javaguide/osi%E4%B8%83%E5%B1%82%E6%A8%A1%E5%9E%8B2.png)

### HTTP

#### 从输入 URL 到页面展示到底发生了什么？

1. DNS 解析
2. TCP 连接
3. 发送 HTTP 请求
4. 服务器处理请求并返回 HTTP 报文
5. 浏览器解析渲染页面
6. 连接结束

#### HTTP VS HTTPS

**端口号**：HTTP 默认是 80，HTTPS 默认是 443。

**URL 前缀**：HTTP 的 URL 前缀是 `http://`，HTTPS 的 URL 前缀是 `https://`。

**安全性和资源消耗**：HTTP 协议运行在 TCP 之上，所有传输的内容都是明文，客户端和服务器端都无法验证对方的身份。HTTPS 是运行在 SSL/TLS 之上的 HTTP 协议，SSL/TLS 运行在 TCP 之上。所有传输的内容都经过加密，加密采用对称加密，但对称加密的密钥用服务器方的证书进行了非对称加密。所以说，HTTP 安全性没有 HTTPS 高，但是 HTTPS 比 HTTP 耗费更多服务器资源。

**SEO（搜索引擎优化）**：搜索引擎通常会更青睐使用 HTTPS 协议的网站，因为 HTTPS 能够提供更高的安全性和用户隐私保护。使用 HTTPS 协议的网站在搜索结果中可能会被优先显示，从而对 SEO 产生影响。

HTTPS实现安全通信主要通过多种加密方式和身份验证实现，主要分三步：(1)客户端向服务器端索要证书并验证公钥;(2)双方协商生成"对话密钥";(3)双方采用"对话密钥"进行加密通信。可能的攻击：中间人攻击、黑客攻击、拒绝服务攻击、服务器劫持。

如何解决确保使用非对称加密传输秘钥过程中被中间人截获篡改的风险问题？

CA(Certificate Authority)证书颁发机构

#### HTTP 1.1, 2.0, 3.0

HTTP/2.0 的多路复用使得不同的请求可以共用一个 TCP 连接，避免建立多个连接带来不必要的额外开销，而 HTTP/1.1 中的每个请求都会建立一个单独的连接

HTTP/2.0 是基于 TCP 协议实现的，HTTP/3.0 新增了 QUIC（Quick UDP Internet Connections） 协议来实现可靠的传输，提供与 TLS/SSL 相当的安全性，具有较低的连接和传输延迟。你可以将 QUIC 看作是 UDP 的升级版本，在其基础上新增了很多功能比如加密、重传等等。

#### HTTP状态码

![常见 HTTP 状态码](https://oss.javaguide.cn/github/javaguide/cs-basics/network/http-status-code.png)

#### Get VS Post

语义（主要区别）：GET 通常用于获取或查询资源，而 POST 通常用于创建或修改资源。

幂等：GET 请求是幂等的，即多次重复执行不会改变资源的状态，而 POST 请求是不幂等的，即每次执行可能会产生不同的结果或影响资源的状态。

格式：GET 请求的参数通常放在 URL 中，形成查询字符串（querystring），而 POST 请求的参数通常放在请求体（body）中。

缓存：由于 GET 请求是幂等的，它可以被浏览器或其他中间节点（如代理、网关）缓存起来，以提高性能和效率。而 POST 请求则不适合被缓存，因为它可能有副作用，每次执行可能需要实时的响应。

安全性：GET 请求和 POST 请求如果使用 HTTP 协议的话，那都不安全，因为 HTTP 协议本身是明文传输的，必须使用 HTTPS 协议来加密传输数据。另外，GET 请求相比 POST 请求更容易泄露敏感数据，因为 GET 请求的参数通常放在 URL 中。

### TCP 

#### vs UDP

| TCP                    | UDP            |            |
| ---------------------- | -------------- | ---------- |
| 是否面向连接           | 是             | 否         |
| 是否可靠               | 是             | 否         |
| 是否有状态             | 是             | 否         |
| 传输效率               | 较慢           | 较快       |
| 传输形式               | 字节流         | 数据报文段 |
| 首部开销               | 20 ～ 60 bytes | 8 bytes    |
| 是否提供广播或多播服务 | 否             | 是         |

- **UDP 一般用于即时通信**，比如：语音、 视频、直播等等。这些场景对传输数据的准确性要求不是特别高，比如你看视频即使少个一两帧，实际给人的感觉区别也不大。
- **TCP 用于对传输准确性要求特别高的场景**，比如文件传输、发送和接收邮件、远程登录等等。

#### TCP 三次握手

![TCP 三次握手图解](https://oss.javaguide.cn/github/javaguide/cs-basics/network/tcp-shakes-hands-three-times.png)

**第一次握手**：Client 什么都不能确认；Server 确认了对方发送正常，自己接收正常

**第二次握手**：Client 确认了：自己发送、接收正常，对方发送、接收正常；Server 确认了：对方发送正常，自己接收正常

**第三次握手**：Client 确认了：自己发送、接收正常，对方发送、接收正常；Server 确认了：自己发送、接收正常，对方发送、接收正常

#### TCP 四次挥手

![TCP 四次挥手图解](https://oss.javaguide.cn/github/javaguide/cs-basics/network/tcp-waves-four-times.png)

#### 保证可靠性

**基于数据块传输**：应用数据被分割成 TCP 认为最适合发送的数据块，再传输给网络层，数据块被称为报文段或段。

**对失序数据包重新排序以及去重**：TCP 为了保证不发生丢包，就给每个包一个序列号，有了序列号能够将接收到的数据根据序列号排序，并且去掉重复序列号的数据就可以实现数据包去重。

**校验和** : TCP 将保持它首部和数据的检验和。这是一个端到端的检验和，目的是检测数据在传输过程中的任何变化。如果收到段的检验和有差错，TCP 将丢弃这个报文段和不确认收到此报文段。

**超时重传** : 当发送方发送数据之后，它启动一个定时器，等待目的端确认收到这个报文段。接收端实体对已成功收到的包发回一个相应的确认信息（ACK）。如果发送端实体在合理的往返时延（RTT）内未收到确认消息，那么对应的数据包就被假设为[已丢失open in new window](https://zh.wikipedia.org/wiki/丢包)并进行重传。

**流量控制** : TCP 连接的每一方都有固定大小的缓冲空间，TCP 的接收端只允许发送端发送接收端缓冲区能接纳的数据。当接收方来不及处理发送方的数据，能提示发送方降低发送的速率，防止包丢失。TCP 使用的流量控制协议是可变大小的滑动窗口协议（TCP 利用滑动窗口实现流量控制）。

**拥塞控制** : 当网络拥塞时，减少数据的发送

#### 拥塞控制

TCP拥塞控制主要涉及四个算法：慢启动、拥塞避免、快速重传和快速恢复。

1. **慢启动（Slow Start）：**
   - **原理：** 在连接刚开始时，TCP会以指数增长的方式增加发送窗口大小，从而加快数据传输速度。
   - **判断拥塞：** 如果发送端发现数据包被丢弃或超时，就会认为网络出现拥塞，触发慢启动。
2. **拥塞避免（Congestion Avoidance）：**
   - **原理：** 在慢启动阶段之后，TCP会以线性增长的方式增加发送窗口，以避免过快地增加数据传输速率，从而更稳定地操作。
   - **判断拥塞：** 通过检测丢包和超时事件来判断网络是否拥塞，如果发生拥塞，TCP会减小发送窗口并执行慢启动。
3. **快速重传（Fast Retransmit）：**
   - **原理：** 如果发送端连续收到三个相同的确认应答（ACK），它会认为有一个分组可能已经丢失，并立即重传该分组，而不必等待超时。
   - **判断拥塞：** 当发送端连续收到相同的 ACK 时，认为可能发生了拥塞，立即进行快速重传。
4. **快速恢复（Fast Recovery）：**
   - **原理：** 在执行快速重传后，TCP会将拥塞窗口减半，并转入快速恢复状态，继续以拥塞避免算法进行传输。
   - **判断拥塞：** 快速重传后，如果再次发生丢包，TCP认为网络可能拥塞，将进入快速恢复状态。

#### 为什么客户端要在TIME_WAIT状态等待一段时间

客户端在 TIME_WAIT 状态等待一段时间的原因主要涉及到 TCP 连接的可靠关闭和避免出现网络中残留的问题。在 TCP 的四次挥手中，客户端进入 TIME_WAIT 状态的主要目的有两点：

1. **确保被动关闭的最后一个 ACK 能够到达服务端：** 在四次挥手中，客户端发送了最后的 ACK（确认），这个 ACK 是为了确认服务端的 FIN 报文。由于网络的不确定性，有可能服务端收到了客户端的 ACK，但客户端没有收到服务端的 ACK，这可能是因为 ACK 报文在传输过程中丢失了。如果客户端在发送完 ACK 后立即关闭连接而不进入 TIME_WAIT 状态，那么如果这个 ACK 丢失，服务端将无法知道它已经被成功接收，从而可能会重发 FIN 报文。而客户端进入 TIME_WAIT 状态，会等待一段时间，确保在这个时间内网络中的 ACK 报文已经传递给服务端，从而避免服务端重发 FIN 报文。
2. **防止出现旧的连接数据包影响新连接：** TIME_WAIT 状态还有一个重要的作用是确保在该状态期间，之前的连接关闭完全。这是为了防止出现旧的连接数据包影响到新的连接。在 TIME_WAIT 状态的持续时间内，之前连接的所有数据包都应该在网络中被处理完毕。

#### 如果server端没有收到第三次ack，但是收到了client端发送的数据

1. **超时重传：** 服务端在发送 SYN-ACK 后，会等待一段时间来等待客户端的 ACK，如果超过了设定的超时时间仍然没有收到 ACK，服务端可以选择重新发送 SYN-ACK 报文，尝试再次建立连接。这是 TCP 协议中的一种可靠性机制，确保连接的稳定性。
2. **丢弃数据：** 如果服务端没有收到第三次握手的 ACK，但已经收到了客户端发送的数据，服务端也可以选择丢弃这些数据。这是因为在三次握手的过程中，连接还没有正式建立，服务端并不知道客户端的状态。

### Ping

PING 基于网络层的 **ICMP（Internet Control Message Protocol，互联网控制报文协议）**，其主要原理就是通过在网络上发送和接收 ICMP 报文实现的。

### Cookie Session

#### Cookie 

简单来说：**`Cookie` 存放在客户端，一般用来保存用户信息**。

应用：

我们在 `Cookie` 中保存已经登录过的用户信息，下次访问网站的时候页面可以自动帮你登录的一些基本信息给填了。除此之外，`Cookie` 还能保存用户首选项，主题和其他设置信息。

使用 `Cookie` 保存 `SessionId` 或者 `Token` ，向后端发送请求的时候带上 `Cookie`，这样后端就能取到 `Session` 或者 `Token` 了。这样就能记录用户当前的状态了，因为 HTTP 协议是无状态的。

Cookie` 还可以用来记录和分析用户行为。举个简单的例子你在网上购物的时候，因为 HTTP 协议是没有状态的，如果服务器想要获取你在某个页面的停留状态或者看了哪些商品，一种常用的实现方式就是将这些信息存放在 `Cookie

#### Session

**`Session` 的主要作用就是通过服务端记录用户的状态。** 典型的场景是购物车，当你要添加商品到购物车的时候，系统不知道是哪个用户操作的，因为 HTTP 协议是无状态的。服务端给特定的用户创建特定的 `Session` 之后就可以标识这个用户并且跟踪这个用户了。

`Cookie` 数据保存在客户端(浏览器端)，`Session` 数据保存在服务器端。相对来说 `Session` 安全性更高。如果使用 `Cookie` 的一些敏感信息不要写入 `Cookie` 中，最好能将 `Cookie` 信息加密然后使用到的时候再去服务器端解密。

#### 如果没有-cookie-的话-session-还能用吗如果没有 Cookie 的话 Session 还能用吗？

一般是通过 `Cookie` 来保存 `SessionID` ，假如你使用了 `Cookie` 保存 `SessionID` 的方案的话， 如果客户端禁用了 `Cookie`，那么 `Session` 就无法正常工作。

但是，并不是没有 `Cookie` 之后就不能用 `Session` 了，比如你可以将 `SessionID` 放在请求的 `url` 里面`https://javaguide.cn/?Session_id=xxx` 。这种方案的话可行，但是安全性和用户体验感降低。当然，为了安全你也可以对 `SessionID` 进行一次加密之后再传入后端。

### Restful API

REST，描述 Representational State Transfer，负责开发建立在 HTTP 协议上的网站应用程序。

#### 原则：

- 分层系统 layered system: 在客户端和 API 服务器之间，层是服务器。客户端和应用程序编程接口 (API) 服s务器之间发送的消息不受添加或删除层的影响，因为 REST（表示状态）使用模块化架构。
- 统一的界面 uniform interface：客户端和服务器必须始终使用相同的协议进行所有通信。该协议是 HTTP REST. Requests should identify resources, by using URI (uniform resource identifier)
- 无状态 statelessness：服务器不保留已发送响应的记录。每个响应都拥有完成交易所需的完整输入。它通过减少服务器负载和内存使用来改进解释。它还消除了由于信息不完整而导致请求失败的可能性。
- 可缓存 cacheability：客户端可以缓存任何资源以提高性能，方法是使用来自服务器的服务器响应来指示资源是否可缓存。
- 按需编码 code on demand：API 的响应可以包含用户可以运行的可执行代码

### RPC

https://javaguide.cn/distributed-system/rpc/rpc-intro.html#rpc-%E6%98%AF%E4%BB%80%E4%B9%88

**RPC（Remote Procedure Call）** 即远程过程调用，通过 RPC 可以帮助我们调用远程计算机上某个服务的方法，这个过程就像调用本地方法一样简单。并且！我们不需要了解底层网络编程的具体细节。

#### gRPC

gRPC采用客户端和服务器模型，以网络设备为gRPC客户端，采集器为gRPC服务器为例，说明gRPC的交互过程：

1. 设备在开启gRPC功能后作为gRPC客户端，采集器作为gRPC服务器。
2. 设备会根据应用服务（如订阅的事件）构建对应数据的格式（GPB/JSON），通过ProtoBuf（Protocol Buffers）编写Proto文件。然后，设备与采集器建立gRPC通道，通过gRPC协议向采集器发送请求消息。
3. 采集器收到请求消息后，会通过ProtoBuf解译Proto文件，还原出事先定义好的数据结构，进行业务处理。
4. 采集器处理完数据后，需要使用ProtoBuf重新编译应答数据，通过gRPC协议向设备发送应答消息。
5. 设备收到应答消息后，结束本次的gRPC交互。

## Java 基础

### Java内存

JVM 可以分为 5 个部分，分别是：

- 1.类加载器（Class Loader）：加载字节码文件到内存。
- 2.运行时数据区（Runtime Data Area）：JVM 核心内存空间结构模型。
  - 方法区：方法区存储虚拟机加载的类信息、常量、静态变量以及即时编译器编译后的代码等数据。永久代-》元空间
  - 本地方法栈
  - 程序计数器
  - Java堆：存放实例化对象，它被所有线程共享，是 GC 的主要管理区域，又可分为年轻代、老年代、永久代。
  - 虚拟机栈
- 3.执行引擎（Execution Engine）：对 JVM 指令进行解析，翻译成机器码，解析完成后提交到操作系统中。
- 4.本地库接口（Native Interface）：供 Java 调用的融合了不同开发语言的原生库。
- 5.本地方法库（Native Libraies）：Java 本地方法的具体实现。

运行时数据区 1.8之后：

![Java 运行时数据区域（JDK1.8 ）](https://oss.javaguide.cn/github/javaguide/java/jvm/java-runtime-data-areas-jdk1.8.png)

#### 线程私有

1. 程序计数器：
   - 字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
   - 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了
   - 程序计数器是唯一一个不会出现 `OutOfMemoryError` 的内存区域，它的生命周期随着线程的创建而创建，随着线程的结束而死亡。
2. 虚拟机栈
   - 除了一些 Native 方法调用是通过本地方法栈实现的(后面会提到)，其他所有的 Java 方法调用都是通过栈来实现的
   - 方法调用的数据需要通过栈进行传递，每一次方法调用都会有一个对应的栈帧被压入栈中，每一个方法调用结束后，都会有一个栈帧被弹出。
   - 栈由一个个栈帧组成，而每个栈帧中都拥有：局部变量表、操作数栈、动态链接（将符号引用转换为调用方法的直接引用）、方法返回地址。**栈帧随着方法调用而创建，随着方法结束而销毁。无论方法正常完成还是异常完成都算作方法结束。**
   - 可能出现的错误：
     - **`StackOverFlowError`：** 若栈的内存大小不允许动态扩展，那么当线程请求栈的深度超过当前 Java 虚拟机栈的最大深度的时候，就抛出 `StackOverFlowError` 错误。
     - **`OutOfMemoryError`：** 如果栈的内存大小可以动态扩展， 如果虚拟机在动态扩展栈时无法申请到足够的内存空间，则抛出`OutOfMemoryError`异常。
3. 本地方法栈
   - **虚拟机栈为虚拟机执行 Java 方法 （也就是字节码）服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。**
   - 一个Native Method就是一个java调用非java代码的接口
   - 标识符native可以与所有其它的java标识符连用，但是abstract除外。这是合理的，因为native暗示这些方法是有实现体的，只不过这些实现体是非java的

#### 线程共享

1. 堆

   - **唯一目的就是存放对象实例，几乎所有的对象实例以及数组都在这里分配内存**

   - 从 JDK 1.7 开始已经默认开启逃逸分析，如果某些方法中的对象引用没有被返回或者未被外面使用（也就是未逃逸出去），那么对象可以直接在栈上分配内存。
   - 在 JDK 7 版本及 JDK 7 版本之前，堆内存被通常分为下面三部分：
     1. 新生代内存(Young Generation)：Eden，S0，S1
     2. 老生代(Old Generation)
     3. 永久代(Permanent Generation)：1.8之后被MetaSpace取代，使用本地内存

   - 大部分情况，对象都会首先在 Eden 区域分配，在一次新生代垃圾回收后，如果对象还存活，则会进入 S0 或者 S1，并且对象的年龄还会加 1(Eden 区->Survivor 区后对象的初始年龄变为 1)，当它的年龄增加到一定程度（默认为 15 岁），就会被晋升到老年代中。

2. 方法区
   - 方法区会存储已被虚拟机加载的 **类信息、字段信息、方法信息、常量、静态变量、即时编译器编译后的代码缓存等数据**。
   - 也就是说永久代以及元空间是 HotSpot 虚拟机对虚拟机规范中方法区的两种实现方式。
3. 运行时常量池
   - Class 文件中除了有类的版本、字段、方法、接口等描述信息外，还有用于存放编译期生成的各种字面量（Literal）和符号引用（Symbolic Reference）的 **常量池表(Constant Pool Table)** 。
4. 字符串常量池
   - 提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的重复创建。
   - JDK1.7 之前，字符串常量池存放在永久代。1.7之后，因为永久代（方法区实现）的 GC 回收效率太低，只有在整堆收集 (Full GC)的时候才会被执行 GC。Java 程序中通常会有大量的被创建的字符串等待回收，将字符串常量池放到堆中，能够更高效及时地回收字符串内存。

5. 直接内存

   直接内存是一种特殊的内存缓冲区，并不在 Java 堆或方法区中分配的，而是通过 JNI 的方式在本地内存上分配的。JDK1.4 中新加入的 **NIO（Non-Blocking I/O，也被称为 New I/O）**，引入了一种基于**通道（Channel）与缓存区（Buffer）的 I/O 方式，它可以直接使用 Native 函数库直接分配堆外内存，然后通过一个存储在 Java 堆中的 DirectByteBuffer 对象作为这块内存的引用进行操作。这样就能在一些场景中显著提高性能，因为避免了在 Java 堆和 Native 堆之间来回复制数据**。

### 对象的创建过程

1. 类加载检查，是否能在常量池中定位到这个类的符号引用，如果没有被加载、解析、和初始化，则执行相应的类加载过程。
2. 分配内存：指针碰撞（没有内存碎片，分界指针）、空闲列表（列表维护可用内存块）
3. 初始化零值：将分配到的内存空间都初始化为零值
4. 设置对象头：这个对象是哪个类的实例、如何才能找到类的元数据信息、对象的哈希码、对象的 GC 分代年龄等信息
5. 执行init（constructor）方法

### Java Garbage Collection

#### 内存分配和回收原则

1. 对象首先在Eden区分配：当 Eden 区没有足够空间进行分配时，虚拟机将发起一次 Minor GC
2. 大对象（需要大量连续内存空间的对象比如字符串、数组）直接进入老年代
3. 长期存活的对象将进入老年代：如果对象在 Eden 出生并经过第一次 Minor GC 后仍然能够存活，并且能被 Survivor 容纳的话，将被移动到 Survivor 空间（s0 或者 s1）中，并将对象年龄设为 1(Eden 区->Survivor 区后对象的初始年龄变为 1)。对象在 Survivor 中每熬过一次 MinorGC,年龄就增加 1 岁，当它的年龄增加到一定程度（默认为 15 岁，根据区分垃圾收集器），就会被晋升到老年代中。对象晋升到老年代的年龄阈值，可以通过参数 `-XX:MaxTenuringThreshold` 来设置。

4. 主要进行GC的区域：

   部分收集 (Partial GC)：

   - 新生代收集（Minor GC / Young GC）：只对新生代进行垃圾收集；
   - 老年代收集（Major GC / Old GC）：只对老年代进行垃圾收集。需要注意的是 Major GC 在有的语境中也用于指代整堆收集；
   - 混合收集（Mixed GC）：对整个新生代和部分老年代进行垃圾收集。

   整堆收集 (Full GC)：收集整个 Java 堆和方法区。

#### 死亡对象判断

1. 引用计数法：**简单，效率高**， **但难解决对象之间循环引用的问题**

   给对象中添加一个引用计数器：

   - 每当有一个地方引用它，计数器就加 1；
   - 当引用失效，计数器就减 1；
   - 任何时候计数器为 0 的对象就是不可能再被使用的。

2. 可达性分析

   通过一系列的称为 **“GC Roots”** 的对象作为起点，从这些节点开始向下搜索，节点所走过的路径称为引用链，当一个对象到 GC Roots 没有任何引用链相连的话，则证明此对象是不可用的，需要被回收。

   **哪些对象可以作为 GC Roots 呢？**

   - 虚拟机栈(栈帧中的局部变量表)中引用的对象
   - 本地方法栈(Native 方法)中引用的对象
   - 方法区中类静态属性引用的对象
   - 方法区中常量引用的对象
   - 所有被同步锁持有的对象
   - JNI（Java Native Interface）引用的对象

   可达性分析法中不可达的对象被第一次标记并且进行一次筛选，筛选的条件是此对象是否有必要执行 `finalize` 方法。当对象没有覆盖 `finalize` 方法，或 `finalize` 方法已经被虚拟机调用过时，虚拟机将这两种情况视为没有必要执行。

   被判定为需要执行的对象将会被放在一个队列中进行第二次标记，除非这个对象与引用链上的任何一个对象建立关联，否则就会被真的回收。

   > finalize方法的有个问题就是，它允许对象的“复活”。当一个对象的finalize方法被调用时，它可以引用this（即将被收集的对象）。这个时候就可以将this这个引用又挂载回对象图（object graph）中，从而逃过了对象收集。因此，在finalize方法返回后，对象不会被轻易的收集。

   

3. 引用类型

   - 强Strong引用：最普遍，不会被GC回收，宁愿抛出OutOfMemory异常
   - 软Soft引用：如果内存空间不足，会被回收，可以和引用队列（ReferenceQueue）联合使用
   - 弱Weak引用：GC扫描时只要发现就会回收，GC是优先级很低的线程，因此不一定很快能发现
   - 虚Phantom引用：不会决定对象的生命周期，主要用来跟踪对象被垃圾回收的活动，必须和引用队列联合使用

4. 判断废弃常量

   假如在字符串常量池中存在字符串 "abc"，如果当前没有任何 String 对象引用该字符串常量的话，就说明常量 "abc" 就是废弃常量，如果这时发生内存回收的话而且有必要的话，"abc" 就会被系统清理出常量池了。

5. 判断无用类：虚拟机可以对满足上述 3 个条件的无用类进行回收，不是必然

   - 该类所有的实例都已经被回收，也就是 Java 堆中不存在该类的任何实例。

   - 加载该类的 `ClassLoader` 已经被回收。

   - 该类对应的 `java.lang.Class` 对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。

#### 垃圾收集算法

1. 标记清楚（Mark  and Sweep)

   首先标记出所有不需要回收的对象，在标记完成后统一回收掉所有没有被标记的对象。这种垃圾收集算法会带来两个明显的问题：

   - **效率问题**：标记和清除两个过程效率都不高。

   - **空间问题**：标记清除后会产生大量不连续的内存碎片。

2. 复制算法

   它可以将内存分为大小相同的两块，每次使用其中的一块。当这一块的内存使用完后，就将还存活的对象复制到另一块去，然后再把使用的空间一次清理掉。这样就使每次的内存回收都是对内存区间的一半进行回收。但依然存在下面这些问题：

   - **可用内存变小**：可用内存缩小为原来的一半。
   - **不适合老年代**：如果存活对象数量比较大，复制性能会变得很差。

3. 标记整理（Mark and Compact）

   标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象回收，而是让所有存活的对象向一端移动，然后直接清理掉端边界以外的内存。由于多了整理这一步，因此效率也不高，适合老年代这种垃圾回收频率不是很高的场景。

4. 分代收集

   只是根据对象存活周期的不同将内存分为几块。一般将 Java 堆分为新生代和老年代，这样我们就可以根据各个年代的特点选择合适的垃圾收集算法。比如在新生代中，每次收集都会有大量对象死去，所以可以选择”标记-复制“算法，只需要付出少量对象的复制成本就可以完成每次垃圾收集。而老年代的对象存活几率是比较高的，而且没有额外的空间对它进行分配担保，所以我们必须选择“标记-清除”或“标记-整理”算法进行垃圾收集。

#### 垃圾收集器

根据具体场景选择适合的GC，JDK 默认垃圾收集器（使用 `java -XX:+PrintCommandLineFlags -version` 命令查看）：JDK 8：Parallel Scavenge（新生代）+ Parallel Old（老年代），JDK 9 ~ JDK20: G1

1. Serial：简单高效、单线程、暂停其他工作线程，**新生代采用标记-复制算法，老年代采用标记-整理算法。**

2. ParNew：Serial的多线程版本

3. Parallel Scavenge：Parallel Scavenge 收集器关注点是吞吐量（高效率的利用 CPU）。CMS 等垃圾收集器的关注点更多的是用户线程的停顿时间（提高用户体验）。所谓吞吐量就是 CPU 中用于运行用户代码的时间与 CPU 总消耗时间的比值。

4. Serial Old：**Serial 收集器的老年代版本**，一种用途是在 JDK1.5 以及以前的版本中与 Parallel Scavenge 收集器搭配使用，另一种用途是作为 CMS 收集器的后备方案。

5. Parallel Old：**Parallel Scavenge 收集器的老年代版本**

6. CMS （Concurrent Mark Sweep）：**以获取最短回收停顿时间为目标的收集器。它非常符合在注重用户体验的应用上使用。**

   CMS 收集器是一种 **“标记-清除”算法**实现的，它的运作过程相比于前面几种垃圾收集器来说更加复杂一些。整个过程分为四个步骤：

   - **初始标记：** 暂停所有的其他线程，并记录下直接与 root 相连的对象，速度很快 ；
   - **并发标记：** 同时开启 GC 和用户线程，用一个闭包结构去记录可达对象。但在这个阶段结束，这个闭包结构并不能保证包含当前所有的可达对象。因为用户线程可能会不断的更新引用域，所以 GC 线程无法保证可达性分析的实时性。所以这个算法里会跟踪记录这些发生引用更新的地方。
   - **重新标记：** 重新标记阶段就是为了修正并发标记期间因为用户程序继续运行而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始标记阶段的时间稍长，远远比并发标记阶段时间短
   - **并发清除：** 开启用户线程，同时 GC 线程开始对未标记的区域做清扫。

   主要优点：**并发收集、低停顿**。但是它有下面三个明显的缺点：

   - **对 CPU 资源敏感；**
   - **无法处理浮动垃圾；**
   - **它使用的回收算法-“标记-清除”算法会导致收集结束时会有大量空间碎片产生。**

7. G1 （Garbage First）：**面向服务器的垃圾收集器,主要针对配备多颗处理器及大容量内存的机器. 以极高概率满足 GC 停顿时间要求的同时,还具备高吞吐量性能特征.**

   - **并行与并发**：G1 能充分利用 CPU、多核环境下的硬件优势，使用多个 CPU（CPU 或者 CPU 核心）来缩短 Stop-The-World 停顿时间。部分其他收集器原本需要停顿 Java 线程执行的 GC 动作，G1 收集器仍然可以通过并发的方式让 java 程序继续执行。
   - **分代收集**：虽然 G1 可以不需要其他收集器配合就能独立管理整个 GC 堆，但是还是保留了分代的概念。
   - **空间整合**：与 CMS 的“标记-清除”算法不同，G1 从整体来看是基于“标记-整理”算法实现的收集器；从局部上来看是基于“标记-复制”算法实现的。
   - **可预测的停顿**：这是 G1 相对于 CMS 的另一个大优势，降低停顿时间是 G1 和 CMS 共同的关注点，但 G1 除了追求低停顿外，还能建立可预测的停顿时间模型，能让使用者明确指定在一个长度为 M 毫秒的时间片段内，消耗在垃圾收集上的时间不得超过 N 毫秒。

   G1 收集器的运作大致分为以下几个步骤：

   - **初始标记**
   - **并发标记**
   - **最终标记**
   - **筛选回收**

   **G1 收集器在后台维护了一个优先列表，每次根据允许的收集时间，优先选择回收价值最大的 Region(这也就是它的名字 Garbage-First 的由来)** 。这种使用 Region 划分内存空间以及有优先级的区域回收方式，保证了 G1 收集器在有限时间内可以尽可能高的收集效率（把内存化整为零）。

8. ZGC收集器：标记复制，JDK15+

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

### Java 类加载机制

Java虚拟机把描述类的数据从Class文件加载到内存，并对数据进行校验、转换解析和初始化，最终形成可以被虚拟机直接使用的Java类型，这个过程被称作虚拟机的类加载机制。

类从被加载到虚拟机内存中开始，到卸载出内存为止，**它的整个生命周期包括：加载，验证，准备，解析，初始化,使用,卸载**这7个阶段.其中其中验证、准备、解析3个部分统称为连接.

类加载的三种方式：

1. 命令行启动应用时候由JVM初始化加载
2. 通过Class.forName()方法动态加载
3. 通过ClassLoader.loadClass()方法动态加载

### String

String的特点：不可边，字符串常量池

不可变的原因：

- String的主要成员变量char value[]是private final类型的。
- String被声明为final class，是典型的Immutable类；

不可变的好处：

- 可以缓存 hash 值：因为 String 的 hash 值经常被使用，例如 String 用作 HashMap 的 key。 不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。
- String Pool 的需要：如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。 只有 String 是不可变的，才可能使用 String Pool。
- 安全性：String 经常作为参数，String 不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 String 是可变的，那么在网络连接过程中，String 被改变，改变 String 对象的那一方以为现在连接的是其它主机，而实际情况却不一定是。
- 线程安全：String 不可变性天生具备线程安全，可以在多个线程中安全地使用。

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

- final 用于修饰属性、方法和类, 分别表示属性不能被重新赋值，方法不可被重写，类不可被继承。
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

- **`sleep()` 方法没有释放锁，而 `wait()` 方法释放了锁** 。
- `wait()` 通常被用于线程间交互/通信，`sleep()`通常被用于暂停执行。
- `wait()` 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 `notify()`或者 `notifyAll()` 方法。`sleep()`方法执行完成后，线程会自动苏醒，或者也可以使用 `wait(long timeout)` 超时后线程会自动苏醒。
- `sleep()` 是 `Thread` 类的静态本地方法，`wait()` 则是 `Object` 类的本地方法。为什么这样设计呢？下一个问题就会聊到。

### synchronized vs volatile

Synchronized keyword solves the problem of execution control, it will prevent other threads from obtaining the monitoring lock of the current object, so that the block of code in the current object protected by the synchronized keyword can not be accessed by other threads, and can not be executed concurrently.

The volatile keyword solves the memory visibility problem by making all reads and writes to volatile variables swipe directly to main memory

Volatile essentially tells jvm that the value of the current variable in the register (working memory) is indeterminate and needs to be read from main memory; synchronized locks the current variable so that only the current thread can access it and other threads are blocked.

- volatile can only be used at the variable level; synchronized can be used at the variable, method, and class levels
- volatile only enables variable modification visibility, not atomicity, while synchronized guarantees variable modification visibility and atomicity
- volatile does not cause thread blocking; synchronized may cause thread blocking.

volatile-tagged variables are not optimized by the compiler; synchronized-tagged variables can be optimized by the compiler

`volatile` 关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized` 关键字两者都能保证。

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

### Thread类的start vs run

new 一个 `Thread`，线程进入了新建状态。调用 `start()`方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。 `start()` 会执行线程的相应准备工作，然后自动执行 `run()` 方法的内容，这是真正的多线程工作。 但是，直接执行 `run()` 方法，会把 `run()` 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作。

**总结：调用 `start()` 方法方可启动线程并使线程进入就绪状态，直接执行 `run()` 方法的话不会以多线程的方式执行。**

### 线程池

线程池就是管理一系列线程的资源池。当有任务要处理时，直接从线程池中获取线程来处理，处理完之后线程并不会立即被销毁，而是等待下一个任务。

创建线程池：**通过`ThreadPoolExecutor`构造函数来创建**

```java
public class MyMonitorThread implements Runnable
{
    private ThreadPoolExecutor executor;
    private int seconds;
    private boolean run=true;

    public MyMonitorThread(ThreadPoolExecutor executor, int delay)
    {
        this.executor = executor;
        this.seconds=delay;
    }
    public void shutdown(){
        this.run=false;
    }
    @Override
    public void run()
    {
        while(run){
                System.out.println(
                    String.format("[monitor] [%d/%d] Active: %d, Completed: %d, Task: %d, isShutdown: %s, isTerminated: %s",
                        this.executor.getPoolSize(),
                        this.executor.getCorePoolSize(),
                        this.executor.getActiveCount(),
                        this.executor.getCompletedTaskCount(),
                        this.executor.getTaskCount(),
                        this.executor.isShutdown(),
                        this.executor.isTerminated()));
                try {
                    Thread.sleep(seconds*1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
        }
            
    }
}

//Get the ThreadFactory implementation to use
ThreadFactory threadFactory = Executors.defaultThreadFactory();
//creating the ThreadPoolExecutor
ThreadPoolExecutor executorPool = new ThreadPoolExecutor(2, 4, 10, TimeUnit.SECONDS, new ArrayBlockingQueue<Runnable>(2), threadFactory, rejectionHandler);
//start the monitoring thread
MyMonitorThread monitor = new MyMonitorThread(executorPool, 3);
Thread monitorThread = new Thread(monitor);
```

**`ThreadPoolExecutor` 3 个最重要的参数：**

- **`corePoolSize` :** 任务队列未达到队列容量时，最大可以同时运行的线程数量。
- **`maximumPoolSize` :** 任务队列中存放的任务达到队列容量的时候，当前可以同时运行的线程数量变为最大线程数。
- **`workQueue`:** 新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，新任务就会被存放在队列中。

#### Different types of a thread pool

1. **FixedThreadPool:**In a fixed-size thread pool, the number of threads is fixed at the time of pool creation. 

   ```
   ExecutorService executor = Executors.newFixedThreadPool(5);
   ```

2. **CachedThreadPool:**In a cached thread pool, the number of threads can dynamically increase or decrease based on the workload. 

   ```
   ExecutorService executor = Executors.newCachedThreadPool();
   ```

3. **SingleThreadExecutor:**A single-threaded executor uses only one worker thread. 

   ```
   ExecutorService executor = Executors.newSingleThreadExecutor();
   ```

4. **ScheduledThreadPool:**A scheduled thread pool is suitable for scheduling tasks to run periodically or after a specific delay. 

   ```
   ScheduledExecutorService scheduledExecutor = Executors.newScheduledThreadPool(3)
   ```

### 实现线程安全

**1、使用synchronized关键字**

synchronized关键字可以修饰方法和代码块，它的语义是保证同一段代码同一时间只能有一个线程在执行。

**2、使用volatile关键字**

volatile关键字用来修饰共享变量。它的语义是保证被修饰的变量在被一个线程修改后，都会通知其他线程，其他线程需要操作该变量时会重新获取，这样每个线程在操作该共享变量时获取到的值都是很新的，但是volatile关键字无法保证原子性，怎么来理解这句话呢?在你写的对该变量操作的一行代码，在执行时是拆分成多条字节码指令后执行的，所以对volatile修饰的变量操作时，需要保证该操作具有原子性。有些操作本身就具有原子性，无需额外编码，比如：volatile修饰boolean类型的变量，对该变量的操作是赋值操作。对于不具备原子性的操作，则需要使用某些方法来保证原子性，比如：使用原子类来替代基本数据类型

**3、使用原子类代替基本数据类型**

java提供三种类型的原子类，当某个操作因为不是原子操作导致的线程安全问题的时候，可以使用原子类来替代。比如：多线程环境下执行a++，可以使用AtomicInteger类incrementAndGet()方法实现。相比synchronized，原子类是使用乐观锁来实现线程安全，synchronized使用悲观锁来实现线程安全。

**4、使用ThreadLocal对各个线程进行隔离**

使用ThreadLocal保存当前线程的变量值，这样你想获取该变量的值的时候，获取到的都是本线程的变量值，不会获取到其他线程设置的值。应用场景比如：在过滤器中使用threadlocal保存变量值，需要使用的时候直接取出来即可，threadlocal可以保证你取到的数据就是你之前设置的，因为在一个线程中。

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

当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

HashMap 的结构，是数组+链表+红黑树的结构，

链表长度超过 8 的时候，链表便会转为红黑树，另外，当链表长度小于 6 的时候，会从红黑树转为链表。

![jdk1.8之后的内部结构-HashMap](https://oss.javaguide.cn/github/javaguide/java/collection/jdk1.8_hashmap.png)



#### hash map, how to find index when put

1. Calculate the hash code of the key:
2. Map the hash code to an index:
3. Handle potential collisions:
   - In cases where multiple keys result in the same index (hash collision), the HashMap uses additional data structures to handle collisions, such as linked lists or binary trees.
4. Store the key-value pair:
   - Once the index is determined, the key-value pair is stored at that index within the HashMap's internal array.

### HashMap VS HashTable

**线程是否安全：**`HashMap` 是非线程安全的，`Hashtable` 是线程安全的,因为 `Hashtable` 内部的方法基本都经过`synchronized` 修饰。（如果你要保证线程安全的话就使用 `ConcurrentHashMap` 吧！）

**效率：** 因为线程安全的问题，`HashMap` 要比 `Hashtable` 效率高一点。另外，`Hashtable` 基本被淘汰，不要在代码中使用它；

**对 Null key 和 Null value 的支持：** `HashMap` 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个；Hashtable 不允许有 null 键和 null 值，否则会抛出 `NullPointerException`。

**初始容量大小和每次扩充容量大小的不同：** ① 创建时如果不指定容量初始值，`Hashtable` 默认的初始大小为 11，之后每次扩充，容量变为原来的 2n+1。`HashMap` 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍。② 创建时如果给定了容量初始值，那么 `Hashtable` 会直接使用你给定的大小，而 `HashMap` 会将其扩充为 2 的幂次方大小

#### HashTable VS ConcurrentHashMap

**底层数据结构：** JDK1.7 的 `ConcurrentHashMap` 底层采用 **分段的数组+链表** 实现，JDK1.8 采用的数据结构跟 `HashMap1.8` 的结构一样，数组+链表/红黑二叉树。`Hashtable` 和 JDK1.8 之前的 `HashMap` 的底层数据结构类似都是采用 **数组+链表** 的形式，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；

**实现线程安全的方式（重要）：**

- 在 JDK1.7 的时候，`ConcurrentHashMap` 对整个桶数组进行了分割分段(`Segment`，分段锁)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。
- 到了 JDK1.8 的时候，`ConcurrentHashMap` 已经摒弃了 `Segment` 的概念，而是直接用 `Node` 数组+链表+红黑树的数据结构来实现，并发控制使用 `synchronized` 和 CAS 来操作。（JDK1.6 以后 `synchronized` 锁做了很多优化） 整个看起来就像是优化过且线程安全的 `HashMap`，虽然在 JDK1.8 中还能看到 `Segment` 的数据结构，但是已经简化了属性，只是为了兼容旧版本；
- **`Hashtable`(同一把锁)** :使用 `synchronized` 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。

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

## Design Pattern![Screenshot 2023-11-24 at 12.02.25 PM](/Users/katefu/Library/Application Support/typora-user-images/Screenshot 2023-11-24 at 12.02.25 PM.png)

### Singleton Pattern

This pattern is used when we need to ensure that only one instance of a class is created and that it can be easily accessed by other objects. For example, a logger class in a large application may be a singleton so that only one instance of the logger is used throughout the application.

它的定义就是确保某一个类只有一个实例，并且提供一个全局访问点。

单例模式具备典型的3个特点：1、只有一个实例。 2、自我实例化。 3、提供全局访问点

节约系统资源、提高了系统效率，同时也能够严格控制客户对它的访问。

- 需要频繁创建的一些类，使用单例可以降低系统的内存压力，减少 GC。
- 某类只要求生成一个对象的时候，如一个班中的班长、每个人的身份证号等。

实现

懒汉式

```java
public class Singleton { 
  private static final Singleton instance;
  
  private Singleton () {}
  
  public static synchronized Singleton getInstance() {    
    if (instance == null) {      
      instance = new Singleton();    
    }    

    return instance;  
  }
}

```

懒汉式(双重检测)

```java
public class Singleton { 
  private static Singleton instance;
  
  private Singleton () {}
  
  public static Singleton getInstance() {
    if (instance == null) {
      synchronized(Singleton.class) { // 注意这里是类级别的锁
        if (instance == null) {       // 这里的检测避免多线程并发时多次创建对象
          instance = new Singleton();
        }
      }
    }
    return instance;
  }
}

```

这种实现方式在 **Java 1.4** 及更早的版本中有些问题，就是指令重排序，可能会导致 `Singleton` 对象被 `new` 出来，并且赋值给 `instance` 之后，还没来得及初始化，就被另一个线程使用了。

要解决这个问题，需要给 `instance` 成员变量加上 `volatile` 关键字，从而禁止指令重排序。

而高版本的 **Java** 已在 **JDK** 内部解决了这个问题，所以高版本的 **Java** 不需要关注这个问题。

双重检测单例优点：

- 对象的创建是线程安全的。
- 支持延时加载。
- 获取对象时不需要加锁。

静态内部类

```java
public class Singleton { 
  private Singleton () {}

  private static class SingletonInner {
    private static final Singleton instance = new Singleton();
  }
  
  public static Singleton getInstance() {
    return SingletonInner.instance;
  }
}

```

- 对象的创建是线程安全的。
- 支持延时加载。
- 获取对象时不需要加锁。

### Factory Pattern

This pattern is used when we want to create objects without exposing the creation logic to the client. For example, a document editor might have a factory that creates different types of documents (PDF, Word, etc.) without the client having to know how each document is created.

所谓抽象工厂模式就是提供一个接口，用于创建相关或者依赖对象的家族，而不需要明确指定具体类。他允许客户端使用抽象的接口来创建一组相关的产品，而不需要关系实际产出的具体产品是什么。这样一来，客户就可以从具体的产品中被解耦。它的优点是隔离了具体类的生成，

```java
public class Client {
    public static void main(String[] args) {
    }

    //抽象产品
    public interface Product {
        void show();
    }

    //具体产品：ProductA
    static class ConcreteProduct1 implements Product {
        public void show() {
            System.out.println("具体产品1显示...");
        }
    }

    //具体产品：ProductB
    static class ConcreteProduct2 implements Product {
        public void show() {
            System.out.println("具体产品2显示...");
        }
    }

    final class Const {
        static final int PRODUCT_A = 0;
        static final int PRODUCT_B = 1;
        static final int PRODUCT_C = 2;
    }

    static class SimpleFactory {
        public static Product makeProduct(int kind) {
            switch (kind) {
                case Const.PRODUCT_A:
                    return new ConcreteProduct1();
                case Const.PRODUCT_B:
                    return new ConcreteProduct2();
            }
            return null;
        }
    }
}
```

### 适配器模式

在我们的应用程序中我们可能需要将两个不同接口的类来进行通信，在不修改这两个的前提下我们可能会需要某个中间件来完成这个衔接的过程。这个中间件就是适配器。所谓适配器模式就是将一个类的接口，转换成客户期望的另一个接口。它可以让原本两个不兼容的接口能够无缝完成对接。

作为中间件的适配器将目标类和适配者解耦，增加了类的透明性和可复用性。

1. 目标（Target）接口：当前系统业务所期待的接口，它可以是抽象类或接口。
2. 适配者（Adaptee）类：它是被访问和适配的现存组件库中的组件接口。
3. 适配器（Adapter）类：它是一个转换器，通过继承或引用适配者的对象，把适配者接口转换成目标接口，让客户按目标接口的格式访问适配者。

```java
package adapter;
//目标接口
interface Target
{
    public void request();
}
//适配者接口
class Adaptee
{
    public void specificRequest()
    {       
        System.out.println("适配者中的业务代码被调用！");
    }
}
//类适配器类
class ClassAdapter extends Adaptee implements Target
{
    public void request()
    {
        specificRequest();
    }
}
//客户端代码
public class ClassAdapterTest
{
    public static void main(String[] args)
    {
        System.out.println("类适配器模式测试：");
        Target target = new ClassAdapter();
        target.request();
    }
}
```



1. Observer Pattern: This pattern is used when we want to notify a set of objects when the state of another object changes. For example, in a GUI application, we might want to update the display whenever a user interacts with a widget.
2. Strategy Pattern: This pattern is used when we want to define a family of algorithms, encapsulate each one as an object, and make them interchangeable. For example, a search engine might have multiple algorithms for ranking search results, and the strategy pattern can be used to swap between them easily.

### 装饰模式

指在不改变现有对象结构的情况下，动态地给该对象增加一些职责（即增加其额外功能）的模式

装饰模式主要包含以下角色。

1. 抽象构件（Component）角色：定义一个抽象接口以规范准备接收附加责任的对象。
2. 具体构件（ConcreteComponent）角色：实现抽象构件，通过装饰角色为其添加一些职责。
3. 抽象装饰（Decorator）角色：继承抽象构件，并包含具体构件的实例，可以通过其子类扩展具体构件的功能。
4. 具体装饰（ConcreteDecorator）角色：实现抽象装饰的相关方法，并给具体构件对象添加附加的责任。

```java
public interface Coffee {
    double cost();
    String getDescription();
}


public class SimpleCoffee implements Coffee {

    @Override
    public double cost() {
        return 2.0; // Cost of simple coffee
    }

    @Override
    public String getDescription() {
        return "Simple Coffee";
    }
}

// This decorator will serve as the base class for all coffee decorators:
public abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee decoratedCoffee) {
        this.decoratedCoffee = decoratedCoffee;
    }

    public double cost() {
        return decoratedCoffee.cost();
    }

    public String getDescription() {
        return decoratedCoffee.getDescription();
    }
}


public class MilkDecorator extends CoffeeDecorator {

    public MilkDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public double cost() {
        return super.cost() + 0.5; // Cost of milk
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", with Milk";
    }
}

//  concrete decorator classes that extend CoffeeDecorator to add additional functionality
public class MilkDecorator extends CoffeeDecorator {

    public MilkDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public double cost() {
        return super.cost() + 0.5; // Cost of milk
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", with Milk";
    }
}


public class WhipCreamDecorator extends CoffeeDecorator {

    public WhipCreamDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public double cost() {
        return super.cost() + 0.7; // Cost of whip cream
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", with Whip Cream";
    }
}

// usage
public class Main {
    public static void main(String[] args) {
        // Order a simple coffee
        Coffee coffee = new SimpleCoffee();
        System.out.println("Cost: $" + coffee.cost() + ", Description: " + coffee.getDescription());

        // Decorate the simple coffee with milk
        coffee = new MilkDecorator(coffee);
        System.out.println("Cost: $" + coffee.cost() + ", Description: " + coffee.getDescription());

        // Decorate the coffee with whip cream
        coffee = new WhipCreamDecorator(coffee);
        System.out.println("Cost: $" + coffee.cost() + ", Description: " + coffee.getDescription());
    }
}
```



### 代理模式

**我们使用代理对象来代替对真实对象(real object)的访问，这样就可以在不修改原目标对象的前提下，提供额外的功能操作，扩展目标对象的功能。**

#### 静态代理

#### 动态代理

我们不需要针对每个目标类都单独创建一个代理类，并且也不需要我们必须实现接口，我们可以直接代理实现类，**从 JVM 角度来说，动态代理是在运行时动态生成类字节码，并加载到 JVM 中的。**

**在 Java 动态代理机制中 `InvocationHandler` 接口和 `Proxy` 类是核心。**

`Proxy` 类中使用频率最高的方法是：`newProxyInstance()` ，这个方法主要用来生成一个代理对象。

```Java
public static Object newProxyInstance(ClassLoader loader,
                                      Class<?>[] interfaces,
                                      InvocationHandler h)
    throws IllegalArgumentException
{
    ......
}
```

这个方法一共有 3 个参数：

1. **loader** :类加载器，用于加载代理对象。
2. **interfaces** : 被代理类实现的一些接口；
3. **h** : 实现了 `InvocationHandler` 接口的对象；

要实现动态代理的话，还必须需要实现`InvocationHandler` 来自定义处理逻辑。 当我们的动态代理对象调用一个方法时，这个方法的调用就会被转发到实现`InvocationHandler` 接口类的 `invoke` 方法来调用。

```java
public interface InvocationHandler {

    /**
     * 当你使用代理对象调用方法的时候实际会调用到这个方法
     */
    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}

```

`invoke()` 方法有下面三个参数：

1. **proxy** :动态生成的代理类
2. **method** : 与代理类对象调用的方法相对应
3. **args** : 当前 method 方法的参数

也就是说：**你通过`Proxy` 类的 `newProxyInstance()` 创建的代理对象在调用方法的时候，实际会调用到实现`InvocationHandler` 接口的类的 `invoke()`方法。** 你可以在 `invoke()` 方法中自定义处理逻辑，比如在方法执行前后做什么事情

### Design Principle

#### 单一职责原则

一个类只负责一项职责，如若不然，就应该把类拆分;比如相机,只有拍照的功能

#### 里氏替换原则

**克服继承的缺点**

- 里氏替换原则中，子类可以扩展父类的功能，但不要改变父类原有的功能，对父类的方法尽量不要重写和重载。因为父类代表了定义好的结构，通过这个规范的接口与外界交互，子类不应该随便破坏它。
- 如果对每一个类型为S的对象o1，都有类型为T的对象o2，使得以T定义的所有程序P在所有的对象o1都代替成o2时，程序P的行为没有发生变化，那么类型S是类型T的子类型。
- 只要父类能出现的地方子类就可以出现，而且替换为子类也不会产生任何错误或异常，使用者可能根本就不需要知道是父类还是子类。但是，反过来就不行了，有子类出现的地方，父类未必就能适应。

#### 依赖倒转原则

**Dependence Inversion Principle**

这个是开闭原则的基础，具体内容：面向接口编程。

- 模块间的依赖通过抽象发生，实现类之间不发生直接的依赖关系，其依赖关系是通过接口或抽象类产生的
- 接口或抽象类不依赖于实现类
- 实现类依赖于接口或抽象类

#### **接口隔离原则**

**Interface Segregation Principle**

这个原则的意思是：每个接口中不存在子类用不到却必须实现的方法，如果不然，就要将接口拆分。使用多个隔离的接口，比使用单个接口（多个接口方法集合到一个的接口）要好。

#### **迪米特法则**

**Law of Demeter**

降低耦合

#### **开闭原则**

**Open Close Principle**

每一个原则都在强调的宗旨就是：解耦，单一，高内聚。

开闭原则解读：

- 通过接口或抽象类约束扩展，对扩展进行边界限定，不允许出现在接口或抽象类中不存在的public方法
- 参数类型、引用对象尽量使用接口或者抽象类，而不是实现类
- 抽象层尽量保持稳定，一旦确定即不允许修改

## Spring & Springboot

Spring is a lightweight, non-invasive Inversion of Control (IoC) and Aspect Oriented Programming (AOP) framework.

Spring 旨在简化 J2EE 企业应用程序开发。Spring Boot 旨在简化 Spring 开发（减少配置文件，开箱即用！）

### Spring bean

*In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container.*

Bean 代指的就是那些被 IoC 容器所管理的对象。

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

**IoC（Inversion of Control:控制反转）** 是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。

### JWT

JWT （JSON Web Token） 是目前最流行的跨域认证解决方案，是一种基于 Token 的认证授权机制。在基于 JWT 进行身份验证的的应用程序中，服务器通过 Payload、Header 和 Secret(密钥)创建 JWT 并将 JWT 发送给客户端。客户端接收到 JWT 之后，会将其保存在 Cookie 或者 localStorage 里面，以后客户端发出的所有请求都会携带这个令牌。

JWT 本质上就是一组字串，通过（`.`）切分成三个为 Base64 编码的部分：

- **Header** : 描述 JWT 的元数据，定义了生成签名的算法以及 `Token` 的类型。
- **Payload** : 用来存放实际需要传递的数据
- **Signature（签名）**：服务器通过 Payload、Header 和一个密钥(Secret)使用 Header 里面指定的签名算法（默认是 HMAC SHA256）生成。

## 数据库

### MySQL

#### MySQL 基础架构

![img](https://oss.javaguide.cn/javaguide/13526879-3037b144ed09eb88.png)

- **连接器：** 身份认证和权限相关(登录 MySQL 的时候)。
- **查询缓存：** 执行查询语句的时候，会先查询缓存（MySQL 8.0 版本后移除，因为这个功能不太实用）。
- **分析器：** 没有命中缓存的话，SQL 语句就会经过分析器，分析器说白了就是要先看你的 SQL 语句要干嘛，再检查你的 SQL 语句语法是否正确。
- **优化器：** 按照 MySQL 认为最优的方案去执行。
- **执行器：** 执行语句，然后从存储引擎返回数据。 执行语句之前会先判断是否有权限，如果没有权限的话，就会报错。
- **插件式存储引擎**：主要负责数据的存储和读取，采用的是插件式架构，支持 InnoDB、MyISAM、Memory 等多种存储引擎。

#### 存储引擎InnoDB VS MyISAM

InnoDB 支持行级别的锁粒度，MyISAM 不支持，只支持表级别的锁粒度。

MyISAM 不提供事务支持。InnoDB 提供事务支持，实现了 SQL 标准定义了四个隔离级别。

MyISAM 不支持外键，而 InnoDB 支持。

MyISAM 不支持 MVCC，而 InnoDB 支持。

虽然 MyISAM 引擎和 InnoDB 引擎都是使用 B+Tree 作为索引结构，但是两者的实现方式不太一样。

MyISAM 不支持数据库异常崩溃后的安全恢复，而 InnoDB 支持。

InnoDB 的性能比 MyISAM 更强大。

#### 索引

##### **优点**：

- 使用索引可以大大加快 数据的检索速度（大大减少检索的数据量）, 这也是创建索引的最主要的原因。
- 通过创建唯一性索引，可以保证数据库表中每一行数据的唯一性。

##### **缺点**：

- 创建索引和维护索引需要耗费许多时间。当对表中的数据进行增删改的时候，如果数据有索引，那么索引也需要动态的修改，会降低 SQL 执行效率。
- 索引需要使用物理文件存储，也会耗费一定空间。

##### 分类：

按照底层存储方式角度划分：

- 聚簇索引（聚集索引）：索引结构和数据一起存放的索引，InnoDB 中的主键索引就属于聚簇索引。
- 非聚簇索引（非聚集索引）：索引结构和数据分开存放的索引，二级索引(辅助索引)就属于非聚簇索引。MySQL 的 MyISAM 引擎，不管主键还是非主键，使用的都是非聚簇索引。

按照应用维度划分：

- 主键索引：加速查询 + 列值唯一（不可以有 NULL）+ 表中只有一个。
- 普通索引：仅加速查询。
- 唯一索引：加速查询 + 列值唯一（可以有 NULL）。
- 覆盖索引：一个索引包含（或者说覆盖）所有需要查询的字段的值。
- 联合索引：多列值组成一个索引，专门用于组合搜索，其效率大于索引合并。
- 全文索引：对文本的内容进行分词，进行搜索。目前只有 `CHAR`、`VARCHAR` ，`TEXT` 列上可以创建全文索引。一般不会使用，效率较低，通常使用搜索引擎如 ElasticSearch 代替。



#### 聚簇索引

聚簇索引（聚集索引）：索引结构和数据一起存放的索引，InnoDB 中的主键索引就属于聚簇索引。

非聚簇索引（非聚集索引）：索引结构和数据分开存放的索引，二级索引(辅助索引)就属于非聚簇索引。MySQL 的 MyISAM 引擎，不管主键还是非主键，使用的都是非聚簇索引。

**优点**：

- **查询速度非常快**：聚簇索引的查询速度非常的快，因为整个 B+树本身就是一颗多叉平衡树，叶子节点也都是有序的，定位到索引的节点，就相当于定位到了数据。相比于非聚簇索引， 聚簇索引少了一次读取数据的 IO 操作。
- **对排序查找和范围查找优化**：聚簇索引对于主键的排序查找和范围查找速度非常快。

**缺点**：

- **依赖于有序的数据**：因为 B+树是多路平衡树，如果索引的数据不是有序的，那么就需要在插入时排序，如果数据是整型还好，否则类似于字符串或 UUID 这种又长又难比较的数据，插入或查找的速度肯定比较慢。
- **更新代价大**：如果对索引列的数据被修改时，那么对应的索引也将会被修改，而且聚簇索引的叶子节点还存放着数据，修改代价肯定是较大的，所以对于主键索引来说，主键一般都是不可被修改的。

#### 哈希表

哈希表这么快，**为什么 MySQL 没有使用其作为索引的数据结构呢？** 主要是因为 Hash 索引不支持顺序和范围查询。假如我们要对表中的数据进行排序或者进行范围查询，那 Hash 索引可就不行了。

#### 红黑树

红黑树是一种自平衡二叉查找树，通过在插入和删除节点时进行颜色变换和旋转操作，使得树始终保持平衡状态，它具有以下特点：

1. 每个节点非红即黑；
2. 根节点总是黑色的；
3. 每个叶子节点都是黑色的空节点（NIL 节点）；
4. 如果节点是红色的，则它的子节点必须是黑色的（反之不一定）；
5. 从根节点到叶节点或空子节点的每条路径，必须包含相同数目的黑色节点（即相同的黑色高度）。

和 AVL 树不同的是，红黑树并不追求严格的平衡，而是大致的平衡。正因如此，红黑树的查询效率稍有下降，因为红黑树的平衡性相对较弱，可能会导致树的高度较高，这可能会导致一些数据需要进行多次磁盘 IO 操作才能查询到，这也是 MySQL 没有选择红黑树的主要原因。也正因如此，红黑树的插入和删除操作效率大大提高了，因为红黑树在插入和删除节点时只需进行 O(1) 次数的旋转和变色操作，即可保持基本平衡状态，而不需要像 AVL 树一样进行 O(logn) 次数的旋转操作

#### B+树

- B 树的所有节点既存放键(key) 也存放数据(data)，而 B+树只有叶子节点存放 key 和 data，其他内节点只存放 key。
- B 树的叶子节点都是独立的;B+树的叶子节点有一条引用链指向与它相邻的叶子节点。
- B 树的检索的过程相当于对范围内的每个节点的关键字做二分查找，可能还没有到达叶子节点，检索就结束了。而 B+树的检索效率就很稳定了，任何查找都是从根节点到叶子节点的过程，叶子节点的顺序检索很明显。
- 在 B 树中进行范围查询时，首先找到要查找的下限，然后对 B 树进行中序遍历，直到找到查找的上限；而 B+树的范围查询，只需要对链表进行遍历即可。

综上，B+树与 B 树相比，具备更少的 IO 次数、更稳定的查询效率和更适于范围查询这些优势。

b+树索引只有两种，一个是聚簇索引（主键索引）一个是非聚簇索引（非主键的其他索引，无论是否唯一），聚簇索引索引叶子节点key为主键value包含整行数据，非聚簇索引的叶子节点的key为索引字段value是主键的值。所以当命中普通索引时查询的字段不在索引中，会先获取到主键的值后再去主键的索引树中获取整行数据，这个称之为回表。

#### 为什么B+树适合索引

B 树和 B+ 都是通过多叉树的方式，会将树的高度变矮，所以这两个数据结构非常适合检索存于磁盘中的数据。

- B+ 树的非叶子节点不存放实际的记录数据，仅存放索引，因此数据量相同的情况下，相比存储即存索引又存记录的 B 树，B+树的非叶子节点可以存放更多的索引，因此 B+ 树可以比 B 树更「矮胖」，查询底层节点的磁盘 I/O次数会更少。
- B+ 树有大量的冗余节点（所有非叶子节点都是冗余索引），这些冗余索引让 B+ 树在插入、删除的效率都更高，比如删除根节点的时候，不会像 B 树那样会发生复杂的树的变化；
- B+ 树叶子节点之间用链表连接了起来，有利于范围查询，而 B 树要实现范围查询，因此只能通过树的遍历来完成范围查询，这会涉及多个节点的磁盘 I/O 操作，范围查询效率不如 B+ 树。

#### B+树 VS Hash

B+树索引适用情况：

1. **范围查询**：B+树索引适用于范围查询，因为它保持了顺序，使得范围查询更为高效。
2. **有序性需求**：适用于需要按顺序访问数据的情况，比如排序查询或范围查询。
3. **支持顺序访问**：B+树的数据结构使得范围扫描、顺序访问非常高效。
4. **磁盘存储**：对于磁盘存储的数据库引擎来说，B+树索引通常表现更好，因为它有利于减少磁盘 I/O 操作。

哈希索引适用情况：

1. **等值查询**：哈希索引适用于等值查询，即通过精确匹配来查询特定值。
2. **索引查找速度**：对于单个值的查找，哈希索引可能比B+树更快，因为哈希函数能够直接计算出索引值的位置，而不需要遍历树结构。
3. **内存表**：在内存表中，哈希索引通常表现较好，因为内存访问速度快，哈希函数可以更快地计算出索引位置。

#### 事务隔离

- **READ-UNCOMMITTED(读取未提交)** ：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。
- **READ-COMMITTED(读取已提交)** ：允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生。
- **REPEATABLE-READ(可重复读)** ：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- **SERIALIZABLE(可串行化)** ：最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。

|     隔离级别     | 脏读 | 不可重复读 | 幻读 |
| :--------------: | :--: | :--------: | :--: |
| READ-UNCOMMITTED |  √   |     √      |  √   |
|  READ-COMMITTED  |  ×   |     √      |  √   |
| REPEATABLE-READ  |  ×   |     ×      |  √   |
|   SERIALIZABLE   |  ×   |     ×      |  ×   |

- 原子性（Atomicity）：即不可分割性，事务中的操作要么全不做，要么全做
- 一致性（Consistency）：一个事务在执行前后，数据库都必须处于正确的状态，满足[完整性约束](https://baike.baidu.com/item/数据完整性约束)
- 隔离性（Isolation）：多个事务并发执行时，一个事务的执行不应影响其他事务的执行
- 持久性（Durability）：事务处理完成后，对数据的修改就是永久的，即便系统故障也不会丢失

#### InnoDB自增主键

1. 简化数据管理：

- **唯一性**：自增主键保证了每个记录的唯一性，因为每次插入都会生成一个新的、递增的主键值，几乎排除了重复值的可能性。
- **简化查询**：自增主键能够大大简化对数据库的查询和管理。由于值是连续递增的，检索数据也更高效。

2. 提高性能：

- **插入效率**：自增主键通常能提高插入记录的效率。由于每次插入的新记录都是在当前最大主键值的基础上自增，避免了在主键冲突或插入位置的查找。
- **索引效率**：InnoDB默认将主键作为聚集索引（Clustered Index），因此自增主键有助于提高数据访问的效率，特别是在范围查询或按顺序访问数据时。

3. 降低碎片化：

- **减少页面分裂**：自增主键有助于减少页面分裂，因为新记录总是追加到当前索引页的末尾，而不是插入到中间某个位置。

4. 安全性和性能的平衡：

- 自增主键是一种可靠的主键生成方式，可以平衡安全性和性能。它不会暴露数据的顺序或模式，从而降低了恶意攻击的可能性，同时也提供了较高的插入效率和查询效率。

#### 锁

1. 行级锁：行级锁是粒度最低的锁，发生锁冲突的概率也最低、并发度最高。但是加锁慢、开销大，容易发生死锁现象。MySQL中只有InnoDB支持行级锁，行级锁分为共享锁和排他锁。在MySQL中，行级锁并不是直接锁记录，而是锁索引。索引分为主键索引和非主键索引两种，如果一条sql语句操作了主键索引，MySQL就会锁定这条主键索引；如果一条语句操作了非主键索引，MySQL会先锁定该非主键索引，再锁定相关的主键索引。在UPDATE、DELETE操作时，MySQL不仅锁定WHERE条件扫描过的所有索引记录，而且会锁定相邻的键值，即所谓的next-key locking。
   1. 共享锁： 又称之为读锁，简称S锁，当事务A对数据加上读锁后，其他事务只能对该数据加读锁，不能做任何修改操作，也就是不能添加写锁。
   2. 排他锁：又称之为写锁，简称X锁，当事务对数据加上写锁后，其他事务既不能对该数据添加读写，也不能对该数据添加写锁，写锁与其他锁都是互斥的。MySQL InnoDB引擎默认update,delete,insert都会自动给涉及到的数据加上排他锁，select语句默认不会加任何锁类型。

2. 页级锁：页级锁是 MySQL 中锁定粒度介于行级锁和表级锁中间的一种锁。表级锁速度快，但冲突多，行级冲突少，但速度慢。因此，采取了折衷的页级锁，一次锁定相邻的一组记录。BDB 引擎支持页级锁。
3. 表级锁：当前操作的整张表加锁，最常使用的 MyISAM 与 InnoDB 都支持表级锁定。MySQL 里面表级别的锁有两种：一种是表锁，一种是元数据锁（meta data lock，MDL)。
4. 全局锁：全局锁就是对整个数据库实例加锁。全库逻辑备份。



### Redis

#### 为什么快

Redis 基于内存，内存的访问速度是磁盘的上千倍；

Redis 基于 Reactor 模式设计开发了一套高效的事件处理模型，主要是单线程事件循环和 IO 多路复用（Redis 线程模式后面会详细介绍到）；

Redis 内置了多种优化过后的数据类型/结构实现，性能非常高。

#### 数据类型

**5 种基础数据类型**：String（字符串）、List（列表）、Set（集合）、Hash（散列）、Zset（有序集合）。

**3 种特殊数据类型**：HyperLogLog（基数统计）、Bitmap （位图）、Geospatial (地理位置)。

### NoSQL

|              | SQL 数据库                                                   | NoSQL 数据库                                                 |
| :----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 数据存储模型 | 结构化存储，具有固定行和列的表格                             | 非结构化存储。文档：JSON 文档，键值：键值对，宽列：包含行和动态列的表，图：节点和边 |
| 发展历程     | 开发于 1970 年代，重点是减少数据重复                         | 开发于 2000 年代后期，重点是提升可扩展性，减少大规模数据的存储成本 |
| 例子         | Oracle、MySQL、Microsoft SQL Server、PostgreSQL              | 文档：MongoDB、CouchDB，键值：Redis、DynamoDB，宽列：Cassandra、 HBase，图表：Neo4j、 Amazon Neptune、Giraph |
| ACID 属性    | 提供原子性、一致性、隔离性和持久性 (ACID) 属性               | 通常不支持 ACID 事务，为了可扩展、高性能进行了权衡，少部分支持比如 MongoDB 。不过，MongoDB 对 ACID 事务 的支持和 MySQL 还是有所区别的。 |
| 性能         | 性能通常取决于磁盘子系统。要获得最佳性能，通常需要优化查询、索引和表结构。 | 性能通常由底层硬件集群大小、网络延迟以及调用应用程序来决定。 |
| 扩展         | 垂直（使用性能更强大的服务器进行扩展）、读写分离、分库分表   | 横向（增加服务器的方式横向扩展，通常是基于分片机制）         |
| 用途         | 普通企业级的项目的数据存储                                   | 用途广泛比如图数据库支持分析和遍历连接数据之间的关系、键值数据库可以处理大量数据扩展和极高的状态变化 |
| 查询语法     | 结构化查询语言 (SQL)                                         | 数据访问语法可能因数据库而异                                 |

#### 优势

- **灵活性：** NoSQL 数据库通常提供灵活的架构，以实现更快速、更多的迭代开发。灵活的数据模型使 NoSQL 数据库成为半结构化和非结构化数据的理想之选。
- **可扩展性：** NoSQL 数据库通常被设计为通过使用分布式硬件集群来横向扩展，而不是通过添加昂贵和强大的服务器来纵向扩展。
- **高性能：** NoSQL 数据库针对特定的数据模型和访问模式进行了优化，这与尝试使用关系数据库完成类似功能相比可实现更高的性能。
- **强大的功能：** NoSQL 数据库提供功能强大的 API 和数据类型，专门针对其各自的数据模型而构建。

### MongoDB

#### SQL对照

| SQL                      | MongoDB                         |
| ------------------------ | ------------------------------- |
| 表（Table）              | 集合（Collection）              |
| 行（Row）                | 文档（Document）                |
| 列（Col）                | 字段（Field）                   |
| 主键（Primary Key）      | 对象 ID（Objectid）             |
| 索引（Index）            | 索引（Index）                   |
| 嵌套表（Embedded Table） | 嵌入式文档（Embedded Document） |
| 数组（Array）            | 数组（Array）                   |



## 算法

### LRU

LRU的设计原理就是，当数据在最近一段时间经常被访问，那么它在以后也会经常被访问。这就意味着，如果经常访问的数据，我们需要然其能够快速命中，而不常访问的数据，我们在容量超出限制内，要将其淘汰。LRU 缓存算法的核心数据结构就是哈希链表，双向链表和哈希表的结合体。

![img](https://s2.51cto.com/oss/202010/30/e16c0b0c0dd665bbbd7c9fba16352ed1.png)

Implementation in Java

**查找和更新时间复杂度为O(1),空间复杂度为O(N)**。

- Cache will have a fixed size.
- We need to store the order of cache access, so we will know which cache was least accessed. We will use **Linkedlist** for this.
- Typically cache comes in <key, value> pair and we should store cache data in an efficient manner, so we will be using **Hashmap** to store the cache data.

```java
class CacheEntity {
  int key;
  int value;
  
  public CacheEntity(int key, int value) {
    this.key = key;
    this.value = value;
  }
}

class LRUCache {
  private int capacity; 
  
  private LinkedList<CacheEntity> linkedList;
  
  private Map<Integer, CacheEntity> hashMap;

  public LRUCache(int capacity) {
    this.capacity = capacity;
    this.linkedList = new LinkedList<CacheEntity>();
    this.hashMap = new HashMap<Integer, CacheEntity>();
  }

  public int get(int key) {
    CacheEntity entity = this.hashMap.get(key);
    if (entity == null) return -1;
    
    moveToHead(entity);
    return entity.value;
  }

  public void put(int key, int value) {
    CacheEntity entity = this.hashMap.get(key);
    
    if (entity != null) {
      entity.value = value;
      moveToHead(entity);
      return;
    }
    
    if (this.hashMap.size() >= this.capacity) evictEntity();  
    
    entity = new CacheEntity(key, value); 
    this.linkedList.addFirst(entity);
    this.hashMap.put(key, entity);
  }
  
  private void moveToHead(CacheEntity entity) {
    this.linkedList.remove(entity);
    this.linkedList.addFirst(entity);
  }
  
  private void evictEntity() {
    CacheEntity entity = this.linkedList.removeLast();
    this.hashMap.remove(entity.key);
  }
}
```

## 智力

1. 有一栋100层高的大楼，给你两个完全相同的玻璃球。假设从某一层开始，丢下玻璃球会摔碎。那么怎么利用手中的两个球，用什么最优策略知道这个临界的层是第几层？？

   ```
   第一次扔k层 ，则次数time=1,第二次，如果破了,要试从1到k-1层,此时需要sumTime=time+k-1=k 次；  
   
   如果没破，还要从2k层扔，则次数为time=2;如果破了，还需要扔k+1到2k-1层，再加上前面扔的两次time=2, 即 sumTime=time+k-2=k。还是k 次；
   
   注意每多扔一次 少测试一层。次数却多一次。且实际只要能测到n-1层就够了
   
   那么假设扔k次能知道临界点，共有n层楼.
    
   
   以此类推如果满足 k+（k-1）+（k-2）+（k-3）+（k-4）+....+（k-k+1) >=n-1 。
   
   可以化简得到：k(k-1)>=2(n-1)
   
   这里，n=100 所以 解得k=14。
   ```

   

2. 海量数据处理 - 10亿个数中找出最大的10000个数（top K问题）

   将1亿个数据分成100份，每份100万个数据，找到每份数据中最大的10000个，最后在剩下的100*10000个数据里面找出最大的10000个。如果100万数据选择足够理想，那么可以过滤掉1亿数据里面99%的数据。100万个数据里面查找最大的10000个数据的方法如下：用快速排序的方法，将数据分为2堆，如果大的那堆个数N大于10000个，继续对大堆快速排序一次分成2堆，如果大的那堆个数N大于10000个，继续对大堆快速排序一次分成2堆，如果大堆个数N小于10000个，就在小的那堆里面快速排序一次，找第10000-n大的数字；递归以上过程，就可以找到第1w大的数。参考上面的找出第1w大数字，就可以类似的方法找到前10000大数字了。此种方法需要每次的内存空间为10^6*4=4MB，一共需要101次这样的比较。

   第五种方法采用最小堆。首先读入前10000个数来创建大小为10000的最小堆，建堆的时间复杂度为O（mlogm）（m为数组的大小即为10000），然后遍历后续的数字，并于堆顶（最小）数字进行比较。如果比最小的数小，则继续读取后续数字；如果比堆顶数字大，则替换堆顶元素并重新调整堆为最小堆。整个过程直至1亿个数全部遍历完为止。然后按照中序遍历的方式输出当前堆中的所有10000个数字。该算法的时间复杂度为O（nmlogm），空间复杂度是10000（常数）。

3. 有 1000 个一模一样的瓶子，其中有 999 瓶是普通的水，有一瓶是毒药。任何喝下毒药的生物都会在一星期之后死亡。现在，你只有 10 只小白鼠和一星期的时间，如何检验出哪个瓶子里有毒药？

   根据2^10=1024，所以10个老鼠可以确定1000个瓶子具体哪个瓶子有毒。具体实现跟3个老鼠确定8个瓶子原理一样。

   000=0

   001=1

   010=2

   011=3

   100=4

   101=5

   110=6

   111=7

   一位表示一个老鼠，0-7表示8个瓶子。也就是分别将1、3、5、7号瓶子的药混起来给老鼠1吃，2、3、6、7号瓶子的药混起来给老鼠2吃，4、5、6、7号瓶子的药混起来给老鼠3吃，哪个老鼠死了，相应的位标为1。如老鼠1死了、老鼠2没死、老鼠3死了，那么就是101=5号瓶子有毒。

   同样道理10个老鼠可以确定1000个瓶子
