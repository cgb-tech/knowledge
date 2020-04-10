# futureTask

futureTask使用了`适配器`的设计模式，将实现了`Callable`接口的类与Thread联系起来，call方法有返回值，而futerTask能够得到返回来而来的值,在一次性操作多个线程写入，但是可能会有一些报异常的情况下显得尤为重要

```java
public class MyThread implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        return 1024;
    }
}
```

```java
public class futureTaskTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {

        FutureTask<Integer> task = new FutureTask<>(new MyThread());

        new Thread(task,"aa").start();

        while (!task.isDone()) {
            System.out.println(Thread.currentThread().getName()+"\t 未完成 ！" );
        }

        Integer integer = task.get();
        System.out.println(Thread.currentThread().getName()+"\t"+integer);

    }
}
```

- futureTask类有get方法来获取线程执行完的值，但是如果使用get的时候线程并没有计算完结果，那么线程调用它的线程将会被阻塞，一般放在程序的最后执行get方法
- 可以使用task的isDone方法来判断是否执行完毕，折中一点的方法，但是还是建议放在调用线程的最后进行值得获取
- 如果task的线程被再次调用 ` new Thread(task,"bb").start();`，那么第二次将不会被执行

会抛`InterruptedException`异常

