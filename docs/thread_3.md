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

1. 新建(new)：新创建了一个线程对象。 
2. 可运行(runnable)：线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获 取cpu的使用权。 
3. 运行(running)：可运行状态(runnable)的线程获得了cpu时间片（timeslice），执行程序代码。 
4. 阻塞(block)：阻塞状态是指线程因为某种原因放弃了cpu使用权，也即让出了cpu timeslice，暂时停止运行。直到线程进入可运行(runnable)状态，才有 机会再次获得cpu timeslice转到运行(running)状态。阻塞的情况分三种：
5. 死亡(dead)

### notify()锁不释放

当方法wait()被执行后，锁自动被释放，但执行完notify()方法后，锁不会自动释放。必须执行完notify()方法所在的synchronized代码块后才释放。

### 管道输入/输出流

### Thread.join()的使用
在很多情况下，主线程生成并起动了子线程，如果子线程里要进行大量的耗时的运算，主线程往往将于子线程之前结束，但是如果主线程处理完其他的事务后，需要用到子线程的处理结果，也就是主线程需要等待子线程执行完成之后再结束，这个时候就要用到join()方法了。另外，一个线程需要等待另一个线程也需要用到join()方法。

### ThreadLocal的使用
ThreadLocal类主要解决的就是让每个线程绑定自己的值，可以将ThreadLocal类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据。


### InheritableThreadLocal
ThreadLocal类固然很好，但是子线程并不能取到父线程的ThreadLocal类的变量，InheritableThreadLocal类就是解决这个问题的。

### Lock