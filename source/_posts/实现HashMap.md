---
title: 实现HashMap(JDK1.7)
date: 2023-09-18 20:18:43
tags: HashMap
categories: Java
keywords: 实现HashMap(JDK1.7)
cover: https://z1.ax1x.com/2023/09/23/pPT37i4.jpg
description: HashMap是数据结构中的哈希表在Java中的具体实现。JDK1.7的HashMap采用数组+链表的方式实现。
---
# 哈希表
HashMap是数据结构中的哈希表在Java中的具体实现。
哈希表(Hash Table)是一种常见的数据结构，也被称为散列表。它通过将键映射到存储桶(Buckets)中的位置来高效的存储和检索数据。哈希表使用一个哈希函数来计算键的散列值，然后将散列值映射到存储桶的索引上。
哈希表由`存储桶数组`和`哈希函数`组成。

# 存储桶数组
存储桶数组(Bucket Array)是哈希表中用于存储数据的主要结构，它是由一个固定数量的存储桶(Bucket)组成的数组。每个存储桶可以存储一个或多个元素，每个元素都由一个键值对(key-value pair)组成，其中键用于计算哈希值，而值代表实际存储的数据。

# 哈希函数
哈希函数(Hash Function)是一种算法，它可以将任意长度的输入数据映射成固定长度输出，这个输出通常称为哈希值。

## 哈希函数构造
哈希函数的构造是哈希表实现的关键之一。常见的哈希函数构造方法：
1. 直接定值法：直接通过键值映射到对应的数组位置。
2. 数字分析法：取键的某些数字作为映射的位置。
3. 平方取中法：取键平放的中间几位作为映射的位置。
4. 折叠法：将键分割成数位相同的几段，然后把它们的叠加和作为映射的位置。
5. 随机数法：使用一个随机数发生器生成一个随机数作为哈希值。
6. 除留余数法：键除以一个足够大的质数，所得的余数作为哈希地址。

# 哈希冲突
哈希冲突(Hash Collision)是指两个或多个不同的元素被哈希函数映射到相同的哈希值。哈希表设计的目标是将元素均匀的分布在不同的槽位上，以避免冲突和提高效率。然而，由于哈希函数的限制和输入数据的多样性，冲突是难以避免的。

## 解决方法
哈希冲突(Hash Collision)解决方法：
1. 开发地址发：当发生冲突的时候，通过线性探测、二次探测、双重哈希等方法，在哈希表中寻找到下一个可用的位置。
2. 链地址法：当发生冲突的时候，在哈希表的每一个槽位上维护一个链表，将冲突的元素存储在同一个槽位上。
3. 再哈希法：构造多个哈希函数，在发生冲突时，更换哈希函数，直到找到空闲位置。

# 实现HashMap
整体设计方案：
* 哈希函数：采用`hashCode()`加上除留余数法
* 哈希冲突解决方法：链地址法

## 定义成员变量
定义`MyHashMap`的成员变量：
1. 定义默认容量
2. 定义默认负载因子
3. 定义MyHashMap的大小
4. 定义桶数组
	自定义内部节点类来实现桶数组
```java
//自定义内部节点类
class Node<K, V>{  
    /**  
     * 键  
     */  
    private K key;  
    /**  
     * 值  
     */  
    private V value;  
  
    /**  
     * 后继  
     */  
    private Node<K, V> next;  
  
    public Node(K key, V value) {  
        this.key = key;  
        this.value = value;  
    }  
  
    public Node(K key, V value, Node<K, V> next) {  
        this.key = key;  
        this.value = value;  
        this.next = next;  
    }  
}
```
```java
/**  
 * 默认容量  
 */  
private static final int DEFAULT_CAPACITY = 16;  
/**  
 * 默认负载因子  
 */  
private static final float DEFAULT_LOAD_FACTOR = 0.75f;  
/**  
 * MyHashMap的大小  
 */  
private int size;  
/**  
 * 桶数组  
 */  
private Node<K, V>[] buckets;
```

## 初始化
定义两个构造函数，一个为无参构造函数，并为桶设置默认容量；另一个为有参构造函数，通过参数指定桶的容量。
```java
/**  
 * 无参构造方法，设置桶数组大小为默认容量  
 */  
public MyHashMap(){  
    this.buckets = new Node[DEFAULT_CAPACITY];  
    this.size = 0;  
}  
  
/**  
 * 有参构造器，设置桶的容量  
 * @param capacity int  
 */
public MyHashMap(int capacity){  
    buckets = new Node[capacity];  
    this.size = 0;  
}
```

## 哈希函数
通过`hashCode()`和除留余数法实现哈希函数，来获取哈希地址。
```java
/**  
 * 哈希函数，获取哈希地址  
 * @param key K  
 * @return int  
 */
private int hash(K key, int length){  
    return Math.abs(key.hashCode() & (length - 1));  
}
```

## 添加元素
**添加元素逻辑：**
* 判断是否需要扩容
* 获取元素插入位置
* 如果要插入的位置为空，则插入元素
* 如果不为空，则遍历链表
* 如果元素的key和节点的相同，则覆盖，否则新建节点插入链表头部(JDK7的HashMap使用头插法)

```java
/**  
 * 添加方法  
 * @param key K  
 * @param value V  
 */public void put(K key, V value){  
    if (size >= buckets.length * DEFAULT_LOAD_FACTOR) {  
        resize();  
    }  
    putVal(key, value, buckets);  
}  
  
/**  
 * 将元素存入Node数组  
 * @param key K  
 * @param value V  
 * @param table Node  
 */private void putVal(K key, V value, Node<K,V>[] table) {  
    //获取插入位置  
    int index = hash(key);  
    Node<K, V> node = table[index];  
    //如果要插入的位置为空，则插入元素  
    if (node == null) {  
        table[index] = new Node<>(key, value);  
        size++;  
        return;    }  
    //如果不为空，说明发生了哈希冲突，遍历链表使用链地址法  
    while (node != null){  
        //如果元素的key和节点的相同，则覆盖  
        if ((node.key.hashCode() == key.hashCode()) && (node.key == key || node.key.equals(key))){  
            node.value = value;  
            return;        }  
        node = node.next;  
    }  
    //新建节点插入链表头部  
    Node<K, V> kvNode = new Node<>(key, value, table[index]);  
    table[index] = kvNode;  
    size++;  
}
```

## 扩容
**扩容逻辑：**
* 创建两倍容量的新数组
* 将当前桶中的元素重新哈希到新的数组
	* 重新MyHashMap设置大小
	* 将旧的桶数组的元素移到新的桶数组中
* 将扩容后的数组赋值给原来的桶数组

```java
/**  
 * 重新哈希元素  
 * @param newNode Node<K,V>[]  
 */private void rehash(Node<K,V>[] newNode) {  
    //重新MyHashMap设置大小  
    this.size = 0;  
    //将旧的桶数组的元素移到新的桶数组中  
    for (int i = 0; i < buckets.length; i++){  
        if (buckets[i] == null){  
            continue;  
        }  
        Node<K, V> node = buckets[i];  
        while (node != null){  
            //将元素放入新的桶数组  
            putVal(node.key, node.value, newNode);  
            node = node.next;  
        }  
    }  
}
```

## 获取元素
**获取元素逻辑：**
* 通过哈希获取地址
* 如果链表存在遍历链表

```java
/**  
 * 获取元素  
 * @param key K  
 * @return V  
 */public V get(K key){  
    //获取地址  
    int index = hash(key, buckets.length);  
    if (buckets[index] == null) {  
        return null;  
    }  
    Node<K, V> node = buckets[index];  
    while (node != null){  
        if ((node.key.hashCode() == key.hashCode()) && (node.key == key || node.key.equals(key))){  
            return node.value;  
        }  
        //如果链表存在遍历链表  
        if (node.next != null){  
            node = node.next;  
        }  
    }  
    return null;  
}
```

## 删除元素
**删除元素逻辑：**
* 如果key存在则删除，不存在直接返回

```java
/**  
 * 移除元素  
 * @param key K  
 */public void remove(K key){  
    //获取地址  
    int index = hash(key, buckets.length);  
    if (buckets[index] == null) {  
        return;  
    }  
    if (buckets[index].key.equals(key)) {  
        buckets[index] = buckets[index].next;  
        return; // 移除成功，结束操作  
    }  
    Node<K, V> node = buckets[index];  
    while (node.next != null) {  
        if (node.next.key.equals(key)) {  
            node.next = node.next.next;  
            return; // 移除成功，结束操作  
        }  
        node = node.next; // 继续遍历链表下一个节点  
    }  
}
```

## 完整代码
```java
public class MyHashMap<K, V> {  
  
    class Node<K, V>{  
        /**  
         * 键  
         */  
        private K key;  
        /**  
         * 值  
         */  
        private V value;  
  
        /**  
         * 后继  
         */  
        private Node<K, V> next;  
  
        public Node(K key, V value) {  
            this.key = key;  
            this.value = value;  
        }  
  
        public Node(K key, V value, Node<K, V> next) {  
            this.key = key;  
            this.value = value;  
            this.next = next;  
        }  
    }  
  
    /**  
     * 默认容量  
     */  
    private static final int DEFAULT_CAPACITY = 16;  
    /**  
     * 默认负载因子  
     */  
    private static final float DEFAULT_LOAD_FACTOR = 0.75f;  
    /**  
     * MyHashMap的大小  
     */  
    private int size;  
    /**  
     * 桶数组  
     */  
    private Node<K, V>[] buckets;  
  
    /**  
     * 无参构造方法，设置桶数组大小为默认容量  
     */  
    public MyHashMap(){  
        this.buckets = new Node[DEFAULT_CAPACITY];  
        this.size = 0;  
    }  
  
    /**  
     * 有参构造器，设置桶的容量  
     * @param capacity int  
     */    public MyHashMap(int capacity){  
        buckets = new Node[capacity];  
        this.size = 0;  
    }  
  
    /**  
     * 哈希函数，获取哈希地址  
     * @param key K  
     * @return int  
     */    private int hash(K key, int length){  
        return Math.abs(key.hashCode() & (length - 1));  
    }  
  
    /**  
     * 添加方法  
     * @param key K  
     * @param value V  
     */    public void put(K key, V value){  
        if (size >= buckets.length * DEFAULT_LOAD_FACTOR) {  
            resize();  
        }  
        putVal(key, value, buckets);  
    }  
  
    /**  
     * 将元素存入Node数组  
     * @param key K  
     * @param value V  
     * @param table Node  
     */    private void putVal(K key, V value, Node<K,V>[] table) {  
        //获取插入位置  
        int index = hash(key, table.length);  
        Node<K, V> node = table[index];  
        //如果要插入的位置为空，则插入元素  
        if (node == null) {  
            table[index] = new Node<>(key, value);  
            size++;  
            return;        }  
        //如果不为空，说明发生了哈希冲突，遍历链表使用链地址法  
        while (node != null){  
            //如果元素的key和节点的相同，则覆盖  
            if ((node.key.hashCode() == key.hashCode()) && (node.key == key || node.key.equals(key))){  
                node.value = value;  
                return;            }  
            node = node.next;  
        }  
        //新建节点插入链表头部  
        Node<K, V> kvNode = new Node<>(key, value, table[index]);  
        table[index] = kvNode;  
        size++;  
    }  
  
    /**  
     * 扩容  
     */  
    private void resize() {  
        //创建两倍容量的新数组  
        Node<K, V>[] newNode = new Node[buckets.length * 2];  
        rehash(newNode);  
        buckets = newNode;  
    }  
  
    /**  
     * 重新哈希元素  
     * @param newNode Node<K,V>[]  
     */    private void rehash(Node<K,V>[] newNode) {  
        //重新MyHashMap设置大小  
        this.size = 0;  
        //将旧的桶数组的元素移到新的桶数组中  
        for (int i = 0; i < buckets.length; i++){  
            if (buckets[i] == null){  
                continue;  
            }  
            Node<K, V> node = buckets[i];  
            while (node != null){  
                //将元素放入新的桶数组  
                putVal(node.key, node.value, newNode);  
                node = node.next;  
            }  
        }  
    }  
  
    /**  
     * 获取元素  
     * @param key K  
     * @return V  
     */    public V get(K key){  
        //获取地址  
        int index = hash(key, buckets.length);  
        if (buckets[index] == null) {  
            return null;  
        }  
        Node<K, V> node = buckets[index];  
        while (node != null){  
            if ((node.key.hashCode() == key.hashCode()) && (node.key == key || node.key.equals(key))){  
                return node.value;  
            }  
            //如果链表存在遍历链表  
            if (node.next != null){  
                node = node.next;  
            }  
        }  
        return null;  
    }  
  
    /**  
     * 移除元素  
     * @param key K  
     */    public void remove(K key){  
        //获取地址  
        int index = hash(key, buckets.length);  
        if (buckets[index] == null) {  
            return;  
        }  
        if (buckets[index].key.equals(key)) {  
            buckets[index] = buckets[index].next;  
            return; // 移除成功，结束操作  
        }  
        Node<K, V> node = buckets[index];  
        while (node.next != null) {  
            if (node.next.key.equals(key)) {  
                node.next = node.next.next;  
                return; // 移除成功，结束操作  
            }  
            node = node.next; // 继续遍历链表下一个节点  
        }  
    }  
  
    /**  
     * 返回MyHashMap大小  
     * @return int  
     */    public int size(){  
        return this.size;  
    }  
}
```

## 测试
```java
public class Test {  
    public static void main(String[] args) {  
        MyHashMap<String, String> map = new MyHashMap<>();  
        map.put("test", "lyj is test");  
        map.remove("test");  
        System.out.println(map.get("test"));  
    }  
}
```