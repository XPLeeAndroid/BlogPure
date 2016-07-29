---
title: 安卓中使用ThreadPoolExcutor
date: 2016-07-29 10:14:02
tags:

 - android
 - 翻译
 
---


> - 原文链接 [https://medium.freecodecamp.com/threadpoolexecutor-in-android-8e9d22330ee3#.hiw1y4s2e](https://medium.freecodecamp.com/threadpoolexecutor-in-android-8e9d22330ee3#.hiw1y4s2e)
> - 翻译： [Adamin90](https://github.com/adamin1990)
> - 转载请注明出处，谢谢！

![](/img/using_threadpoolexcutor.png)

这篇文章将涉及到线程池，线程池执行程序，和他们在Android中的使用。
我们将使用很多的利用，详细的（thoroughly）介绍这些主题。
<!--more-->
# Thread Pools (线程池) #

一个线程池管理一池的工作线程（准确的数量依赖于它的实现方式）。
一个task队列等待池中的空闲线程执行队列中的task.Task被生产者加入队列中，工作线程作为消费者，只要池中有空闲线程在等待新的后台任务，就会从task队列中消费任务。

# ThreadPoolExcutor #

ThreadPoolExcutor 从线程池中的一个线程执行一个给定的task。

    ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
	int corePoolSize,
	int maximumPoolSize,
	long keepAliveTime,
	TimeUnit unit,
	BlockingQueue<Runnable> workQueue
	);

参数解释：

1. **corePoolSize:** 线程池中保留线程的最小数目，最开始线程池中没有线程，但是随着task被加入队列，新线程被创建。如果有空闲的线程，但是线程的数目小于corePoolSize，就会创建新的线程。
2. **maximumPoolSize:** 线程池中线程的最大值，如果线程数量超过corePoolSize,线程数量>=corePoolSize,那么只有队列满的时候才会创建新的工作线程。
3. **keepAliveTime:** 当线程数量超过corepoolsize，非corepoolsize的空闲线程将等待一个新的task，如果在这个定义的时间参数内没有等到新的task，该线程将被终止。
4. **unit：** **keppAliveTime**的时间单位
5. **workQueue:**  task队列，持有runnable task，必须是一个[BlockingQueue](https://developer.android.com/reference/java/util/concurrent/BlockingQueue.html).

# 为什么在Android和JAVA应用程序中使用Thread Pool Executor? #

1. 它是一个强大的任务执行框架，支持任务添加到队列，任务取消，任务优先级。
2. 降低了线程创建的开销，它在线程池内管理一定数量的线程。

# 在Android中使用ThreadPoolExcutor #

首先，创建一个PriorityThreadFactory:

    import android.os.Process;
	
	import java.util.concurrent.ThreadFactory;
	
	/**
	 * Created by Adam on 2016/7/29.
	 */
	public class PriorityThreadFactory implements ThreadFactory {
    private final int mThreadPrority;

    public PriorityThreadFactory(int mThreadPrority) {
        this.mThreadPrority = mThreadPrority;
    }

    @Override
    public Thread newThread(final Runnable r) {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                try {
                    Process.setThreadPriority(mThreadPrority);
                } catch (IllegalArgumentException e) {
                    e.printStackTrace();
                } catch (SecurityException e) {
                    e.printStackTrace();
                }
                r.run();
                ;

            }
        };
        return new Thread(runnable);
    }
	}


创建一个MainThreadExecutor:

    import android.os.Handler;
	import android.os.Looper;
		
	import java.util.concurrent.Executor;

	/**
	 * Created by Adam on 2016/7/29.
	 */
	public class MainThreadExecutor implements Executor {

    private final Handler handler = new Handler(Looper.getMainLooper());

    @Override
    public void execute(Runnable command) {

        handler.post(command);

    }
	}


创建一个DefaultExecutorSupplier:

    import android.os.Process;
	
	import java.util.concurrent.Executor;
	import java.util.concurrent.LinkedBlockingDeque;
	import java.util.concurrent.LinkedBlockingQueue;
	import java.util.concurrent.ThreadFactory;
	import java.util.concurrent.ThreadPoolExecutor;
	import java.util.concurrent.TimeUnit;
	
	/**
	 * Created by Adam on 2016/7/29.
	 */
	public class DefaultExecutorSupplier {
    /*
	    *指定线程数量
     */
    public static final int NUMBER_OF_CORES = Runtime.getRuntime().availableProcessors();
    /**
	     * 后台任务的线程池
     */
    private final ThreadPoolExecutor mForBackgroundTasks;
    /**
	     * 轻量后台任务的线程池
     */
    private final ThreadPoolExecutor mForLightWeightBackgroundTasks;

    /**
	     * 主线程任务的线程池executor
     */
    private final Executor mMainThreadExcutor;

    private static DefaultExecutorSupplier mInstance;

    /**
	     * 返回DefaultExecutorSupplier的实例
     */
    public static DefaultExecutorSupplier getInstance() {

        if (mInstance == null) {

            synchronized (DefaultExecutorSupplier.class) {
                mInstance = new DefaultExecutorSupplier();
            }
        }

        return mInstance;

    }

    private DefaultExecutorSupplier() {
        ThreadFactory backgroundPriorityThreadFactory = new PriorityThreadFactory(Process.THREAD_PRIORITY_BACKGROUND);
        mForBackgroundTasks = new ThreadPoolExecutor(
                NUMBER_OF_CORES * 2,
                NUMBER_OF_CORES * 2,
                60L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<Runnable>(),
                backgroundPriorityThreadFactory

        );

        mForLightWeightBackgroundTasks = new ThreadPoolExecutor(
                NUMBER_OF_CORES * 2,
                NUMBER_OF_CORES * 2,
                60L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<Runnable>(),
                backgroundPriorityThreadFactory
        );

        mMainThreadExcutor = new MainThreadExecutor();
    }


    /*
	  * returns the thread pool executor for background task
	  */
    public ThreadPoolExecutor forBackgroundTasks() {
        return mForBackgroundTasks;
    }

    /*
	    * returns the thread pool executor for light weight background task
	    */
    public ThreadPoolExecutor forLightWeightBackgroundTasks() {
        return mForLightWeightBackgroundTasks;
    }

    /*
	    * returns the thread pool executor for main thread task
	    */
    public Executor forMainThreadTasks() {
        return mMainThreadExcutor;
    }
	
	
	}

**注意：不同线程池的数量依赖于你的需求**

# 现在在你的代码中这样使用 #

        /*
		* 后台任务
		*/
    public void doSomeBackgroundWork() {
        DefaultExecutorSupplier.getInstance().forBackgroundTasks()
                .execute(new Runnable() {
                    @Override
                    public void run() {
                        // 在这里后台工作.
                    }
                });
    }

    /*
	    * 轻量后台任务
	    */
    public void doSomeLightWeightBackgroundWork() {
        DefaultExecutorSupplier.getInstance().forLightWeightBackgroundTasks()
                .execute(new Runnable() {
                    @Override
                    public void run() {
                        // 在这里做一些轻量后台工作.
                    }
                });
    }

    /*
	    * 主线程任务
	    */
    public void doSomeMainThreadWork() {
        DefaultExecutorSupplier.getInstance().forMainThreadTasks()
                .execute(new Runnable() {
                    @Override
                    public void run() {
                        // 做一些中线程工作.
                    }
                });
    }


这样，我们可以为网络任务，I/O任务，重型的后台任务和其他任务创建不同的线程池。

# 怎样取消一个task？ #

为了取消一个task，你必须得到task的**future**。所以，不要使用**execute**，使用**submit**，将返回一个future。现在future就可以用来取消task了。

     Future future= DefaultExecutorSupplier.getInstance().forBackgroundTasks()
                .submit(new Runnable() {
                    @Override
                    public void run() {

                    }
                });

        future.cancel(true);

# 如何设置task的优先级？ #

假设队列里有20个任务，线程池持有4个线程，我们根据task的优先级处理他们，因为线程池此时同时可处理4个线程。

但是假设我们需要我们最后推进队列的任务最先执行，我们需要为该任务设置**立即**的优先当线程从队列里拿取新任务时。

为了设置任务的优先级，我们需要创建一个线程池executor。

为优先级创建一个枚举类:

    /**
	 * Created by Adam on 2016/7/29.
	 */
	public enum Priority {
    /**
	     * 注意：不要在任何情况下改变顺序，否则会使排序不准确
     */
    /**
	     * 最低优先级，预加载数据用
     */
    LOW,
    /**
	     * 中优先级
     */
    MEDIUM,
    /**
	     * 高优先级
     */
    HIGH,
    /**
	     * 立即
     */
    IMMEDIATE,
	}


## 创建一个PriorityRunnable ##

    public class PriorityRunnable implements Runnable {

    private final Priority priority;

    public PriorityRunnable(Priority priority) {
        this.priority = priority;
    }

    @Override
    public void run() {

    }
    public Priority getPriority(){
        return  priority;
    }
	}

创建一个PriorityThreadPoolExecutor,继承自ThreadPoolExecutor.我们必须创建PriorityFutureTask,将实现Comparable<PriorityFutureTask>接口。

    import java.util.concurrent.BlockingQueue;
	import java.util.concurrent.Callable;
	import java.util.concurrent.Future;
	import java.util.concurrent.FutureTask;
	import java.util.concurrent.PriorityBlockingQueue;
	import java.util.concurrent.ThreadFactory;
	import java.util.concurrent.ThreadPoolExecutor;
	import java.util.concurrent.TimeUnit;
	
	/**
	 * Created by Adam on 2016/7/29.
	 */
	public class PriorityThreadPoolExecutor extends ThreadPoolExecutor {
    public PriorityThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory) {
        super(corePoolSize, maximumPoolSize, keepAliveTime, unit, new PriorityBlockingQueue<Runnable>(), threadFactory);
    }


    @Override
    public Future<?> submit(Runnable task) {
        PriorityFutureTask futureTask = new PriorityFutureTask((PriorityRunnable) task);
        execute(futureTask);
        return futureTask;
    }

    private static final class PriorityFutureTask extends FutureTask<PriorityRunnable>
            implements Comparable<PriorityFutureTask> {

        private final PriorityRunnable priorityRunnable;

        public PriorityFutureTask(PriorityRunnable priorityRunnable) {
            super(priorityRunnable, null);
            this.priorityRunnable = priorityRunnable;
        }

        @Override
        public int compareTo(PriorityFutureTask another) {
            Priority p1 = priorityRunnable.getPriority();
            Priority p2 = another.priorityRunnable.getPriority();
            return p2.ordinal() - p1.ordinal();
        }
    }
	}

首先在DefaultExcutorSupplier,用PriorityThreadPoolExecutor代替ThreadPoolExecutor.

            ThreadFactory backgroundPriorityThreadFactory = new PriorityThreadFactory(Process.THREAD_PRIORITY_BACKGROUND);
	//        mForBackgroundTasks = new ThreadPoolExecutor(
	//                NUMBER_OF_CORES * 2,
	//                NUMBER_OF_CORES * 2,
	//                60L,
	//                TimeUnit.SECONDS,
	//                new LinkedBlockingQueue<Runnable>(),
	//                backgroundPriorityThreadFactory
	//
	//        );
        mForBackgroundTasks=new PriorityThreadPoolExecutor(
                NUMBER_OF_CORES * 2,
                NUMBER_OF_CORES * 2,
                60L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<Runnable>(),
                backgroundPriorityThreadFactory
        );


下面的例子演示了如何设置高优先级：

       public void doSomeTaskAtHighPriority(){
        DefaultExecutorSupplier.getInstance().forBackgroundTasks()
                .submit(new PriorityRunnable(Priority.HIGH){
                    @Override
                    public void run() {
                        super.run();
                    }
                });
    }