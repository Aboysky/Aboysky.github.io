# Java并发基本理论

# Java并发

## 线程

### 线程和进程的区别

### 创建线程的方式

- 其实新建线程的方式就只有一种，就是Thread类的Start0方法，但是这个方法是一个private native方法。并且这个方法也只在start方法中被调用到了。而start方法他的执行逻辑是先去判断这个线程有没有执行过start方法，有一个状态位，如果没有，就去调用start0方法。start0方法是一个本地方法，他在里面去回调run方法。 所以创建线程的方法只有这个start0方法。
- 继承Thread类重写run方法
- 实现Runnable接口
- Callable

	- Future接口定义一个规则，就是可以取值和取消任务，然后就是RunnableFuture，它实现了Runable和Future，最后就是FutureTask，它实现了RunnableFuture，一般Callable其实还是去通过Thread或者线程池的submit去提交一个任务

### 线程的状态转换

- NEW、RUNNABLE、WAIT、TIME_WAIT、BLOCKED、TERMINATED
- wait、LockSupport.park()

### 基本API

- yield
- sleep

	- TimeUnit.sleep

- join
- start
- run

### 怎么去停止线程？

- 1. 不推荐的方式 stop 、suspend
- 2. 中断 interrupt

	- isInterrupt()方法

		- 重载，如果参数设置为true，则会清除中断标记，而interrupted方法则是调用了这个方法

	- interrupted() 方法，会还原中断标记

- 3. 标志对象

	- volatile

## 线程同步的基本方法

### CAS(Compare and swap)

- 比较并交换，AQS和偏向锁，原子类，类的初始化等等都用的是这个
- 基于cmpxchg指令
- 缺点

	- ABA问题，增加版本号
	- 空自旋等待成功

### volatile

- 功能

	- 保证可见性
	- 禁止重排序

- 实现

	- 可见性的保证

		- jvm层面 ，use前read，write后store
		- cpu层面

		  因为volatile关键字主要是去实现共享变量的可见性，在cpu执行中，cpu并不直接向内存交换数据，而是和缓存行，也就是cpu拿和修改数据都是在缓存行里面。这个时候就会有一个问题，不同cpu中的L1,L2缓存中可能不一样。而volatile关键字在底层主要就是在指令前加了lock前缀，而lock前缀最关键的作用就是会引起处理器缓存回写到内存。一个处理器的缓存回写到内存会导致其他处理器的缓存无效。处理器使用嗅探技术保证它的内部缓存、系统内存和其他处理器的
		  缓存的数据在总线上保持一致。
		  

	- 禁止重排序的实现

		- 内存屏障
		- happens-before

		  一个volatile对象的写，happens-before任何后续对这个volatile对象的读
		  
		  

### syncronized

- 功能

	- 原子性
	- 可见性
	- 可重入性

- 实现

	- 普通同步方法
	- 静态同步方法
	- 同步代码块

		- monitorenter
		- monitorexit

- 原理

	- 不管是monitorenter这些指令还是ACC_SYNCRONOUS标志，他们的目的都只是一个，判别这是一个同步方法，去要去控制线程同步。此时他们都会去找此对象关联的monitor对象，尝试去acquire这个监视器。如果拿到了就进去，如果没有拿到，则进入这个monitor负责的阻塞队列中。
	- 前面的也不一定准备，因为jdk1.6的时候对这个做了优化，增加了偏向锁，轻量级锁。所以你进去的时候会先尝试偏向锁，然后再试后面的。锁升级

- 锁升级

	- 无锁
	- 偏向锁

		- 大部分时候都是一个线程在访问。。。只需要比较线程id是否一致，。。
		- 先看偏向锁指向的线程id是否存活，不存活的话直接cas改掉线程id，获得锁，存活则看它是否需要继续争抢锁，如果争抢，升级为轻量级锁，或者判断epoch的大小是否超过40，如果超过，也升级为轻量级锁。

	- 轻量级锁

		- 轻量级锁的主要思想就是大部分的锁立马就可以拿到，如果直接去阻塞然后唤醒，因为需要转换内核和用户态，那效率就很低，所以他就自旋等待，一般是10次，如果没有拿到，升级为重量锁，或者自旋等待时又有一个线程来抢，则立马升级为重量锁。jdk后续将自旋锁改为了自适应自旋锁，也就是等待时间可以参考前面是否有线程自旋等待成功。如果有，就等久一点

	- 重量级锁

### Lock

- ReentrantLock
- ReentrantRedaWriteLock

## Java内存模型

### 概述

Java它想建立一个抽象的高级的内存模型，以去抵消掉不同的底层所带来的影响。方便程序员的编写。他不是一个具体的实现，他更多的是一种规范，去声明和指定以及保证多个线程正确的同步执行。
它对于多线程，每一个线程都有一个自己的本地内存，这个本地存储是每个线程独享的，然后他们有一个共享内存，一般就是堆。而本地内存和主内存之间的数据交换就是由JMM控制。


### 原子性

### 有序性

- 双重检查锁定

	- 由于对象new时分配空间，初始化，将地址空间传递给对象，二三步的执行顺序并不一定就是这样，所以可能导致访问到未被正确初始化的对象。
	- 解决办法

		- volatile
		- 借助于类初始化的锁机制，用静态内部类初始化一个

### 可见性

### happens-before原则

程序顺序原则，即在一个线程内必须保证语义串行性，也就是说按照代码顺序执行。
锁规则 解锁(unlock)操作必然发生在后续的同一个锁的加锁(lock)之前，也就是说，如果对于一个锁解锁后，再加锁，那么加锁的动作必须在解锁动作之后(同一个锁)。
volatile规则 volatile变量的写，先发生于读，这保证了volatile变量的可见性，简单的理解就是，volatile变量在每次被线程访问时，都强迫从主内存中读该变量的值，而当该变量发生变化时，又会强迫将最新的值刷新到主内存，任何时刻，不同的线程总是能够看到该变量的最新值。
线程启动规则 线程的start()方法先于它的每一个动作，即如果线程A在执行线程B的start方法之前修改了共享变量的值，那么当线程B执行start方法时，线程A对共享变量的修改对线程B可见
传递性 A先于B ，B先于C 那么A必然先于C
线程终止规则 线程的所有操作先于线程的终结，Thread.join()方法的作用是等待当前执行的线程终止。假设在线程B终止之前，修改了共享变量，线程A从线程B的join方法成功返回后，线程B对共享变量的修改将对线程A可见。
线程中断规则 对线程 interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过Thread.interrupted()方法检测线程是否中断。
对象终结规则 对象的构造函数执行，结束先于finalize()方法


### final内存语义

- 禁止把final域的写重排序到构造函数之外，可以保证在对象引用为任意线程可见之前,对象的final域已经被正确初始化过了
- 初次读对象引用与初次读该对象包含的final
域,

## 锁

### Lock接口

/*
 * ORACLE PROPRIETARY/CONFIDENTIAL. Use is subject to license terms.
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 */

/*
 *
 *
 *
 *
 *
 * Written by Doug Lea with assistance from members of JCP JSR-166
 * Expert Group and released to the public domain, as explained at
 * http://creativecommons.org/publicdomain/zero/1.0/
 */

package java.util.concurrent.locks;

import java.util.concurrent.TimeUnit;

/**
 * {@code Lock} implementations provide more extensive locking
 * operations than can be obtained using {@code synchronized} methods
 * and statements.  They allow more flexible structuring, may have
 * quite different properties, and may support multiple associated
 * {@link Condition} objects.
 *
 * <p>A lock is a tool for controlling access to a shared resource by
 * multiple threads. Commonly, a lock provides exclusive access to a
 * shared resource: only one thread at a time can acquire the lock and
 * all access to the shared resource requires that the lock be
 * acquired first. However, some locks may allow concurrent access to
 * a shared resource, such as the read lock of a {@link ReadWriteLock}.
 *
 * <p>The use of {@code synchronized} methods or statements provides
 * access to the implicit monitor lock associated with every object, but
 * forces all lock acquisition and release to occur in a block-structured way:
 * when multiple locks are acquired they must be released in the opposite
 * order, and all locks must be released in the same lexical scope in which
 * they were acquired.
 *
 * <p>While the scoping mechanism for {@code synchronized} methods
 * and statements makes it much easier to program with monitor locks,
 * and helps avoid many common programming errors involving locks,
 * there are occasions where you need to work with locks in a more
 * flexible way. For example, some algorithms for traversing
 * concurrently accessed data structures require the use of
 * &quot;hand-over-hand&quot; or &quot;chain locking&quot;: you
 * acquire the lock of node A, then node B, then release A and acquire
 * C, then release B and acquire D and so on.  Implementations of the
 * {@code Lock} interface enable the use of such techniques by
 * allowing a lock to be acquired and released in different scopes,
 * and allowing multiple locks to be acquired and released in any
 * order.
 *
 * <p>With this increased flexibility comes additional
 * responsibility. The absence of block-structured locking removes the
 * automatic release of locks that occurs with {@code synchronized}
 * methods and statements. In most cases, the following idiom
 * should be used:
 *
 * <pre> {@code
 * Lock l = ...;
 * l.lock();
 * try {
 *   // access the resource protected by this lock
 * } finally {
 *   l.unlock();
 * }}</pre>
 *
 * When locking and unlocking occur in different scopes, care must be
 * taken to ensure that all code that is executed while the lock is
 * held is protected by try-finally or try-catch to ensure that the
 * lock is released when necessary.
 *
 * <p>{@code Lock} implementations provide additional functionality
 * over the use of {@code synchronized} methods and statements by
 * providing a non-blocking attempt to acquire a lock ({@link
 * #tryLock()}), an attempt to acquire the lock that can be
 * interrupted ({@link #lockInterruptibly}, and an attempt to acquire
 * the lock that can timeout ({@link #tryLock(long, TimeUnit)}).
 *
 * <p>A {@code Lock} class can also provide behavior and semantics
 * that is quite different from that of the implicit monitor lock,
 * such as guaranteed ordering, non-reentrant usage, or deadlock
 * detection. If an implementation provides such specialized semantics
 * then the implementation must document those semantics.
 *
 * <p>Note that {@code Lock} instances are just normal objects and can
 * themselves be used as the target in a {@code synchronized} statement.
 * Acquiring the
 * monitor lock of a {@code Lock} instance has no specified relationship
 * with invoking any of the {@link #lock} methods of that instance.
 * It is recommended that to avoid confusion you never use {@code Lock}
 * instances in this way, except within their own implementation.
 *
 * <p>Except where noted, passing a {@code null} value for any
 * parameter will result in a {@link NullPointerException} being
 * thrown.
 *
 * <h3>Memory Synchronization</h3>
 *
 * <p>All {@code Lock} implementations <em>must</em> enforce the same
 * memory synchronization semantics as provided by the built-in monitor
 * lock, as described in
 * <a href="https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.4">
 * Chapter 17 of
 * <cite>The Java&trade; Language Specification</cite></a>:
 * <ul>
 * <li>A successful {@code lock} operation has the same memory
 * synchronization effects as a successful <em>Lock</em> action.
 * <li>A successful {@code unlock} operation has the same
 * memory synchronization effects as a successful <em>Unlock</em> action.
 * </ul>
 *
 * Unsuccessful locking and unlocking operations, and reentrant
 * locking/unlocking operations, do not require any memory
 * synchronization effects.
 *
 * <h3>Implementation Considerations</h3>
 *
 * <p>The three forms of lock acquisition (interruptible,
 * non-interruptible, and timed) may differ in their performance
 * characteristics, ordering guarantees, or other implementation
 * qualities.  Further, the ability to interrupt the <em>ongoing</em>
 * acquisition of a lock may not be available in a given {@code Lock}
 * class.  Consequently, an implementation is not required to define
 * exactly the same guarantees or semantics for all three forms of
 * lock acquisition, nor is it required to support interruption of an
 * ongoing lock acquisition.  An implementation is required to clearly
 * document the semantics and guarantees provided by each of the
 * locking methods. It must also obey the interruption semantics as
 * defined in this interface, to the extent that interruption of lock
 * acquisition is supported: which is either totally, or only on
 * method entry.
 *
 * <p>As interruption generally implies cancellation, and checks for
 * interruption are often infrequent, an implementation can favor responding
 * to an interrupt over normal method return. This is true even if it can be
 * shown that the interrupt occurred after another action may have unblocked
 * the thread. An implementation should document this behavior.
 *
 * @see ReentrantLock
 * @see Condition
 * @see ReadWriteLock
 *
 * @since 1.5
 * @author Doug Lea
 */
public interface Lock {

    /**
     * Acquires the lock.
     *
     * <p>If the lock is not available then the current thread becomes
     * disabled for thread scheduling purposes and lies dormant until the
     * lock has been acquired.
     *
     * <p><b>Implementation Considerations</b>
     *
     * <p>A {@code Lock} implementation may be able to detect erroneous use
     * of the lock, such as an invocation that would cause deadlock, and
     * may throw an (unchecked) exception in such circumstances.  The
     * circumstances and the exception type must be documented by that
     * {@code Lock} implementation.
     */
    void lock();

    /**
     * Acquires the lock unless the current thread is
     * {@linkplain Thread#interrupt interrupted}.
     *
     * <p>Acquires the lock if it is available and returns immediately.
     *
     * <p>If the lock is not available then the current thread becomes
     * disabled for thread scheduling purposes and lies dormant until
     * one of two things happens:
     *
     * <ul>
     * <li>The lock is acquired by the current thread; or
     * <li>Some other thread {@linkplain Thread#interrupt interrupts} the
     * current thread, and interruption of lock acquisition is supported.
     * </ul>
     *
     * <p>If the current thread:
     * <ul>
     * <li>has its interrupted status set on entry to this method; or
     * <li>is {@linkplain Thread#interrupt interrupted} while acquiring the
     * lock, and interruption of lock acquisition is supported,
     * </ul>
     * then {@link InterruptedException} is thrown and the current thread's
     * interrupted status is cleared.
     *
     * <p><b>Implementation Considerations</b>
     *
     * <p>The ability to interrupt a lock acquisition in some
     * implementations may not be possible, and if possible may be an
     * expensive operation.  The programmer should be aware that this
     * may be the case. An implementation should document when this is
     * the case.
     *
     * <p>An implementation can favor responding to an interrupt over
     * normal method return.
     *
     * <p>A {@code Lock} implementation may be able to detect
     * erroneous use of the lock, such as an invocation that would
     * cause deadlock, and may throw an (unchecked) exception in such
     * circumstances.  The circumstances and the exception type must
     * be documented by that {@code Lock} implementation.
     *
     * @throws InterruptedException if the current thread is
     *         interrupted while acquiring the lock (and interruption
     *         of lock acquisition is supported)
     */
    void lockInterruptibly() throws InterruptedException;

    /**
     * Acquires the lock only if it is free at the time of invocation.
     *
     * <p>Acquires the lock if it is available and returns immediately
     * with the value {@code true}.
     * If the lock is not available then this method will return
     * immediately with the value {@code false}.
     *
     * <p>A typical usage idiom for this method would be:
     * <pre> {@code
     * Lock lock = ...;
     * if (lock.tryLock()) {
     *   try {
     *     // manipulate protected state
     *   } finally {
     *     lock.unlock();
     *   }
     * } else {
     *   // perform alternative actions
     * }}</pre>
     *
     * This usage ensures that the lock is unlocked if it was acquired, and
     * doesn't try to unlock if the lock was not acquired.
     *
     * @return {@code true} if the lock was acquired and
     *         {@code false} otherwise
     */
    boolean tryLock();

    /**
     * Acquires the lock if it is free within the given waiting time and the
     * current thread has not been {@linkplain Thread#interrupt interrupted}.
     *
     * <p>If the lock is available this method returns immediately
     * with the value {@code true}.
     * If the lock is not available then
     * the current thread becomes disabled for thread scheduling
     * purposes and lies dormant until one of three things happens:
     * <ul>
     * <li>The lock is acquired by the current thread; or
     * <li>Some other thread {@linkplain Thread#interrupt interrupts} the
     * current thread, and interruption of lock acquisition is supported; or
     * <li>The specified waiting time elapses
     * </ul>
     *
     * <p>If the lock is acquired then the value {@code true} is returned.
     *
     * <p>If the current thread:
     * <ul>
     * <li>has its interrupted status set on entry to this method; or
     * <li>is {@linkplain Thread#interrupt interrupted} while acquiring
     * the lock, and interruption of lock acquisition is supported,
     * </ul>
     * then {@link InterruptedException} is thrown and the current thread's
     * interrupted status is cleared.
     *
     * <p>If the specified waiting time elapses then the value {@code false}
     * is returned.
     * If the time is
     * less than or equal to zero, the method will not wait at all.
     *
     * <p><b>Implementation Considerations</b>
     *
     * <p>The ability to interrupt a lock acquisition in some implementations
     * may not be possible, and if possible may
     * be an expensive operation.
     * The programmer should be aware that this may be the case. An
     * implementation should document when this is the case.
     *
     * <p>An implementation can favor responding to an interrupt over normal
     * method return, or reporting a timeout.
     *
     * <p>A {@code Lock} implementation may be able to detect
     * erroneous use of the lock, such as an invocation that would cause
     * deadlock, and may throw an (unchecked) exception in such circumstances.
     * The circumstances and the exception type must be documented by that
     * {@code Lock} implementation.
     *
     * @param time the maximum time to wait for the lock
     * @param unit the time unit of the {@code time} argument
     * @return {@code true} if the lock was acquired and {@code false}
     *         if the waiting time elapsed before the lock was acquired
     *
     * @throws InterruptedException if the current thread is interrupted
     *         while acquiring the lock (and interruption of lock
     *         acquisition is supported)
     */
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;

    /**
     * Releases the lock.
     *
     * <p><b>Implementation Considerations</b>
     *
     * <p>A {@code Lock} implementation will usually impose
     * restrictions on which thread can release a lock (typically only the
     * holder of the lock can release it) and may throw
     * an (unchecked) exception if the restriction is violated.
     * Any restrictions and the exception
     * type must be documented by that {@code Lock} implementation.
     */
    void unlock();

    /**
     * Returns a new {@link Condition} instance that is bound to this
     * {@code Lock} instance.
     *
     * <p>Before waiting on the condition the lock must be held by the
     * current thread.
     * A call to {@link Condition#await()} will atomically release the lock
     * before waiting and re-acquire the lock before the wait returns.
     *
     * <p><b>Implementation Considerations</b>
     *
     * <p>The exact operation of the {@link Condition} instance depends on
     * the {@code Lock} implementation and must be documented by that
     * implementation.
     *
     * @return A new {@link Condition} instance for this {@code Lock} instance
     * @throws UnsupportedOperationException if this {@code Lock}
     *         implementation does not support conditions
     */
    Condition newCondition();
}



- 显示的获取锁和释放锁
- 可中断的获取锁
- 可超时的获取锁
- 基本API

	- 

### 队列同步器AbstractQueueSynchronizer

- 获取状态的方法

	- getState
	- SetState
	- CompareAndSet

- 需要重写的方法

	- 

- 提供的模板方法

	- 

- 同步队列

	- Node
	- FIFO的双向队列，只有他的前驱节点为头节点才有资格去尝试获取锁，如果获取到之后，将自己设置为头节点，然后将原头节点的next指针设置为空。

- 独占式获取锁

  public final void acquire(int arg) {
      if (!tryAcquire(arg) &&
          acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
          selfInterrupt();
  }
  将节点添加进同步队列
  private Node addWaiter(Node mode) {
      Node node = new Node(mode);
  
      for (;;) {
          Node oldTail = tail;
          if (oldTail != null) {
              node.setPrevRelaxed(oldTail);
              if (compareAndSetTail(oldTail, node)) {
                  oldTail.next = node;
                  return node;
              }
          } else {
              initializeSyncQueue();
          }
      }
  }
  在队列中的节点不断的获取锁
  final boolean acquireQueued(final Node node, int arg) {
      boolean interrupted = false;
      try {
          for (;;) {
              // 获取前驱节点
              final Node p = node.predecessor();
              if (p == head && tryAcquire(arg)) {
                  setHead(node);
                  p.next = null; // help GC
                  return interrupted;
              }
              if (shouldParkAfterFailedAcquire(p, node))
                  interrupted |= parkAndCheckInterrupt();
          }
      } catch (Throwable t) {
          cancelAcquire(node);
          if (interrupted)
              selfInterrupt();
          throw t;
      }
  }
  
  

- 独占式释放锁

  public final boolean release(int arg) {
      if (tryRelease(arg)) {
          Node h = head;
          if (h != null && h.waitStatus != 0)
              unparkSuccessor(h);
          return true;
      }
      return false;
  }
  
  private void unparkSuccessor(Node node) {
      /*
       * If status is negative (i.e., possibly needing signal) try
       * to clear in anticipation of signalling.  It is OK if this
       * fails or if status is changed by waiting thread.
       */
      int ws = node.waitStatus;
      if (ws < 0)
          node.compareAndSetWaitStatus(ws, 0);
  
      /*
       * Thread to unpark is held in successor, which is normally
       * just the next node.  But if cancelled or apparently null,
       * traverse backwards from tail to find the actual
       * non-cancelled successor.
       */
      Node s = node.next;
      if (s == null || s.waitStatus > 0) {
          s = null;
          //倒序遍历，找到最先的一个waitStatus小于等于0的node
          for (Node p = tail; p != node && p != null; p = p.prev)
              if (p.waitStatus <= 0)
                  s = p;
      }
      if (s != null)
          LockSupport.unpark(s.thread);
  }
  为什么需要倒序遍历寻找？
         因为addWaiter时的操作并不是原子操作，可能出现前置指针好了，但是后指针没有指成功。并且canceled也是将后指针指向了自己，所以倒序遍历寻找最好
  
  
  

### ReentrantLock

- 可重入锁
- 公平和非公平锁

  ReentrantLock 分为公平锁和非公平锁，可以通过构造方法来指定具体类型：
  
  public ReentrantLock() {
      sync = new NonfairSync();
  }
  
  public ReentrantLock(boolean fair) {
      sync = fair ? new FairSync() : new NonfairSync();
  }
  默认一般使用非公平锁，它的效率和吞吐量都比公平锁高的多(后面会分析具体原因)。
  
  

### ReentrantReadWriteLock

- 
- 同步状态

	- 前16位标识读，后16位标识写

- 锁降级

### LockSupport类

- 

### Condition接口

- 类似于Object对象的wait/notify方法
- 
- 

### ConditionObject

- 是aqs下的一个内部类
- 等待队列

	- FIFO的队列
	- 

- 同步队列入等待队列

	- 

- 从等待队列移入同步队列

	- 

## 并发容器

### ConcurrentHashMap

- jdk1.7

	- segment数组加上entry数组，segment实现的时ReentrantLock，来实现分段锁

- jdk1.8

	- Node

	  static class Node<K,V> implements Map.Entry<K,V> {
	      final int hash;
	      final K key;
	      volatile V val;
	      volatile Node<K,V> next;
	  
	  

	- put

	  public V put(K key, V value) {
	      return putVal(key, value, false);
	  }
	  
	  final V putVal(K key, V value, boolean onlyIfAbsent) {
	      // key value都不能为空
	      if (key == null || value == null) throw new NullPointerException();
	      int hash = spread(key.hashCode());
	      int binCount = 0;
	      for (Node<K,V>[] tab = table;;) {
	          Node<K,V> f; int n, i, fh; K fk; V fv;
	          if (tab == null || (n = tab.length) == 0)
	              // 第一次插入的时候初始化
	              tab = initTable();
	           // 如果这个node没有值的话，直接插入进去
	          else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
	              if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value)))
	                  break;                   // no lock when adding to empty bin
	          }
	          // hash位moved说明它正在扩容，然后去帮助扩容
	          else if ((fh = f.hash) == MOVED)
	              tab = helpTransfer(tab, f);
	          else if (onlyIfAbsent // check first node without acquiring lock
	                   && fh == hash
	                   && ((fk = f.key) == key || (fk != null && key.equals(fk)))
	                   && (fv = f.val) != null)
	              return fv;
	          else {
	              V oldVal = null;
	              synchronized (f) {
	                  if (tabAt(tab, i) == f) {
	                      if (fh >= 0) {
	                          binCount = 1;
	                          for (Node<K,V> e = f;; ++binCount) {
	                              K ek;
	                              if (e.hash == hash &&
	                                  ((ek = e.key) == key ||
	                                   (ek != null && key.equals(ek)))) {
	                                  oldVal = e.val;
	                                  if (!onlyIfAbsent)
	                                      e.val = value;
	                                  break;
	                              }
	                              Node<K,V> pred = e;
	                              if ((e = e.next) == null) {
	                                  pred.next = new Node<K,V>(hash, key, value);
	                                  break;
	                              }
	                          }
	                      }
	                      else if (f instanceof TreeBin) {
	                          Node<K,V> p;
	                          binCount = 2;
	                          if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
	                                                         value)) != null) {
	                              oldVal = p.val;
	                              if (!onlyIfAbsent)
	                                  p.val = value;
	                          }
	                      }
	                      else if (f instanceof ReservationNode)
	                          throw new IllegalStateException("Recursive update");
	                  }
	              }
	              if (binCount != 0) {
	                  if (binCount >= TREEIFY_THRESHOLD)
	                      treeifyBin(tab, i);
	                  if (oldVal != null)
	                      return oldVal;
	                  break;
	              }
	          }
	      }
	      addCount(1L, binCount);
	      return null;
	  }
	  
	  //初始化数组
	  
	  private final Node<K,V>[] initTable() {
	      Node<K,V>[] tab; int sc;
	      while ((tab = table) == null || tab.length == 0) {
	          // 说明有数组正在初始化
	          if ((sc = sizeCtl) < 0)
	              Thread.yield(); // lost initialization race; just spin
	          else if (U.compareAndSetInt(this, SIZECTL, sc, -1)) {
	              try {
	                  if ((tab = table) == null || tab.length == 0) {
	                      int n = (sc > 0) ? sc : DEFAULT_CAPACITY;
	                      @SuppressWarnings("unchecked")
	                      Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
	                      table = tab = nt;
	                      sc = n - (n >>> 2);
	                  }
	              } finally {
	                  sizeCtl = sc;
	              }
	              break;
	          }
	      }
	      return tab;
	  }
	
- get
	- 扩容
	
  扩容有两个时机进行扩容，一个是某个node的长度大于等于8的时候，他就会调用treeIfyBin方法去尝试是否可以树化，这个方法首先会判断node数组的长度，如果小于64，则考虑先将数组扩容。tryPreSize方法进行扩容。这个方法里面则会调用transfer方法来进行扩容，transfer方法负责迁移node节点，他有一个transferIndex指针，最开始时这个指针指向数组最右端，通过这个指针和每次每个线程需要迁移的node数，迁移的node数最少为16，然后将index指针cas修改掉，并且将已经迁移好的node节点变为forwardingNode，如果这个时候有线程访问，如果这个线程是去put或者remove方法，那么他会调用helptransfer方法来帮助扩容。如果进去方法后发现index指针为0或者扩容线程已经到最大的线程数，则直接返回。如果时get则会去调用forwardingIndex中的find方法访问新的node数组中对应的值。
	
	- cas加上syncronized

### CopyOnWriteArrayList

- 所有对数组有修改的操作都在新数组在进行，也就是用空间换时间。
- 它重写了一个COWIterator内部类，内部迭代的可能只是一个快照而已。

### ThreadLocal

- 每一个Thread里面都有一个threadlocals对象，这个对象的类型是ThreadLocal.ThreadLocalMap类型的对象，ThreadLocalMap内部是一个Entry数组，而Entry是ThreadLocalMap的一个静态内部类，它继承了WeakReference<ThreadLocal<?>>类型，Entry增加了一个内部属性value。因为线程和ThreadLocal之间的引用是弱引用，所以当线程使用完成后gc时就会自动回收这个对象，避免内存泄漏。
- get
- set
- remove

### CountDownLatch

- 倒数计数器，构造方法传入一个整型值，主线程通过await方法阻塞，直到活动线程多次调用countdown方法将state的状态减为0时主线程结束休眠。底层使用的是aqs的共享锁。当state不为0时，表示都可以获取共享锁。await可以多个线程调用，然后都被阻塞到同步队列中。countdown一个线程也可以调用多次

### CyclicBarrier

- 可以循环使用的障碍，创建此对象时可以传入一个整形值和一个Runable对象，代表在这个障碍前需要多少个线程到达，也就是需要被调用多少次await方法的线程等待在这个障碍前。内部使用的是非公平的ReentrantLock

### Exchanger

- 用于两个线程间交互信息，当线程调用exchange方法后阻塞，直到另一个线程调用exchange方法

## 线程池

### 概念

- 是一种基于池化思想管理线程的工具

### 好处

- 降低资源消耗：通过池化技术重复利用已创建的线程，降低线程创建和销毁造成的损耗。
- 提高响应速度：任务到达时，无需等待线程创建即可立即执行。
- 提高线程的可管理性：线程是稀缺资源，如果无限制创建，不仅会消耗系统资源，还会因为线程的不合理分布导致资源调度失衡，降低系统的稳定性。使用线程池可以进行统一的分配、调优和监控。
- 提供更多更强大的功能：线程池具备可拓展性，允许开发人员向其中增加更多的功能。比如延时定时线程池ScheduledThreadPoolExecutor，就允许任务延期执行或定期执行。

### 总体设计

- 
- 

### 生命周期管理

- ctl属性

	- workercounter
	- runstate
	- 

- 线程池状态转换

	- 
	- 

### 任务调度

### 拒绝策略


