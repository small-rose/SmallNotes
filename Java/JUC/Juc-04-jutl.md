---
layout: default
title: Multi-Thread Tool 
parent: Java Concurrent
grand_parent: Java
nav_order: 21
---



Here are juc tool api .
{: .fs-6 .fw-300 }


## Here are java  CyclicBarrier、CountDownLatch  and Semaphore .
{: .no_toc .text-delta }

1. TOC
{:toc}


## CountDownLatch

### 介绍和用途
    
CountDownLatch 是一个同步助手类，在完成一组正在其他线程中执行的操作之前，它允许一个或多个线程一直等待。
    
又称闭锁、倒计数锁存器，倒计数器。

CountDownLatch类位于java.util.concurrent包下，在完成某些运算时，只有其他所有线程的运算全部完成，当前运算才继续执行。

它相当于一个计数器。用一个给定的数值初始化CountDownLatch，之后计数器就从这个值开始倒计数，直到计数值达到零。

###  基本原理


它通过一个计数器来实现，我们初始化 CountDownLatch 对象时指定计数器的值，每当一个指定的操作执行完成后，计数值就减一。当计数值达到零时，它表示所有需要等待的操作都完成了，此时阻塞在 CountDownLatch 上的线程就可以恢复执行任务。

利用它可以实现类似计数器的功能。

比如有一个任务A，它要等待其他4个任务执行完毕之后才能执行，此时就可以利用CountDownLatch来实现这种功能。

CountDownLatch是通过“共享锁”实现的。

> 在创建CountDownLatch时，会传递一个int类型参数，该参数是“锁计数器”的初始状态，表示该“共享锁”最多能被count个线程同时获取，这个值只能被设置一次，而且CountDownLatch没有提供任何机制去重新设置这个计数值。主线程必须在启动其他线程后立即调用await()方法。这样主线程的操作就会在这个方法上阻塞，直到其他线程完成各自的任务。当某线程调用该CountDownLatch对象的await()方法时，该线程会等待“共享锁”可用时，才能获取“共享锁”进而继续运行。而“共享锁”可用的条件，就是“锁计数器”的值为0。而“锁计数器”的初始值为count，每当一个线程调用该CountDownLatch对象的countDown()方法时，才将“锁计数器”-1；通过这种方式，必须有count个线程调用countDown()之后，“锁计数器”才为0，而前面提到的等待线程才能继续运行。

两个重要的方法

- await()函数的作用是让线程阻塞等待其他线程，直到CountDownLatch的计数值变为0，才继续执行之后的操作。

- countDown()函数：这个函数用来将CountDownLatch的计数值减1，如果计数达到0，则释放所有等待的线程。


###  应用场景

　　某一个任务或动作前，它需要等待其他的一些任务都执行完毕之后它才能继续执行。

　　比如：开5个多线程去下载，当5个线程都执行完了才算下载成功，可以合并文件



```java
/**
下载文件线程
**/
public class Download extends Callable<File>{
    private int num;
    private CountDownLatch latch;
    public Download(int num,CountDownLatch latch){
        this.num = num;
        this.latch = latch;
    }
    
    public File call() throws Exception {
        try {
            System.out.println("线程"+this.num+"正在下载文件...");
            Thread.sleep(2000);
            System.out.println("线程"+this.num+"下载完成");
            String filePath = "";
            countDownLatch.countDown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return new File(filePath);
    }
}

public class DownloadTest{
    //开启多线程
    ExecutorService executorService = Executors.newFixedThreadPool(10);

    public public static void main(String[] args){
          int N = 8; // 下载线程数
          List<FutureTask<File>> futureList = new ArrayList();
          CountDownLatch countDownLatch = new CountDownLatch(N); 
          for(int i=0;i<N;i++){
             futureList.add(executorService.submit(new Download(i, countDownLatch)));
          }
          countDownLatch.await();

          Iterator<Future<File>> iterable = futureList.iterator();
          //遍历一遍
          List<File> fileList = new ArrayList();
          while(iterable.hasNext()){
          Future<File> future = iterable.next();
              //获取结果
              fileList.add(future.get());
          }
          //合并文件
          mergeFile(fileList);
    }
}
```


### 注意事项和最佳实践

CountDownLatch 的计数器无法被重置，如果需要一个能够重置计数的版本，可以考虑使用 CyclicBarrier。在使用时还需注意异常处理，避免由于异常造成的线程永远等待的情况。


## Semaphore

### 介绍和用途

Semaphore（信号量）是一种基于计数的同步机制，它可以用来控制同时访问特定资源的线程数量，是实现资源池或者限制容量的一个有力工具。


### 工作原理

Semaphore 管理一组许可证（permits），线程可以通过 acquire() 方法获取许可证，如果 Semaphore 内部计数为零，表示没有许可证可用，线程将会阻塞直到有许可证被释放。相反，线程完成任务后，可以通过 release() 方法释放许可证，并将其返回给信号量。

信号量可以控制同时访问的线程个数，通过acquire()获取一个许可，如果没有就等待，而通过release()释放一个许可。

Semaphore类中比较重要的几个方法

（1）public void acquire()：用来获取一个许可，若无许可能够获得，则会一直等待，知道获得许可。

  （2）public void acquire(int permits)：获取permits个许可。

　（3）public void release()：释放许可，注意，在释放许可之前，必须先获得许可。

　（4）public void release(int permits)：释放permits个许可。

上面4个方法都会被阻塞，如果想立即得到执行结果，可以使用下面几个方法：

　　1）public boolean tryAcquire()：尝试获取一个许可，若获取成功，则立即返回true，若获取失败，则立即返回false。

　　2）public boolean tryAcquire(long timeout,TimeUnit unit)：尝试获取一个许可，若在指定的时间内获取成功，则立即返回true，否则立即返回false。

　　3）public boolean tryAcquire(int permits)：尝试获取permits个许可，若获取成功，则立即返回true，若获取失败，则立即返回false。

　　4）public boolean tryAcquire(int permits,long timeout,TimeUnit unit)：尝试获取permits个许可，若在指定的时间内获取成功，则立即返回true，否则立即返回false。

　　5）还可以通过availablePermits()方法得到可用的许可数目。

### 2. 基本应用场景

比如一个银行网点有3个服务柜台，一个服务柜台同时只能服务1个顾客，只有服务业务办理完了，其他顾客才能继续到柜台办理业务。那么我们就可以通过Semaphore来实现：

> Semaphore其实和锁有点类似，它一般用于控制对某组资源的访问权限。比如数据库连接池，限制同时访问的连接数，或者在限流控制中限制同时执行的线程数量。

```java
/**
柜台服务工作线程
**/
public class Worker extends Thread{
    private int num;
    private Semaphore semaphore;
    public Worker(int num,Semaphore semaphore){
        this.num = num;
        this.semaphore = semaphore;
    }
    public void run() {
        try {
            semaphore.acquire();
            System.out.println("客人"+this.num+"正在柜台办理业务...");
            Thread.sleep(2000);
            System.out.println("客人"+this.num+"业务办理结束离开柜台");
            latch.countDown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class SemaphoreTest{

    public public static void main(String[] args){
          int N = 8; // 客户数
          Semaphore semaphore = new Semaphore(3); //柜台数量
          for(int i=0;i<N;i++){
              new Worker(i,semaphore).start();
          }
    }
}
```

### 注意事项和最佳实践

使用 Semaphore 时，要确保在资源使用后，准确无误地释放许可证，否则可能会导致其他线程永久等待。

在实际应用中，尤其在复杂的业务逻辑里，通常建议使用 try-finally` 语句确保许可证的正确释放。 

同时，合理配置许可证的数量对于系统的稳定性和性能至关重要。

对于资源竞争激烈的场景，设置过少的许可证可能会导致系统响应时间增长；反之，设置过多的许可证则可能会超出系统资源的实际承载能力，造成资源的浪费或系统崩溃。




## CyclicBarrier


### 介绍和用途

CyclicBarrier 是一个同步助手类，它允许一组线程互相等待，直到所有线程都到达一个公共的屏障点（Barrier Point）后才继续执行。

### 工作原理

CyclicBarrier 通过内部计数器来跟踪到达屏障点的线程数。当线程到达屏障点时，它调用 await() 方法，并阻塞直到指定数量的线程都到达了屏障点。此时，屏障打开，所有阻塞的线程将继续执行。

不同于 CountDownLatch，CyclicBarrier 是可重用的。
 
也有称之为回环栅栏，通过它可以实现让一组线程等待至某个状态之后再全部同时执行，叫做回环，是因为当所有等待线程都被释放以后，CyclicBarrier可以被重用。我们暂且把这种状态叫做barrier，当调用await()方法之后，线程就处于barrier了。
 

CyclicBarrier 通过内部计数器来跟踪到达屏障点的线程数。当线程到达屏障点时，它调用 await() 方法，并阻塞直到指定数量的线程都到达了屏障点。此时，屏障打开，所有阻塞的线程将继续执行。不同于 CountDownLatch，CyclicBarrier 是可重用的。

CyclicBarrier中最重要的方法就是await方法，它有两个重载版本：

（1）public int await()：用来挂起当前线程，直至所有线程都到达barrier状态再同时执行后续任务。

（2）public int await(long timeout,TimeUnit unit)：让这些线程等待至一定的时间，如果还有线程没有到达barrier状态就直接让到达barrier的线程执行后续任务。

### 与CountDownLatch差异

CountDownLatch和CyclicBarrier的区别?

（1）CountDownLatch的作用是允许1个线程等待其他线程执行完成之后，它才执行；而CyclicBarrier则是允许N个线程相互等待至某个公共屏障点，然后这一组线程再同时执行。

（2）CountDownLatch的计数器的值无法被重置，这个初始值只能被设置一次，是不能够重用的；CyclicBarrier是可以重用的。

### 应用示例

```java
public class CyclicBarrierDemo {
    public static void main(String[] args) {
         int N = 4;
         CyclicBarrier barrier = new CyclicBarrier(N, 
                                                new Runnable() {
                                                        @Override
                                                       public void run() {
                                                       // 当所有线程到达屏障点时执行
                                                       System.out.println("所有计算完成，进行数据合并...");
                                                }
         });
         for(int i=0;i<N;i++){
             new Writer(barrier).start();
         }
    }
}

public class Writer extends Thread{
     private int num;
     private CyclicBarrier cyclicBarrier;
     public Writer(int num, CyclicBarrier cyclicBarrier) {
         this.num = num;
         this.cyclicBarrier = cyclicBarrier;
     }
     public void run() {
         try {
             Thread.sleep(5000); //以睡眠来模拟线程需要预定写入数据操作
             System.out.println("线程  #"+num+" 写入数据完毕，等待其他线程写入");

             cyclicBarrier.await();

             System.out.println("线程 #" +num+ " 继续后续操作...");

         } catch (InterruptedException | BrokenBarrierException e){
             e.printStackTrace();
         }
     }
 }
```

### 注意事项和最佳实践

使用 CyclicBarrier 时需要注意，如果任何线程在等待过程中因为中断或者超时而提前离开屏障点, 或者等待线程的数目永远不足以达到屏障点，这将导致所有在屏障点等待的线程抛出 BrokenBarrierException。

因此，在使用时需要妥善处理这些可能的异常场景。 为了避免这种情形，可以在 await 方法中设置一个超时时间，并适当处理 TimeoutException。

同时，可以通过 isBroken 方法检查屏障点的状态，以便在必要时对线程进行重新安排或者重置屏障点。 

除此之外，设计上建议只在所有参与线程要完成的任务确实需要互相等待时才使用 CyclicBarrier，在任务独立的情况下使用 CountDownLatch 会更为合适。
