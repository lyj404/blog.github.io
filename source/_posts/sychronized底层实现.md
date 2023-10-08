---
title: sychronized底层实现
date: 2023-09-20 16:14:47
tags: sychronized
categories: Java
cover: https://z1.ax1x.com/2023/09/23/pPT37i4.jpg
description: sychronized是Java的关键字，用于实现线程之间的同步，保证多个线程对共享资源的安全访问，也被称为同步锁
---
# sychronized
`sychronized`是Java的关键字，用于实现线程之间的同步，保证多个线程对共享资源的安全访问，也被成为同步锁。
`sychronized`的作用是保证在同一时刻，被修饰的代码块或者方法只会有一个线程执行，以达到保证并发安全的效果。

# sychronized的使用方式
`sychronized`主要有三种使用方式：
1. **修饰实例方法：对于当前实例加锁**
```java
public sychronized void methodName(){
}
```
2. **修饰静态方法：对于当前类对象加锁**
```java
public static sychronized void methodName(){}
```
3. **修饰代码块：对给定的对象加锁**
```java
sychronized(this){}
```

# sychronized的底层实现
sychronized的底层实现依赖于JVM，因此sychronized的与JVM内存的存储：Java对象头、以及monitor对象监视器有关。

## Java对象头
在JVM中，对象在内存中的存储布局，分为三个部分：
* 对象头
* 实例数据
* 对齐填充

在Java中，每个对象都有一个对象头（Object Header），它包含了一些用于管理对象的元数据信息。
对象头的具体结构和内容取决于JVM的实现和配置，并且可能会因为不同版本的JVM和不同的操作系统而有所区别。一些常见的对象头结构和字段如下：
1. Mark Word（标记字段）：占用对象头的一部分，用于存储对象的标记状态和锁信息。sychronized使用的锁对象是存储在Java的对象头的标记字段里。标记字段通常包含了以下信息
	* 对象的哈希码（用于支持`hashCode()`方法）
	* 锁状态（是否被锁定、偏向锁或轻量级锁的标识等）
	* 并发标记（用于支持垃圾回收、对象分代等）
	* 偏向锁的线程ID
	* 偏向时间戳
2. 类型指针：指向对象的类元数据的指针，用于确定对象属于哪个类。
3. 数组长度：如果对象是数组类型，则会包含数字的长度信息。

[![对象头](https://z1.ax1x.com/2023/09/20/pP5h0gI.png)](https://imgse.com/i/pP5h0gI)

>除常见字段外，对象头可能还包含其他与垃圾回收、锁机制和JIT编译等相关的信息。并且对象头的大小是固定的，在不同JVM上可能有所不同。例如：在32位JVM上对象头通常占8个字节，而在64位JVM上通常占12或16个字节。

## Monitor
通过`javap -c -s -v -l SynchronizedDemo.class`命令反编译代码，可以看到相对应的字节码指令。
`sychronized`在修饰代码块的时候，JVM采用`monitorenter`和`monitorexit`两个指令来实现同步，`monitorenter`指令指向同步代码块开始的位置，`monitorexit`指令指向同步代码块结束的位置。
[![Monitorenter](https://z1.ax1x.com/2023/09/20/pP5hvx1.png)](https://imgse.com/i/pP5hvx1)
`sychronized`在修饰实例方法的时候，JVM采用`ACC_SYNCHRONIZED`标识符来实现同步，通过这个标识来指明这是一个同步方法
[![acc_synchronized](https://z1.ax1x.com/2023/09/20/pP5hj2R.png)](https://imgse.com/i/pP5hj2R)
>上述的三个命令都是基于`Monitor`实现的。

实例对象结构中有对象头，对象头中有一个结构Mark Word，Mark Word的指针指向了`Monitor`。
`Monitor`是一种同步机制，在JVM中，`Monitor`的实现是由ObjectMonitor实现的，称之为`Monitor`锁。
```c++
ObjectMonitor() {
    _header       = NULL;
    _count        = 0; // 由于synchronized是可重入锁，count用于记录当前对象锁拥有者线程获取锁的次数
    _waiters      = 0,
    _recursions   = 0;
    _object       = NULL;
    _owner        = NULL;
    _WaitSet      = NULL; // 调用了wait方法，处于WAIT/TIME_WAIT的线程，会被加入到WaitSet
    _WaitSetLock  = 0 ;
    _Responsible  = NULL ;
    _succ         = NULL ;
    _cxq          = NULL ;
    FreeNext      = NULL ;
    _EntryList    = NULL ; // 处于等待锁block状态的线程，会被加入到EntryList
    _SpinFreq     = 0 ;
    _SpinClock    = 0 ;
    OwnerIsThread = 0 ;
  }
```

对象监视器主要基于`ObjectMonitor`结构体中的EntryList、WaitSet两个队列以及计数器count实现的。
1. 当有多个线程同时想要获取某个对象锁时，首先会进入EntryList队列。
2. 当某个线程获取到对象锁时，线程成为对象锁的拥有者，准备开始运行加锁代码时，执行字节码指令`monitorenter`，此时`count++`。
3. 当对象拥有者再次获取锁时，由于`sychronized`锁是可重入的，此时`count++`，而不是在EntryList队列中阻塞等待锁。
4. 每个加锁代码块**运行完成或因异常退出时**，都会执行`monitorexit`指令，此时`count--`，当count等于0时，拥有对象锁的线程释放锁。
5. 拥有锁的线程在运行的过程中调用了`wait()`方法，线程将会进入到WaitSet队列中，等待被`notify()`唤醒或等待的时间到了，才可能再次成为锁的拥有者。

# 锁升级
锁解决了数据的安全性问题，但是同时带来了性能的下降，因此在JDK1.6之后对于`sychronized`锁做了一些优化，为了减少获得锁和释放锁带来ed开销，引入了偏向锁、轻量级锁、重量级锁。
[![锁升级](https://z1.ax1x.com/2023/09/20/pP5Iw8J.png)](https://imgse.com/i/pP5Iw8J)
**无锁**
没有对资源进行锁定，所有的线程都能访问并修改同一个资源，但是同时只有一个线程能够成功。
**偏向锁**
大多数情况下，锁不仅不存在多线程竞争，而且总是同一线多次获得，为了让线程获取锁的代价更低引入了偏向锁。偏向锁会偏向第一个获得它的线程，如果在接下来的执行过程中，该锁没有被其他线程获取，则持有偏向锁的线程永远不需要同步。
**轻量级锁**
当偏向锁被另外的线程获取时，偏向锁会升级为轻量锁，其他线程会通过自旋的方式尝试获取锁，不会阻塞，从而提高性能。
**重量级锁**
其他线程试图获取锁时，都会被阻塞，只有持有锁的线程释放锁之后才会唤醒这些线程。

[![Mark Word](https://z1.ax1x.com/2023/09/20/pP5Ivxs.png)](https://imgse.com/i/pP5Ivxs)
1. 当无锁的时候，Mark Word记录对象的hashCode，锁标志位是01，是否偏向锁是0
2. 当对象被当做同步锁并有一个线程抢到锁时，锁标志位还是01，但是是否偏向锁变成了01，并且记录了当前线程的ID
3. 当有其他线程来获取锁时，发现处于偏向锁状态，会使用CAS操作来尝试获取锁，并且这个操作时有可能成功的，获取获取成功，Mark Word中保存的线程ID将会替换成当前线程的；如果抢锁失败，偏向锁会升级成为轻量级锁，JVM会在当前线程的线程栈开辟一块单独的空间，保存指向对象锁Mark Word的指针，同时在对象锁Mark Word中保存指向这片空间的指针，如果保存成功，代表线程抢到了锁，就把Mark Word的锁标志位改为00
4. 轻量级锁抢锁失败，JVM会使用自旋锁，自旋锁不是一个锁，只是代表不断地重试，尝试抢锁。自旋锁在JDK1.7是默认开始的，且自旋次数由JVM决定，自旋锁重试之后任然失败，将会升级成为重量级锁，锁标志位改为10。
