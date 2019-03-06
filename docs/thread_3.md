### synchronized关键字
### volatile关键字
#### 保证可见性
#### 不保证原子性
自增
### synchronized关键字和volatile关键字比较
- `volatile`关键字是线程同步的轻量级实现，所以`volatile`性能肯定比`synchronized`关键字要好。但是`volatile`关键字只能用于变量而`synchronized`关键字可以修饰方法以及代码块。`synchronized`关键字在`JavaSE1.6`之后进行了主要包括为了减少获得锁和释放锁带来的性能消耗而引入的偏向锁和轻量级锁以及其它各种优化之后执行效率有了显著提升，实际开发中使用`synchronized`关键字还是更多一些。
- 多线程访问`volatile`关键字不会发生阻塞，而`synchronized`关键字可能会发生阻塞
- `volatile`关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized`关键字两者都能保证。
- `volatile`关键字用于解决变量在多个线程之间的可见性，而`synchronized`关键字解决的是多个线程之间访问资源的同步性。

### 线程的基本状态

1. 新建(`new`)：新创建了一个线程对象。 
2. 可运行(`runnable`)：线程对象创建后，其他线程(比如`main`线程）调用了该对象的`start`()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获 取`cpu`的使用权。 
3. 运行(`running`)：可运行状态(`runnable`)的线程获得了cpu时间片（`timeslice`），执行程序代码。 
4. 阻塞(`block`)：阻塞状态是指线程因为某种原因放弃了cpu使用权，也即让出了`cpu` `timeslice`，暂时停止运行。直到线程进入可运行(runnable)状态，才有 机会再次获得`cpu` `timeslice`转到运行(`running`)状态。
5. 死亡(`dead`)

### notify()锁不释放

当方法`wait`()被执行后，锁自动被释放，但执行完`notify`()方法后，锁不会自动释放。必须执行完`notify`()方法所在的`synchronized`代码块后才释放。

### 管道输入/输出流

### Thread.join()的使用
在很多情况下，主线程生成并起动了子线程，如果子线程里要进行大量的耗时的运算，主线程往往将于子线程之前结束，但是如果主线程处理完其他的事务后，需要用到子线程的处理结果，也就是主线程需要等待子线程执行完成之后再结束，这个时候就要用到`join`()方法了。另外，一个线程需要等待另一个线程也需要用到`join`()方法。

### ThreadLocal的使用
`ThreadLocal`类主要解决的就是让每个线程绑定自己的值，可以将`ThreadLocal`类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据。


### InheritableThreadLocal
`ThreadLocal`类固然很好，但是子线程并不能取到父线程的`ThreadLocal`类的变量，`InheritableThreadLocal`类就是解决这个问题的。

### Lock

#### Lock接口提供的synchronized关键字不具备的主要特性
|特性	|描述|
|------|------|
|尝试非阻塞地获取锁|	当前线程尝试获取锁，如果这一时刻锁没有被其他线程获取到，则成功获取并持有锁|
|能被中断地获取锁|	获取到锁的线程能够响应中断，当获取到锁的线程被中断时，中断异常将会被抛出，同时锁会被释放|
|超时获取锁|	在指定的截止时间之前获取锁， 超过截止时间后仍旧无法获取则返回|

#### Lock接口的实现类：ReentrantLock

#### Condition接口

在使用`notify`/`notifyAll`()方法进行通知时，被通知的线程是有`JVM`选择的，使用`ReentrantLock`类结合`Condition`实例可以实现“选择性通知”，这个功能非常重要，而且是`Condition`接口默认提供的。

而`synchronized`关键字就相当于整个`Lock`对象中只有一个`Condition`实例，所有的线程都注册在它一个身上。如果执行`notifyAll`()方法的话就会通知所有处于等待状态的线程这样会造成很大的效率问题，而`Condition`实例的`signalAll`()方法 只会唤醒注册在该`Condition`实例中的所有等待线程

```java
public class UseSingleConditionWaitNotify {

    public static void main(String[] args) throws InterruptedException {

        MyService service = new MyService();

        ThreadA a = new ThreadA(service);
        a.start();

        Thread.sleep(3000);

        service.signal();

    }

    static public class MyService {

        private Lock lock = new ReentrantLock();
        public Condition condition = lock.newCondition();

        public void await() {
            lock.lock();
            try {
                System.out.println(" await时间为" + System.currentTimeMillis());
                condition.await();
                System.out.println("这是condition.await()方法之后的语句，condition.signal()方法之后我才被执行");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }

        public void signal() throws InterruptedException {
            lock.lock();
            try {               
                System.out.println("signal时间为" + System.currentTimeMillis());
                condition.signal();
                Thread.sleep(3000);
                System.out.println("这是condition.signal()方法之后的语句");
            } finally {
                lock.unlock();
            }
        }
    }

    static public class ThreadA extends Thread {

        private MyService service;

        public ThreadA(MyService service) {
            super();
            this.service = service;
        }

        @Override
        public void run() {
            service.await();
        }
    }
}

```

**在使用`wait`/`notify`实现等待通知机制的时候我们知道必须执行完`notify`()方法所在的`synchronized`代码块后才释放锁。在这里也差不多，必须执行完`signal`所在的`try`语句块之后才释放锁，`condition`.`await`()后的语句才能被执行。**

### 公平锁与非公平锁

`Lock`锁分为：公平锁 和 非公平锁。公平锁表示线程获取锁的顺序是按照线程加锁的顺序来分配的，即先来先得的`FIFO`先进先出顺序。而非公平锁就是一种获取锁的抢占机制，是随机获取锁的，和公平锁不一样的就是先来的不一定先的到锁，这样可能造成某些线程一直拿不到锁，结果也就是不公平的了。

### ReadWriteLock接口的实现类：ReentrantReadWriteLock

我们刚刚接触到的**`ReentrantLock`（排他锁**）具有完全互斥排他的效果，即**同一时刻只允许一个线程访问**，这样做虽然虽然保证了实例变量的线程安全性，但效率非常低下。`ReadWriteLock`接口的实现类-`ReentrantReadWriteLock`读写锁就是为了解决这个问题。

**读写锁维护了两个锁，一个是读操作相关的锁也成为共享锁，一个是写操作相关的锁 也称为排他锁。通过分离读锁和写锁，其并发性比一般排他锁有了很大提升。**

**多个读锁之间不互斥，读锁与写锁互斥，写锁与写锁互斥（只要出现写操作的过程就是互斥的。）。**在没有线程`Thread`进行写入操作时，进行读取操作的多个`Thread`都可以获取读锁，而进行写入操作的`Thread`只有在获取写锁后才能进行写入操作。即多个`Thread`可以同时进行读取操作，但是同一时刻只允许一个`Thread`进行写入操作。

### 多线程就一定好吗？快吗？？

并发编程的目的就是为了能提高程序的执行效率提高程序运行速度，但是并发编程并不总是能提高程序运行速度的，而且并发编程可能会遇到很多问题，比如：内存泄漏、上下文切换、死锁还有受限于硬件和软件的资源闲置问题。

多线程就是几乎同时执行多个线程（一个处理器在某一个时间点上永远都只能是一个线程！即使这个处理器是多核的，除非有多个处理器才能实现多个线程同时运行）。CPU通过给每个线程分配CPU时间片来实现伪同时运行，因为CPU时间片一般很短很短，所以给人一种同时运行的感觉。

### 上下文切换

当前任务在执行完`CPU`时间片切换到另一个任务之前会先保存自己的状态，以便下次再切换会这个任务时，可以再加载这个任务的状态。任务从保存到再加载的过程就是一次上下文切换。

上下文切换通常是计算密集型的。也就是说，它需要相当可观的处理器时间，在每秒几十上百次的切换中，每次切换都需要纳秒量级的时间。所以，上下文切换对系统来说意味着消耗大量的 `CPU` 时间，事实上，可能是操作系统中时间消耗最大的操作。 
`Linux`相比与其他操作系统（包括其他类 `Unix` 系统）有很多的优点，其中有一项就是，其上下文切换和模式切换的时间消耗非常少。

那么我们现在可能会考虑 ：**如何减少上下文切换的次数呢？？？**

### 减少上下文切换

![thread_3_pic](https://user-gold-cdn.xitu.io/2018/4/7/1629f6660dcf1739?w=790&h=275&f=jpeg&s=16730)

上下文切换又分为2种：**让步式上下文切换**和**抢占式上下文切换**。前者是指执行线程主动释放`CPU`，与锁竞争严重程度成正比，可通过减少锁竞争和使用`CAS`算法来避免；后者是指线程因分配的时间片用尽而被迫放弃`CPU`或者被其他优先级更高的线程所抢占，一般由于线程数大于`CPU`可用核心数引起，可通过适当减少线程数和使用协程来避免。

> 总结一下：

1. 减少锁的使用。因为多线程竞争锁时会引起上下文切换。
2. 使用CAS算法。这种算法也是为了减少锁的使用。CAS算法是一种无锁算法。
3. 减少线程的使用。人物很少的时候创建大量线程会导致大量线程都处于等待状态。
4. 使用协程。

### CAS算法
> CAS（比较与交换，Compare and swap） 是一种有名的无锁算法。无锁编程，即不使用锁的情况下实现多线程之间的变量同步，也就是在没有线程被阻塞的情况下实现变量的同步，所以也叫非阻塞同步（Non-blocking Synchronization）。实现非阻塞同步的方案称为“无锁编程算法”（ Non-blocking algorithm）。 
  相对应的，独占锁是一种悲观锁，synchronized就是一种独占锁，它假设最坏的情况，并且只有在确保其它线程不会造成干扰的情况下执行，会导致其它所有需要锁的线程挂起，等待持有锁的线程释放锁。
  
### 协程

![thread_3_pic_1](https://user-gold-cdn.xitu.io/2018/4/7/1629fe92f6b5ff44?w=1163&h=522&f=png&s=47162)

> 协程也可以说是微线程或者说是轻量级的线程，它占用的内存更少并且更灵活。很多编程语言中都有协程。Lua, Ruby 等等都有自己的协程实现。Go完全就是因为协程而发展壮大的。维基百科上面并没有Java实现协程的方式，但是不代表Java不能实现协程。比如可以使用Java实现的开源协程库：Quasar。Quasar官网：http://www.paralleluniverse.co/quasar/，。这个库实现了一种可以和Go语言中的Goroutine相对标的编程概念：Fiber。Fiber是一种真正的协程。
  
### 避免死锁

> 在操作系统中，死锁是指两个或两个以上的进程在执行过程中，由于竞争资源或者由于彼此通信而造成的一种阻塞的现象，若无外力作用，它们都将无法推进下去。此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程称为死锁进程。

在线程中，如果两个线程同时等待对方释放锁也会产生死锁。

锁是一个好东西，但是使用不当就会造成死锁。一旦死锁产生程序就无法继续运行下去。所以如何避免死锁的产生，在我们使用并发编程时至关重要。

根据《Java并发编程的艺术》有下面四种避免死锁的常见方法：

- **避免一个线程同时获得多个锁**
- **避免一个线程在锁内同时占用多个资源，尽量保证每个锁只占用一个资源**
- **尝试使用定时锁，使用`lock`.`tryLock`(`timeout`)来替代使用内部锁机制**
- **对于数据库锁，加锁和解锁必须在一个数据库连接里，否则会出现解锁失败的情况**

### 使用线程池的好处

**线程池**提供了一种限制和管理资源（包括执行一个任务）。 每个线程池还维护一些基本统计信息，例如已完成任务的数量。
这里借用《Java并发编程的艺术》提到的来说一下使用线程池的好处：

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。当任务到达时，任务可以不需要的等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

#### 在《阿里巴巴Java开发手册》“并发处理”这一章节，明确指出线程资源必须通过线程池提供，不允许在应用中自行显示创建线程。
为什么呢？

> 使用线程池的好处是减少在创建和销毁线程上所消耗的时间以及系统资源开销，解决资源不足的问题。如果不使用线程池，有可能会造成系统创建大量同类线程而导致消耗完内存或者“过度切换”的问题。
  
#### 另外《阿里巴巴Java开发手册》中强制线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险
> Executors 返回线程池对象的弊端如下：
  
  - FixedThreadPool 和 SingleThreadExecutor ： 允许请求的队列长度为 Integer.MAX_VALUE,可能堆积大量的请求，从而导致OOM。
  - CachedThreadPool 和 ScheduledThreadPool ： 允许创建的线程数量为 Integer.MAX_VALUE ，可能会创建大量线程，从而导致OOM。
  
### ScheduledThreadPoolExecutor和Timer的比较

- Timer对系统时钟的变化敏感，ScheduledThreadPoolExecutor不是；
- Timer只有一个执行线程，因此长时间运行的任务可以延迟其他任务。 ScheduledThreadPoolExecutor可以配置任意数量的线程。 此外，如果你想（通过提供ThreadFactory），你可以完全控制创建的线程;
- 在TimerTask中抛出的运行时异常会杀死一个线程，从而导致Timer死机:-( …即计划任务将不再运行。ScheduledThreadExecutor不仅捕获运行时异常，还允许您在需要时处理它们（通过重写afterExecute方法 ThreadPoolExecutor）。抛出异常的任务将被取消，但其他任务将继续运行。

### 各种线程池的适用场景介绍

- **FixedThreadPool**： 适用于为了满足资源管理需求，而需要限制当前线程数量的应用场景。它适用于负载比较重的服务器；

- **SingleThreadExecutor**： 适用于需要保证顺序地执行各个任务并且在任意时间点，不会有多个线程是活动的应用场景。

- **CachedThreadPool**： 适用于执行很多的短期异步任务的小程序，或者是负载较轻的服务器；

- **ScheduledThreadPoolExecutor**： 适用于需要多个后台执行周期任务，同时为了满足资源管理需求而需要限制后台线程的数量的应用场景，

- **SingleThreadScheduledExecutor**： 适用于需要单个后台线程执行周期任务，同时保证顺序地执行各个任务的应用场景。
