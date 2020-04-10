# HashMap

## 1、参数的含义

### 1.1 为什么传的初始值为2的幂次方

```java
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " +
                                           initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +
                                           loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
}
```

```java
//使用异或运算 和 移位运算 把低位的二级制位数都变成1
//最后加1返回出去，找到离当前值-1最近的2的幂次方的值
//最大移位是16个移位运算，因为如果大于十六的话直接取最大值 转上个代码块的第六行
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

### 1.2  key的hash代码

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

### 1.3 负载因子

```java
 /**   0:    0.60653066
     * 1:    0.30326533
     * 2:    0.07581633
     * 3:    0.01263606
     * 4:    0.00157952
     * 5:    0.00015795
     * 6:    0.00001316
     * 7:    0.00000094
     * 8:    0.00000006
     * 每一个节点存放数据的概率
     **/
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

### 1.4 实际存储数据的结构

```java
/**
     * The table, initialized on first use, and resized as
     * necessary. When allocated, length is always a power of two.
     * (We also tolerate length zero in some operations to allow
     * bootstrapping mechanics that are currently not needed.)
     */
transient Node<K,V>[] table; 
```

### 1.5 存放具体元素的集合

```java
/**
     * Holds cached entrySet(). Note that AbstractMap fields are used
     * for keySet() and values().
     */
transient Set<Map.Entry<K,V>> entrySet;
```

### 1.6 实际存储的数量

```java
/**
     * The number of key-value mappings contained in this map.
     */
transient int size;
```

### 1.7 记录hashmap实际被修改的次数

```java
/**
     * The number of times this HashMap has been structurally modified
     * Structural modifications are those that change the number of mappings in
     * the HashMap or otherwise modify its internal structure (e.g.,
     * rehash).  This field is used to make iterators on Collection-views of
     * the HashMap fail-fast.  (See ConcurrentModificationException).
     */
transient int modCount;
//每次扩容或者修改map结构的计数器
```

### 1.8 临界值

```java
/**
     * The next size value at which to resize (capacity * load factor).
     *
     * @serial
     */
// (The javadoc description is true upon serialization.
// Additionally, if the table array has not been allocated, this
// field holds the initial array capacity, or zero signifying
// DEFAULT_INITIAL_CAPACITY.)
int threshold;
```

### 1.9 哈希表的加载因子

```java
/**
     * The load factor for the hash table.
     *
     * @serial
     */
final float loadFactor;
//用来衡量hashmap满的程度，实时的加载因子
```

## 2、1.7和1.8的差别

### 2.1 创建区别

- 1.7在new的时候就会创建数组
- 1.8的时候只给负载因子给一个默认值，在put的方法会判断是不是已经创建了，没有创建则创建一个

### 2.2 结构区别

- 1.7使用的数组加链表，并且默认使用头插法 
- 1.8使用数组，链表加红黑树，默认使用尾插法

## 3、构造函数

```java
public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
}

//把m里面的元素复制到当前map中
final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
    //获取m元素个数
    int s = m.size();
    if (s > 0) {
        //判断是否初始化
        if (table == null) { // pre-size
            //未初始化，s是元素个数
            //+1.0f让小数向上取整，减少resize的次数
            float ft = ((float)s / loadFactor) + 1.0F;
            //是否小于最大值，小于的话强转成int，大于的话用最大值替代
            int t = ((ft < (float)MAXIMUM_CAPACITY) ?
                     (int)ft : MAXIMUM_CAPACITY);
            //是否大于阈值，如果大于阈值，则初始化阈值
            if (t > threshold)
                threshold = tableSizeFor(t);
        }
        else if (s > threshold)
            //已初始化，且元素大于阈值，则扩容
            resize();
        //将m中所有的元素添加到hashmap中
        for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
            K key = e.getKey();
            V value = e.getValue();
            putVal(hash(key), key, value, false, evict);
        }
    }
}
```

## 4、put方法

### 4.1 hash

```java
static final int hash(Object key) {
    int h;
    //为什么要右移16位
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200317/123701624.png)

说明:高16 位不变，低16位和高16位进行了一个异或运算。

问题:为什么要怎么操作

**如果当length的长度很小，假设是16，那么length-1 --> 111，这样的值和hashCode.直接做按位与操作，实际上只使用了哈希值的后4位，高位将没有任何意义。如果当哈希值高位变化很大，低位变化很小，这样就非常容易造成哈希冲突，所以这里要把高位和低位都利用起来，从而解决这个问题。**==为了防止hash冲突==

---

### 4.2 实现步骤： 

1. 先通过hash值计算出key映射到哪个桶:

2. 如果桶上没有碰撞冲突，则直接插入;

3. 如果出现碰撞冲突了，则需要处理冲突:

   1. 如果该桶使用红黑树处理冲突，则调用红黑树的方法插入数据;
   2. 否则采用传统的链式方法插入。如果链的长度达到临界值，则把链转变为红黑树;

4. 如果桶中存在重复的键，则为该键替换新值value;

5. 如果sie大于阈值threshold,则进行扩容;

   具体的方法如下: 

   ```java
   public V put(K key, V value) {
       return putVal(hash(key), key, value, false, true);
   }
   ```

   1. HashMap只提供了put用于添加元素，putVal 方法只是给put方法调用的一一个方法， 并没有提供给用户使用。所以我们重点看 putVal方法。
   2. 我们可以看到在putVal0方法中key在这里执行了一下hash()方法,来看一- 下Hash方法是如何实现的。

```java
/**
 * Implements Map.put and related methods.
 *
 * @param hash key的hash值
 * @param key  key原本的值
 * @param value 要存储的value的值
 * @param onlyIfAbsent 如果为true，则不更改原有的值
 * @param evict 如果为false，table为为创建状态
 * @return previous value, or null if none
 */
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; 
    Node<K,V> p; 
    //n存放数组长度，i存放key计算后的hash值
    int n, i;
    //table 表示存储在Map中的元素的数组
    if ((tab = table) == null || (n = tab.length) == 0)
        //如果为空就resize实例化一个数组
        n = (tab = resize()).length;
    //使用hash的 & 运算，相当于 % 运算，当是2的幂次方时，&运算，当都是1的时候才为1
    //将获取到当前下表位置的node赋值给p
    if ((p = tab[i = (n - 1) & hash]) == null)
        //创建一个node next是指向下一个节点的指针
        //直接创建一个node节点赋值给当前下边元素
        tab[i] = newNode(hash, key, value, null);
    else {
        //当前下标位置不为null
        Node<K,V> e; 
        K k;
        //寻找key-value所在node的位置
       	//该位置上数据的hash等于上面计算的hash，并且value也相等
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            //将该位置的节点赋值给e
            e = p;
        else if (p instanceof TreeNode)
            //判断当前下标数据类型是否是红黑树
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            //不是红黑树，当前下标位置的key与也要插入的key
            for (int binCount = 0; ; ++binCount) {
                //如果等于null说明e是表尾
                if ((e = p.next) == null) {
                    //直接将数据写入下一个节点
                    p.next = newNode(hash, key, value, null);
                    //判断是否到达转为红黑树的阈值
                    //7表示第八个节点
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        //转为红黑树
                        treeifyBin(tab, hash);
                    break;
                }
                //如果当前位置的key与存放位置的key相同，跳出
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                //说明新添加的元素的节点和当前节点不相同，继续找下一个元素
                p = e;
            }
        }
        //e不为空，说明上面找到了一个空间存储key-value的node
        if (e != null) { // existing mapping for key
            //拿到旧值Value
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                //新的值赋值给节点
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}

```

## 5、数组扩容

- 当hashmap中元素个数超过 数组长度*负载因子 时扩容 
- 当hashmap的其中一个链表的对象个数达到了8个，此时如果数组长度没有达到64，那么HashMap也会进行扩容。如果达到了64个，那么这个链表会变成红黑树，节点类型由Node变成TreeNode

---

1.7的时候需要`重新计算hash`，但是1.8的hash得到要么不是原来的位置，要么就是在原来的位置+原来容器的大小

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200318/103720829.png)

- 在元素重新计算hash之后，因为 n 变为原来的 2 倍，那么 n-1 的标记范围在高位多 1
- 因此在扩容hashmap的时候，不需要重新计算hash，只需要看看原来的 hash 值新增的那个 bit 是 1 还是 0 就可以了。是 0 的话下标不变，是 1 的话下表变为原来的 容量的值 + 原来的 hash 值

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200318/170932344.png)

### resize

```java
final Node<K,V>[] resize() {
    // 得到旧的hash桶
    Node<K,V>[] oldTab = table;
    // 获取未扩容时的数组长度
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    // 旧的临界值
    int oldThr = threshold;
    // 定义新的容量和临界值
    int newCap, newThr = 0;
    if (oldCap > 0) {
        // 旧容量大于0
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            //不扩容，直接返回该数组
            return oldTab;
        }
        //扩大到两倍判断 newCap是否小于最大容量
        //原数组程度大于等于数组初始化长度
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            //当前容量在默认和最大值的一半之间
            //新的临界值为当前临界值的2倍
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        //判断旧的临界值,旧容量为0，当前临界值不为零，让新的临界值等于当前临界值
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        // 当当前容量和临界值都为0的时候，让新的容量等于默认值，临界值=初始容量*加载因子
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    //经过上面对新临界值的计算后如果还为0
    if (newThr == 0) {
        //计算新的临界值
        float ft = (float)newCap * loadFactor;
        //判断新容量小于最大值，计算出的临界值也小于最大值
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    //使用新的容量创建新数组
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    //赋值给hash桶
    table = newTab;
    if (oldTab != null) {
        //遍历旧桶，把旧桶中的元素重新计算下标赋值给新桶
        //j 表示数组下标
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            //把当前数组赋值给e，且e不为null
            if ((e = oldTab[j]) != null) {
                //置空，原本的数据可以被回收
                oldTab[j] = null;
                //判断下一个节点如果是空
                if (e.next == null)
                    //如果没有下一个节点，说明不是链表，当前桶上只有一个键值对，直接计算下标后插入
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    //节点是红黑树，进行切割操作
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    //到这里说明该位置元素是链表
                    //loHead代表链表头结点，loTail代表数据链表
                    Node<K,V> loHead = null, loTail = null;
                    //hiHead代表新位置链表头结点，hiTail新位置链表
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    //循环链表
                    do {
                        next = e.next;
                        //如果为true，则在resize后不需要移动位置
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

---

## 6、remove()

- 根据 key 删除元素

```java
//删除是有返回值的，返回值是被删除的key所对应的value
public V remove(Object key) {
    Node<K,V> e;
    return (e = removeNode(hash(key), key, null, false, true)) == null ?
        null : e.value;
}
```

```java
final Node<K,V> removeNode(int hash, Object key, Object value,
                           boolean matchValue, boolean movable) {
    Node<K,V>[] tab; 
    Node<K,V> p;
    int n, index;
    //判断 tab 是否为 null
    //获取 tab 的长度，并判断是不是 0 
    if ((tab = table) != null && (n = tab.length) > 0 &&
        //计算key对应的 hash ，取出对应的 value 赋值给 p ，并判断是不是为 null
        (p = tab[index = (n - 1) & hash]) != null) {
        Node<K,V> node = null, e; 
        K k; 
        V v;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            //当前第一个位置的元素是要找的元素
            node = p;
        //取出下一个节点赋值给 e ，并且不为 null
        else if ((e = p.next) != null) {
            if (p instanceof TreeNode)
                node = ((TreeNode<K,V>)p).getTreeNode(hash, key);
            else {
                //找节点
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key ||
                         (key != null && key.equals(k)))) {
                        node = e;
                        break;
                    }
                    p = e;
                } while ((e = e.next) != null);
            }
        }
        //判断 node 不为空
        if (node != null && (!matchValue || (v = node.value) == value ||
                             (value != null && value.equals(v)))) {
            if (node instanceof TreeNode)
                ((TreeNode<K,V>)node).removeTreeNode(this, tab, movable);
            // p 是找到的节点
            else if (node == p)
                // 说明 node 是第一个节点，把下一个节点赋值给当前节点
                tab[index] = node.next;
            else
                p.next = node.next;
            ++modCount;
            --size;
            afterNodeRemoval(node);
            return node;
        }
    }
    return null;
}
```

---

## 7、get()

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
```

```java
/**
 * Implements Map.get and related methods.
 *
 * @param hash hash for key
 * @param key the key
 * @return the node, or null if none
 */
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab;
    Node<K,V> first, e; 
    int n; K k;
    // 判断数组不为空，这个查询步骤相当于是 remove 里面的步骤
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 下标位置第一个元素的 key 就是我们要找的 key
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) {
            // 红黑树的方法
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

## 8、hashmap的遍历方式

1. 分别遍历 key 和 value	

   1. ```java
      public static void main(String[] args) {
          Map<String, Object> map = getMap();
          map.keySet().forEach(System.out::println);
          map.values().forEach(System.out::println);
      }
      ```

2. 迭代器

   1. ```java
      while (iterator.hasNext()) {
          Map.Entry<String,Object> entry = iterator.next();
      }
      ```

3. 通过get方式(不建议)

   1. 因为迭代多次

4. foreach

   1. ```java
      map.forEach((key,value)->{
          System.out.println(key + " : "+ value);
      });
      ```





