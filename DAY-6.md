JAVA 21:
	PLATFORM THREADS: 
	Before Java 21, every thread you created was a Platform Thread.
	A Platform Thread is a traditional Java thread that is mapped one-to-one with an operating system (OS) thread.
	1 Java Thread = 1 OS Thread
	Every Java thread requires its own OS thread.
	
	Why is this a problem?
	OS threads are expensive.
	Each OS thread requires:
		• Memory (stack)
		• Scheduling by the operating system
		• Context switching
	
	
	VIRTUAL THREADS:
	A Virtual Thread is a lightweight Java thread managed by the JVM instead of being permanently tied to a dedicated operating system thread.
	
	Platform Thread	Virtual Thread
	Heavyweight	Lightweight
	1 Java Thread = 1 OS Thread	Many Virtual Threads share a smaller number of OS threads
	Expensive	Cheap
	Limited scalability	Designed for massive scalability
	Available before Java 21	Introduced in Java 21
	Managed by OS                         Managed by JVM
	
	
	Carrier thread: A Carrier Thread is a Platform Thread (backed by an OS thread) that temporarily executes a Virtual Thread. It's simply a Platform Thread that the JVM uses to run Virtual Threads.
	
	Virtual Thread	Carrier Thread
	Lightweight	Heavyweight
	Managed by JVM	Platform Thread managed by OS
	Millions can exist	Only a limited number are needed
	Doesn't own an OS thread permanently	Permanently backed by an OS thread
	
	Virtual Threads are lightweight tasks managed by the JVM. They are executed on a small number of Carrier Threads (which are Platform Threads). When a Virtual Thread blocks, the JVM can free the Carrier Thread to execute another Virtual Thread, making much better use of OS threads.
	
	Virtual Thread
	
	↓
	
	Database Call
	
	↓
	
	Virtual Thread pauses
	
	↓
	
	Carrier Thread becomes free
	
	↓
	
	Carrier Thread executes another Virtual Thread
	
	
	Virtual Threads are lightweight Java threads managed by the JVM. They are not permanently attached to an OS thread. Instead, the JVM schedules them onto a smaller number of Carrier Threads (which are Platform Threads backed by OS threads). When a Virtual Thread blocks, the JVM can free the Carrier Thread to execute another Virtual Thread, allowing applications to handle a very large number of concurrent tasks efficiently.
	
	Mounting: Mounting means attaching a Virtual Thread to a Carrier Thread so it can execute.
	
	Unmounting: Unmounting means detaching a Virtual Thread from the Carrier Thread so the Carrier Thread can execute another Virtual Thread.
	
	Pinning:
	Pinning occurs when a Virtual Thread cannot be unmounted from its Carrier Thread while it is blocked, causing the Carrier Thread to remain occupied.One common example is when a Virtual Thread is blocked while inside certain synchronized sections that prevent unmounting.
	
