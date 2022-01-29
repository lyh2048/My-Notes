# Java Concurrency

## 进程与线程

进程：当一个程序被运行，从磁盘加载这个程序的代码至内存，这时就开启了一个进程，进程可以视为程序的一个实例

线程：一个进程之内可以分为一到多个线程，一个线程就是一个指令流，将指令流中的一条条指令以一定的顺序交给CPU执行，Java中，线程作为最小调度单位，进程作为资源分配的最小单位。在Windows中进程是不活动的，只是作为线程的容器。

两者对比：

- 进程基本是上相互独立的，而线程存在于进程内，是进程的一个子集
- 进程拥有共享的资源，如内存空间等，供其内部的线程共享
- 进程间通信较为复杂
  - 同一台计算机的进程通信称为IPC
  - 不同计算机之间的进程通信，需要通过网络，并遵守共同的协议
- 线程通信相对简单，因为它们共享进程内的内存，一个例子是多个线程可以访问同一个共享变量
- 线程更轻量，线程上下文切换成本一般要比进程上下文切换成本低

并发：是同一时间应对多件事情的能力

并行：是同一个时间动手做多件事情的能力

## Java线程

### 创建线程

方法一：直接使用Thread

```java
package org.example.test;

import lombok.extern.slf4j.Slf4j;

@Slf4j(topic = "c.Test1")
public class Test1 {
    public static void main(String[] args) {
        Thread t = new Thread() {
            @Override
            public void run() {
                log.debug("running");
            }
        };
        t.setName("t1");
        t.start();
        log.debug("running");
    }
}

```



方法二：使用Runnable配合Thread

```java
package org.example.test;

import lombok.extern.slf4j.Slf4j;

@Slf4j(topic = "c.Test2")
public class Test2 {
    public static void main(String[] args) {
        Runnable r = new Runnable() {
            @Override
            public void run() {
                log.debug("running");
            }
        };
        Thread t = new Thread(r, "t1");
        t.start();
        log.debug("running");
    }
}

```



方法三：FutureTask配合Thread

```java
package org.example.test;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

@Slf4j(topic = "c.Test3")
public class Test3 {
    public static void main(String[] args) {
        FutureTask<Integer> task = new FutureTask<>(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                log.debug("running");
                Thread.sleep(1000);
                return 100;
            }
        });
        Thread t = new Thread(task, "t1");
        t.start();
        log.debug("running");
    }
}

```

### 线程运行



## 参考资料

[黑马程序员全面深入学习Java并发编程，JUC并发编程全套教程](https://www.bilibili.com/video/BV16J411h7Rd)

