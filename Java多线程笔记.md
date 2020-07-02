##### Java多线程笔记

- 线程创建
  - 继承Thread类
  - 实现Runnable接口
  - 实现Callable接口

1. Thread

   - 自定义线程类继承Thread类
   - 重写run方法，编写线程执行体
   - 创建线程对象，调用start()方法启动线程

   - 总结：
     - 注意，线程开启不一定立即执行，有cpu调度执行

```java
public class ThreadDemo extends Thread{
    @Override
    public void run(){
        for (int i = 0; i < 20; i++) {
            System.out.println("我在看代码----" + i);
        }
    }

    public static void main(String[] args) {

        ThreadDemo threadDemo = new ThreadDemo();
        threadDemo.start();

        for (int i = 0; i < 2000; i++) {
            System.out.println("我在学习多线程--" + i);
        }
    }
}
```

2. Runnable

- 实现runnable接口，重写run方法，执行线程需要丢入runnable接口实现类，调用start方法

```java
public class RunnableDemo1 implements Runnable{
    @Override
    public void run(){
        for (int i = 0; i < 20; i++) {
            System.out.println("我在看代码----" + i);
        }
    }

    public static void main(String[] args) {

        RunnableDemo1 runnableDemo1 = new RunnableDemo1();
        new Thread(runnableDemo1).start();
        for (int i = 0; i < 2000; i++) {
            System.out.println("我在学习多线程--" + i);
        }
    }
}
```

3. Callable

```java
public class CallableDemo implements Callable<Object> {
    @Override
    public Object call() throws Exception {
        return 111;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {

        CallableDemo callableDemo = new CallableDemo();

        ExecutorService executorService = Executors.newFixedThreadPool(3);

        Future<Object> f1 = executorService.submit(callableDemo);

        Object ob = f1.get();
        System.out.println(ob);

        executorService.shutdownNow();

    }
}
```

4. 静态代理模式

   - 真实对象和代理对象都要实现同一个接口
   - 代理对象要代理真实对象
   - 好处：
     - 代理对象可以做很多真实对象无法做的事
     - 真实对象专注做自己的事儿

   ```java
   public class Proxy {
       public static void main(String[] args) {
           WeddingCompany weddingCompany = new WeddingCompany(new You());
           weddingCompany.HappyMarry();
       }
   }
   
   interface Marry{
       void HappyMarry();
   }
   
   class You implements Marry{
   
       @Override
       public void HappyMarry() {
           System.out.println("结婚了");
       }
   }
   
   class WeddingCompany implements Marry{
   
       private Marry marry;
   
       public WeddingCompany(Marry marry){
           this.marry = marry;
       }
   
       @Override
       public void HappyMarry() {
           before();
           this.marry.HappyMarry();
           after();
       }
   
       private void before(){
           System.out.println("结婚之前");
       }
   
       private void after(){
           System.out.println("结婚之后");
       }
   }
   ```

***

- 线程状态
  - 创建状态	Thread t = new Thread();
  - 就绪状态    start();
  - 运行状态    cpu调度
  - 阻塞状态    sleep();
  - 死亡状态    运行结束，死亡状态
- 线程方法
  -  setPriority(int newPriority) 更改线程的优先级
  - sleep() 在指定毫秒数内让当前正在执行的线程休眠
  - join() 等待该线程终止
  - yield() 暂停当前正在执行的线程，并且执行其他线程
  - interrupt 中断线程（别用这个方式）
  - isAlive() 测试线程是否处于活动状态

***

- 停止线程

  - 不推荐使用jdk提供的stop，destroy方法
  - 推荐线程自己停下来
  - 建议使用一个标识位进行终止变量
    - 当flag = false，则线程终止运行。

  ```java
  //测试stop
  //建议线程正常停止，利用次数，不建议死循环
  //建议使用标志位
  //不要使用stop或destroy方法
  public class ThreadDemo2 implements Runnable{
  
      //设置一个标识
      private boolean flag = true;
      @Override
      public void run() {
          int i = 0;
          while (flag){
              System.out.println("run thread" + i++);
          }
      }
  
      public void stop(){
          this.flag = false;
          System.out.println("线程停止");
      }
  
      public static void main(String[] args) {
          ThreadDemo2 threadDemo2 = new ThreadDemo2();
  
          new Thread(threadDemo2).start();
  
          for (int i = 0; i < 1000; i++) {
              System.out.println("main" + i);
              if(i == 900){
                  threadDemo2.stop();
              }
          }
      }
  }
  ```

***

- 线程休眠

  - sleep指定当前线程阻塞的毫秒数
  - sleep存在interruptedException异常
  - sleep时间达到后线程进入就绪状态
  - sleep可以模拟网络延时，倒计时等
  - 每个对象都有一把锁，sleep不会释放锁

  ```java
  public class SleepDemo{
  
      public static void tenDown() throws InterruptedException {
          int num = 10;
          while (true){
              Thread.sleep(1000);
              System.out.println(num--);
              if(num <= 0){
                  break;
              }
          }
      }
  
  
      public static void main(String[] args) throws InterruptedException {
  //        tenDown();
          Date time = new Date(System.currentTimeMillis());
          while (true){
              Thread.sleep(1000);
              System.out.println(new SimpleDateFormat("HH:mm:ss").format(time ));
              time = new Date(System.currentTimeMillis());
          }
      }
  }
  ```

***

- 线程礼让
  -  yield，让当前正在执行的线程暂停，但不阻塞
  - 将线程从运行状态转为就绪状态
  - 让cpu重新调度，礼让不一定成功，看cpu心情

```java
public class YieldDemo{
    public static void main(String[] args) {
        MyYield myYield = new MyYield();
        new Thread(myYield,"a").start();
        new Thread(myYield,"b").start();
    }
}

class MyYield implements Runnable {

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"--start");
        Thread.yield();
        System.out.println(Thread.currentThread().getName()+"--end");
    }
}
```

***

- join
  - 合并线程，等待此线程执行完成后，再执行其他线程，其他线程阻塞
  - 可以想象成插队

```java
public class JoinDemo implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("vip来了"+i);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        JoinDemo joinDemo = new JoinDemo();
        Thread thread = new Thread(joinDemo);
        thread.start();

        for (int i = 0; i < 1000; i++) {
            if(i == 200){
                thread.join();
            }
            System.out.println("main"+i);
        }
    }
}
```

***

- 线程状态观测
  - [`NEW`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#NEW)
    尚未启动的线程处于此状态。
  - [`RUNNABLE`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#RUNNABLE)
    在Java虚拟机中执行的线程处于此状态。
  - [`BLOCKED`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#BLOCKED)
    被阻塞等待监视器锁定的线程处于此状态。
  - [`WAITING`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#WAITING)
    正在等待另一个线程执行特定动作的线程处于此状态。
  - [`TIMED_WAITING`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#TIMED_WAITING)
    正在等待另一个线程执行动作达到指定等待时间的线程处于此状态。
  - [`TERMINATED`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#TERMINATED)
    已退出的线程处于此状态。
  - 线程死亡后不可以再start线程。

```java
public class StatusDemo {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("//////");
        });

        //观察状态
        Thread.State state = thread.getState();
        System.out.println(state);

        thread.start();
        state = thread.getState();
        System.out.println(state);

        while (state != Thread.State.TERMINATED){
            Thread.sleep(1000);
            state = thread.getState();
            System.out.println(state);
        }
         
    }
}
```

***

- 线程优先级
  - java提供一个线程调度器来监控线程中启动后进入就绪状态的所有线程，线程调度器按照优先级决定应该调度哪个线程来执行。
  - 线程的优先级用数字表示，范围从1~10
  - 使用以下方法改变或者获取优先级
    - getPrioriry()
    - setPrioriry() 
  - 先设置优先级再启动

  ```java
  public class PrioriityDemo{
      public static void main(String[] args) {
          System.out.println(Thread.currentThread().getName()+"-->"+Thread.currentThread().getPriority());
          MyPriority myPriority = new MyPriority();
  
          Thread t1 = new Thread(myPriority);
          Thread t2 = new Thread(myPriority);
          Thread t3 = new Thread(myPriority);
          Thread t4 = new Thread(myPriority);
  
          t1.start();
          t2.setPriority(1);
          t2.start();
          t3.setPriority(4);
          t3.start();
          t4.setPriority(Thread.MAX_PRIORITY);
          t4.start();
  
      }
  }
  
  class MyPriority implements Runnable{
  
      @Override
      public void run() {
          System.out.println(Thread.currentThread().getName()+"--->"
          +Thread.currentThread().getPriority());
      }
  }
  ```

***

- 守护线程（daemon）

  - 线程分为用户线程和守护线程
  - 虚拟机必须确保用户线程执行完毕
  - 虚拟机不用等待守护线程执行完毕
  - 如：后台记录操作日志，监控内存，垃圾回收等待等等

  ```java
  public class DaemonDemo {
      public static void main(String[] args) {
          God god = new God();
          You1 you1 = new You1();
  
          Thread thread = new Thread(god);
          thread.setDaemon(true);
          thread.start();
  
          new Thread(you1).start();
      }
  }
  
  class You1 implements Runnable{
  
      @Override
      public void run() {
          for (int i = 0; i < 36500; i++) { 
              System.out.println("活着");
          }
          System.out.println("bye");
      }
  }
  
  class God implements Runnable{
  
      @Override
      public void run() {
          while (true){
              System.out.println("上帝保佑你");
          }
      }
  }
  ```

***

- 线程同步  (多个线程操作同一个资源)

  - 处理多线程问题时，多个线程访问同一个对象，并且某些线程还想修改这个对象，这时候我们就需要线程同步，线程同步其实就是一种等待机制，多个需要同时访问此对象的线程进入这个对象的等待池形成队列，等前面线程使用完毕后，下一个线程再使用

  - **队列和锁 **
  - 由于同一个进程的多个线程共享同一块存储空间，在带来方便的同时，也带来了访问冲突的问题，为了确保数据在方法中被访问的正确性，在访问时加入锁机制 synchronized，当一个线程获得对象的排它锁，独占资源，其他线程必须等待，使用后释放锁即可，存在以下问题：
    - 一个线程持有锁会导致其他所有需要此锁的线程挂起
    - 在多线程竞争下，加锁释放锁会导致比较多的上下文切换和调度延时，引起性能问题
    - 如果一个优先级高的线程等待一个优先级低的线程释放锁，会导致优先级倒置，引起性能问题
  - 同步块：synchronized（obj）{}
    - obj可以是任何对象，但推荐使用共享资源作为同步监视器
    - 同步方法中无需指定同步监视器，因为同步监视器就是this，就是这个对象本身，或者是class
    - 同步监视器的执行过程
      1. 第一个线程访问，锁定同步监视器，执行其中代码
      2. 第二个线程访问，发现同步监视器被锁定，无法访问
      3. 第一个线程访问完毕，解锁同步监视器
      4. 第二个线程访问，发现同步监视器没有锁，然后锁定并访问
    - 同步块同步的obj是需要操作的对象

***

- CopyOnWriteArrayList 线程安全的list

***

- 线程协作
  - 生产者消费者模式
    - 加入仓库中只能存放一件产品，生产者将生产出来的产品放入仓库，消费者将仓库中产品取走消费
    - 如果仓库中没有产品，则生产者将产品放入仓库，否则停止生产并等待，知道仓库中的产品被消费者取走为止
    - 如果仓库中放有产品，则消费者可以将产品取走消费，否则停止消费并等待，直到仓库中再次放入产品为止
  - 这是一个线程同步的问题，生产者和消费者共享同一个资源，并且生产者和消费者之间互相依赖，互为条件
    - 对于生产者，没有生产产品之前，要通知消费者等待吗，而生产了产品之后，又需要马上通知消费者消费
    - 对于消费者，在消费之后，要通知消费者已经结束消费，需要生成新的产品，以供消费
    - 在生产者消费者问题中，仅有synchronized是不够的
      - synchronized可阻止并发更新同一个共享资源，实现了同步
      - synchronized不能用来实现不同线程之间的消息传递（通信）

- 线程通信
  - wait()
    - 表示线程一直等待，直到其它线程通知，跟sleep不同的是，wait会释放锁
  - wait（long timeout）制定等待的毫秒数
  - notify（）唤醒一个处于等待状态的线程
  - notifyAll()唤醒同一个对象上所有调用wait方法的线程，优先级别高的线程优先调度

