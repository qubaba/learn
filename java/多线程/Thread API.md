[TOC]



# Thread线程对象

## 构造函数

### Thread()

无参构造，创建一个线程，没有执行代码。线程名称为Thread-num的形式。

### Thread(Runnable target)

Runnable接口不是线程对象。创建一个线程对象，执行Runnable子类的run()方法。线程名称为Thread-num的形式。

### Thread(ThreadGroup group, Runnable target)

group：线程组，标志当先线程属于哪个线程组。如果为Null的话取parent得ThreadGroup。

target：一个接口，Thread执行start()调用的是这个接口的run()方法。

创建一个线程对象并添加这个线程到group线程组，执行target的run()方法。线程名称为Thread-num的形式。

### Thread(String name)

name：线程名称。

创建一个线程对象，执行Runnable子类的run()方法。线程名称为name。

### Thread(ThreadGroup group, String name)

group：线程所属组，Null时取parent得group。

name：线程名称。

创建一个线程对象并添加这个线程到group线程组，执行Runnable子类的run()方法。线程名称为name。

### Thread(Runnable target, String name)

target：待执行的任务。

name：线程名称。

创建一个线程对象，执行target的run()方法。线程名称为name。

### Thread(ThreadGroup group, Runnable target, String name)

group：线程所属组，Null时取parent得group。

target：待执行的任务。

name：线程名称。

创建一个线程对象并添加这个线程到group线程组，执行target的run()方法。线程名称为name。

### Thread(ThreadGroup group, Runnable target, String name, long stackSize)

group：线程所属组，Null时取parent得group。

target：待执行的任务。

name：线程名称。

stackSize：指定虚拟机栈内存大小。

创建一个线程对象添加这个线程到group线程组并设置虚拟机栈的大小，执行target的run()方法。线程名称为name。

## API

### interrupt()

打断正在执行的线程，可以打断wait()、sleep()、join()方法。打断join()方法需要单独启动一个县城打断它，注意县城之间的关系是谁的join()。

### isInterrupted()

当前线程是否中断。

### setPriority(int newPriority)

设置线程优先级。别试图用这个来控制线程的执行顺序。

### getPriority()

获取当前线程的优先级。

### setDaemon()

设置是否为守护线程，即parent现成需不需要等待当前线程执行完成。





## 关闭线程

### 通过flag控制线程的执行

定义flag的方式，有机会调用到设置flag的时候关闭线程。

```java
package com.java.concurrency04;

public class CloseThread {

    public static class Worker extends Thread {
        private volatile boolean start = true;

        @Override
        public void run() {
            while (start) {
                try {
                    System.out.println("运行中....");
                    sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }

        public void shutdown() {
            this.start = false;
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Worker worker = new Worker();
        worker.start();
        Thread.sleep(1000);
        worker.shutdown();
    }
}
```

### 通过线程中断来关闭线程

```java
package com.java.concurrency04;

public class CloseThread2 {

    public static class Worker extends Thread {
        @Override
        public void run() {
            while (!Thread.currentThread().isInterrupted()) {
                System.out.println("运行中....");
            }
        }

    }

    public static void main(String[] args) throws InterruptedException {
        Worker worker = new Worker();
        worker.start();
        Thread.sleep(1000);
        worker.interrupt();
    }
}
```

### 强制关闭线程

主要用途在于没有触发关闭县城的机会的时候，通过该方式强制关闭线程

```java
package com.java.concurrency04;

public class ThreadService {
    private Thread executeThread;
    private boolean finished = false; // 定义线程是否执行完成状态

    public void execute(Runnable task) {
        // 定义执行线程
        executeThread = new Thread(() -> {
            // 为执行线程添加守护线程
            Thread runner = new Thread(task, "runner");
            runner.setDaemon(true);
            // 执行线程守护线程启动
            runner.start();

            try {
                // 执行线程需要等守护线程执行完成才能执行。
                runner.join();
                finished = true;
            } catch (InterruptedException e) {

                System.out.println("收到打断信息， 打断完成");
            }
        }, "executeThread");
        // 执行线程启动
        executeThread.start();
    }

    public void shutdown(long mills) {
        long timeMillis = System.currentTimeMillis();
        // 判断线程是否执行完成
        while (!finished) {
            if (System.currentTimeMillis() - timeMillis > mills) {
                System.out.println("任务超时，进行打断");
                /*
                如果超出预期时间就打断执行线程，在打断执行线程的时候由于目标任务线程是执行线程的守护线程
                所以执行线程守护线程也会同时打断。
                */
                executeThread.interrupt();
                break;
            }

            try {
                executeThread.sleep(1000);
            } catch (InterruptedException e) {
                System.out.println("执行线程被打断");
                break;
            }
        }
        finished = false;
    }
}

```

```java
package com.java.concurrency04;

public class ThreadForce {

    public static void main(String[] args) {
        // 创建线程服务
        ThreadService service = new ThreadService();
        long startTime = System.currentTimeMillis();
        // 执行目标线程
        service.execute(() -> {
            while (true) {
            }
        });
        service.shutdown(3000);
        long endTime = System.currentTimeMillis();
        System.out.println(endTime - startTime);
    }
}

```

