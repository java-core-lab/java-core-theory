## Java Core: Concurrency

### <a name="start"></a> Вопросы раздела

- [x] [1. Processes and Threads](#intro)

- [x] [2. Creating Threads. Thread types. Thread priorities. Thread states](#creating)

- [x] [3. Using of ExecutorService](#service)

- [x] [4. Threading problem (deadlock, livelock, etc.)](#problem)

- [x] [5. Concurrent collections](#collections)

- [x] [6. Synchronized and volatile keywords. java.util.atomic package ](#keywords)

- [x] [7. Fork/Join Framework](#fork)

- [x] [8. Parallel Streams](#parallel)

[К оглавлению](https://gitlab.com/ITokarev/java-core/blob/master/README.md)


---

### <a name="intro"></a> 1. Processes and Threads

---

#### Процессы
Процесс — это совокупность кода и данных, разделяющих общее виртуальное адресное пространство. Чаще всего одна программа состоит из одного процесса, но бывают и исключения (например, браузер Chrome создает отдельный процесс для каждой вкладки, что дает ему некоторые преимущества, вроде независимости вкладок друг от друга). Процессы изолированы друг от друга, поэтому прямой доступ к памяти чужого процесса невозможен (взаимодействие между процессами осуществляется с помощью специальных средств).

Для каждого процесса ОС создает так называемое «виртуальное адресное пространство», к которому процесс имеет прямой доступ. Это пространство принадлежит процессу, содержит только его данные и находится в полном его распоряжении. Операционная система же отвечает за то, как виртуальное пространство процесса проецируется на физическую память.

Схема этого взаимодействия представлена на картинке. Операционная система оперирует так называемыми страницами памяти, которые представляют собой просто область определенного фиксированного размера. Если процессу становится недостаточно памяти, система выделяет ему дополнительные страницы из физической памяти. Страницы виртуальной памяти могут проецироваться на физическую память в произвольном порядке.


![Processes and memory](https://habrastorage.org/storage2/c37/835/c12/c37835c1280215a60671823d0007e41f.png)

При запуске программы операционная система создает процесс, загружая в его адресное пространство код и данные программы, а затем запускает главный поток созданного процесса.

#### Потоки

Один поток – это одна единица исполнения кода. Каждый поток последовательно выполняет инструкции процесса, которому он принадлежит, параллельно с другими потоками этого процесса.

Следует отдельно обговорить фразу «параллельно с другими потоками». Известно, что на одно ядро процессора, в каждый момент времени, приходится одна единица исполнения. То есть одноядерный процессор может обрабатывать команды только последовательно, по одной за раз (в упрощенном случае). Однако запуск нескольких параллельных потоков возможен и в системах с одноядерными процессорами. В этом случае система будет периодически переключаться между потоками, поочередно давая выполняться то одному, то другому потоку. Такая схема называется псевдо-параллелизмом. Система запоминает состояние (контекст) каждого потока, перед тем как переключиться на другой поток, и восстанавливает его по возвращению к выполнению потока. В контекст потока входят такие параметры, как стек, набор значений регистров процессора, адрес исполняемой команды и прочее…

Проще говоря, при псевдопараллельном выполнении потоков процессор мечется между выполнением нескольких потоков, выполняя по очереди часть каждого из них.

Вот как это выглядит:

![Parallel](https://habrastorage.org/storage2/b26/e6d/c2c/b26e6dc2c8fb51acc03a76ead0c3abd8.png)

Цветные квадраты на рисунке – это инструкции процессора (зеленые – инструкции главного потока, синие – побочного). Выполнение идет слева направо. После запуска побочного потока его инструкции начинают выполняться вперемешку с инструкциями главного потока. Кол-во выполняемых инструкций за каждый подход не определено. 

То, что инструкции параллельных потоков выполняются вперемешку, в некоторых случаях может привести к конфликтам доступа к данным. 

[More information Oracle](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html)
[More information](https://habr.com/post/164487/)

[К оглавлению](#start)

---

###  <a name="creating"></a> 2. Creating Threads. Thread types. Thread priorities. Thread states

---

#### 2.1 Creating threads

An application that creates an instance of Thread must provide the code that will run in that thread. There are two ways to do this:

Provide a Runnable object. The Runnable interface defines a single method, run, meant to contain the code executed in the thread. The Runnable object is passed to the Thread constructor, as in the HelloRunnable example:
```java
public class HelloRunnable implements Runnable {

    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new Thread(new HelloRunnable())).start();
    }

}
```

Subclass Thread. The Thread class itself implements Runnable, though its run method does nothing. An application can subclass Thread, providing its own implementation of run, as in the HelloThread example:

```java
public class HelloThread extends Thread {

    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new HelloThread()).start();
    }

}
```
Notice that both examples invoke Thread.start in order to start the new thread.

Which of these idioms should you use? The first idiom, which employs a Runnable object, is more general, because the Runnable object can subclass a class other than Thread. The second idiom is easier to use in simple applications, but is limited by the fact that your task class must be a descendant of Thread. This lesson focuses on the first approach, which separates the Runnable task from the Thread object that executes the task. Not only is this approach more flexible, but it is applicable to the high-level thread management APIs covered later.

The Thread class defines a number of methods useful for thread management. These include static methods, which provide information about, or affect the status of, the thread invoking the method. The other methods are invoked from other threads involved in managing the thread and Thread object. 

Another way of creating of a thread (we can run that type of thread through using ExecutorService):

```java
public class HelloThread implements java.util.concurrent.Callable<String> {

    public String call(){
        System.out.println("Hello from a thread!");
        return "Callable thread";
    }

}
```

#### 2.2 Types Of Threads In Java

* User Thread :
User threads are threads which are created by the application or user. They are high priority threads. JVM (Java Virtual Machine) will not exit until all user threads finish their execution. JVM wait for these threads to finish their task. These threads are foreground threads.

* Daemon Thread :
Daemon threads are threads which are mostly created by the JVM. These threads always run in background. These threads are used to perform some background tasks like garbage collection and house-keeping tasks. These threads are less priority threads. JVM will not wait for these threads to finish their execution. JVM will exit as soon as all user threads finish their execution. JVM doesn’t wait for daemon threads to finish their task.

[More information about types](https://javaconceptoftheday.com/types-threads-java/)

#### 2.3 Thread priorities

Each thread have a priority. Priorities are represented by a number between 1 and 10. In most cases, thread schedular schedules the threads according to their priority (known as preemptive scheduling). But it is not guaranteed because it depends on JVM specification that which scheduling it chooses.

3 constants defined in Thread class:

* public static int MIN_PRIORITY
* public static int NORM_PRIORITY
* public static int MAX_PRIORITY

Default priority of a thread is 5 (NORM_PRIORITY). The value of MIN_PRIORITY is 1 and the value of MAX_PRIORITY is 10.

[More info](https://www.javatpoint.com/priority-of-a-thread)

#### 2.4 Thread states

 Thread have following states 
* New
* Runnable
* Running
* Waiting/blocked/sleeping
* Terminated (Dead) 

 Thread states in diagram 
 
[Thread lifecycle diagram](https://lh4.googleusercontent.com/oR0_liUxjMnfgFS-fSuc0x2vCCPLQ0Vdw6w2rBVGloaE_84tRNprqNJEJiyI1unMY8Vpj2CDK9GiQGy03_RmteRz-aM31iIQcZsVZhIH2cLrne_5nY9miXKDmQqEHdY60_WopC0)

Thread states in detail 

 New : When instance of thread is created using new operator it is in new state, but the start() method has not been invoked on the thread yet, thread is not eligible to run yet.
 Thread object is considered alive but thread is not alive yet.

 Runnable :  When start() method is called on thread it enters runnable state. 
As soon as Thread enters runnable state it is eligible to run, but not running. (Thread scheduler has not scheduled the Thread execution yet, Thread has not entered in run() method yet)
A thread first enters the runnable state when the start() method is invoked, but a thread can also return to the runnable state after either running or coming back from a
blocked, waiting, or sleeping state. 
Thread is considered alive in runnable state.
Thread is in Runnable pool.

 Running : Thread scheduler selects thread to go from runnable to running state. In running state Thread starts executing by entering run() method.
Thread scheduler selects thread from the runnable pool on basis of priority, if priority of two threads is same, threads are scheduled in unpredictable manner. Thread scheduler behaviour is completely unpredictable.
 When threads are in running state, yield() method can make thread to go in Runnable state.

 Waiting/blocked/sleeping : In this state a thread is not eligible to run. 
Thread is still alive, but currently it’s not eligible to run. In other words.

 How can Thread go from running to waiting state ?
  By calling wait() method thread go from running to waiting state. In waiting state it will wait for other threads to release object monitor/lock. 
 How can Thread return from waiting to runnable state ?
  Once notify() or notifyAll() method is called object monitor/lock becomes available and thread can again return to runnable state.


 How can Thread go from running to sleeping state ?
   By calling sleep() method thread go from running to sleeping state. In sleeping state it will wait for sleep time to get over.
 How can Thread return from sleeping to runnable state ?
   Once specified sleep time is up thread can again return to runnable state.

 Suspend() method can be used to put thread in waiting state and resume() method is the only way which could put thread in runnable state.

 Thread also may go from running to waiting state if it is waiting for some I/O operation to take place. Once input is available thread may return to running state.


Terminated (Dead) : A thread is considered dead when its run() method completes. 
Once thread is dead it cannot be started again doing so will throw runtimeException i.e. IllegalThreadStateException.

 destroy() method puts thread directly into dead state. 
 
 [К оглавлению](#start)
 
---

### <a name="service"></a> 3. Using of ExecutorService

---

Облегчив с помощью интерфейса Callable создание задач для параллельного выполнения, пакет java.util.concurrent также берет на себя работу по запуску и остановке потоков. Вместо объекта Thread предлагается использовать объект типа ExecutorService, с помощью которого пользователь может просто поместить задачу в очередь на выполнение и ждать получения результата. Можно сказать, что ExecutorService – это значительно усовершенствованная реализация шаблона WorkerThread.

ExecutorService – это интерфейс, поэтому для выполнения задач используются его конкретные потомки, адаптированные под требования разрабатываемого приложения. Однако программисту нет необходимости создавать собственную реализацию ExecutorService, так как в пакете java.util.concurrent уже присутствуют различные варианты реализации ExecutorService. Доступ к ним можно получить через статические методы служебного класса Executors, метод которого newFixedThreadPool возвращает объект типа ExecutorService со встроенной поддержкой шаблона ThreadPool. Также в классе Executors есть и другие методы для создания объектов ExecutorService с различными свойствами.

Наибольший интерес в ExecutorService представляет метод submit, через который задача ставится в очередь на выполнение. На вход этот метод принимает объект типа Callable или Runnable, а возвращает некий параметризованный объект типа Future. Этот объект можно использовать для доступа к результату выполнения задачи, который будет возвращен из метода call соответствующего Callable-объекта. При этом через объект Future можно проверить, закончено ли уже выполнение задачи – с помощью метода isDone и через метод get получить доступ к результату или исключительной ситуации, если в процессе выполнения задачи произошла ошибка.

Таким образом, при запуске задач с помощью классов из пакета java.util.concurrent не требуется прибегать к низкоуровневой поточной функциональности класса Thread, достаточно создать объект типа ExecutorService с нужными свойствами и передать ему на исполнение задачу типа Callable. Впоследствии можно легко просмотреть результат выполнения этой задачи с помощью объекта Future, как показано в листинге 4.

Листинг 4. Запуск задачи с помощью классов пакета java.util.concurrent

```java
 public class ExecutorServiceSample {
     public static void main(String[] args) {
         //создать ExecutorService на базе пула из пяти потоков
         ExecutorService es1 = Executors.newFixedThreadPool(5);
         //поместить задачу в очередь на выполнение
         Future<String> f1 = es1.submit(new CallableSample());        
         while(!f1.isDone()) {
             //подождать пока задача не выполнится
         }
        try {
            //получить результат выполнения задачи
            System.out.println("task has been completed : " + f1.get());
        } catch (InterruptedException ie) {           
            ie.printStackTrace(System.err);
        } catch (ExecutionException ee) {
            ee.printStackTrace(System.err);
        }
        es1.shutdown();
   }
}
```
Стоит обратить внимание на строку, где происходит остановка объекта ExecutorService с помощью метода shutdown. Дело в том, что потоки в объекте ExecutorService не останавливаются сами, как обычно, поэтому их необходимо явно остановить с помощью этого метода, при этом если в ExecutorService находятся невыполненные задачи, то потоки будут остановлены только, когда завершится последняя задача.

[More info](https://docs.oracle.com/javase/tutorial/essential/concurrency/executors.html)

[К оглавлению](#start)

---

###  <a name="problem"></a> 4. Threading problem (deadlock, livelock, etc.)

---

Deadlock describes a situation where two or more threads are blocked forever, waiting for each other. Here's an example.

Alphonse and Gaston are friends, and great believers in courtesy. A strict rule of courtesy is that when you bow to a friend, you must remain bowed until your friend has a chance to return the bow. Unfortunately, this rule does not account for the possibility that two friends might bow to each other at the same time. This example application, Deadlock, models this possibility:

```java
public class Deadlock {
    static class Friend {
        private final String name;
        public Friend(String name) {
            this.name = name;
        }
        public String getName() {
            return this.name;
        }
        public synchronized void bow(Friend bower) {
            System.out.format("%s: %s"
                + "  has bowed to me!%n", 
                this.name, bower.getName());
            bower.bowBack(this);
        }
        public synchronized void bowBack(Friend bower) {
            System.out.format("%s: %s"
                + " has bowed back to me!%n",
                this.name, bower.getName());
        }
    }

    public static void main(String[] args) {
        final Friend alphonse =
            new Friend("Alphonse");
        final Friend gaston =
            new Friend("Gaston");
        new Thread(new Runnable() {
            public void run() { alphonse.bow(gaston); }
        }).start();
        new Thread(new Runnable() {
            public void run() { gaston.bow(alphonse); }
        }).start();
    }
}
```


When Deadlock runs, it's extremely likely that both threads will block when they attempt to invoke bowBack. Neither block will ever end, because each thread is waiting for the other to exit bow.
[Another example of deadlock](https://gitlab.com/ITokarev/java-core/blob/master/src/main/java/by/tokarev/core/deadlock/DeadLock.java)

Starvation and Livelock
Starvation and livelock are much less common a problem than deadlock, but are still problems that every designer of concurrent software is likely to encounter.

Starvation

Starvation describes a situation where a thread is unable to gain regular access to shared resources and is unable to make progress. This happens when shared resources are made unavailable for long periods by "greedy" threads. For example, suppose an object provides a synchronized method that often takes a long time to return. If one thread invokes this method frequently, other threads that also need frequent synchronized access to the same object will often be blocked.

Livelock

A thread often acts in response to the action of another thread. If the other thread's action is also a response to the action of another thread, then livelock may result. As with deadlock, livelocked threads are unable to make further progress. However, the threads are not blocked — they are simply too busy responding to each other to resume work. This is comparable to two people attempting to pass each other in a corridor: Alphonse moves to his left to let Gaston pass, while Gaston moves to his right to let Alphonse pass. Seeing that they are still blocking each other, Alphone moves to his right, while Gaston moves to his left. They're still blocking each other, so...


[Info with examples](https://www.codejava.net/java-core/concurrency/understanding-deadlock-livelock-and-starvation-with-code-examples-in-java) 

[К оглавлению](#start)

---

### <a name="collections"></a> 5. Concurrent collections

---


The java.util.concurrent package includes a number of additions to the Java Collections Framework. These are most easily categorized by the collection interfaces provided:

* BlockingQueue defines a first-in-first-out data structure that blocks or times out when you attempt to add to a full queue, or retrieve from an empty queue.
* ConcurrentMap is a subinterface of java.util.Map that defines useful atomic operations. These operations remove or replace a key-value pair only if the key is present, or add a key-value pair only if the key is absent. Making these operations atomic helps avoid synchronization. The standard general-purpose implementation of ConcurrentMap is ConcurrentHashMap, which is a concurrent analog of HashMap.
* ConcurrentNavigableMap is a subinterface of ConcurrentMap that supports approximate matches. The standard general-purpose implementation of ConcurrentNavigableMap is ConcurrentSkipListMap, which is a concurrent analog of TreeMap.

All of these collections help avoid Memory Consistency Errors by defining a happens-before relationship between an operation that adds an object to the collection with subsequent operations that access or remove that object.

[More info](https://habr.com/company/luxoft/blog/157273/)

[К оглавлению](#start)

---

### <a name="keywords"></a> 6. Synchronized and volatile keywords. java.util.atomic package 

---

The Java programming language provides two basic synchronization idioms: synchronized methods and synchronized statements. 

To make a method synchronized, simply add the synchronized keyword to its declaration:

```java
public class SynchronizedCounter {
    private int c = 0;

    public synchronized void increment() {
        c++;
    }

    public synchronized void decrement() {
        c--;
    }

    public synchronized int value() {
        return c;
    }
}
```
If count is an instance of SynchronizedCounter, then making these methods synchronized has two effects:

* First, it is not possible for two invocations of synchronized methods on the same object to interleave. When one thread is executing a synchronized method for an object, all other threads that invoke synchronized methods for the same object block (suspend execution) until the first thread is done with the object.

* Second, when a synchronized method exits, it automatically establishes a happens-before relationship with any subsequent invocation of a synchronized method for the same object. This guarantees that changes to the state of the object are visible to all threads.

Note that constructors cannot be synchronized — using the synchronized keyword with a constructor is a syntax error. Synchronizing constructors doesn't make sense, because only the thread that creates an object should have access to it while it is being constructed.

Another way to create synchronized code is with synchronized statements. Unlike synchronized methods, synchronized statements must specify the object that provides the intrinsic lock:
```java
public void addName(String name) {
    synchronized(this) {
        lastName = name;
        nameCount++;
    }
    nameList.add(name);
}
```

In this example, the addName method needs to synchronize changes to lastName and nameCount, but also needs to avoid synchronizing invocations of other objects' methods. (Invoking other objects' methods from synchronized code can create problems that are described in the section on Liveness.) Without synchronized statements, there would have to be a separate, unsynchronized method for the sole purpose of invoking nameList.add.

Synchronized statements are also useful for improving concurrency with fine-grained synchronization. Suppose, for example, class MsLunch has two instance fields, c1 and c2, that are never used together. All updates of these fields must be synchronized, but there's no reason to prevent an update of c1 from being interleaved with an update of c2 — and doing so reduces concurrency by creating unnecessary blocking. Instead of using synchronized methods or otherwise using the lock associated with this, we create two objects solely to provide locks.

```java
public class MsLunch {
    private long c1 = 0;
    private long c2 = 0;
    private Object lock1 = new Object();
    private Object lock2 = new Object();

    public void inc1() {
        synchronized(lock1) {
            c1++;
        }
    }

    public void inc2() {
        synchronized(lock2) {
            c2++;
        }
    }
}
```

Use this idiom with extreme care. You must be absolutely sure that it really is safe to interleave access of the affected fields.

#### Atomic Access

In programming, an atomic action is one that effectively happens all at once. An atomic action cannot stop in the middle: it either happens completely, or it doesn't happen at all. No side effects of an atomic action are visible until the action is complete.

We have already seen that an increment expression, such as c++, does not describe an atomic action. Even very simple expressions can define complex actions that can decompose into other actions. However, there are actions you can specify that are atomic:

* Reads and writes are atomic for reference variables and for most primitive variables (all types except long and double).
* Reads and writes are atomic for all variables declared volatile (including long and double variables).

Atomic actions cannot be interleaved, so they can be used without fear of thread interference. However, this does not eliminate all need to synchronize atomic actions, because memory consistency errors are still possible. Using volatile variables reduces the risk of memory consistency errors, because any write to a volatile variable establishes a happens-before relationship with subsequent reads of that same variable. This means that changes to a volatile variable are always visible to other threads. What's more, it also means that when a thread reads a volatile variable, it sees not just the latest change to the volatile, but also the side effects of the code that led up the change.

Using simple atomic variable access is more efficient than accessing these variables through synchronized code, but requires more care by the programmer to avoid memory consistency errors. Whether the extra effort is worthwhile depends on the size and complexity of the application.

#### Atomic Variables

The java.util.concurrent.atomic package defines classes that support atomic operations on single variables. All classes have get and set methods that work like reads and writes on volatile variables. That is, a set has a happens-before relationship with any subsequent get on the same variable. The atomic compareAndSet method also has these memory consistency features, as do the simple atomic arithmetic methods that apply to integer atomic variables.

To see how this package might be used, let's return to the Counter class we originally used to demonstrate thread interference:

```java
class Counter {
    private int c = 0;

    public void increment() {
        c++;
    }

    public void decrement() {
        c--;
    }

    public int value() {
        return c;
    }

}
```

One way to make Counter safe from thread interference is to make its methods synchronized, as in SynchronizedCounter:

```java
class SynchronizedCounter {
    private int c = 0;

    public synchronized void increment() {
        c++;
    }

    public synchronized void decrement() {
        c--;
    }

    public synchronized int value() {
        return c;
    }

}
```
For this simple class, synchronization is an acceptable solution. But for a more complicated class, we might want to avoid the liveness impact of unnecessary synchronization. Replacing the int field with an AtomicInteger allows us to prevent thread interference without resorting to synchronization, as in AtomicCounter:

```java
import java.util.concurrent.atomic.AtomicInteger;

class AtomicCounter {
    private AtomicInteger c = new AtomicInteger(0);

    public void increment() {
        c.incrementAndGet();
    }

    public void decrement() {
        c.decrementAndGet();
    }

    public int value() {
        return c.get();
    }

}
```

[К оглавлению](#start)

---

### <a name="fork"></a> 7. Fork/Join Framework

---

The fork/join framework was presented in Java 7. It provides tools to help speed up parallel processing by attempting to use all available processor cores – which is accomplished through a divide and conquer approach.

In practice, this means that the framework first “forks”, recursively breaking the task into smaller independent subtasks until they are simple enough to be executed asynchronously.

After that, the “join” part begins, in which results of all subtasks are recursively joined into a single result, or in the case of a task which returns void, the program simply waits until every subtask is executed.

To provide effective parallel execution, the fork/join framework uses a pool of threads called the ForkJoinPool, which manages worker threads of type ForkJoinWorkerThread.

#### ForkJoinPool

The ForkJoinPool is the heart of the framework. It is an implementation of the ExecutorService that manages worker threads and provides us with tools to get information about the thread pool state and performance.

Worker threads can execute only one task at the time, but the ForkJoinPool doesn’t create a separate thread for every single subtask. Instead, each thread in the pool has its own double-ended queue (or deque, pronounced deck) which stores tasks.

This architecture is vital for balancing the thread’s workload with the help of the work-stealing algorithm.

In Java 8, the most convenient way to get access to the instance of the ForkJoinPool is to use its static method commonPool(). As its name suggests, this will provide a reference to the common pool, which is a default thread pool for every ForkJoinTask.

According to Oracle’s documentation, using the predefined common pool reduces resource consumption, since this discourages the creation of a separate thread pool per task.

```
ForkJoinPool commonPool = ForkJoinPool.commonPool();
```
The same behavior can be achieved in Java 7 by creating a ForkJoinPool and assigning it to a public static field of a utility class:

```
public static ForkJoinPool forkJoinPool = new ForkJoinPool(2);
```
Now it can be easily accessed:

```
ForkJoinPool forkJoinPool = PoolUtil.forkJoinPool;
```
With ForkJoinPool’s constructors, it is possible to create a custom thread pool with a specific level of parallelism, thread factory, and exception handler. In the example above, the pool has a parallelism level of 2. This means that pool will use 2 processor cores.

#### ForkJoinTask < V >

ForkJoinTask is the base type for tasks executed inside ForkJoinPool. In practice, one of its two subclasses should be extended: the RecursiveAction for void tasks and the RecursiveTask<V> for tasks that return a value. They both have an abstract method compute() in which the task’s logic is defined.

#### Submitting Tasks to the ForkJoinPool

To submit tasks to the thread pool, few approaches can be used.

The submit() or execute() method (their use cases are the same):

```
forkJoinPool.execute(customRecursiveTask);
int result = customRecursiveTask.join();
```
The invoke() method forks the task and waits for the result, and doesn’t need any manual joining:

```
int result = forkJoinPool.invoke(customRecursiveTask);
```

The invokeAll() method is the most convenient way to submit a sequence of ForkJoinTasks to the ForkJoinPool. It takes tasks as parameters (two tasks, var args, or a collection), forks them returns a collection of Future objects in the order in which they were produced.

Alternatively, you can use separate fork() and join() methods. The fork() method submits a task to a pool, but it doesn’t trigger its execution. The join() method is be used for this purpose. In the case of RecursiveAction, the join() returns nothing but null; for RecursiveTask<V>, it returns the result of the task’s execution:

```
customRecursiveTaskFirst.fork();
result = customRecursiveTaskLast.join();
```

In our RecursiveTask< V > example we used the invokeAll() method to submit a sequence of subtasks to the pool. The same job can be done with fork() and join(), though this has consequences for the ordering of the results.

To avoid confusion, it is generally a good idea to use invokeAll() method to submit more than one task to the ForkJoinPool.

[К оглавлению](#start)

---

### <a name="parallel"></a> 8. Parallel Streams

---

Stream API поддерживает параллельные потоки. Распараллеливание потоков позволяет задействовать несколько ядер процессора (если целевая машина многоядерная) и тем самым может повысить производительность и ускорить вычисления. В то же время говорить, что применение параллельных потоков на многоядерных машинах однозначно повысит производительность - не совсем корректно. В каждом конкретном случае надо проверять и тестировать.

Чтобы сделать обычный последовательный поток параллельным, надо вызвать у объекта Stream метод parallel. Кроме того, можно также использовать метод parallelStream() интерфейса Collection для создания параллельного потока из коллекции.

В то же время если рабочая машина не является многоядерной, то поток будет выполняться как последовательный.

Применение параллельных потоков во многих случаях будет аналогично. Например:

```java

import java.util.Optional;
import java.util.stream.Stream;
 
public class Program {
  
    public static void main(String[] args) {
         
        Stream<Integer> numbersStream = Stream.of(1, 2, 3, 4, 5, 6);
        Optional<Integer> result = numbersStream.parallel().reduce((x,y)-> x*y);
        System.out.println(result.get()); // 720
    }
}
```

Однако не все функции можно без ущерба для точности вычисления перенести с последовательных потоков на параллельные. Прежде всего такие функции должны быть без сохранения состояния и ассоциативными, то есть при выполнении слева направо давать тот же результат, что и при выполнении справа налево, как в случае с произведением чисел. Например:

```
Stream<String> wordsStream = Stream.of("мама", "мыла", "раму");
String sentence = wordsStream.parallel().reduce("Результат:", (x,y)->x + " " + y);
System.out.println(sentence);
```

Результатом этой функции будет консольный вывод:

Результат: мама Результат: мыла Результат: раму

Данный вывод не является правильным. Если же мы не уверены, что на каком-то этапе работы с параллельным потоком он адекватно сможет выполнить какую-нибудь операцию, то мы можем преобразовать этот поток в последовательный посредством вызова метода sequential():

```
Stream<String> wordsStream = Stream.of("мама", "мыла", "раму", "hello world");
String sentence = wordsStream.parallel()
        .filter(s->s.length()<10) // фильтрация над параллельным потоком
        .sequential()
        .reduce("Результат:", (x,y)->x + " " + y); // операция над последовательным потоком
System.out.println(sentence);
```

И возьмем другой пример:

```
Stream<Integer> numbersStream = Stream.of(1, 2, 3, 4, 5, 6);
Integer result = numbersStream.parallel().reduce(1, (x,y)->x * y);
System.out.println(result);
```

Фактически здесь происходит перемножение чисел. При этом нет разницы между 1 * 2 * 3 * 4 * (5 * 6) или 5 * 6 * 1 * (2 * 3) * 4. Мы можем расставить скобки любым образом, разместить последовательность чисел в любом порядке, и все равно мы получим один и тот же результат. То есть данная операция является ассоциативной и поэтому может быть распараллелена.

Фактически применение параллельных потоков сводится к тому, что данные в потоке будут разделены на части, каждая часть обрабатывается на отдельном ядре процессора, и в конце эти части соединяются, и над ними выполняются финальные операции. Рассмотрим некоторые критерии, которые могут повлиять на производительность в параллельных потоках:

* Размер данных. Чем больше данных, тем сложнее сначала разделять данные, а потом их соединять.

* Количество ядер процессора. Теоретически, чем больше ядер в компьютере, тем быстрее программа будет работать. Если на машине одно ядро, нет смысла применять параллельные потоки.

* Чем проще структура данных, с которой работает поток, тем быстрее будут происходить операции. Например, данные из ArrayList легко использовать, так как структура данной коллекции предполагает последовательность несвязанных данных. А вот коллекция типа LinkedList - не лучший вариант, так как в последовательном списке все элементы связаны с предыдущими/последующими. И такие данные трудно распараллелить.

* Над данными примитивных типов операции будут производиться быстрее, чем над объектами классов


Как правило, элементы передаются в поток в том же порядке, в котором они определены в источнике данных. При работе с параллельными потоками система сохраняет порядок следования элементов. Исключение составляет метод forEach(), который может выводить элементы в произвольном порядке. И чтобы сохранить порядок следования, необходимо применять метод forEachOrdered:

```
phones.parallelStream()
    .sorted()
    .forEachOrdered(s->System.out.println(s));
```
Сохранение порядка в параллельных потоках увеличивает издержки при выполнении. Но если нам порядок не важен, то мы можем отключить его сохранение и тем самым увеличить производительность, использовав метод unordered:

```
phones.parallelStream()
    .sorted()
    .unordered()
    .forEach(s->System.out.println(s));
```

[К оглавлению](#start)