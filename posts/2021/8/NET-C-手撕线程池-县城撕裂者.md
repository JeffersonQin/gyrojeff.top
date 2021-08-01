---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-01 12:18:11'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-01 12:18:11'
parent: 0
password: ''
slug: NET-C-手撕线程池-县城撕裂者
status: publish
tags: [.NET, C#]
template: ''
title: .NET C# 手撕线程池 | 县城撕裂者
type: post
---
我们都知道C#有现成的`ThreadPool`，但是为什么不手撕线程，成为县城撕裂者呢？

# 关于线程池

From Wikipedia: https://zh.wikipedia.org/wiki/线程池

**线程池**（英语：thread pool）：一种线程使用模式。线程过多会带来调度开销，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务。这避免了在处理短时间任务时创建与销毁线程的代价。线程池不仅能够保证内核的充分利用，还能防止过分调度。可用线程数量应该取决于可用的并发处理器、处理器内核、内存、网络sockets等的数量。 例如，线程数一般取cpu数量+2比较合适，线程数过多会导致额外的线程切换开销。

任务调度以执行线程的常见方法是使用同步队列，称作任务队列。池中的线程等待队列中的任务，并把执行完的任务放入完成队列中。

线程池模式一般分为两种：HS/HA半同步/半异步模式、L/F领导者与跟随者模式。

- 半同步/半异步模式又称为生产者消费者模式，是比较常见的实现方式，比较简单。分为同步层、队列层、异步层三层。同步层的主线程处理工作任务并存入工作队列，工作线程从工作队列取出任务进行处理，如果工作队列为空，则取不到任务的工作线程进入挂起状态。由于线程间有数据通信，因此不适于大数据量交换的场合。
- 领导者跟随者模式，在线程池中的线程可处在3种状态之一：领导者leader、追随者follower或工作者processor。任何时刻线程池只有一个领导者线程。事件到达时，领导者线程负责消息分离，并从处于追随者线程中选出一个来当继任领导者，然后将自身设置为工作者状态去处置该事件。处理完毕后工作者线程将自身的状态置为追随者。这一模式实现复杂，但避免了线程间交换任务数据，提高了CPU cache相似性。在ACE(Adaptive Communication Environment)中，提供了领导者跟随者模式实现。

线程池的伸缩性对性能有较大的影响。

- 创建太多线程，将会浪费一定的资源，有些线程未被充分使用。
- 销毁太多线程，将导致之后浪费时间再次创建它们。
- 创建线程太慢，将会导致长时间的等待，性能变差。
- 销毁线程太慢，导致其它线程资源饥饿。

我们这里实现的是**半异步模式**。

# C#中`lock`的用法

官方文档：https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/lock-statement

> `lock` 语句获取给定对象的互斥 lock，执行语句块，然后释放 lock。 持有 lock 时，持有 lock 的线程可以再次获取并释放 lock。 阻止任何其他线程获取 lock 并等待释放 lock。

通俗的说 `lock(x)` 语句可以保证`x`元素同时只被当前线程访问，如果其他线程要用`x`，那么将会等待，直到结束`lock`块。

注意：`x`必须要是引用类型 (`Reference Type`) 的对象, https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/reference-types

# C#中`int / long / uint / ulong`的线程安全操作

官方文档：https://docs.microsoft.com/en-us/dotnet/api/system.threading.interlocked?view=net-5.0

里面详细列举了如何操作。

# C#中线程安全的集合类

官方文档：https://docs.microsoft.com/zh-cn/dotnet/standard/collections/thread-safe/

|                类型                 |                                                          描述                                                          |
| :---------------------------------: | :--------------------------------------------------------------------------------------------------------------------: |
|       `BlockingCollection<T>`       | 为实现 `IProducerConsumerCollection<T>` 的所有类型提供限制和阻止功能。有关详细信息，请参阅 `BlockingCollection` 概述。 |
| `ConcurrentDictionary<TKey,TValue>` |                                               键值对字典的线程安全实现。                                               |
|        `ConcurrentQueue<T>`         |                                          FIFO（先进先出）队列的线程安全实现。                                          |
|        `ConcurrentStack<T>`         |                                          LIFO（后进先出）堆栈的线程安全实现。                                          |
|         `ConcurrentBag<T>`          |                                              无序元素集合的线程安全实现。                                              |
|  `IProducerConsumerCollection<T>`   |                                  类型必须实现以在 `BlockingCollection` 中使用的接口。                                  |

# `Thread` or `Task`?

注意：在`C#`当中，两者差别不大。

一般而言，`Thread`是指线程，而`Task`是指任务，任务需要线程来实现。

# `stackoverflow`上的县城撕裂者

https://stackoverflow.com/questions/5826981/how-to-reuse-threads-in-net-3-5

注：
- 关于`Monitor.PulseAll(object)`: https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.monitor.pulse?view=net-5.0

```C#
public class SuperQueue<T> : IDisposable where T : class
{
    readonly object _locker = new object();
    readonly List<Thread> _workers;
    readonly Queue<T> _taskQueue = new Queue<T>();
    readonly Action<T> _dequeueAction;

    /// <summary>
    /// Initializes a new instance of the <see cref="SuperQueue{T}"/> class.
    /// </summary>
    /// <param name="workerCount">The worker count.</param>
    /// <param name="dequeueAction">The dequeue action.</param>
    public SuperQueue(int workerCount, Action<T> dequeueAction)
    {
        _dequeueAction = dequeueAction;
        _workers = new List<Thread>(workerCount);

        // Create and start a separate thread for each worker
        for (int i = 0; i < workerCount; i++)
        {
            Thread t = new Thread(Consume) { IsBackground = true, Name = string.Format("SuperQueue worker {0}",i )};
            _workers.Add(t);
            t.Start();
        }
    }

    /// <summary>
    /// Enqueues the task.
    /// </summary>
    /// <param name="task">The task.</param>
    public void EnqueueTask(T task)
    {
        lock (_locker)
        {
            _taskQueue.Enqueue(task);
            Monitor.PulseAll(_locker);
        }
    }

    /// <summary>
    /// Consumes this instance.
    /// </summary>
    void Consume()
    {
        while (true)
        {
            T item;
            lock (_locker)
            {
                while (_taskQueue.Count == 0) Monitor.Wait(_locker);
                item = _taskQueue.Dequeue();
            }
            if (item == null) return;

            // run actual method
            _dequeueAction(item);
        }
    }

    /// <summary>
    /// Performs application-defined tasks associated with freeing, releasing, or resetting unmanaged resources.
    /// </summary>
    public void Dispose()
    {
        // Enqueue one null task per worker to make each exit.
        _workers.ForEach(thread => EnqueueTask(null));

        _workers.ForEach(thread => thread.Join());

    }
}
```

# 稍加改进

由于我还有想要知晓什么时候任务结束的需求，所以我设计了一个计算`idleWorker`数量的方法：

```C#
using System;
using System.Collections.Generic;
using System.Threading;

namespace Ayase.ThreadLib
{
    /// <summary>
    /// Custom Thread Pool Implementation.
    /// Modified from https://stackoverflow.com/questions/5826981/how-to-reuse-threads-in-net-3-5,
    /// by dashton@stackoverflow, ChrisWue@stackoverflow
    /// </summary>
    /// <typeparam name="T"></typeparam>
    public class IThreadPool<T> : IDisposable where T : class
    {
        private readonly object _locker = new object();
        private readonly object _idleLocker = new object();
        private readonly List<Thread> _workers;
        private readonly Queue<T> _taskQueue = new Queue<T>();
        private readonly Action<T> _dequeueAction;
        public readonly long WorkerCount;

        private int _idleWorkerCount = 0;


        /// <summary>
        /// The count of idle worker
        /// </summary>
        public int IdleWorkerCount
        {
            get { return _idleWorkerCount; }
        }

        public bool Idle;

        /// <summary>
        /// Initializes a new instance of the <see cref="IThreadPool{T}"/> class.
        /// </summary>
        /// <param name="workerCount">The worker count.</param>
        /// <param name="dequeueAction">The dequeue action.</param>
        public IThreadPool(int workerCount, Action<T> dequeueAction)
        {
            Idle = true;
            WorkerCount = workerCount;
            _idleWorkerCount = workerCount;
            _dequeueAction = dequeueAction;
            _workers = new List<Thread>(workerCount);

            // Create and start a separate thread for each worker
            for (int i = 0; i < workerCount; i ++)
            {
                Thread t = new Thread(Consume) { IsBackground = true, Name = string.Format("IThreadPool worker {0}", i) };
                _workers.Add(t);
                t.Start();
            }
        }


        /// <summary>
        /// Enqueues the task.
        /// </summary>
        /// <param name="task">The task.</param>
        public void EnqueueTask(T task)
        {
            lock (_locker)
            {
                _taskQueue.Enqueue(task);
                Idle = false;
                Monitor.PulseAll(_locker);
            }
        }


        /// <summary>
        /// Consumes this instance.
        /// </summary>
        private void Consume()
        {
            while (true)
            {
                T item;
                lock (_locker)
                {
                    while (_taskQueue.Count == 0) Monitor.Wait(_locker);
                    item = _taskQueue.Dequeue();
                }
                if (item == null) return;

                Interlocked.Decrement(ref _idleWorkerCount);
                // run actual method
                _dequeueAction(item);
                Interlocked.Increment(ref _idleWorkerCount);
                if (_idleWorkerCount == WorkerCount && _taskQueue.Count == 0)
                {
                    lock (_idleLocker)
                    {
                        Idle = true;
                        Monitor.PulseAll(_idleLocker);
                    }
                } else Idle = false;
            }
        }


        /// <summary>
        /// Waiting for everything to finish.
        /// </summary>
        public void Join()
        {
            lock (_idleLocker)
            {
                while (!Idle) Monitor.Wait(_idleLocker);
            }
            Dispose();
        }


        /// <summary>
        /// Performs application-defined tasks associated with freeing, releasing, or resetting unmanaged resources.
        /// </summary>
        public void Dispose()
        {
            // Enqueue one null task per worker to make each exit.
            _workers.ForEach(thread => EnqueueTask(null));
            _workers.ForEach(thread => thread.Join());
        }
    }
}
```
