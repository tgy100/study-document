### 一.线程的状态

在Java中线程的状态一共被分成6种：

![](image/thread_run_ state.jpg)

1. 初始态：NEW

   创建一个Thread对象，但还未调用start()启动线程时，线程处于初始态。

2. 运行态：RUNNABLE

   在Java中，运行态包括就绪态 和 运行态。

   - 就绪态
     - 该状态下的线程已经获得执行所需的所有资源，只要CPU分配执行权就能运行。
     - 所有就绪态的线程存放在就绪队列中。
   - 运行态
     - 获得CPU执行权，正在执行的线程。
     - 由于一个CPU同一时刻只能执行一条线程，因此每个CPU每个时刻只有一条运行态的线程。

3. 阻塞态

   - 当一条正在执行的线程请求某一资源失败时，就会进入阻塞态。
   - 而在Java中，阻塞态专指请求锁失败时进入的状态。
   - 由一个阻塞队列存放所有阻塞态的线程。
   - 处于阻塞态的线程会不断请求资源，一旦请求成功，就会进入就绪队列，等待执行。

   PS：锁、IO、Socket等都资源。

4. 等待态

   - 当前线程中调用wait、join、park函数时，当前线程就会进入等待态。
   - 也有一个等待队列存放所有等待态的线程。
   - 线程处于等待态表示它需要等待其他线程的指示才能继续运行。
   - 进入等待态的线程会释放CPU执行权，并释放资源（如：锁）

5. 超时等待态

   - 当运行中的线程调用sleep(time)、wait、join、parkNanos、parkUntil时，就会进入该状态；
   - 它和等待态一样，并不是因为请求不到资源，而是主动进入，并且进入后需要其他线程唤醒；
   - 进入该状态后释放CPU执行权 和 占有的资源。
   - 与等待态的区别：到了超时时间后自动进入阻塞队列，开始竞争锁。

6. 终止态

   线程执行结束后的状态。

### 二.创建线程的方式

#### 1.继承Thread类

```java
class MyThread extends Thread{

    @Override
    public void run() {

        for (int i = 0; i < 30; i++) {

            System.out.println("子线程执行 +" + i);
        }
    }
}
```

```java
//调用
new MyThread().start();
```

#### 2.Runnable接口

1. 实现Runnable接口

   ```java
   class MyRunnable implements Runnable{
   
   	@Override
       public void run() {
           for (int i = 0; i < 30; i++) {
   
               System.out.println("子线程Runnable执行 +" + i);
           }
       }
   }
   ```

   ```java
   //调用
   new Thread(new MyRunnable()).start();
   ```

2. 内部类

   ```java
   new Thread(new Runnable() {
       @Override
       public void run() {
   
           for (int i = 0; i < 30; i++) {
   
           	System.out.println("子线程Runnable执行 +" + i);
           }
       }
   });
   ```

3. lambda表达式

   ```java
   new Thread(()->{
   
       for (int i = 0; i < 30; i++) {
   
       	System.out.println("子线程Runnable执行 +" + i);
       }
   }).start();
   ```

#### 3.有返回值和异常

继承Callable接口，实现call方法

```java
public class ReturnValueThread implements Callable<Integer> {


    @Override
    public Integer call() throws Exception {

        System.out.println("等待中.....");
        Thread.sleep(1000);
        return 3;
    }

    public static void main(String[] args) throws Exception {

        //创建未来任务
        FutureTask<Integer> futureTask = new FutureTask<>(new ReturnValueThread());
        
        //执行未来任务
        new Thread(futureTask).start();

        System.out.println("我先做点别的");
        //获取现场执行的返回值
        int num = futureTask.get();

        System.out.println(num);

    }
}
```

#### 4.定时器

#### 5.线程池

#### 6.spring

#### 7.lambda表达式