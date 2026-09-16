Sealed classes:

A Sealed Class is a class that restricts inheritance by allowing only specified classes to extend or implement it.

Problem is in java8, we have a banking application and anyone can extend it there is not restriction on it. Suppose you have payment appplocation which support upimethod, bankmenthod and chequemethod, so only these 3 can externd main class but in java 8 any other class can extwnd main class so to overcome this in java 17 we have sealed class
Ex: 
public sealed class Payment
    permits CreditCardPayment,
            UpiPayment,
            NetBankingPayment {
}

Syntax:
public sealed class Parent
    permits Child1, Child2 {

}



Every permitted subclass must choose one of these:
1. final
No further inheritance.
public final class CreditCardPayment
        extends Payment {
}

2. sealed
Continue restricting inheritance.
public sealed class Vehicle
    permits Car, Bike {
}

3. non-sealed
Open inheritance again.
public non-sealed class UpiPayment
        extends Payment {
}
Now anyone can extend UpiPayment.


Instanceof:
Pattern Matching for instanceof is a Java 17 feature that combines type checking and type casting into a single operation, eliminating the need for explicit casting after an instanceof check.

Example:
Object obj = "Hello";

Java8:
if (obj instanceof String) { //checking if its obj of string

    String str = (String) obj; // again typecasting (its repetivive task)

    System.out.println(str.length());
}


Java17:
if (obj instanceof String str) {

    System.out.println(str.length());
}
So pattern matching is essentially syntactic sugar over the old approach—it doesn't change what the program means, it just makes it cleaner and less error-prone.


Example:
if (obj instanceof String str) {

    System.out.println(str.length());
}
    System.out.println(str.length());

Output- compilation error , because str is a local varibale confined to its method only. So we cannot use It outside of its method


Example:
if (obj instanceof String s) {
    System.out.println(s);
}

System.out.println(s);
Output- compilation error

Example
if (!(obj instanceof String s)) {
    return;
}

System.out.println(s);
Output- s will be prinyed because (false) we get so it comes out of loop and print it


Switch Expressions:
Switch Expression is a Java 17 feature that allows switch to return a value, use the -> syntax, and eliminates the need for break statements, making code more concise and less error-prone.

Switch statement execute code, switch expression returns value

Java17
String day = "MON";

String result = switch(day) {

    case "MON", "TUE", "WED", "THU", "FRI" -> "Weekday";

    case "SAT", "SUN" -> "Weekend";

    default -> "Invalid";
};

System.out.println(result);


Java8
String day = "MON";
String result;

switch (day) {

    case "MON":
    case "TUE":
    case "WED":
    case "THU":
    case "FRI":
        result = "Weekday";
        break;

    case "SAT":
    case "SUN":
        result = "Weekend";
        break;

    default:
        result = "Invalid";
}

Benefits of this is, no fall through(means if we don’t use break statement oncw condition is satisified all the other values will be printed its leads to infitied times and can crash appliation). Using java 17 features will make sure not to have this error of fall through


Diff between yield and return:
return	yield
Returns from a method	Returns from a switch expression
Ends the entire method	Ends only the switch expression
Available in all Java versions	Introduced with switch expressions
Used inside methods	Used only inside a switch expression block (case -> { ... })


public String getDay() {

    System.out.println("Start");

    return "Monday";

    // System.out.println("End"); // Unreachable
}
After return, the method is over.



public void demo() {

    String day = "MON";

    String result = switch(day) {

        case "MON" -> {
            System.out.println("Inside Switch");
            yield "Weekday";
        }

        default -> "Holiday";
    };

    System.out.println(result);

    System.out.println("Method Continues");
}
• yield finishes the switch.
• The method continues.

Threading:

Process- A process is an independent program in execution with its own memory, resources, and execution environment.

• Independent
• Has its own memory
• Has its own resources
• Can contain one or more threads

Thread: A thread is the smallest unit of execution inside a process.

A process does the work.
A thread performs the work.

Example: restaurent is process and workers are thread

Process	Thread
Independent program	Smallest unit of execution
Own memory	Shares process memory
Heavyweight	Lightweight
Expensive to create	Cheaper to create
Communication is slower	Communication is faster

Multithreading: Multithreading is the process of executing multiple threads concurrently within a single process to improve responsiveness and better utilize system resources.

Notice I said "concurrently" rather than "simultaneously."
	• On a single CPU core, threads usually make progress by taking turns very quickly (concurrency).
	• On multiple CPU cores, threads can truly run at the same time (parallelism).

Many people say:
	"Multithreading means multiple threads run simultaneously."
That's not always true.

Suppose an interviewer asks:
	Is Java multithreading concurrent or parallel?
The best answer is:
	Java supports both concurrency and parallelism. On a single-core CPU, threads execute concurrently by taking turns. On a multi-core CPU, multiple threads can execute in parallel.


Example;
class MyThread extends Thread {

    @Override
    public void run() {

        System.out.println("Thread is running");

    }
}


To start thread we do 
MyThread t1 = new MyThread();
t1.start();

start()
t1.start();
	• Creates a new thread
	• JVM schedules it
	• Calls run() internally

Main Thread

↓

start()

↓

JVM creates

↓

Thread-0

↓

run() - 2 threads run independently

run()
t1.run();
Does not create a new thread.
It is just a normal method call.


Main Thread

↓

run()

↓

Back to Main Thread - only one thread exists


Runnable: Runnable is a Java interface used to define the work a thread should do.
Thread represents the thread of execution, while Runnable represents the task to be executed by a thread. Runnable is generally preferred because it separates the task from the thread and avoids the limitations of single inheritance.

Important: call start(), not run() directly. Calling run() normally just runs the code in the current thread; start() runs it concurrently in a new thread.

MyThread - Worker + Work together.

┌────────────┐
│ Worker     │
│            │
│ Work       │
└────────────┘


Runnable- Work and Worker are separate.

Task

┌────────────┐
│ Work Only  │
└────────────┘

↓

Given to

↓

┌────────────┐
│ Thread     │
│ Worker     │
└────────────┘

One Runnable can be executed by different Threads.
class MyTask implements Runnable {

    public void run() {

    }

}

NOTE: Runnable Is not another type of thread, it is a task. Execution is still done by thread


Why dint java use just thread?
class MyThread extends Thread {

    public void run() {
        // Task
    }
}

The problem is that the task and the worker are tightly coupled.
Java prefers to separate responsibilities.
This is called the Separation of Concerns principle.
	• Runnable = defines what to do.
	• Thread = defines who executes it.
This design is more flexible and reusable.


Runnable task = new MyTask();

↓

Task Created

↓

Thread t = new Thread(task);

↓

Thread Object Created

↓

t.start();

↓

New Thread Starts

↓

JVM calls task.run()


nterview Trick Question
What is the output?
Runnable r = () -> System.out.println("Hello");
Thread t = new Thread(r);
System.out.println("Main");
Output?
Main
Why?
Because start() was never called.
Creating a Runnable and creating a Thread object does not start execution.


Creating a Thread object ≠ Starting a Thread

Can we predict the execution order of multiple threads?
✅ Answer: No.
Because thread scheduling is handled by the OS scheduler (with the JVM interacting with it), and the exact execution order is not deterministic.


Diff between thread and runnable:
	1. Inheriance:
	
	class Animal {
	}
	
	class Dog extends Animal {
	}
	
	class Dog extends Animal, Thread {
	}
	This cant be done
	
	
	Runnable- 
	class Dog extends Animal implements Runnable {
	
	    @Override
	    public void run() {
	        System.out.println("Running...");
	    }
	}
	
	2. Sepration of concerns
	In thread one class hold all the responsibilities -> worker + task together
	In runnable task and execution are present separte defining separation of concerns
	3. Reusable: 

Thread	Runnable
Class	Interface
Represents a thread	Represents a task
Uses extends	Uses implements
Limited by single inheritance	No inheritance limitation
Worker + task together	Worker and task separate
Less flexible	More flexible
Rarely used in modern projects	Preferred in real projects


Thread Lifecycle:
Thread Lifecycle is the sequence of states that a thread goes through from its creation until its termination.

                 NEW
                  │
             start()
                  │
                  ▼
             RUNNABLE
                  │
       CPU selected by Scheduler
                  │
                  ▼
             RUNNING
          /     |      \
         /      |       \
 sleep()  wait()   synchronized lock
   │         │            │
   ▼         ▼            ▼
TIMED     WAITING     BLOCKED
WAITING
   │         │            │
   └─────────┴────────────┘
             │
             ▼
          RUNNABLE
             │
             ▼
          RUNNING
             │
     run() finishes
             ▼
        TERMINATED

New - A thread is in the NEW state after the thread object is created but before start() is called.
Example : Thread t = new MyThread();

Runnable - RUNNABLE means the thread is ready to run and is waiting for CPU time from the scheduler.
 
Running - Tnread is running 

Example:
Thread t1 = new MyThread();
Thread t2 = new MyThread();
Thread t3 = new MyThread();


t1.start();
t2.start();
t3.start();


Output; 
t1 → RUNNING

t2 → RUNNABLE

t3 → RUNNABLE

Does start() immediately execute run()?
❌ No.
It first moves the thread to RUNNABLE. The scheduler decides when it actually runs.

Blocked:  A thread enters the BLOCKED state when it is waiting to acquire a monitor lock (synchronized) that is currently held by another thread.
Thread-1
↓

Lock Acquired

↓

Running

---------------------

Thread-2

↓

Waiting for Lock

↓

BLOCKED
As soon as thread 1 releases, lock thread 2 becomes runable


Waiting: A thread enters the WAITING state when it waits indefinitely for another thread to perform a particular action.
Main Thread

↓

worker.join()

↓

WAITING

↓

Worker finishes

↓

Main Thread continues


Timed wait: A thread enters TIMED_WAITING when it waits for a specified amount of time. Thread.sleep(5000);

WAITING	TIMED_WAITING
Wait forever	Wait for a specific time
join()	sleep()
Needs another thread to continue	Automatically resumes after timeout

Terminated
A thread enters the TERMINATED state after the run() method finishes execution.

One Interview Question
Suppose the interviewer asks:
	Can a terminated thread become runnable again?
Answer:
	No. Once a thread reaches the TERMINATED state, it cannot be restarted. If we want to execute the task again, we must create a new Thread object.


Thread methods:
	1. Sleep() ; Thread.sleep() is a static method that pauses the execution of the current thread for a specified amount of time. During this period, the thread enters the TIMED_WAITING state.

Syntax: Thread.sleep(milliseconds);

Sleep will not release lock, it will pause execution

	2. Join(): The join() method causes the current thread to wait until the specified thread completes its execution.
	Thread t = new MyThread(); //main
	t.start();
	t.join(); //joined thread
	System.out.println("Program Finished");
	Now what happens?
	Main Thread says:
		"I won't continue until thread t finishes."
		
		
		Thread t1 = new MyThread();
		Thread t2 = new MyThread();
		t1.start();
		
		t2.start();
		
		t1.join();
		
		System.out.println("Main Thread");
		
		Which thread waits? Main thread waits
		
		3. Yield(): Thread.yield() is a static method that hints to the thread scheduler that the current thread is willing to pause its execution and allow other runnable threads of the same priority to execute.
		Interview Questions
		Q1.
		Does yield() guarantee that another thread will execute?
		✅ No.
		
		Q2.
		Is yield() static?
		✅ Yes.
		
		Q3.
		Which thread calls yield()?
		✅ The currently running thread.
		
		Q4.
		What state does the thread move to after yield()?
		✅ RUNNABLE (conceptually, because it gives up the CPU and becomes eligible to run again).
		
		
		4. Current thread(): Thread.currentThread() is a static method that returns a reference to the thread that is currently executing the code.
		Example:
		public class Demo {
		
		    public static void main(String[] args) {
		
		        System.out.println(Thread.currentThread());
		
		    }
		
		}
		
		Thread Name  : main
		
		Priority     : 5
		
		Thread Group : main
		
		getName() returns the name of a thread.
		setName() changes the name of a thread.
		
		5. isAlive() returns true if a thread has been started and has not yet terminated. Otherwise, it returns false.
		6. Interrupt(): interrupt() is a method that sends an interruption request (signal) to a thread. It does not forcibly stop the thread.
	
	
	Synchronization:
	
	
	Race condition: A race condition occurs when two or more threads access and modify shared data simultaneously, leading to incorrect or unpredictable results.
	
	Easy Rule
	Safe ✅
	Read
Read
Read
	No problem.
	
	Unsafe ⚠️
	Read
Write
	Can lead to inconsistent or stale values.
	
	Unsafe ⚠️
	Write
Write
	Can lead to race conditions and lost updates.
	
	Interview Perspective
	If an interviewer asks:
		Can a race condition happen when one thread reads and another writes?
	The safe answer is:
		Yes. Concurrent reads and writes without proper synchronization can lead to inconsistent or stale data being observed.
	
	
	Critical section:
	A critical section is a part of the code that accesses shared mutable data and therefore must not be executed by more than one thread at the same time.
	Example: balance = balance - amount withdraw //this is critical section
	
	Synchronization:
	synchronized is a keyword that allows only one thread at a time to execute a critical section of code by acquiring a monitor lock.
	
	Without it:
	class BankAccount {
	
	    int balance = 1000;
	
	    public void withdraw(int amount) {
	
	        if(balance >= amount) {
	
	            balance = balance - amount;
	
	        }
	
	    }
	
	}
	Two thread can execute withdraw() leading to inconsistency
	
	With it:
	class BankAccount {
	
	    int balance = 1000;
	
	    public synchronized void withdraw(int amount) {
	
	        if(balance >= amount) {
	
	            balance = balance - amount;
	
	        }
	
	    }
	
	}
	
	It locks object - synchronized instance methods lock the object (this), not the method.
	
	
	Interview Question
	What does a synchronized instance method lock?
	✅ Answer: The object's monitor lock (this).
	
	Interview Question
	Suppose the interviewer asks:
		If two threads call the same synchronized method on two different objects, will they block each other?
	Answer:
		No. Each object has its own monitor lock. Since the threads are locking different objects, they can execute concurrently.
	
	
	Feature	Object Lock	Class Lock
	Used with	synchronized instance method	static synchronized method
	Lock acquired	Object (this)	ClassName.class
	Number of locks	One per object	One per class
	Different objects can run simultaneously?	✅ Yes	❌ No
	
	
	Synchronized Method
		Locks the entire method.
	public synchronized void withdraw() {
    // Entire method is synchronized
}
	
	Synchronized Block
		Locks only a specific section of code.
	public void withdraw() {
	// Normal code
	synchronized(this) {
	// Critical section
	}
	}
	
	
	Synchronized Method	Synchronized Block
	Entire method locked	Only selected code locked
	Simpler to write	More flexible
	Lower concurrency	Better concurrency
	Can reduce performance	Better performance
	Good for small methods	Preferred for larger methods
	
	
	ADVANCED MULTITHREADING - Volatile: volatile is a keyword that ensures that changes made to a variable by one thread are immediately visible to all other threads.
	
	volatile	synchronized
	Solves visibility problems	Solves race conditions
	No locking	Uses locking
	Multiple threads can execute together	Only one thread enters critical section
	Doesn't make count++ safe	Makes critical section safe
	
	Feature	volatile	synchronized
	Visibility	✅	✅
	Atomicity	❌	✅
	Locking	❌	✅
	Prevents Race Condition	❌	✅
	Performance	Faster	Slightly slower (due to locking)
	
	
	Atomic:
	An atomic operation is an operation that is completed as a single, indivisible unit. It either happens completely or not at all. No other thread can interfere while it is happening.
	
	import java.util.concurrent.atomic.AtomicInteger;
	
	public class Demo {
	
	    public static void main(String[] args) {
	
	        AtomicInteger count = new AtomicInteger(0);
	
	        count.incrementAndGet();
	
	        System.out.println(count.get());
	
	    }
	
	}
	
	Output : 1
	
	AtomicInteger	synchronized
	No explicit lock	Uses lock
	Faster for single-variable updates	Better for complex critical sections
	Works on one variable	Can protect multiple statements
	
	Situation	Best Choice
	Visibility (stop flag)	volatile
	Increment/Counter	AtomicInteger
	Bank Transfer	synchronized
	
	
	ReentrantLock:
	ReentrantLock is a class that provides explicit locking with more flexibility and control than the synchronized keyword.
	
	ReentrantLock lock = new ReentrantLock();
	
	lock.lock();
	
	try {
	
	    // Critical Section
	
	} finally {
	
	    lock.unlock();
	
	}
	
	Why we have finally? Because in try if we get any exception and if finally is not present it will be locked which is dangerous. So we have to use it
	
	NOTE: same thread can acquire lock multiple times, its called as reentrancy
	
	synchronized	ReentrantLock
	Keyword	Class
	Automatic lock/unlock	Manual lock/unlock
	Simpler	More flexible
	No timeout	Supports timeout
	No tryLock()	Supports tryLock()
	Less control	More control
	
	Feature	volatile	AtomicInteger	synchronized	ReentrantLock
	Visibility	✅	✅	✅	✅
	Atomic Operations	❌	✅ (single variable)	✅	✅
	Locking	❌	❌	✅	✅
	Manual Locking	❌	❌	❌	✅
	tryLock()	❌	❌	❌	✅
	Best For	Status flags	Counters	Critical sections	Advanced locking
	
	
	DEADLOCK:
	A deadlock is a situation where two or more threads are permanently blocked because each thread is waiting for a resource (lock) held by another thread.
	
	Race Condition	Deadlock
	Threads continue running	Threads stop progressing
	Incorrect data	Program gets stuck
	Caused by unsynchronized access	Caused by circular waiting for locks
	
	
	How to prevent deadlock?
	Acquire lock in same order, keep synchronized section as small as possible, Use tryLock() (with ReentrantLock) when appropriate.
	if (lock.tryLock()) {
	    try {
	        // work
	    } finally {
	        lock.unlock();
	    }
	}
	
	
	Can Java automatically detect and resolve a deadlock?
	Answer:
	❌ No.
	The JVM can detect deadlocks (using tools like thread dumps or monitoring APIs), but it does not automatically resolve them.
	The application or developer must fix the code.
	
	Deadlock akways results in high cpu usuage? FALSE, because when in deadlock they will always be waiting for lock to be reelease and in that instance nothing will be executed
	
	
	
	
		
	




