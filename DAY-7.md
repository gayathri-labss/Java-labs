# Day 7: Executor Framework, CompletableFuture, and File Handling

## 1) Executor

An `Executor` is an interface that provides a simple way to execute tasks.

```java
Executor executor = command -> {
    new Thread(command).start();
};

executor.execute(() -> {
    System.out.println("Hello");
});
```

### Key idea
- `Runnable` defines the task.
- `Thread` actually executes the task.
- `Executor` decides how and where the task runs.

---

## 2) Thread Pool

A thread pool is a collection of pre-created threads that are reused to execute multiple tasks.

### Why use a thread pool?
- Avoid creating a new thread for every task
- Improve performance
- Reuse threads efficiently

---

## 3) Executor Framework

The Executor Framework is a high-level API introduced in Java 5 to manage and execute tasks efficiently using thread pools instead of creating threads manually.

### Main interfaces
- `Executor`
- `ExecutorService`

---

## 4) ExecutorService

`ExecutorService` is an interface that extends `Executor` and provides:
- lifecycle management
- task submission
- shutdown operations

### Syntax
```java
ExecutorService executor = Executors.newFixedThreadPool(3);
```

### `execute()`
`execute()` is used to execute a `Runnable` task and does not return any result.

```java
ExecutorService executor = Executors.newFixedThreadPool(3);

executor.execute(() -> {
    System.out.println("Task Executed");
});
```

### `submit()`
`submit()` executes a task and returns a `Future` object so you can track the task and retrieve its result.

```java
ExecutorService executor = Executors.newFixedThreadPool(3);

Future<?> future = executor.submit(() -> {
    System.out.println("Task Executed");
});
```

### Interview Tip
If an interviewer asks:
- Which one do you use in real projects?

A strong answer is:
> I usually prefer `submit()` because it returns a `Future`, which lets me monitor completion, handle exceptions, or retrieve a result when needed. For simple fire-and-forget tasks, `execute()` is sufficient.

---

## 5) Runnable vs Callable

### Runnable
`Runnable` is a functional interface that defines a task.

```java
public interface Runnable {
    void run();
}
```

### Callable
`Callable` is a functional interface that represents a task which can return a result and throw checked exceptions.

```java
public interface Callable<V> {
    V call() throws Exception;
}
```

`V` represents the type of value returned by the task.

Examples:
- `Callable<Integer>` → returns an `Integer`
- `Callable<String>` → returns a `String`

### Runnable vs Callable

| Runnable | Callable |
| --- | --- |
| Functional interface | Functional interface |
| `void run()` | `V call()` |
| No return value | Returns a value |
| No checked exceptions | Can throw checked exceptions |

---

## 6) Evolution of Task Interfaces

### Step 1: Runnable (Java 1.0)
```java
public interface Runnable {
    void run();
}
```

Questions:
- Can it return a value? ❌ No
- Can it throw checked exceptions? ❌ No

### Step 2: Callable (Java 5)
```java
public interface Callable<V> {
    V call() throws Exception;
}
```

Questions:
- Can it return a value? ✅ Yes
- Can it throw checked exceptions? ✅ Yes

> NOTE: `Callable` is similar to `Runnable`; it defines the task.

---

## 7) Who executes a Callable?

`Callable` → `ExecutorService` → `submit()` → `Thread Pool` → `Thread executes`

### Can we create a thread using Callable?
No. A `Thread` constructor accepts a `Runnable`, not a `Callable`.

---

## 8) Future

`Future` is an interface that represents the result of an asynchronous computation.

Important point:
- `Future` is not the result itself.
- It is a placeholder for the result.

### Common methods

| Method | Meaning |
| --- | --- |
| `submit()` | Submit task |
| `Future` | Placeholder for result |
| `get()` | Retrieve result (waits if necessary) |
| `isDone()` | Check if task has finished |

> NOTE: `Callable` creates the result. `Future` holds the result.

---

## 9) CompletableFuture

Because `Future` has some limitations:
- `get()` blocks the current thread
- difficult to chain multiple asynchronous tasks
- difficult to combine results from multiple tasks
- limited support for asynchronous callbacks

`CompletableFuture` solves all of these.

### Why was it introduced when we already had Future?
A strong answer is:
> `Future` is limited because `get()` blocks the calling thread and it doesn't provide a convenient way to chain or combine asynchronous tasks. `CompletableFuture` supports non-blocking programming, task chaining, result combination, and better exception handling.

### Runnable vs ExecutorService

| Runnable | ExecutorService |
| --- | --- |
| Defines the task | Executes the task |
| Contains business logic | Manages threads |
| Doesn't know about threads | Assigns tasks to threads |

### `CompletableFuture`
`CompletableFuture` is a class that represents the result of an asynchronous computation and provides methods to chain, combine, and handle asynchronous tasks without manually blocking the current thread.

### Future vs CompletableFuture

| Future | CompletableFuture |
| --- | --- |
| Introduced in Java 5 | Introduced in Java 8 |
| `get()` often blocks | Supports non-blocking continuation |
| Limited API | Rich API for chaining and combining tasks |
| Hard to combine multiple tasks | Easy to combine multiple tasks |
| Basic exception handling | Better exception handling |

---

## 10) CompletableFuture Methods

### 1. `runAsync()`
`runAsync()` is used to execute a task asynchronously that does not return a result.

```java
CompletableFuture<Void> future =
    CompletableFuture.runAsync(() -> {
        System.out.println("Sending Email");
    });
```

### 2. `supplyAsync()`
`supplyAsync()` executes a task asynchronously and returns a result.

```java
CompletableFuture<String> future =
    CompletableFuture.supplyAsync(() -> {
        return "Hello";
    });
```

### `runAsync()` vs `supplyAsync()`

| `runAsync()` | `supplyAsync()` |
| --- | --- |
| No result | Returns a result |
| Similar to `Runnable` | Similar to `Callable` |

### 3. `thenApply()`
`thenApply()` transforms the result of a `CompletableFuture` into another value.

```java
CompletableFuture<String> future =
    CompletableFuture
        .supplyAsync(() -> "gayathri")
        .thenApply(name -> name.toUpperCase());
```

### 4. `thenAccept()`
`thenAccept()` consumes the result of a previous `CompletableFuture` without returning another result.

```java
CompletableFuture
    .supplyAsync(() -> "Gayathri")
    .thenAccept(name -> {
        System.out.println(name);
    });
```

### 5. `thenCompose()`
`thenCompose()` is used to chain two dependent asynchronous tasks, where the second task depends on the result of the first task.

```java
CompletableFuture<String> future =
    CompletableFuture
        .supplyAsync(() -> "Gayathri")
        .thenCompose(name ->
            CompletableFuture.supplyAsync(() ->
                "Hello " + name
            )
        );
```

### 6. `thenCombine()`
`thenCombine()` is used to combine the results of two independent `CompletableFuture` objects after both complete.

```java
CompletableFuture<String> customer =
    CompletableFuture.supplyAsync(() -> "Gayathri");

CompletableFuture<Integer> balance =
    CompletableFuture.supplyAsync(() -> 50000);

CompletableFuture<String> dashboard =
    customer.thenCombine(balance,
        (name, amount) ->
            name + " : ₹" + amount
    );
```

### Methods summary

| Method | Purpose | Similar To |
| --- | --- | --- |
| `runAsync()` | Execute async task without returning a value | `Runnable` |
| `supplyAsync()` | Execute async task and return a value | `Callable` |
| `thenApply()` | Transform the result | `Function<T,R>` |
| `thenAccept()` | Consume/use the result | `Consumer<T>` |
| `thenCompose()` | Chain dependent async tasks | Async chaining |
| `thenCombine()` | Combine independent async tasks | Parallel async tasks |

---

## 11) File Handling

The `File` class represents the path (location) of a file or directory in the file system. It allows you to perform operations like creating, deleting, renaming, and checking file properties.

```java
File file = new File("student.txt");
```

This creates a Java object representing the file path. It does not create the physical file.

### Common methods to create file

#### 1. Create file
```java
File file = new File("student.txt");
file.createNewFile();
```

#### 2. Check if file exists
```java
file.exists();
```

#### 3. Delete file
```java
file.delete();
```

#### 4. Get file name
```java
file.getName();
```

#### 5. Get absolute path
```java
file.getAbsolutePath();
```

#### 6. Check if it is a file
```java
file.isFile();
```

#### 7. Check if it is a directory
```java
file.isDirectory();
```

> NOTE: The `File` class doesn't read or write data. It only provides information about the file or directory.

---

## 12) FileReader and BufferedReader

### FileReader
`FileReader` is used to read character data from a text file.

```java
FileReader reader = new FileReader("student.txt");
```

It reads one character at a time.

```java
reader.read();
```

This returns a single character. Because of this, it is not very efficient.

### BufferedReader
`BufferedReader` reads text efficiently by using an internal buffer and also provides methods like `readLine()`.

```java
BufferedReader reader = new BufferedReader(
    new FileReader("student.txt")
);
```

Example content:
```text
Gayathri
Backend Developer
India
```

- First call returns `Gayathri`
- Second call returns `Backend Developer`
- Third call returns `India`

### FileReader vs BufferedReader

| FileReader | BufferedReader |
| --- | --- |
| Reads one character at a time | Reads using a buffer |
| Slower for large files | Faster |
| Has `read()` | Has `read()` and `readLine()` |
| Usually used directly only for simple cases | Preferred for reading text files |

---

## 13) Modern file handling with `Path`

A `Path` is an interface that represents the path to a file or directory.

Instead of:
```java
File file = new File("student.txt");
```

Modern Java uses:
```java
Path path = Paths.get("student.txt");
```

### Common examples
```java
Files.createFile(path);
Files.delete(path);
Files.copy(source, destination);
Files.move(source, destination);
```

### Old API vs Modern API

| Old API | Modern API |
| --- | --- |
| `File` | `Path` |
| `FileReader` | `Files.readString()` |
| `FileWriter` | `Files.writeString()` |

### Interview Tip
If someone asks:
- Should I use `File` or `Path` in Java 21?

A strong answer is:
> For new applications, `Path` and the `Files` utility class are generally preferred because they provide a richer, more modern API. The `File` class is still supported and you will often encounter it in older codebases.

---

## Final Quick Summary

- `Runnable` = task without result
- `Callable` = task with result and exceptions
- `ExecutorService` = executes tasks using a thread pool
- `Future` = placeholder for a result
- `CompletableFuture` = modern async API with chaining and combining support
- `File` = path representation
- `Path` + `Files` = modern file handling

---

![Uploading image.png…]()
