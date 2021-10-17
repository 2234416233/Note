# Java 并发：Thread 类深度解析



**摘要：**

　　Java 中 Thread类 的各种操作与线程的生命周期密不可分，了解线程的生命周期有助于对Thread类中的各方法的理解。一般来说，线程从最初的创建到最终的消亡，要经历创建、就绪、运行、阻塞 和 消亡 五个状态。在线程的生命周期中，上下文切换通过存储和恢复CPU状态使得其能够从中断点恢复执行。结合 线程生命周期，本文最后详细介绍了 Thread 各常用 API。特别地，在介绍会导致线程进入Waiting状态(包括Timed Waiting状态)的相关API时，笔者会特别关注两个问题：

- **客户端调用该API后，是否会释放锁(如果此时拥有锁的话)；**
- **客户端调用该API后，是否会交出CPU(一般情况下，线程进入Waiting状态(包括Timed Waiting状态)时都会交出CPU)；**

------

## 一. 线程的生命周期

　　Java 中 Thread类 的具体操作与线程的生命周期密不可分，了解线程的生命周期有助于对Thread类中的各方法的理解。

　　在 Java虚拟机 中，线程从最初的创建到最终的消亡，要经历若干个状态：**创建(new)**、**就绪(runnable/start)**、**运行(running)**、**阻塞(blocked)**、**等待(waiting)**、**时间等待(time waiting)** 和 **消亡(dead/terminated)**。在给定的时间点上，一个线程只能处于一种状态，各状态的含义如下图所示：

　　　　　　　　　　　　![JVM中线程的状态.png-55kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/JVMthread_status.png)

　　当我们需要线程来执行某个子任务时，就必须先创建一个线程。但是线程创建之后，不会立即进入就绪状态，因为线程的运行需要一些条件（比如程序计数器、Java栈、本地方法栈等），只有线程运行需要的所有条件满足了，才进入就绪状态。当线程进入就绪状态后，不代表立刻就能获取CPU执行时间，也许此时CPU正在执行其他的事情，因此它要等待。当得到CPU执行时间之后，线程便真正进入运行状态。线程在运行状态过程中，可能有多个原因导致当前线程不继续运行下去，比如用户主动让线程睡眠（睡眠一定的时间之后再重新执行）、用户主动让线程等待，或者被同步块阻塞，此时就对应着多个状态：time waiting（睡眠或等待一定的时间）、waiting（等待被唤醒）、blocked（阻塞）。当由于突然中断或者子任务执行完毕，线程就会被消亡。

　　实际上，Java只定义了六种线程状态，分别是 New, Runnable, Waiting，Timed Waiting、Blocked 和 Terminated。为形象表达线程从创建到消亡之间的状态，下图将Runnable状态分成两种状态：正在运行状态和就绪状态：
　　
　　　　　　　　　　　　　![线程的生命周期.jpg-52.8kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/thread_live.jpg)

------

## 二. 上下文切换

　　以单核CPU为例，CPU在一个时刻只能运行一个线程。CPU在运行一个线程的过程中，转而去运行另外一个线程，这个叫做线程 **上下文切换**（对于进程也是类似）。

　　由于可能当前线程的任务并没有执行完毕，所以在切换时需要保存线程的运行状态，以便下次重新切换回来时能够紧接着之前的状态继续运行。举个简单的例子：比如，一个线程A正在读取一个文件的内容，正读到文件的一半，此时需要暂停线程A，转去执行线程B，当再次切换回来执行线程A的时候，我们不希望线程A又从文件的开头来读取。

　　因此需要记录线程A的运行状态，那么会记录哪些数据呢？因为下次恢复时需要知道在这之前当前线程已经执行到哪条指令了，所以需要记录程序计数器的值，另外比如说线程正在进行某个计算的时候被挂起了，那么下次继续执行的时候需要知道之前挂起时变量的值时多少，因此需要记录CPU寄存器的状态。所以，**一般来说，线程上下文切换过程中会记录程序计数器、CPU寄存器状态等数据。**

　　实质上， **线程的上下文切换就是存储和恢复CPU状态的过程，它使得线程执行能够从中断点恢复执行，这正是有程序计数器所支持的。**

　　虽然多线程可以使得任务执行的效率得到提升，但是由于在线程切换时同样会带来一定的开销代价，并且多个线程会导致系统资源占用的增加，所以在进行多线程编程时要注意这些因素。

------

## 三. 线程的创建

　　**在 Java 中，创建线程去执行子任务一般有两种方式：继承 Thread 类和实现 Runnable 接口。其中，Thread 类本身就实现了 Runnable 接口，而使用继承 Thread 类的方式创建线程的最大局限就是不支持多继承。**特别需要注意两点，

- **实现多线程必须重写run()方法，即在run()方法中定义需要执行的任务**;

- **run()方法不需要用户来调用**。

  　　　　　　　　　　　　　　![Thread类 的结构.png-3.1kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/Thread_define.png)

线程创建的代码示例：

```java
public class ThreadTest {
    public static void main(String[] args) {

        //使用继承Thread类的方式创建线程
        new Thread(){
            @Override
            public void run() {
                System.out.println("Thread");
            }
        }.start();

        //使用实现Runnable接口的方式创建线程
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("Runnable");
            }
        });
        thread.start();

        //JVM 创建的主线程 main
        System.out.println("main");
    }
}/* Output: (代码的运行结果与代码的执行顺序或调用顺序无关)
        Thread
        main
        Runnable
 *///:~
```

　　创建好自己的线程类之后，就可以创建线程对象了，然后通过start()方法去启动线程。注意，run() 方法中只是定义需要执行的任务，并且其不需要用户来调用。当通过start()方法启动一个线程之后，若线程获得了CPU执行时间，便进入run()方法体去执行具体的任务。如果用户直接调用run()方法，即相当于在主线程中执行run()方法，跟普通的方法调用没有任何区别，此时并不会创建一个新的线程来执行定义的任务。**实际上，start()方法的作用是通知 “线程规划器” 该线程已经准备就绪，以便让系统安排一个时间来调用其 run()方法，也就是使线程得到运行。**Thread 类中的 run() 方法定义为：

```java
    /* What will be run. */
    private Runnable target;  // 类 Thread 的成员

    /**
     * If this thread was constructed using a separate <code>Runnable</code> run object, 
     * then that <code>Runnable</code> object's <code>run</code> method is called; otherwise, 
     * this method does nothing and returns. 
     * 
     * Subclasses of <code>Thread</code> should override this method. 
     *
     */
    public void run() {
        if (target != null) {
            target.run();
        }
    }
```

------

## 四. Thread 类详解

　　Thread 类实现了 Runnable 接口，在 Thread 类中，有一些比较关键的属性，比如name是表示Thread的名字，可以通过Thread类的构造器中的参数来指定线程名字，priority表示线程的优先级（最大值为10，最小值为1，默认值为5），daemon表示线程是否是守护线程，target表示要执行的任务。

　　　　　　　　　　　　![Thread类.png-38.9kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/Thread_class.png)

------

1、与线程运行状态有关的方法

　1) start 方法

　start() 用来启动一个线程，**当调用该方法后，相应线程就会进入就绪状态，该线程中的run()方法会在某个时机被调用。**

------

　2）run 方法

　**run()方法是不需要用户来调用的。****当通过start()方法启动一个线程之后，一旦线程获得了CPU执行时间，便进入run()方法体去执行具体的任务。**注意，创建线程时必须重写run()方法，以定义具体要执行的任务。

　　　　　　　　　　　![Thread-run.png-26.7kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/Thread-run.png)
　
　**一般来说，有两种方式可以达到重写run()方法的效果：**

- **直接重写：**直接继承Thread类并重写run()方法；
- **间接重写：**通过Thread构造函数传入Runnable对象 (注意，实际上重写的是 Runnable对象 的run() 方法)。

------

　3）sleep 方法

　　方法 sleep() 的作用是在指定的毫秒数内让当前正在执行的线程（即 currentThread() 方法所返回的线程）睡眠，并交出 CPU 让其去执行其他的任务。当线程睡眠时间满后，不一定会立即得到执行，因为此时 CPU 可能正在执行其他的任务。所以说，**调用sleep方法相当于让线程进入阻塞状态。**该方法有如下两条特征：

- **如果调用了sleep方法，必须捕获InterruptedException异常或者将该异常向上层抛出；**

- **sleep方法不会释放锁，也就是说如果当前线程持有对某个对象的锁，则即使调用sleep方法，其他线程也无法访问这个对象。**

  　　　　　　　　　　　![sleep 定义.png-31.6kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/sleep_define.png)

------

　4）yield 方法

　　调用 yield()方法会让当前线程交出CPU资源，让CPU去执行其他的线程。但是，yield()不能控制具体的交出CPU的时间。需要注意的是，

- yield()方法只能让 **拥有相同优先级的线程** 有获取 CPU 执行时间的机会；

- **调用yield()方法并不会让线程进入阻塞状态，而是让线程重回就绪状态**，它只需要等待重新得到 CPU 的执行；

- **它同样不会释放锁**。

  　　　　　　　　　　　　![yield 的定义.png-9.3kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/yield_define.png)


```java
public class MyThread extends Thread {

    @Override
    public void run() {
        long beginTime = System.currentTimeMillis();
        int count = 0;
        for (int i = 0; i < 50000; i++) {
            Thread.yield();             // 将该语句注释后，执行会变快
            count = count + (i + 1);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("用时：" + (endTime - beginTime) + "毫秒！");
    }

    public static void main(String[] args) {
        MyThread thread = new MyThread();
        thread.start();
    }
}
```

------

　5）join 方法
　
　　**假如在main线程中调用thread.join方法，则main线程会等待thread线程执行完毕或者等待一定的时间。**详细地，如果调用的是无参join方法，则等待thread执行完毕；如果调用的是指定了时间参数的join方法，则等待一定的时间。join()方法有三个重载版本：

```java
public final synchronized void join(long millis) throws InterruptedException {...}
public final synchronized void join(long millis, int nanos) throws InterruptedException {...}
public final void join() throws InterruptedException {...}
```

　　以 join(long millis) 方法为例，其内部调用了Object的wait()方法，如下图：
　　　　　　　　　　　　![这里写图片描述](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20170208184518762)

　　根据以上源代码可以看出，**join()方法是通过wait()方法 (Object 提供的方法) 实现的。\**当 millis == 0 时，会进入 while(isAlive()) 循环，并且只要子线程是活的，宿主线程就不停的等待。\** wait(0) 的作用是让当前线程(宿主线程)等待，而这里的当前线程是指 Thread.currentThread() 所返回的线程。所以，虽然是子线程对象(锁)调用wait()方法，但是阻塞的是宿主线程。**

------

　　看下面的例子，**当 main线程 运行到 thread1.join() 时，main线程会获得线程对象thread1的锁(wait 意味着拿到该对象的锁)。只要 thread1线程 存活， 就会调用该对象锁的wait()方法阻塞 main线程，直至 thread1线程 退出才会使 main线程 得以继续执行。**

```java
//示例代码
public class Test {

    public static void main(String[] args) throws IOException  {
        System.out.println("进入线程"+Thread.currentThread().getName());
        Test test = new Test();
        MyThread thread1 = test.new MyThread();
        thread1.start();
        try {
            System.out.println("线程"+Thread.currentThread().getName()+"等待");
            thread1.join();
            System.out.println("线程"+Thread.currentThread().getName()+"继续执行");
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    } 

    class MyThread extends Thread{
        @Override
        public void run() {
            System.out.println("进入线程"+Thread.currentThread().getName());
            try {
                Thread.currentThread().sleep(5000);
            } catch (InterruptedException e) {
                // TODO: handle exception
            }
            System.out.println("线程"+Thread.currentThread().getName()+"执行完毕");
        }
    }
}/* Output:
        进入线程main
        线程main等待
        进入线程Thread-0
        线程Thread-0执行完毕
        线程main继续执行
 *///~
```

　　看上面的例子，**当 main线程 运行到 thread1.join() 时，main线程会获得线程对象thread1的锁(wait 意味着拿到该对象的锁)。只要 thread1线程 存活， 就会调用该对象锁的wait()方法阻塞 main线程。那么，main线程被什么时候唤醒呢？**事实上，**有wait就必然有notify。**在整个jdk里面，我们都不会找到对thread1线程的notify操作。这就要看jvm代码了：

```C++
作者：cao
链接：https://www.zhihu.com/question/44621343/answer/97640972
来源：知乎

//一个c++函数：
void JavaThread::exit(bool destroy_vm, ExitType exit_type) ；

//这个函数的作用就是在一个线程执行完毕之后，jvm会做的收尾工作。里面有一行代码：ensure_join(this);

该函数源码如下：

static void ensure_join(JavaThread* thread) {
    Handle threadObj(thread, thread->threadObj());

    ObjectLocker lock(threadObj, thread);

    thread->clear_pending_exception();

    java_lang_Thread::set_thread_status(threadObj(), java_lang_Thread::TERMINATED);

    java_lang_Thread::set_thread(threadObj(), NULL);

    //thread就是当前线程，就是刚才说的thread1线程。
    lock.notify_all(thread);

    thread->clear_pending_exception();
}
```

　　至此，thread1线程对象锁调用了notifyall，那么main线程也就能继续跑下去了。

------

　　由于 join方法 会调用 wait方法 让宿主线程进入阻塞状态，并且会释放线程占有的锁，并交出CPU执行权限。结合 join 方法的声明，有以下三条：

- **join方法同样会会让线程交出CPU执行权限；**

- **join方法同样会让线程释放对一个对象持有的锁；**

- **如果调用了join方法，必须捕获InterruptedException异常或者将该异常向上层抛出。**　　

  　　更多关于 join方法 的介绍见[《Java 并发：线程间通信与协作》](http://blog.csdn.net/justloveyou_/article/details/54929949) 一文。

------

　6）interrupt 方法

　interrupt，顾名思义，即中断的意思。**单独调用interrupt方法可以使得 \**处于阻塞状态的线程\** 抛出一个异常，也就是说，\**它可以用来中断一个正处于阻塞状态的线程\****；另外，通过 interrupted()方法 和 isInterrupted()方法 可以停止正在运行的线程。interrupt 方法在 JDK 中的定义为：

　　　　　　　　　　　　　　![interrupt 定义.png-18.7kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/interrupt_define.png)
　
　interrupted() 和 isInterrupted()方法在 JDK 中的定义分别为：
　　　　　　　　　　　　　　![interrupted以及isInterrupted.png-69.2kB](C:\Users\fang\Downloads\interrupted_isInterrupted.png)

　下面看一个例子：

```java
public class Test {

    public static void main(String[] args) throws IOException  {
        Test test = new Test();
        MyThread thread = test.new MyThread();
        thread.start();
        try {
            Thread.currentThread().sleep(2000);
        } catch (InterruptedException e) {

        }
        thread.interrupt();
    } 

    class MyThread extends Thread{
        @Override
        public void run() {
            try {
                System.out.println("进入睡眠状态");
                Thread.currentThread().sleep(10000);
                System.out.println("睡眠完毕");
            } catch (InterruptedException e) {
                System.out.println("得到中断异常");
            }
            System.out.println("run方法执行完毕");
        }
    }
}/* Output:
        进入睡眠状态
        得到中断异常
        run方法执行完毕
 *///~
```

　从这里可以看出，**通过interrupt方法可以中断处于阻塞状态的线程**。那么能不能中断处于非阻塞状态的线程呢？看下面这个例子：

```java
public class Test {

    public static void main(String[] args) throws IOException  {
        Test test = new Test();
        MyThread thread = test.new MyThread();
        thread.start();
        try {
            Thread.currentThread().sleep(2000);
        } catch (InterruptedException e) {}
        thread.interrupt();
    } 

    class MyThread extends Thread{
        @Override
        public void run() {
            int i = 0;
            while(i<Integer.MAX_VALUE){
                System.out.println(i+" while循环");
                i++;
            }
        }
    }
}
```

　运行该程序会发现，while循环会一直运行直到变量i的值超出Integer.MAX_VALUE。所以说，**直接调用interrupt() 方法不能中断正在运行中的线程**。但是，**如果配合 isInterrupted()/interrupted() 能够中断正在运行的线程，因为调用interrupt()方法相当于将中断标志位置为true，那么可以通过调用isInterrupted()/interrupted()判断中断标志是否被置位来中断线程的执行。**比如下面这段代码：

```java
public class Test {
    public static void main(String[] args) throws IOException  {
        Test test = new Test();
        MyThread thread = test.new MyThread();
        thread.start();
        try {
            Thread.currentThread().sleep(2000);
        } catch (InterruptedException e) {

        }
        thread.interrupt();
    } 

    class MyThread extends Thread{
        @Override
        public void run() {
            int i = 0;
            while(!isInterrupted() && i<Integer.MAX_VALUE){
                System.out.println(i+" while循环");
                i++;
            }
        }
    }
}
```

　但是，**一般情况下，不建议通过这种方式来中断线程，一般会在MyThread类中增加一个 \**volatile 属性\** isStop 来标志是否结束 while 循环，然后再在 while 循环中判断 isStop 的值。**例如：

```java
class MyThread extends Thread{
        private volatile boolean isStop = false;
        @Override
        public void run() {
            int i = 0;
            while(!isStop){
                i++;
            }
        }

        public void setStop(boolean stop){
            this.isStop = stop;
        }
    }
```

　那么，就可以在外面通过调用setStop方法来终止while循环。

　　更多关于 volatile关键字 的介绍见[《Java 并发：volatile 关键字解析》](http://blog.csdn.net/justloveyou_/article/details/53672005) 一文。

------

　7）stop方法

　stop() 方法已经是一个 **废弃的** 方法，它是一个 **不安全的** 方法。因为调用 stop() 方法会直接终止run方法的调用，并且会抛出一个ThreadDeath错误，如果线程持有某个对象锁的话，会完全释放锁，导致对象状态不一致。所以， stop() 方法基本是不会被用到的。

------

8、线程的暂停与恢复

1) 线程的暂停、恢复方法在 JDK 中的定义

　　**暂停线程意味着此线程还可以恢复运行。在 Java 中，我可以使用 suspend() 方法暂停线程，使用 resume() 方法恢复线程的执行，但是这两个方法已被废弃，因为它们具有固有的死锁倾向**。如果目标线程挂起时在保护关键系统资源的监视器上保持有锁，则在目标线程重新开始以前，任何线程都不能访问该资源。如果重新开始目标线程的线程想在调用 resume 之前锁定该监视器，则会发生死锁。

**实例方法 suspend() 在类Thread中的定义：**

　　　　　　　　　　　![suspend 的定义.png-46.2kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/suspend_define.png)
　　
　　　　
**实例方法 resume() 在类Thread中的定义：**

　　　　　　　　　　　![resume 的定义.png-36.3kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/resume_define.png)

------

2) 死锁

　　**具体地，在使用 suspend 和 resume 方法时，如果使用不当，极易造成公共的同步对象的独占，使得其他线程无法得到公共同步对象锁，从而造成死锁。**下面举两个示例：

```java
// 示例 1
public class SynchronizedObject {

    public synchronized void printString() {        // 同步方法
        System.out.println("Thread-" + Thread.currentThread().getName() + " begins.");
        if (Thread.currentThread().getName().equals("a")) {
            System.out.println("线程a suspend 了...");
            Thread.currentThread().suspend();
        }
        System.out.println("Thread-" + Thread.currentThread().getName() + " is end.");
    }

    public static void main(String[] args) throws InterruptedException {

        final SynchronizedObject object = new SynchronizedObject();     // 两个线程使用共享同一个对象

        Thread a = new Thread("a") {
            @Override
            public void run() {
                object.printString();
            }
        };
        a.start();

        new Thread("b") {
            @Override
            public void run() {
                System.out.println("thread2 启动了，在等待中(发生“死锁”)...");
                object.printString();
            }
        }.start();

        System.out.println("main 线程睡眠 " + 5 +" 秒...");
        Thread.sleep(5000);
        System.out.println("main 线程睡醒了...");

        a.resume();
        System.out.println("线程 a resume 了...");
    }
}/* Output:
        Thread-a begins.
        线程a suspend 了...
        thread2 启动了，在等待中(发生死锁)...
        main 线程睡眠 5 秒...
        main 线程睡醒了...
        线程 a resume 了...
        Thread-a is end.
        Thread-b begins.
        Thread-b is end.
 *///:~
```

　　在示例 2 中，特别要注意的是，**println() 方法实质上是一个同步方法。**如果 thread 线程刚好在执行打印语句时被挂起，那么将会导致 main线程中的字符串 “main end!” 迟迟不能打印。其中，println() 方法定义如下：

　　　　　　　　　　　　![println 方法.png-13.5kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/println_define.png)

```java
// 示例 2
public class MyThread extends Thread {

    private long i = 0;

    @Override
    public void run() {
        while (true) {
            i++;
            System.out.println(i);
        }
    }

    public static void main(String[] args) {
        try {
            MyThread thread = new MyThread();
            thread.start();
            Thread.sleep(1);
            thread.suspend();
            System.out.println("main end!");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

------

2、线程常用操作

　1)、获得代码调用者信息

　**currentThread() 方法返回代码段正在被哪个线程调用的信息**。其在 Thread类 中定义如下：

　　　　　　　　　　　　　　　　![currentThread.png-8.9kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/currentThread.png)

　　下面的例子给出了 currentThread() 方法的使用方式：

```java
public class CountOperate extends Thread {

    public CountOperate() {
        super("Thread-CO");     // 线程 CountOperate 的名字
        System.out.println("CountOperate---begin");
        System.out.println("Thread.currentThread().getName()="
                + Thread.currentThread().getName());        
        System.out.println("this.getName()=" + this.getName());       
        System.out.println("CountOperate---end");
    }

    @Override
    public void run() {
        System.out.println("run---begin");
        System.out.println("Thread.currentThread().getName()="
                + Thread.currentThread().getName());    
        System.out.println("this.getName()=" + this.getName());    
        System.out.println("run---end");
    }

    public static void main(String[] args) {
        CountOperate c = new CountOperate();
        Thread t1 = new Thread(c);
        t1.setName("A");
        t1.start();         
        c.start();         
    }
}/* Output:(输出结果不唯一)
        CountOperate---begin                             ....... 行 1
        Thread.currentThread().getName()=main            ....... 行 2
        this.getName()=Thread-CO                         ....... 行 3
        CountOperate---end                               ....... 行 4
        run---begin                                      ....... 行 5
        Thread.currentThread().getName()=A               ....... 行 6
        run---begin                                      ........行 7
        Thread.currentThread().getName()=Thread-CO       ....... 行 8
        this.getName()=Thread-CO                         ....... 行 9
        run---end                                        ....... 行 10
        this.getName()=Thread-CO                         ....... 行 11
        run---end                                        ....... 行 12
 *///:~
```

　　首先来看前四行的输出。我们知道 CountOperate 继承了 Thread 类，那么 CountOperate 就得到了 Thread类的所有非私有属性和方法。CountOperate 构造方法中的 super(“Thread-CO”);意味着调用了父类Thread的构造器Thread(String name)，也就是为 CountOperate线程 赋了标识名。由于该构造方法是由main()方法调用的，因此此时 Thread.currentThread() 返回的是main线程；而 this.getName() 返回的是CountOperate线程的标识名。

　　其次，在main线程启动了t1线程之后，CPU会在某个时机执行类CountOperate的run()方法。此时，Thread.currentThread() 返回的是t1线程，因为是t1线程的启动使run()方法得到了执行；而 this.getName() 返回的仍是CountOperate线程的标识名，因为此时this指的是传进来的CountOperate对象(具体原因见上面对run()方法的介绍)，由于它本身也是一个线程对象，所以可以调用getName()得到相应的标识名。

　　在main线程启动了CountOperate线程之后，CPU也会在某个时机执行类该线程的run()方法。此时，Thread.currentThread() 返回的是CountOperate线程，因为是CountOperate线程的启动使run()方法得到了执行；而 this.getName() 返回的仍是CountOperate线程的标识名，因为此时this指的就是刚刚创建的CountOperate对象本身，所以得到的仍是 “Thread-CO ”。

------

2）、判断线程是否处于活动状态

　　**方法 isAlive() 的功能是判断调用该方法的线程是否处于活动状态。**其中，**活动状态指的是线程已经 start (无论是否获得CPU资源并运行) 且尚未结束。**
　　
　　　　　　　　　　　　　　![这里写图片描述](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20161203224546845)

　　下面的例子给出了 isAlive() 方法的使用方式：

```java
public class CountOperate extends Thread {

    public CountOperate() {
        System.out.println("CountOperate---begin");

        System.out.println("Thread.currentThread().getName()="
                + Thread.currentThread().getName());        // main
        System.out.println("Thread.currentThread().isAlive()="
                + Thread.currentThread().isAlive());        // true

        System.out.println("this.getName()=" + this.getName());         // Thread-0
        System.out.println("this.isAlive()=" + this.isAlive());         // false

        System.out.println("CountOperate---end");
    }

    @Override
    public void run() {
        System.out.println("run---begin");

        System.out.println("Thread.currentThread().getName()="
                + Thread.currentThread().getName());        // A
        System.out.println("Thread.currentThread().isAlive()="
                + Thread.currentThread().isAlive());        // true

        System.out.println("this.getName()=" + this.getName());     // Thread-0
        System.out.println("this.isAlive()=" + this.isAlive());     // false

        System.out.println("run---end");
    }

    public static void main(String[] args) {
        CountOperate c = new CountOperate();
        Thread t1 = new Thread(c);
        System.out.println("main begin t1 isAlive=" + t1.isAlive());        // false
        t1.setName("A");
        t1.start();
        System.out.println("main end t1 isAlive=" + t1.isAlive());      // true
    }
}
```

　　该程序所反映的知识点与上面的程序类似，此不赘述。　

------

3)、获取线程唯一标识

　　**方法 getId() 的作用是取得线程唯一标识，由JVM自动给出**。

　　　　　　　　　　　　　　![getId 定义.png-17.7kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/getId_define.png)

```java
// 示例
public class Test {
    public static void main(String[] args) {
        Thread runThread = Thread.currentThread();
        System.out.println(runThread.getName() + " " + runThread.getId());
    }
}/* Output:
        main 1
 *///:~
```

------

4)、getName和setName

　　用来得到或者设置线程名称。**如果我们不手动设置线程名字，JVM会为该线程自动创建一个标识名，形式为： Thread-数字。**

------

5)、getPriority和setPriority

　　在操作系统中，**线程可以划分优先级，优先级较高的线程得到的CPU资源较多，也就是CPU优先执行优先级较高的线程**。**设置线程优先级有助于帮助 “线程规划器” 确定在下一次选择哪个线程来获得CPU资源。**特别地，**在 Java 中，线程的优先级分为 1 ~ 10 这 10 个等级**，如果小于 1 或大于 10，则 JDK 抛出异常 IllegalArgumentException ，该异常是 RuntimeException 的子类，属于不受检异常。JDK 中使用 3 个常量来预置定义优先级的值，如下：

```java
public static final int MIN_PRIORITY = 1; 
public static final int NORM_PRIORITY = 5; 
public static final int MAX_PRIORITY = 10; 
```

　　在 Thread类中，方法 setPriority() 的定义为：

　　　　　　　　　　　　![这里写图片描述](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20161211210318131)

------

(1). 线程优先级的继承性

　　在 Java 中，线程的优先级具有继承性，比如 A 线程启动 B 线程， 那么 B 线程的优先级与 A 是一样的。

```java
class MyThread2 extends Thread {
    @Override
    public void run() {
        System.out.println("MyThread2 run priority=" + this.getPriority());
    }
}

public class MyThread1 extends Thread {
    @Override
    public void run() {
        System.out.println("MyThread1 run priority=" + this.getPriority());
        MyThread2 thread2 = new MyThread2();
        thread2.start();
    }

    public static void main(String[] args) {
        System.out.println("main thread begin priority="
                + Thread.currentThread().getPriority());
        Thread.currentThread().setPriority(6);
        System.out.println("main thread end   priority="
                + Thread.currentThread().getPriority());
        MyThread1 thread1 = new MyThread1();
        thread1.start();
    }
}/* Output:
        main thread begin priority=5
        main thread end   priority=6
        MyThread1 run priority=6
        MyThread2 run priority=6
 *///:~
```

------

(2). 线程优先级的规则性和随机性

　　**线程的优先级具有一定的规则性，也就是CPU尽量将执行资源让给优先级比较高的线程。**特别地，高优先级的线程总是大部分先执行完，但并不一定所有的高优先级线程都能先执行完。

------

6)、守护线程 (Daemon)

　　**在 Java 中，线程可以分为两种类型，即用户线程和守护线程。**守护线程是一种特殊的线程，具有“陪伴”的含义：当进程中不存在非守护线程时，则守护线程自动销毁，典型的守护线程就是垃圾回收线程。**任何一个守护线程都是整个JVM中所有非守护线程的保姆，只要当前JVM实例中存在任何一个非守护线程没有结束，守护线程就在工作；只有当最后一个非守护线程结束时，守护线程才随着JVM一同结束工作。** 在 Thread类中，方法 setDaemon() 的定义为：

　　　　　　　　　　　　![这里写图片描述](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20161211210235505)

```java
public class MyThread extends Thread {

    private int i = 0;

    @Override
    public void run() {
        try {
            while (true) {
                i++;
                System.out.println("i=" + (i));
                Thread.sleep(1000);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        try {
            MyThread thread = new MyThread();
            thread.setDaemon(true);       //设置为守护线程
            thread.start();
            Thread.sleep(3000);
            System.out.println("main 线程结束，也意味着守护线程 thread 将要结束");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}/* Output: (结果不唯一)
        i=1
        i=2
        i=3
        main 线程结束，也意味着守护线程 thread 将要结束
 *///:~
```

------

## 三. 小结

1). 对于上述线程的各项基本操作，其 **所操作的对象** 满足：

- 若该操作是静态方法，也就是说，该方法属于类而非具体的某个对象，那么该操作的作用对象就是 currentThread() 方法所返回 Thread 对象；
- 若该操作是实例方法，也就是说，该方法属于对象，那么该操作的作用对象就是调用该方法的 Thread 对象。

------

2). 对于上述线程的各项基本操作，有：

- **线程一旦被阻塞，就会释放 CPU；**
- **当线程出现异常且没有捕获处理时，JVM会自动释放当前线程占用的锁，因此不会由于异常导致出现死锁现象。**
- **对于一个线程，CPU 的释放 与 锁的释放没有必然联系。**

------

3). Thread类 中的方法调用与线程状态关系如下图：
　　　　
　　　　　　　　　　　　　![Thread方法与状态.jpg-72.4kB](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/Thread_defien_status11e.jpg)