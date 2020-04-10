# Happens-before 规则

- 程序次序规则:在程序中若操作A先于操作B发生，那么线程中操作A也先于操作B发生

- 对象终结规则: 一个对象的构造函数的完成先行发生于其finalize()方法
- 锁规则:对同一个锁，加锁操作先行发生于解锁操作
- 传递规则:如果操作A先行发生于操作B ,而操作B又先行发生于操作C ,则操作A先行发生于操作C
- volatile变量规则:对一个volatile变量的写操作先行发生于对这个变量的读操作
- 线程启动规则: Thread对象的start()方法先行发生于此线程中的每个指令操作
- 线程中断规则: 一个线程对另一一个线程调用interrupt()方法，先行发生于被中断线程检测到中断事件
- 线程结束规则:线程中所有的操作都先行发生于线程的终止时,如线程结束、Thread.join()返回

# synchronized与volatile

## synchronized

### `synchronized方法`

- 在方法上标注static关键字

  - ```java
     private static synchronized void soutStatic() {
            System.out.println(Thread.currentThread().getName() + "is sleeping");
            try {
                Thread.sleep(3000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + ": 1");
            System.out.println(Thread.currentThread().getName() + ": finish sleeping");
        }
      
    运行结果
    static1is sleeping
    static1: 1
    static1: finish sleeping
    static2is sleeping
    static2: 1
    static2: finish sleeping
    ```

- 在方法上不标注static

  - ```java
     private synchronized void soutNoneStatic() {
            System.out.println(Thread.currentThread().getName() + "is sleeping");
            try {
                Thread.sleep(3000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + ": 2");
            System.out.println(Thread.currentThread().getName() + ": finish sleeping");
        }
      
    //运行结果
    Thread-dif1is sleeping
    Thread-dif2is sleeping
    Thread-dif2: 2
    Thread-dif2: finish sleeping
    Thread-dif1: 2
    Thread-dif1: finish sleeping
    ```

以上可以看出，如果加static关键字，那么锁将会加在类上，如果不加的话，只会对对象加锁

- 方法使用ACC_SYNCHRONIZED标识
- 如果是static方法，锁是作用在类上
- 如果是非static方法，锁是作用在具体的类对象上

### `synchronized代码块`

- 使用monitorenter和minitorexit指令控制线程进出

### `synchronized`与`ReentrantLock`

相同点

- 都是用于多线程中对资源加锁，控制代码同一时间只有单个线程在执行
- 当一个线程获取了锁，其他线程均需要阻塞等待
- 均为可重入锁

不同点

- synchronized是java语言的关键字，有虚拟机字节码指令实现
- ReentrantLock是java sdk提供的API级别锁实现
- synchronized可以在方法级别锁，ReentrantLock则不行
- ReentrantLock可以通过方法tryLock等待指定时间的锁，超时返回，synchronized则不行
- ReentrantLock提供了公平锁和非公平锁实现，synchronized是有非公平锁

## volatile

volatile是java语言关键字，也是一个指令的关键字

### 注意事项

- 用来保证多线程间对变量的内存可见性，将最新变量值及时通知给其他线程
- 禁止volatile前后的程序指令进行重排序
- 不保证线程安全，不可用于数字的线程安全递增

### volatile使用场景

1. 修饰状态变量：用于线程间访问该变量，保证各线程可以看到最新的内存值

2. 单实例对象构造：避免多线程情况下用于内存不可见而重复多次构造对象

## 区别

- synchronized是用于`同步锁控制`，具有`原子性`，控制同一时间只有一个线程执行一个方法或代码块
- volatile只保证线程间的内存`可见性`，不具备锁的特性，无法保证修饰对象的原子性







































