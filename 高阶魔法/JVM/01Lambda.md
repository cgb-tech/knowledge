## 接口里面可以定义的

- default 方法
- static 方法
- @FunctionalInterface 函数接口

## 保证list线程安全

- Vector
- Collections.synchronizedList(new ArrayList<>());
- CopyOnWriteArrayList()