[toc]

# 什么是CAS？JAVA中哪些地方用到来CAS？

## 什么是CAS

CAS 全称compareAndSet，比较并交换的意思，CAS为无锁操作(其实就是CPU级别的锁，跟操作系统没关系，而且CPU级别的锁比较快)。

顾名思义CAS操作分为两步，先比较后交换。既然要进行比较然后在进行交换，那肯定是涉及到了三个参数，自己V、和谁进行比较 E、比较完之后需要修改成谁 U。

V即为需要进行比较的参数，E为变量在内存中的最新值，U为需要修改的值。CAS在接收到三个参数后，先对V和E进行比较，如果V=E证明变量V没有被其他线程修改过，CAS就会把V替换成U；如V != E 证明这个变量已经被其他线程修改了，所以CVS认为本次操作是一次无效操作，不会进行任何操作。



```java
// JDK版本为 jdk1.8.0_161 
public static void main(String[] args) {
        // 1
        AtomicInteger atomicInteger = new AtomicInteger(10);
        // 2
        boolean flag = atomicInteger.compareAndSet(10, 11);
        System.out.println(flag); // true
        // 3
        flag = atomicInteger.compareAndSet(10, 11);
        System.out.println(flag);// false
 }
```



上面代码中，第一步是定义了一个原子性的整数类型atomicInteger，它的值为10；第二步对这个value进行CAS操作，因为AtomicInteger对CAS进行了封装，V永远是AtomicInteger内部的value，所以第二步的CAS少了V这个参数，CAS拿到atomicInteger变量的value和传进去的第一个参数进行比较，通过比较发现value = 10 这个条件是成立的，于是就把第二个参数11替换成了原来的10（整个第二步是原子性操作），并且返回一个boolean类型的操作成功true；第三步CAS又拿着自己的value 11（因为在第二步已经替换成为11了）和第一个参数 10 进行比较，通过比较发现11 != 10 条件不成立，于是不进行任何操作，并且返回一个boolean类型的操作失败 false。

## JAVA中哪些地方用到来CAS

目前CAS在jdk中主要应用在J.U.C相关包下的Atomic相关类中，主要有AtomicInteger、AtomicLong、AtomicBoolean、AtomicDouble、AtomicReference、AtomicReferenceFieldUpdater等。

以AtomicInteger类下的getAndUpdate为例 ：

```java
/**
  * Atomically updates the current value with the results of
  * applying the given function, returning the previous value. The
  * function should be side-effect-free, since it may be re-applied
  * when attempted updates fail due to contention among threads.
  *
  * @param updateFunction a side-effect-free function
  * @return the previous value
  * @since 1.8
  */
public final int getAndUpdate(IntUnaryOperator updateFunction) {
    int prev, next;
    do {
        prev = get();
        next = updateFunction.applyAsInt(prev);
    } while (!compareAndSet(prev, next));
    return prev;
}

/**
  * Gets the current value.
  *
  * @return the current value
  */
public final int get() {
    return value;
}
```

第一步：首先通过get()方法获取到当前对象的value。

第二步：updateFunction为函数引用，不过他的操作也是为了赋值，并返回预期结果。

第三步：进行CAS操作，传入第一步get()获取到的值和目前对象中的值进行比较，如果一样就修改为next,不一样就继续循环，直到CAS操作返回true为止。

第四步：返回当前获取到的值prev。



