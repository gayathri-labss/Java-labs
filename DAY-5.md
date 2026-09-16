# DAY 5 — Sealed Classes, Pattern Matching, Switch Expressions, and Multithreading

## Sealed classes

A sealed class restricts which classes can extend or implement it. This was introduced to provide stronger control over inheritance.

Example:

```java
public sealed class Payment
    permits CreditCardPayment,
            UpiPayment,
            NetBankingPayment {
}
```

Syntax:

```java
public sealed class Parent
    permits Child1, Child2 {
}
```

Every permitted subclass must choose one of these modifiers:

1. `final` — no further inheritance allowed.

```java
public final class CreditCardPayment extends Payment {
}
```

2. `sealed` — continue restricting inheritance (must declare its permitted subclasses).

```java
public sealed class Vehicle permits Car, Bike {
}
```

3. `non-sealed` — open inheritance again; anyone can extend this class.

```java
public non-sealed class UpiPayment extends Payment {
}
```

## Pattern matching for instanceof

Pattern matching with `instanceof` (Java 16/17 feature) combines type check and cast into one step.

Java 8:

```java
Object obj = "Hello";
if (obj instanceof String) {
    String str = (String) obj;
    System.out.println(str.length());
}
```

Java 17:

```java
Object obj = "Hello";
if (obj instanceof String str) {
    System.out.println(str.length());
}
```

Note:
- The pattern variable (`str` above) is scoped to the `if` statement (and its guarded block). Using it outside the guarded scope causes a compilation error.

Example showing scoping:

```java
if (obj instanceof String s) {
    System.out.println(s);
}
// System.out.println(s); // compilation error: s not in scope
```

A common pattern to use the variable afterwards:

```java
if (!(obj instanceof String s)) {
    return;
}
System.out.println(s); // s is in scope here because the guard ensures obj is a String
```

## Switch expressions

Switch expressions (Java 12+ preview, standardized later) allow a `switch` to return a value, support `->` syntax, and eliminate fall-through by default.

Java 17 example:

```java
String day = "MON";
String result = switch (day) {
    case "MON", "TUE", "WED", "THU", "FRI" -> "Weekday";
    case "SAT", "SUN" -> "Weekend";
    default -> "Invalid";
};
System.out.println(result);
```

Java 8 equivalent (statement-style switch):

```java
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
```

Benefits:
- No fall-through by default (avoids accidental bugs when forgetting `break`).
- More concise and clear syntax.

### yield vs return

- `return` ends a method and returns a value to the caller.
- `yield` returns a value from a switch expression (it ends only the switch expression block, not the surrounding method).

Example using `yield`:

```java
public void demo() {
    String day = "MON";
    String result = switch (day) {
        case "MON" -> {
            System.out.println("Inside Switch");
            yield "Weekday";
        }
        default -> "Holiday";
    };
    System.out.println(result);
    System.out.println("Method Continues");
}
```

## Threading and Concurrency

### Process vs Thread

- Process: an independent program in execution with its own memory and resources. Can contain one or more threads.
- Thread: the smallest unit of execution within a process. Threads in the same process share memory.

Example analogy: a restaurant is a process; workers (chefs, waiters) are threads.

### Multithreading: concurrency vs parallelism

- Concurrency: multiple threads make progress by taking turns (on a single CPU core).
- Parallelism: multiple threads truly run at the same time (on multiple CPU cores).

Java supports both concurrency and parallelism depending on the hardware and scheduler.

### Creating threads: Thread vs Runnable

Extending `Thread`:

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread is running");
    }
}

MyThread t1 = new MyThread();
t1.start(); // creates a new OS-level thread and calls run() internally
```

Implementing `Runnable` (preferred separation of concerns):

```java
class MyTask implements Runnable {
    @Override
    public void run() {
        // task
    }
}

Runnable task = new MyTask();
Thread t = new Thread(task);
t.start();
```

Important: call `start()` to run code concurrently; calling `run()` directly just executes it on the current thread.

Trick question:

```java
Runnable r = () -> System.out.println("Hello");
Thread t = new Thread(r);
System.out.println("Main");
// Output: Main
// Because t.start() was never called
```

### Thread lifecycle

States: NEW -> RUNNABLE -> RUNNING -> (TIMED_WAITING / WAITING / BLOCKED) -> RUNNABLE -> RUNNING -> TERMINATED

- NEW: thread object created, before `start()`.
- RUNNABLE: ready to run, waiting for CPU time.
- RUNNING: selected by scheduler and executing.
- BLOCKED: waiting to acquire a monitor lock.
- WAITING: waiting indefinitely for another thread (e.g., `join()` without timeout).
- TIMED_WAITING: waiting with timeout (e.g., `sleep(long)`).
- TERMINATED: `run()` has finished. A terminated thread cannot be restarted.

### Common thread methods

- `start()` — creates a new thread and executes `run()` in it.
- `run()` — the entry point for thread execution; calling it directly does not create a new thread.
- `sleep(long millis)` — pauses the current thread (TIMED_WAITING). Does not release locks.
- `join()` — current thread waits until the specified thread terminates.
- `yield()` — a hint to the scheduler to pause the current thread and allow others to run (no guarantees).
- `Thread.currentThread()` — returns a reference to the currently executing thread.
- `isAlive()` — true if the thread has been started and not yet terminated.
- `interrupt()` — requests interruption on a thread; does not forcibly stop it.

### Synchronization and race conditions

A race condition occurs when multiple threads read and write shared data concurrently without proper synchronization, leading to incorrect results.

Critical section: code that accesses shared mutable data and must be protected.

Use `synchronized` to protect critical sections. Example:

```java
class BankAccount {
    private int balance = 1000;

    public synchronized void withdraw(int amount) {
        if (balance >= amount) {
            balance = balance - amount;
        }
    }
}
```

- A synchronized instance method locks the object's monitor (`this`).
- A `static synchronized` method locks the `ClassName.class` monitor.

Synchronized block example (more flexible):

```java
public void withdraw(int amount) {
    // non-critical code
    synchronized (this) {
        // critical section
    }
}
```

### volatile, AtomicInteger, and ReentrantLock

- `volatile` ensures visibility of changes across threads but does not provide atomicity for compound actions (e.g., `count++`).
- `AtomicInteger` provides atomic single-variable operations (e.g., `incrementAndGet()`).

```java
AtomicInteger count = new AtomicInteger(0);
count.incrementAndGet();
System.out.println(count.get()); // 1
```

- `ReentrantLock` provides explicit locking with more flexibility (tryLock, timed lock, fairness policy). Always unlock in a `finally` block:

```java
ReentrantLock lock = new ReentrantLock();
lock.lock();
try {
    // critical section
} finally {
    lock.unlock();
}
```

When to use:
- Visibility / stop flag: `volatile`
- Counters: `AtomicInteger`
- Complex critical sections or advanced locking: `synchronized` or `ReentrantLock`

### Deadlock

A deadlock happens when two or more threads are blocked forever, each waiting to acquire locks held by the other.

Prevention strategies:
- Acquire locks in a consistent global order.
- Keep synchronized sections small.
- Use `tryLock()` with timeouts when appropriate to avoid indefinite waiting.

The JVM can detect deadlocks via tooling (thread dumps), but it does not automatically resolve them.

---

