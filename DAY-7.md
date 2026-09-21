Executer :
Executor executor = command -> {
    new Thread(command).start();
};

executor.execute(() -> {
    System.out.println("Hello");
});

A Thread Pool is a collection of pre-created threads that are reused to execute multiple tasks.

The Executor Framework is a high-level API introduced in Java 5 to manage and execute tasks efficiently using thread pools instead of creating threads manually.

Executer: Executor is an interface that provides a simple way to execute tasks.

ExecutorService: ExecutorService is an interface that extends Executor and provides lifecycle management for thread pools, task submission, and shutdown operations.
Synxtax: ExecutorService executor = Executors.newFixedThreadPool(3);

Runnable: What should be executed?
Thread: Who will execute the task?
Executer: Who should execute this task? I'll decide.
Runnable defines the task. Executor decides how and where the task is executed.

execute() is used to execute a Runnable task and does not return any result.
ExecutorService executor =
        Executors.newFixedThreadPool(3);

executor.execute(() -> {
    System.out.println("Task Executed");
});


submit() executes a task and returns a Future object that allows you to track the task's execution and retrieve its result.
ExecutorService executor =
        Executors.newFixedThreadPool(3);

Future<?> future = executor.submit(() -> {
    System.out.println("Task Executed");
});

	
Interview Tip ⭐⭐⭐⭐⭐
If an interviewer asks:
	Which one do you use in real projects?
A strong answer is:
	I usually prefer submit() because it returns a Future, which lets me monitor completion, handle exceptions, or retrieve a result when needed. For simple fire-and-forget tasks, execute() is sufficient.





Callable: Callable is a functional interface that represents a task which can return a result and throw checked exceptions.

Syntax:
public interface Callable<V> {

    V call() throws Exception;

}
V =  value, It represents the type of value that the task will return.

Callable<Integer> = This task returns an Integer

Runnable	Callable
Functional Interface	Functional Interface
void run()	V call()
No return value	Returns a value
No checked exceptions	Can throw checked exceptions


Evolution of Task Interfaces
Step 1: Runnable (Java 1.0)
public interface Runnable {
void run();
}
Question:
Can it return a value?
❌ No
Can it throw checked exceptions?
❌ No


Step 2: Callable (Java 5)
public interface Callable<V> {
V call() throws Exception;
}
Question:
Can it return a value?
✅ Yes
Can it throw checked exceptions?
✅ Yes

NOTE: Callable is same as runnable, it will define the task.


Who executes callable?
Ans:

Callable

↓

ExecutorService

↓

submit()

↓

Thread Pool

↓

Thread Executes


Can we create thread using callable?
Ans: No, because thread constructor accepts runnable and not callable

Future:
Future is an interface that represents the result of an asynchronous computation. 
The Future is not the result.
It is a placeholder for the result.

Future	Meaning
submit()	Submit task
Future	Placeholder for result
get()	Retrieve result (waits if necessary)
isDone()	Check if task has finished

NOTE: Callable creates the result. Future holds the result.

Completable Future:
Because Future has some limitations:
	• ❌ get() blocks the current thread.
	• ❌ Difficult to chain multiple asynchronous tasks.
	• ❌ Difficult to combine results from multiple tasks.
	• ❌ Limited support for asynchronous callbacks.
CompletableFuture solves all of these.

An interviewer might ask:
	Why was CompletableFuture introduced when we already had Future?
A strong answer is:
	Future is limited because get() blocks the calling thread and it doesn't provide a convenient way to chain or combine asynchronous tasks. CompletableFuture supports non-blocking programming, task chaining, combining results, and better exception handling.


Runnable	ExecutorService
Defines the task	Executes the task
Contains business logic	Manages threads
Doesn't know about threads	Assigns tasks to threads


CompletableFuture is a class that represents the result of an asynchronous computation and provides methods to chain, combine, and handle asynchronous tasks without manually blocking the current thread in many common scenarios.

Future	CompletableFuture
Introduced in Java 5	Introduced in Java 8
get() often blocks	Supports non-blocking continuation
Limited API	Rich API for chaining and combining tasks
Hard to combine multiple tasks	Easy to combine multiple tasks
Basic exception handling	Better exception handling

Type	Example
Interface	Runnable, Callable, Executor, ExecutorService, Future
Class	CompletableFuture, Executors, Thread

Method1:
runAsync(): runAsync() is used to execute a task asynchronously that does not return a result. Run this task in another thread without blocking the current thread.

Syntax:
CompletableFuture<Void> future =
    CompletableFuture.runAsync(() -> {

        System.out.println("Sending Email");

    });

Method2:
supplyAsync(): supplyAsync() executes a task asynchronously and returns a result.

runAsync()	supplyAsync()
No result	Returns a result
Similar to Runnable	Similar to Callable

CompletableFuture<String> future =
    CompletableFuture.supplyAsync(() -> {
        return "Hello";
    });


Runnable	runAsync()
Functional interface	Static method of CompletableFuture
Defines a task	Executes a task asynchronously
Doesn't execute by itself	Schedules execution immediately
Needs Thread or ExecutorService	Uses an executor internally (or one you provide)
No return value	Returns CompletableFuture<Void>

Callable	supplyAsync()
Functional interface	Static method of CompletableFuture
Defines a task	Executes a task asynchronously
Doesn't execute by itself	Starts execution immediately
Usually executed using ExecutorService.submit()	Uses an executor internally (or one you provide)
Returns a value when executed	Returns a CompletableFuture<T>

Method3: 
thenApply(): thenApply() is used to transform the result of a CompletableFuture into another value.

Syntax:
CompletableFuture<String> future =
    CompletableFuture
        .supplyAsync(() -> "gayathri")
        .thenApply(name -> name.toUpperCase());

Method4:
thenAccept(): thenAccept() consumes the result of a previous CompletableFuture without returning another result.

Syntax:
CompletableFuture
    .supplyAsync(() -> "Gayathri")
    .thenAccept(name -> {
        System.out.println(name);
    });

Method5:
thenCompose():  thenCompose() is used to chain two dependent asynchronous tasks, where the second task depends on the result of the first task.

Syntax:
CompletableFuture<String> future =
    CompletableFuture
        .supplyAsync(() -> "Gayathri")
        .thenCompose(name ->
            CompletableFuture.supplyAsync(() ->
                "Hello " + name
            )
        );

Method6:
thenCombine(): thenCombine() is used to combine the results of two independent CompletableFutures after both complete.

Syntax:
CompletableFuture<String> customer =
    CompletableFuture.supplyAsync(() -> "Gayathri");

CompletableFuture<Integer> balance =
    CompletableFuture.supplyAsync(() -> 50000);

CompletableFuture<String> dashboard =
    customer.thenCombine(balance,
        (name, amount) ->
            name + " : ₹" + amount
    );




Method	Purpose	Similar To
runAsync()	Execute async task without returning a value	Runnable
supplyAsync()	Execute async task and return a value	Callable
thenApply()	Transform the result	Function<T,R>
thenAccept()	Consume/use the result	Consumer<T>
thenCompose()	Chain dependent async tasks	Async chaining
thenCombine()	Combine independent async tasks	Parallel async tasks


FILE HANDLING:
The File class represents the path (location) of a file or directory in the file system. It allows you to perform operations like creating, deleting, renaming, and checking file properties.

Syntax: File file = new File("student.txt"); //Creates a Java object representing the file path. It does not create the physical file.

Common methods to create file:
1.Create file: 
File file = new File("student.txt");
file.createNewFile();

2.check if file exist:
file.exists(); //true or false

3.delete file:
file.delete();

4.get file name:
file.getName();

5.to get absolute path:
file.getAbsolutePath();

6.check if it’s a file:
file.isFile();

7.check if it’s a direcotry:
file.isDirectory();

Note: 
The File class doesn't read or write data.
It only provides information about the file or directory.

FileReader: 
FileReader is used to read character data from a text file.
Syntax: FileReader reader = new FileReader("student.txt");
It reads one character at a time.

Example: reader.read(); //returns A
reader.read(); //returns B
it returns only one character and hence its not efficeint

BufferedReader:
BufferedReader reads text efficiently by using an internal buffer and also provides methods like readLine().

Syntax:
BufferedReader reader = new BufferedReader(
        new FileReader("student.txt")
    );

Example:
Gayathri
Backend Developer
India

First call return gayathri, second call returns backend developer and so on

FileReader	BufferedReader
Reads one character at a time	Reads using a buffer
Slower for large files	Faster
Has read()	Has read() and readLine()
Usually used directly only for simple cases	Preferred for reading text files

Modern file handling:
Path: Path is an interface that represents the path to a file or directory.

Creating a path: instead of File file = new File("student.txt");, modern java has Path path = Paths.get("student.txt");

Create a file: Files.createFile(path);
Delete: Files.delete(path);
Copy: Files.copy(source, destination);
Move: Files.move(source, destination);

Old API	Modern API
File	Path
FileReader	Files.readString()
FileWriter	Files.writeString()

Interview Tip
If someone asks:
	Should I use File or Path in Java 21?
A strong answer is:
	For new applications, Path and the Files utility class are generally preferred because they provide a richer, more modern API. The File class is still supported and you'll often encounter it in older codebases.



![Uploading image.png…]()
