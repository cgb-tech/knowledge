# ReentrantLock解析

##  1、AQS

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200321/170225345.png)

- 整个AQS里面的对头，所对应的的Node里面的Thread永远为null

```java
abstract static class Sync extends AbstractQueuedSynchronizer {
    private transient volatile Node head;

    /**
     * Tail of the wait queue, lazily initialized.  Modified only via
     * method enq to add new wait node.
     */
    private transient volatile Node tail;

    /**
     * The synchronization state.
     */
    private volatile int state;
}
```

### 1.1 node内部类

```java
// 保存了一个 node 的队列，像一个链表，保存了线程，下一个节点，上一个节点，与当前状态
static final class Node {
    volatile Node prev;
    volatile Node next;
    volatile Thread thread;
    volatile int waitStatus;
}
```

## 2、 RentrantLock源码 加锁



![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200321/170208203.png)

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200321/183251848.jpg)



### 2.1 fairSync 与 NonfairSync

```java
static final class NonfairSync extends Sync {
    private static final long serialVersionUID = 7316153563782823691L;

    /**
         * Performs lock.  Try immediate barge, backing up to normal
         * acquire on failure.
         */
    // 不判断队列是不是有内容，直接加锁
    final void lock() {
        if (compareAndSetState(0, 1))
            setExclusiveOwnerThread(Thread.currentThread());
        else
            acquire(1);
    }

    protected final boolean tryAcquire(int acquires) {
        return nonfairTryAcquire(acquires);
    }
}
```

```java
static final class FairSync extends Sync {
    private static final long serialVersionUID = -3000897897090466540L;

    final void lock() {
        // 
        acquire(1);
    }

    /**
         * Fair version of tryAcquire.  Don't grant access unless
         * recursive call or no waiters or is first.
         */
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
}
```

### 2.2 acquire()

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        // 加锁失败的时候所要执行的过程
        // addWaiter(Node.EXCLUSIVE), arg) 只表示 t 入队列了
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```

### 2.3 tryAcquire()

```java
protected boolean tryAcquire(int arg) {
    throw new UnsupportedOperationException();
}
//fairSync
protected final boolean tryAcquire(int acquires) {
    // 获取当前线程
    final Thread current = Thread.currentThread();
    // 获取状态值 volitail int state
    int c = getState();
    // 判断锁是不是自由状态
    if (c == 0) {
        // 判断有没有初始化链表（队列）
        // 判断是不是需要排队，有歧义
        if (!hasQueuedPredecessors() &&
            // cas 加锁
            compareAndSetState(0, acquires)) {
            // 设置持有这把锁的线程为自己
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}
// unFairSync
protected final boolean tryAcquire(int acquires) {
    return nonfairTryAcquire(acquires);
}
final boolean nonfairTryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        if (compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    // 查看当前线程是不是持有锁的那个线程
    else if (current == getExclusiveOwnerThread()) {
        // 如果相同 c + 1 
        int nextc = c + acquires;
        // 判断nextc = Integer.MAX + 1 （ <0 ）,溢出
        if (nextc < 0) // overflow
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}
```

### 2.4  hasQueuedPredecessors()

```java
// 判断队列是不是有值
// 判断队列是初始化了，需不需要排队
// 判断队列没有初始化，需不需要排队
public final boolean hasQueuedPredecessors() {
    // The correctness of this depends on head being initialized
    // before tail and on head.next being accurate if the current
    // thread is first in queue.
    Node t = tail; // Read fields in reverse initialization order
    Node h = head;
    Node s;
    // 判断队头和队尾，没有初始化的话是返回false，不需要排队，cas加锁
    return h != t &&
        // 队列被初始化了，且加上虚拟节点的节点个数大于 1
        // 将 h.next 赋值给 s ，判断s 是不是null ，当队列大于 1 的时候返回为false，执行下一步
        ((s = h.next) == null || s.thread != Thread.currentThread());
    	//当前持有锁的线程并不是队列中的第二个线程 ，返回true，整个结果返回true，在上一个调用者取反为false
}
```

### 2.5 acquireQueued()

```java
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        // 打断
        boolean interrupted = false;
        // 自旋了
        for (;;) {
            // 拿到node 的上一个节点
            final Node p = node.predecessor();
            // 判断前一个节点是不是头部，去尝试拿一下锁
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            // 拿不到锁的执行流程
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

### 2.6 addWaiter()

```java
private Node addWaiter(Node mode) {
    // 根据线程实例化一个 node
    Node node = new Node(Thread.currentThread(), mode);
    // Try the fast path of enq; backup to full enq on failure
    // 将 AQS 里面的尾部赋值给一个新的 Node
    Node pred = tail;
    if (pred != null) {
        node.prev = pred;
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    // 如果tail 为 null 的话，将新实例化的 node 传给 enq方法
    enq(node);
    return node;
}
```

### 2.7 enq()

```java
private Node enq(final Node node) {
    for (;;) {
        // 将队尾赋值给 t ，队尾为空
        Node t = tail;
        // 判断
        if (t == null) { // Must initialize
            //利用 cas 创建一个 node 节点，并且赋值给 head 节点（当前 head 节点为 null ）
            if (compareAndSetHead(new Node()))
                // 头部赋值给尾部
                tail = head;
        } else {
            // node 的上一个节点为t ，相当于 node 入队，维护好链表的关系
            node.prev = t;
            // cas 拿 aqs 尾部节点是不是 t ，如果是则更新为 node 节点
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```

### 2.8 predecessor()

```java
// 拿到上一个节点
final Node predecessor() throws NullPointerException {
    Node p = prev;
    if (p == null)
        throw new NullPointerException();
    else
        return p;
}
```

### 2.9  shouldParkAfterFailedAcquire()

- 为什么不在一开始 ws 为 0 的时候就返回为 true？
  - 因为需要多自选一次，尝试获取锁
  - 为了尽量不 park
  - ws = 0 是一个必要的阶段

```java
// 第一次执行自旋的时候上一个节点的 ws 改为 -1，但是当第二次执行的时候直接返回为 true
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    // 当前线程的状态,默认是0
    int ws = pred.waitStatus;
    // SIGNAL = -1
    if (ws == Node.SIGNAL)
        /*
             * This node has already set status asking a release
             * to signal it, so it can safely park.
             */
        return true;
    if (ws > 0) {
        /*
             * Predecessor was cancelled. Skip over predecessors and
             * indicate retry.
             */
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {
        /*
             * waitStatus must be 0 or PROPAGATE.  Indicate that we
             * need a signal, but don't park yet.  Caller will need to
             * retry to make sure it cannot acquire before parking.
             */
        // 将 ws 改成 -1，方法结束
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}

```

### 2.10 parkAndCheckInterrupt()

```java
// 当前线程阻塞（park）
private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);
    return Thread.interrupted();
}
```

## 3、ReentrantLock 源码 解锁  release

