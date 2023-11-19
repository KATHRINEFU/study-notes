# Java Study Notes

## Multi threading

### Concurrent HashMap

HashTable 和SynchronizedMap 都属于同步容器，这些类实现线程安全的方式是：将它们的状态封装起来，对每个公有方法的同步使得**每次只有一个**线程能访问容器的状态。以这种所有访问都串行化的方式，同步容器保证了线程安全性，但是这种方法的代价是严重降低并发行（想象下，所有只有读线程的情况），当多个线程竞争容器的锁时，吞吐量将严重降低。

Java 5.0 提供了并发容器来改进同步容器的性能。并发容器可以使多个线程并发的访问容器。通过并发容器来替代同步容器，可以极大地提高伸缩性并降低风险。

ConcurrentHashMap 就是一种同步容器，它采用了**分段锁（Lock Striping）**机制增加并发行。在这种机制下：

1. 任意数量的读线程可以并发地访问 Map
2. 读线程和写线程可以并发的访问 Map
3. **一定数量**的线程可以并发修改 Map

#### 数据结构

###### JDK 1.7

在 JDK 1.7 中，ConcurrentHashMap 内部维护了一个 segment 数组，每一个 Segment 相当于类似于另外一个 HashMap。每个 Segment 维护一个 HashEntry 数组，HashEntry 采用了链表结构。

所以访问一个 Entry 需要进行两次哈希，第一次哈希找到 Segment，第二次哈希是在 table 中找到链表。分段锁加在 Segment 上，也就是说不同的 Segment 可以并发操作，两条写线程所要更改的数据第一次哈希到同一个 Segment 时，线程才会同步。

在 JDK 1.8 中，对 ConcurrentHashMap 进行了两个地方的优化:

1. 取消了 Segment 结构，直接采用transient volatile HashEntry<K,V>[] table保存数据，采用 table 数组元素作为锁，从而实现了对每一行数据进行加锁，进一步减少并发冲突的概率。
2. 将原先table数组＋单向链表的数据结构，变更为table数组＋单向链表＋红黑树的结构。虽然ConcurrentHashMap类默认的加载因子为0.75，但是在数据量过大或者运气不佳的情况下，还是会存在一些队列长度过长的情况，对于个数超过8(默认值)的链表，jdk1.8中采用了红黑树的结构，那么查询的时间复杂度可以降低到O(logN)，可以改进性能。

就数据结构上来看，JDK 1.8 下的 ConcurrentHashMap 和 普通的 HashMap 更加相近，但是前者通过对 table 元素的加锁，保证多个 put 线程可以并发访问。

> https://www.jianshu.com/p/858d86118030