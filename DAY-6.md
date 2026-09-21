# Day 6: Java 21 - Platform Threads vs Virtual Threads

## 1) Java 21: Platform Threads

Before Java 21, every thread you created was a Platform Thread.

A Platform Thread is a traditional Java thread mapped one-to-one with an operating system (OS) thread.

### Rule
- 1 Java Thread = 1 OS Thread

This causes problems because:
- OS threads are expensive
- each OS thread uses memory (stack)
- each thread needs scheduling by the operating system
- context switching is costly

---

## 2) Virtual Threads

A Virtual Thread is a lightweight Java thread managed by the JVM instead of being permanently tied to a dedicated operating system thread.

### Comparison

| Feature | Platform Thread | Virtual Thread |
| --- | --- | --- |
| Weight | Heavyweight | Lightweight |
| Java Thread = OS Thread | Yes | No |
| Memory | More memory | Less memory |
| Scalability | Limited | Designed for massive scalability |
| Managed by | OS | JVM |
| Available | Before Java 21 | Introduced in Java 21 |

---

## 3) Carrier Thread

A Carrier Thread is a Platform Thread (backed by an OS thread) that temporarily executes a Virtual Thread.

In simple words:
- Virtual threads are scheduled onto a smaller number of carrier threads.
- When a virtual thread blocks, the JVM can unmount it and run another virtual thread on the same carrier thread.

### Example flow

```text
Virtual Thread
    ↓
Carrier Thread executes it
    ↓
Virtual Thread blocks
    ↓
Carrier Thread becomes free
    ↓
Carrier Thread executes another Virtual Thread
```

This makes Java applications much more efficient at handling a huge number of concurrent tasks.

---

## 4) Why Virtual Threads?

Virtual Threads are lightweight Java threads managed by the JVM.

They are not permanently attached to an OS thread. Instead, the JVM schedules them onto a smaller number of Carrier Threads.

### Benefits
- huge number of concurrent tasks possible
- low memory usage
- better throughput
- efficient for I/O-heavy applications

---

## 5) Mounting and Unmounting

### Mounting
Mounting means attaching a Virtual Thread to a Carrier Thread so it can execute.

### Unmounting
Unmounting means detaching a Virtual Thread from the Carrier Thread so the Carrier Thread can execute another Virtual Thread.

This helps the JVM use carrier threads efficiently.

---

## 6) Pinning

Pinning occurs when a Virtual Thread cannot be unmounted from its Carrier Thread while it is blocked.

This can happen while executing certain synchronized sections or blocking operations that prevent unmounting.

### Example
A Virtual Thread is blocked while inside a synchronized section, so it cannot be unmounted.

In that case, the Carrier Thread remains occupied.

---

## 7) Important Summary

Virtual Threads are lightweight Java threads managed by the JVM.

- They are not permanently attached to an OS thread.
- The JVM schedules them onto a smaller number of Carrier Threads.
- When a Virtual Thread blocks, the JVM can switch to another Virtual Thread.
- This allows applications to handle a very large number of concurrent tasks efficiently.

---

## 8) Key Points for Interview

### Platform Thread
- heavy
- expensive
- one Java thread = one OS thread
- limited scalability

### Virtual Thread
- lightweight
- many virtual threads can share fewer carrier threads
- efficient for concurrency and I/O-heavy workloads
- managed by JVM

---

## 9) Memory and Scalability Comparison

| Type | Result |
| --- | --- |
| Platform Thread | Expensive and limited |
| Virtual Thread | Lightweight and scalable |

### Example
A large number of Virtual Threads can run on a much smaller number of Carrier Threads, which improves CPU and memory efficiency.

---

## 10) Final Takeaway

Java 21 introduced Virtual Threads to solve the problem of creating too many OS threads.

They provide:
- better scalability
- lower memory cost
- efficient handling of many concurrent tasks

So, Virtual Threads are a major improvement for modern Java applications, especially for I/O-bound workloads.

---

## Quick Revision Notes

- Platform Thread = traditional thread
- Virtual Thread = lightweight thread managed by JVM
- Carrier Thread = OS-backed thread used to run virtual threads
- Mounting = attach virtual thread to carrier thread
- Unmounting = detach virtual thread from carrier thread
- Pinning = virtual thread cannot be unmounted while blocked

