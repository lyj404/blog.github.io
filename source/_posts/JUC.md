---
title: JUC
date: 2023-08-27 15:09:51
tags: JUC
categories: Java
description: JUC（Java Util Concurrent）是Java5之后新增的一组并发编程工具包，提供了一系列高效、线程安全的并发集合，方便在多线程环境下处理共享数据
---
# JUC

## 1、什么是JUC

```
java.util.concurrent
java.util.concurrent.atomic
java.util.concurrent.locks
```

java.util 工具包

**业务：普通的线程代码Thread**

**Runnable**  没有返回值，效率相比Callable较低

## 2、线程和进程

> 线程、进程

进程：一个程序，QQ.ext Music.exe 程序的集合

一个进程往往可以包含多个线程，至少包含一个

Java默认有几个线程？ 2个 main GC

线程：开了一个进程Typora，写字，自动保存(线程负责)

对于Java而言：Thread   Runnable  Callable

**Java真的可以开启线程吗？** 开不了

```java
    public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
	//本地方法，底层的c++, Java无法操作硬件
    private native void start0();
```

> 并发、并行

并发编程：并发、并行

并发（多线程操作一个资源）

* CPU一核，模拟出来多条线程，快速交替

并行（多个人一起行走）

* CPU多核，多个线程可以同时执行: 线程池

```java
public class Test1 {
    public static void main(String[] args) {
        //获取cpu的核数
        //cpu密集型，IO密集型
        System.out.println(Runtime.getRuntime().availableProcessors());
    }
}
```

并发编程的本质：**充分利用cup的资源**

> 线程有几个状态

```java
public enum State {
  
    	//新生
        NEW,

    	//运行
        RUNNABLE,

      	//阻塞
        BLOCKED,

    	//等待
        WAITING,

    	//超时等待
        TIMED_WAITING,
    
    	//终止
        TERMINATED;
    }
```

> wait/sleep区别

1. **来自不同的类**

   wait => Object

   sleep => Thread

2. **关于锁的释放**

   wait会释放锁,sleep睡觉了,抱着锁睡觉,不会释放

3. **使用的范围是不同的**

   wait

   `wait必须在同步代码块中`

   sleep可以在任何地方睡

4. **是否需要捕获异常**

   wait不需要捕获异常

   sleep必须捕获异常

## 3、Lock锁(重点)

> 传统Synchronize

```java
public class SaleTicketDemo01 {
    public static void main(String[] args) {
        //并发：多个线程操作一个资源类
        Ticket ticket = new Ticket();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"A").start();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"B").start();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"C").start();
    }
}

/**
 * 资源类 oop
 */
class Ticket{
    /**
     * 属性、方法
     */
    private int number = 50;

    /**
     * 卖票的方式
     * synchronized 本质：队列，锁
     */
    public synchronized void sale(){
        if(number > 0){
            System.out.println(Thread.currentThread().getName() + "卖出了" + (number--) + "票，剩余：" + number);
        }
    }
}
```

> Lock

公平锁: 十分公平

**非公平锁: 十分不公平,可以插队(默认)**

```java
public class SaleTicketDemo02 {
    public static void main(String[] args) {
        //并发：多个线程操作一个资源类
        Ticket2 ticket = new Ticket2();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"A").start();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"B").start();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"C").start();
    }
}

/**
 * 资源类 oop
 */
class Ticket2{
    /**
     * 属性、方法
     */
    private int number = 50;

    Lock lock = new ReentrantLock();

    /**
     * 卖票的方式
     * synchronized 本质：队列，锁
     */
    public void sale(){
        //加锁
        lock.lock();
        try {
            //业务代码
            if(number > 0){
                System.out.println(Thread.currentThread().getName() + "卖出了" + (number--) + "票，剩余：" + number);
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            //解锁
            lock.unlock();
        }
    }
}
```

> Synchronized 和Lock区别

1. Synchronized 内置的Java关键字,Lock是一个Java类

2. Synchronized 无法判断锁的状态,Lock可以判断是否获取到锁

3. Synchronized 会自动释放锁,Lock必须要手动释放锁! 如果不释放会**死锁**

4. Synchronized线程1(获得锁、阻塞)、线程2（等待，傻傻的等待）；Lock锁就不一定会等待下去
5. Synchronized 可重入锁，不可以中断，非公平的；lock，可重入锁，可以判断锁，非公平（可以自己设置）
6. Synchronized 适合锁少量的代码同步问题，Lock适合锁大量的同步方法

## 4、生产者和消费者问题

> 生产者和消费者问题 Synchronized版

```java
public class A {
    public static void main(String[] args) {
        Data data = new Data();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                }catch (InterruptedException e){
                    e.printStackTrace();
                }
            }
        },"A").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();
    }
}

/**
 * 数字，资源类
 * 等待、业务、通知
 */
class Data{
    private int number = 0;

    /**
     * +1
     */
    public synchronized void increment() throws InterruptedException{
        if(number != 0){
            //等待
            this.wait();
        }
        number ++;
        //通知其他线程，我+1完成了
        System.out.println(Thread.currentThread().getName() + "=>" + number);
        this.notifyAll();
    }

    /**
     * -1
     */
    public synchronized void decrement() throws InterruptedException {
        if(number == 0){
            //等待
            this.wait();
        }
        number --;
        //通知其他线程，我-1完成了
        System.out.println(Thread.currentThread().getName() + "=>" + number);
        this.notifyAll();
    }
}
```

> 存在a,b,c,d四个线程！虚假唤醒问题

if 改为 while

> JUC版的生产者和消费者

通过lock可以找到condition

代码实现

```java
public class B {
    public static void main(String[] args) {
        Data2 data2 = new Data2();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data2.increment();
                }catch (InterruptedException e){
                    e.printStackTrace();
                }
            }
        },"A").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data2.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data2.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data2.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"D").start();
    }
}

class Data2{
    private int number = 0;

    Lock lock = new ReentrantLock();
    Condition condition = lock.newCondition();

    /**
     * +1
     */
    public void increment() throws InterruptedException{
        lock.lock();
        try {
            while (number != 0){
                //等待
                condition.await();
            }
            number ++;
            //通知其他线程，我+1完成了
            System.out.println(Thread.currentThread().getName() + "=>" + number);
            condition.signalAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }

    }

    /**
     * -1
     */
    public void decrement() throws InterruptedException {
        lock.lock();
        try {
            while (number == 0){
                //等待
                condition.await();
            }
            number --;
            //通知其他线程，我-1完成了
            System.out.println(Thread.currentThread().getName() + "=>" + number);
            condition.signalAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
```

**任何一个新的技术，绝对不是仅仅只是覆盖了原来的技术，优势和补充**

> Condition精准的通知

```java
public class C {
    public static void main(String[] args) {

        Data3 data = new Data3();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data.printA();
            }
        },"A").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data.printB();
            }
        },"B").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data.printC();
            }
        },"C").start();
    }
}

class Data3{
    private final Lock lock = new ReentrantLock();
    private final Condition condition1 = lock.newCondition();
    private final Condition condition2 = lock.newCondition();
    private final Condition condition3 = lock.newCondition();
    private int number = 1;

    public void printA(){
        lock.lock();
        try {
            while (number != 1){
                //等待
                condition1.await();
            }
            System.out.println(Thread.currentThread().getName() + "=>AAA");
            number = 2;
            condition2.signal();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void printB(){
        lock.lock();
        try {
            while (number != 2){
                condition2.await();
            }
            System.out.println(Thread.currentThread().getName() + "=>BBB");
            number = 3;
            condition3.signal();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void printC(){
        lock.lock();
        try {
            while (number != 3){
                condition3.await();
            }
            System.out.println(Thread.currentThread().getName() + "=>CCC");
            number = 1;
            condition1.signal();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    //生产线： 下单-》支付-》交易-》物流
}
```

## 5、8锁现象

如何判断锁是谁！永远的知道，什么是锁，锁到底锁的是谁？

### 深刻理解锁

```java
/**
 * @author: lyj
 * @date: 2022/7/30 14:21
 * 8锁，就是关于锁的8个问题
 * 1、标准情况下：两个线程先打印 发短信还是 打电话？
 * 2.发短信延迟4秒，两个线程是先打印发短信还是打电话 发短信
 */
public class Test1 {
    public static void main(String[] args) {
        Phone phone = new Phone();

        new Thread(() ->{
            phone.sendSms();
        },"B").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        }catch (InterruptedException e){
            e.printStackTrace();
        }

        new Thread(() ->{
            phone.call();
        },"B").start();
    }
}

class Phone{

    /**
     * synchronize 锁的对象是方法的调用者
     * 两个方法，谁先拿到谁先执行
     */
    public synchronized  void sendSms(){
        try {
            TimeUnit.SECONDS.sleep(4);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("sendSms");
    }
    public synchronized void call(){
        System.out.println("call");
    }
}
```

```java
public class Test2 {
    public static void main(String[] args) {
        Phone1 phone = new Phone1();
        Phone1 phone1 = new Phone1();

        new Thread(() ->{
            phone.sendSms();
        },"B").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        }catch (InterruptedException e){
            e.printStackTrace();
        }

        new Thread(() ->{
            phone1.call();
        },"B").start();
    }
}

class Phone1{

    /**
     * synchronize 锁的对象是方法的调用者
     */
    public synchronized  void sendSms(){
        try {
            TimeUnit.SECONDS.sleep(4);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("sendSms");
    }

    public synchronized void call(){
        System.out.println("call");
    }

    //没有锁 ，不受锁的影响
    public void hello(){
        System.out.println("hello");
    }
}
```

```java
public class Test3 {
    public static void main(String[] args) {
        Phone2 phone = new Phone2();
        Phone2 phone1 = new Phone2();

        new Thread(() ->{
            phone.sendSms();
        },"A").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        }catch (InterruptedException e){
            e.printStackTrace();
        }

        new Thread(() ->{
            phone1.call();
        },"B").start();
    }
}

class Phone2 {
    //static静态方法
    //类一加载就有了，锁的是class
    public static synchronized void sendSms() {
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("sendSms");
    }

    public static synchronized void call() {
        System.out.println("call");
    }
}
```

```java
/**
 * @author: lyj
 * @date: 2022/7/30 14:46
 * 7.1个静态同步方法，1个普通同步方法，一个对象，先打印发短信还是打电话
 * 8.两个对象是先打印打电话还是发短信
 */
public class Test4 {
    public static void main(String[] args) {
        Phone3 phone = new Phone3();
        Phone3 phone1 = new Phone3();

        new Thread(() ->{
            phone.sendSms();
        },"A").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        }catch (InterruptedException e){
            e.printStackTrace();
        }

        new Thread(() ->{
            phone1.call();
        },"B").start();
    }
}

class Phone3 {
    //static静态方法
    //类一加载就有了，锁的是class
    public static synchronized void sendSms() {
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("sendSms");
    }

    public synchronized void call() {
        System.out.println("call");
    }
}

```

> 总结

new this 具体的一个手机

static class为唯一的一个模板

## 6、集合类不安全

> List不安全

```java
public class ListTest {
    public static void main(String[] args) {
        /**
         * 并发下ArrayList不安全
         * 解决方法：
         *  1.List<String> list = new Vector<>();
         *  2.List<String> list = Collections.synchronizedList(new ArrayList<>());
         *  3.List<String> list = new CopyOnWriteArrayList<>();
         */
        //CopyOnWrite 写入是复制， cow 计算机程序设计领域的一种优化策略
        //多个线程调用的时候，list，读取的时候，固定的，写入（覆盖）
        //在写入的时候避免覆盖，造成数据问题

        List<String> list = new CopyOnWriteArrayList<>();

        for (int i = 0; i <= 100; i++) {
           new Thread(() ->{
               list.add(UUID.randomUUID().toString().substring(0,5));
               System.out.println(list);
           },String.valueOf(i)).start();
        }
    }
}
```

> Set不安全

```java
/**
 * @author: lyj
 * @date: 2022/7/30 15:11
 * Exception in thread "1" java.util.ConcurrentModificationException
 */
public class SetTest {
    public static void main(String[] args) {
//        Set<String> set = new HashSet<>();
//        Set<String> set = Collections.synchronizedSet(new HashSet<>());

        Set<String> set = new CopyOnWriteArraySet<>();

        for (int i = 0; i < 30; i++) {
            new Thread(() ->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(set);
            },String.valueOf(i)).start();
        }
    }
}
```

hashset底层

```java
public HashSet() {
        map = new HashMap<>();
}

// add set本质就是map key是无法重复的
 public boolean add(E e) {
        return map.put(e, PRESENT)==null;
 }
private static final Object PRESENT = new Object(); //不变的常量
```

> HashMap不安全

## 7、Callable（简单）

1、可以有返回值

2、可以抛出异常

3、方法不同,run()/call();

```java
public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        new Thread().start();

        MyThread myThread = new MyThread();
        //适配器
        FutureTask futureTask = new FutureTask(myThread);

        new Thread(futureTask,"A").start();

        //获取Callable的返回值
        Integer o = (Integer) futureTask.get();
        System.out.println(o);

    }
}
class MyThread implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        System.out.println("call()");
        return 1024;
    }
}
```

## 8、常用的辅助类

### 8.1、CountDownLatch

```java
public class CountDownLatcheDemo {
    public static void main(String[] args) throws InterruptedException {
        //总数6
        CountDownLatch countDownLatch = new CountDownLatch(6);

        for (int i = 0; i <= 6; i++) {
            new Thread(() ->{
                System.out.println(Thread.currentThread().getName() + "go out");
                countDownLatch.countDown();
            },String.valueOf(i)).start();
        }
        //等待计数器归零
        countDownLatch.await();

        System.out.println("close Door");
    }
}
```

`countDownLatch.countDown()`//数量-1

`countDownLatch.await()`//等待计数器归零，然后在向下执行

每次有线程调用`countDownLatch.countDown()`减一，等归零之后会唤醒await()

### 8.2、CycliBarrier

```java
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        /**
         * 集齐7个龙珠召唤神龙
         */
        //召唤龙珠的线程
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,() ->{
            System.out.println("召唤神龙成功");
        });

        for (int i = 0; i < 7; i++) {
            final int temp = i;
            new Thread(() ->{
                System.out.println(Thread.currentThread().getName()+ "收集" + temp + "龙珠");

                try {
                    //等待
                    cyclicBarrier.await();
                }catch (InterruptedException e){
                    e.printStackTrace();
                }catch (BrokenBarrierException e){
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

### 8.3、Semaphore

**Semaphore:** 信号量

```java
public class SemaphoreTest {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);

        for (int i = 0; i < 6; i++) {
            new Thread(() ->{
                //acquire 得到
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "抢到车位");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName() + "离开车位");
                }catch (InterruptedException e){
                    e.printStackTrace();
                }finally {
                    semaphore.release();
                }
                //release 释放
            },String.valueOf(i)).start();
        }
    }
}
```

`semaphore.acquire()`获得，假设如果已经满了，等待，等待被释放为止

`semaphore.release()`释放，会将当前的信号量释放+1，然后唤醒等待的线程

## 9、读写锁

**ReadWriteLock**

```java
/**
 * @author: lyj
 * @date: 2022/7/30 16:01
 * ReadWriteLock
 * 读-读，可以共存
 * 读-写，不能共存
 * 写-写，不能共存
 */
public class ReadWriteLockTest {
    public static void main(String[] args) {
        MyCacheLock myCache = new MyCacheLock();

        for (int i = 1; i <= 5; i++) {
            final int temp = i;
            new Thread(() ->{
                myCache.put(temp+"",temp+"");
            },String.valueOf(i)).start();
        }

        for (int i = 1; i <= 5; i++) {
            final int temp = i;
            new Thread(() ->{
                myCache.get(temp+"");
            },String.valueOf(i)).start();
        }
    }
}

/**
 * 自定义缓存
 */
class MyCache{
    private volatile Map<String,Object> map = new HashMap<>(16);

    //存，写
    public void put(String key,Object value){
        System.out.println(Thread.currentThread().getName() + "写入" + key);
        map.put(key,value);
        System.out.println(Thread.currentThread().getName() + "写入ok");
    }

    //取，读
    public void get(String key){
        System.out.println(Thread.currentThread().getName() + "读取" + key);
        Object obj = map.get(key);
        System.out.println(Thread.currentThread().getName() + "读取ok");
    }
}

class MyCacheLock{
    private volatile Map<String,Object> map = new HashMap<>(16);
    //读写锁，更加细粒度的操作
    private ReadWriteLock lock = new ReentrantReadWriteLock();

    //存，写入的时候，只希望同时只有一个线程写
    public void put(String key,Object value){
        lock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "写入" + key);
            map.put(key,value);
            System.out.println(Thread.currentThread().getName() + "写入ok");
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.writeLock().unlock();
        }
    }

    //取，读，所有人都可以读
    public void get(String key){
        lock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "读取" + key);
            Object obj = map.get(key);
            System.out.println(Thread.currentThread().getName() + "读取ok");
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.readLock().unlock();
        }
    }
}
```

## 10、阻塞队列

**什么情况下会使用阻塞队列：**多线程并发处理，线程池

**使用队列**

添加、移除

### 四组API

| 方式       | 抛出异常 | 有返回值，不抛异常 | 阻塞等待 | 超时等待 |
| ---------- | -------- | ------------------ | -------- | -------- |
| 添加       | add      | offer              | put      | offer    |
| 移除       | remove   | poll               | take     | poll     |
| 判断队列首 | element  | peek               |          |          |

```java
  /**
     * 抛出异常
     */
    public static void test1(){
        //队列的大小
        ArrayBlockingQueue arrayBlockingQueue = new ArrayBlockingQueue(3);

        System.out.println(arrayBlockingQueue.add("a"));
        System.out.println(arrayBlockingQueue.add("b"));
        System.out.println(arrayBlockingQueue.add("c"));
        //Exception in thread "main" java.lang.IllegalStateException: Queue full
        //System.out.println(arrayBlockingQueue.add("d"));
        System.out.println(arrayBlockingQueue.remove());
        System.out.println(arrayBlockingQueue.remove());
        System.out.println(arrayBlockingQueue.remove());
        //Exception in thread "main" java.util.NoSuchElementException
        //System.out.println(arrayBlockingQueue.remove());
    }
```

```java
 /**
     * 不抛异常
     */
    public static void test2(){
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue(3);

        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("b"));
        System.out.println(blockingQueue.offer("c"));
        //false
        //System.out.println(blockingQueue.offer("c"));

        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        //null
        //System.out.println(blockingQueue.poll());
    }
```

```java
  /**
     * 等待，阻塞（一直阻塞）
     */
    public static void test3() throws InterruptedException {
        ArrayBlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);

        blockingQueue.put("a");
        blockingQueue.put("b");
        blockingQueue.put("c");
        //队列没有位置了，一直阻塞
        //blockingQueue.put("d");

        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
    }
```

```java
    /**
     * 等待，阻塞（等待超时）
     */
    public static void test4() throws InterruptedException{
        ArrayBlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);

        blockingQueue.offer("a");
        blockingQueue.offer("b");
        blockingQueue.offer("c");
        blockingQueue.offer("d",2, TimeUnit.SECONDS);

        System.out.println("========");

        blockingQueue.poll();
        blockingQueue.poll();
        blockingQueue.poll();
        blockingQueue.poll(2,TimeUnit.SECONDS);
    }
```

> SynchronousQueue同步队列

没有容量，进去一个元素，必须等待取出来之后，才能再里面放一个元素

put 、take

```java
/**
 * @author: lyj
 * @date: 2022/7/30 19:38
 * 同步队列和其他的BlockingQueue不一样
 */
public class SynchronousQueueTest {
    public static void main(String[] args) {
        //同步队列
        BlockingQueue<String> queue = new SynchronousQueue<>();

        new Thread(() ->{
            try {
                System.out.println(Thread.currentThread().getName() + "put 1");
                queue.put("1");
                System.out.println(Thread.currentThread().getName() + "put 2");
                queue.put("2");
                System.out.println(Thread.currentThread().getName() + "put 3");
                queue.put("3");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"T1").start();

        new Thread(() ->{
            try {
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName() + queue.take());
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName() + queue.take());
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName() + queue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"T2").start();
    }
}
```

## 11、线程池（重点）

线程池：三大方法、7大参数、4钟拒绝策略

> 池化技术

程序的运行，本质：占用系统的资源！优化资源的使用！ =》池化技术

线程池、连接池、内存池、对象池……

池化技术：事先准备好一些资源，有人要用，就来我这里拿，用完之后返还

**线程池的好处：**

1、降低资源的消耗

2、提高相应的速度

3、方便管理

**线程复用，可以控制最大并发数，管理线程**

> 三大方法

```java
/**
 * @author: lyj
 * @date: 2022/7/30 19:54
 * Executors工具，3大方法
 * 使用了线程池，使用线程池来创建线程
 */
public class Demo01 {
    public static void main(String[] args) {
        //单个线程
        //ExecutorService pool =  Executors.newSingleThreadExecutor();
        //创建一个固定线程池的大小
        //ExecutorService pool = Executors.newFixedThreadPool(5);
        //可伸缩的，遇强则强，遇弱则弱
        ExecutorService pool = Executors.newCachedThreadPool();

        try {
            for (int i = 0; i < 10; i++) {
                pool.execute(() ->{
                    System.out.println(Thread.currentThread().getName() + " ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //线程池用完，程序结束，关闭线程池
            pool.shutdown();
        }
    }
}
```

> 7大参数

```java
//源码分析
 public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }

   public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }

public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }

//本质：ThreadPoolExecutor
 public ThreadPoolExecutor(int corePoolSize,//核心线程池大小
                              int maximumPoolSize,//最大核心线程大小
                              long keepAliveTime,//超时了没有人调用就会释放
                              TimeUnit unit,//超时单位
                              BlockingQueue<Runnable> workQueue,//阻塞队列
                              ThreadFactory threadFactory,//线程工厂，创建线程的，一般不用动
                              RejectedExecutionHandler handler) {//拒绝策略
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

> 手动创建线程池

```java
public class Demo02 {
    public static void main(String[] args) {
        //银行满了，还有人进来，不处理这个人，抛出异常
        ExecutorService pool = new ThreadPoolExecutor(
                2,
                5,
                3,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());

        try {
            //最大承载数，Dequeue + max
            for (int i = 0; i < 9; i++) {
                pool.execute(() ->{
                    System.out.println(Thread.currentThread().getName() + " ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //线程池用完，程序结束，关闭线程池
            pool.shutdown();
        }
    }
}
```

> 四种拒绝策略

```java
/**
 * @author: lyj
 * @date: 2022/7/31 10:26
 * 七大参数
 * 四大拒绝策略
 * new ThreadPoolExecutor.AbortPolicy() 银行满了，还有人进来，不处理这个人，抛出异常
 * new ThreadPoolExecutor.CallerRunsPolicy() 哪来的去哪里
 * new ThreadPoolExecutor.DiscardPolicy() 队列满了，丢掉任务，不会抛出异常，
 * new ThreadPoolExecutor.DiscardOldestPolicy() 队列满了，尝试去和最早的竞争，也不会抛出异常
 *
 */
public class Demo02 {
    public static void main(String[] args) {
        //银行满了，还有人进来，不处理这个人，抛出异常
        ExecutorService pool = new ThreadPoolExecutor(
                2,
                5,
                3,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.DiscardOldestPolicy());

        try {
            //最大承载数，Dequeue + max
            for (int i = 0; i < 9; i++) {
                pool.execute(() ->{
                    System.out.println(Thread.currentThread().getName() + " ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //线程池用完，程序结束，关闭线程池
            pool.shutdown();
        }
    }
}
```

**了解：**

```java
//最大线程池到底如何定义
//1.cup密集型，几核就是几，可以保持cup的效率最高
//2.IO密集型，判断你程序中十分耗IO的线程
//程序 15个大型任务，io十分占用资源
```

## 12、ForkJoin

> 什么是ForkJoin

ForkJoin在jdk1.7，并行执行任务！提高效率，大数据量！

大数据：Map Reduce（把大任务拆分成小人物）

ForkJoin特点：工作窃取（一个线程执行完了，就会帮助未完成的线程去完成任务）

```java
//测试
public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //6065
        //test1();
        //4223
        //test2();
        //229
        test3();
    }

    public static void test1(){
        Long sum = 0L;
        long start = System.currentTimeMillis();
        for (Long i = 1L; i <= 10_0000_0000L ; i++) {
            sum += i;
        }
        long end = System.currentTimeMillis();
        System.out.println("sum=" + sum + " 时间：" + (end-start));
    }

    public static void test2() throws ExecutionException, InterruptedException {
        long start = System.currentTimeMillis();

        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask<Long> task = new ForkJoinDemo(0L, 10_0000_0000L);
        //提交任务
        ForkJoinTask<Long> submit = forkJoinPool.submit(task);
        long sum = submit.get();
        long end = System.currentTimeMillis();
        System.out.println("sum=" + sum + " 时间：" + (end-start));
    }

    public static void test3(){
        long start = System.currentTimeMillis();
        //stream并行流
        long sum = LongStream.rangeClosed(0L, 10_0000_0000L).parallel().reduce(0,Long::sum);

        long end = System.currentTimeMillis();
        System.out.println("sum=" + sum + " 时间：" + (end-start));
    }

}
//forkjoin
public class ForkJoinDemo extends RecursiveTask<Long> {

    private Long start;
    private Long end;

    /**
     * 临界值
     */
    private Long temp  = 10000L;

    public ForkJoinDemo(Long start, Long end) {
        this.start = start;
        this.end = end;
    }


    /**
     * 计算方法
     * @return
     */
    @Override
    protected Long compute() {
        if((end-start) < temp){
            Long sum = 0L;

            for (Long i = start; i <= end; i++) {
                sum += i;
            }
            return sum;
        }else{
           //中间值
           long middle = (start + end) /2;
           ForkJoinDemo task1 = new ForkJoinDemo(start,middle);
           //拆分任务，把任务压入线程队列
           task1.fork();
           ForkJoinDemo task2 = new ForkJoinDemo(middle+1,end);
           task2.fork();

           return task1.join() + task2.join();
        }
    }
}
```

## 13、异步回调

> Future设计的初衷：对将来的某个事件的结果进行建模

```java
public class Demo01 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //发起一个请求
        //没有返回值的runAsync异步回调
        /*
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "runAsync");
        });

        System.out.println("==============");

        //获取阻塞执行结果
        completableFuture.get();
        */

        /**
         * 有返回值的supplyAsync 异步回调
         */
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() ->{
            System.out.println(Thread.currentThread().getName() + "supplyAsync");
            int i = 1/0;
            return 1024;
        });

        completableFuture.whenComplete((t,u) -> {
            System.out.println("t = " + t);
            System.out.println("u = " + u);
        }).exceptionally((e) ->{
            System.out.println(e.getMessage());
            return 2333;
        }).get();
    }
}
```

## 14、JMM

> 什么是JMM

JMM：Java内存模型，不存在的东西，概念！约定！

**关于JMM的一些同步的约定**

1、线程解锁前，必须把共享变量**立刻**刷回主存

2、线程加锁前，必须读取主存中的最新值到工作内存中

3、加锁和解锁是同一把锁

问题：程序不知道线程中的值已经被修改过来

## 15、Volatile

> Volatile的理解

Volatile是Java虚拟机提供的**轻量的同步机制**

> 1、保证可见性

```java
public class JMMDemo {
    /**
     * 不加volatile程序就会死循环，
     * 加了volatile可以保证可见性
     */
    private volatile static int num = 0;

    public static void main(String[] args) {
        new Thread(() ->{
            while (num == 0){

            }
        }).start();


        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        num = 1;
        System.out.println(num);
    }

}
```

> 2、不保证原子性

原子性：不可分割

线程A在执行任务的时候，不能被打扰，也不能被分割。要么同时成功，要么同时失败

```java
/**
 * @author: lyj
 * @date: 2022/7/31 15:37
 * 不保证原子性
 */
public class VDemo01 {

    private volatile static int num = 0;

    public static void add(){
        num++;
    }

    //理论上是两万
    public static void main(String[] args) {
        for (int i = 0; i < 20; i++) {
            new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount() > 2){
            Thread.yield();
        }

        System.out.println(Thread.currentThread().getName() + num);
    }
}
```

**如果不加lock和synchronize，怎么保证原子性**

使用源自类，解决原子性问题

> 原子类为什么这么高级

```java
/**
 * @author: lyj
 * @date: 2022/7/31 15:37
 * 不保证原子性
 */
public class VDemo01 {

    private volatile static AtomicInteger num = new AtomicInteger();

    public static void add(){
        //num++;
        num.getAndIncrement();
    }

    //理论上是两万
    public static void main(String[] args) {
        for (int i = 0; i < 20; i++) {
            new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount() > 2){
            Thread.yield();
        }

        System.out.println(Thread.currentThread().getName() + num);
    }
}
```

> 3、禁止指令重排

什么是指令重排：**你写的程序，计算机并不是按照你写的那样去执行的**

源代码 ---> 编译器优化的重排 ---> 指令并行也能重排 ---> 内存系统也可能重排 ---> 执行

```java
int x = 1;//1
int y = 2;//2
x = x +5;//3
y = x * x;//4
我们所期望的: 1234  但是执行的时候可能是： 2134   1324
```

**volatile可以避免指令重排**

内存屏障。CPU指令。作用：

1、保证特定的操作的执行顺序

2、可以保证某些变量的内存可见性（利用这些特性volatile实现了可见性）

## 16、彻底玩转单例模式

饿汉式  DCL懒汉式

> 饿汉式

```java
/**
 * @author: lyj
 * @date: 2022/7/31 15:59
 * 饿汉式单例
 */
public class Hungry {
    /**
     * 可能会浪费空间
     */
    private byte[] data1 = new byte[1024 * 1024];
    private byte[] data2 = new byte[1024 * 1024];
    private byte[] data3 = new byte[1024 * 1024];
    private byte[] data4 = new byte[1024 * 1024];

    private Hungry(){}

    public static final Hungry HUNGRY = new Hungry();

    public static Hungry getInstance(){
        return  HUNGRY;
    }
}
```

> DCL懒汉式

```java
public class LazyMan {

    private static boolean flag = false;

    private LazyMan(){
        synchronized (LazyMan.class){
            if(!flag){
                flag = true;
            }else{
                throw new RuntimeException("不要去使用反射破坏");
            }
        }
        System.out.println("Thread.currentThread().getName() = " + Thread.currentThread().getName());
    }

    private volatile static LazyMan lazyMan;

    public static LazyMan getInstance(){
        if (lazyMan == null) {
            synchronized (LazyMan.class){
                if(lazyMan == null){
                    lazyMan = new LazyMan();
                    /**
                     * 1。分配内存空间
                     * 2.执行构造方法，初始化对象
                     * 3.把这个对象指向这个空间
                     */
                }
            }
        }
        return lazyMan;
    }

    /**
     * 多线程并发
     */
    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException, NoSuchFieldException {
//        for (int i = 0; i < 10; i++) {
//            new Thread(() ->{
//                LazyMan.getInstance();
//            }).start();
//        }

        //反射
//        LazyMan instance = LazyMan.getInstance();
        Field flag = LazyMan.class.getDeclaredField("flag");
        flag.setAccessible(true);
        Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);
        LazyMan instance2 = declaredConstructor.newInstance();
        flag.set(instance2,false);


        LazyMan instance3 = declaredConstructor.newInstance();

        System.out.println(instance3);
        System.out.println(instance2);
    }
}
```

> 静态内部类

```java
public class Holder {

    private Holder(){}

    public static Holder getInstance(){
        return InnerClass.HOLDER;
    }

    public static class InnerClass{
        private static final Holder HOLDER = new Holder();
    }
}
```

> enum类

```java
/**
 * @author: lyj
 * @date: 2022/7/31 16:22
 * enum是什么？ 本身也是一个class
 *
 */
public enum EnumSingle {

    INSTANCE;

    public EnumSingle getInstance(){
        return INSTANCE;
    }
}

class Test{
    //NoSuchMethodException: com.lyj.demo01.singer.EnumSingle.<init>()
    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        EnumSingle enumSingle = EnumSingle.INSTANCE;
        Constructor<EnumSingle> declaredConstructor = EnumSingle.class.getDeclaredConstructor(String.class,int.class);
        declaredConstructor.setAccessible(true);
        EnumSingle instance = declaredConstructor.newInstance();

        System.out.println(enumSingle);
        System.out.println(instance);
    }
}
```

## 17、深入理解CAS

> 什么是CAS

```java
public class CASDemo {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(2020);

        /**
         * 期望、更新
         * 如果我期望的值达到了，那么就更新，否则，就不更新 CAS是CPU的并发器
         */
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());

        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());
    }
}
```

CAS：比较当前工作内存中的值和主内存的值，如果这个值是期望的，那么则执行操作！如果不是一直循环

**缺点：**

1、循环会耗时

2、一次性只能保证一个共享变量的原子性

3、ABA问题

> CAS: ABA问题（狸猫换太子）

```java
public class CASDemo {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(2020);

        /**
         * 期望、更新
         * 如果我期望的值达到了，那么就更新，否则，就不更新 CAS是CPU的并发器
         */
        //=======捣乱的线程=========
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());
        System.out.println(atomicInteger.compareAndSet(2021, 2020));
        System.out.println(atomicInteger.get());

        //=======捣乱的线程=========
        System.out.println(atomicInteger.compareAndSet(2020, 6666));
        System.out.println(atomicInteger.get());
    }
}
```

## 18、原子引用

带版本号的原子引用

```java
//解决ABA问题
public class CASDemo {
    public static void main(String[] args) {
        //AtomicInteger atomicInteger = new AtomicInteger(2020);
        //如果泛型是包装类，注意引用问题
        AtomicStampedReference<Integer> atomicInteger = new AtomicStampedReference<>(1,1);

        new Thread(() ->{
            //获得版本号
            int stamp = atomicInteger.getStamp();
            System.out.println("a1=" + stamp);

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(atomicInteger.compareAndSet(1, 2, atomicInteger.getStamp(), atomicInteger.getStamp() + 1));
            System.out.println("a2=" + atomicInteger.getStamp());

            System.out.println(atomicInteger.compareAndSet(2, 1, atomicInteger.getStamp(), atomicInteger.getStamp() + 1));
            System.out.println("a3=" + atomicInteger.getStamp());
        },"a").start();

        new Thread(() ->{
            //获得版本号
            int stamp = atomicInteger.getStamp();
            System.out.println("b1=" + stamp);

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(atomicInteger.compareAndSet(1, 6, atomicInteger.getStamp(), atomicInteger.getStamp() + 1));
            System.out.println("b2=" + atomicInteger.getStamp());
        },"b").start();



        /**
         * 期望、更新
         * 如果我期望的值达到了，那么就更新，否则，就不更新 CAS是CPU的并发器
         */
        //=======捣乱的线程=========
       /* System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());
        System.out.println(atomicInteger.compareAndSet(2021, 2020));
        System.out.println(atomicInteger.get());

        //=======捣乱的线程=========
        System.out.println(atomicInteger.compareAndSet(2020, 6666));
        System.out.println(atomicInteger.get());*/
    }
}
```

## 19、各种锁的理解

### 19.1 公平锁、非公平锁

公平锁：非常公平，不能插队

非公平锁：非常不公平，可以插队(默认都是非公平锁)

```java
public ReentrantLock() {
        sync = new NonfairSync();
 
}

public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
}
```

### 19.2 可重入锁

可重入锁(递归锁)

>Synchronize

```java
public class Demo01 {
    public static void main(String[] args) {
        Phone phone = new Phone();

        new Thread(() ->{
            phone.sms();
        },"A").start();

        new Thread(() ->{
            phone.sms();
        },"B").start();
    }
}

class Phone{
    public synchronized void sms(){
        System.out.println(Thread.currentThread().getName() + "sms");
        call();
    }

    public synchronized void call(){
        System.out.println(Thread.currentThread().getName() + "call");
    }
}
```

> lock

```java
public class Dmeo02 {
    public static void main(String[] args) {
        Phone2 phone = new Phone2();

        new Thread(() ->{
            phone.sms();
        },"A").start();

        new Thread(() ->{
            phone.sms();
        },"B").start();
    }
}

class Phone2{
    Lock lock = new ReentrantLock();

    public void sms(){
        //锁必须配对不然会死锁
        lock.lock();
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "sms");
            call();
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void call(){
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "call");
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
```

### 19.3 自旋锁

```java
//自定义自旋锁
public class SpinLockDemo {
    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    /**
     * 加锁
     */
    public void myLock(){
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName() + "===> myLock");

        while (!atomicReference.compareAndSet(null,thread)){}
    }

    /**
     * 加锁
     */
    public void myUnLock(){
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName() + "===> myUnLock");

        atomicReference.compareAndSet(thread,null);
    }
}
//测试
public class TestSpinLock {
    public static void main(String[] args) throws InterruptedException {
//        ReentrantLock reentrantLock = new ReentrantLock();
//        reentrantLock.lock();
//        reentrantLock.unlock();

        /**
         * 底层使用的自旋锁CAS
         */
        SpinLockDemo lock = new SpinLockDemo();

        new Thread(() ->{
            lock.myLock();
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                lock.myUnLock();
            }
        },"t1").start();

        TimeUnit.SECONDS.sleep(1);

        new Thread(() ->{
            lock.myLock();
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                lock.myUnLock();
            }
        },"t2").start();


        lock.myUnLock();
    }
}
```

### 19.4 死锁

**死锁测试**

```java
class MyThread implements Runnable{

    private final String lockA;
    private final String lockB;

    public MyThread(String lockA,String lockB){
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA){
            System.out.println(Thread.currentThread().getName() + "lock: " + lockA + "get =>" + lockB);

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            synchronized (lockB){
                System.out.println(Thread.currentThread().getName() + "lock: " + lockB + "get =>" + lockA);
            }
        }
    }
}
```

1、使用`jps -l`定位进程号

2、使用`jstack` 进程号找到死锁
