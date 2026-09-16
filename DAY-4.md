# JAVA 8: Functional Programming

## Overview

**Functional Programming (FP)** is a programming style where behaviour (functions) can be passed around and composed to solve problems in a concise way.

Java supports this through:
- Functional Interfaces
- Lambda Expressions
- Method References
- Streams

> **Note:** Java is not a pure Functional Programming language like Haskell. It is primarily an Object-Oriented language that added Functional Programming features in Java 8.

### OOP vs Functional Programming

| Object-Oriented Programming | Functional Programming |
|---|---|
| Focuses on objects | Focuses on behaviour/functions |
| More boilerplate | Less boilerplate |
| Often changes object state | Encourages fewer side effects |
| Classes are central | Functions are central |

---

## 1. Functional Interface

### Definition
An interface with exactly one abstract method.

### Purpose
Supports Lambda Expressions.

### Rules
- One abstract method
- Multiple default methods allowed
- Multiple static methods allowed
- `@FunctionalInterface` is optional but recommended

### Built-in Examples
- `Runnable`
- `Callable`
- `Comparator`
- `Predicate`
- `Function`
- `Consumer`
- `Supplier`

---

## 2. Lambda Expression

A **Lambda Expression** is a concise way of implementing a Functional Interface.

### Syntax
```java
(parameters) -> {
    statements;
}
```

### Example: Before and After

**Before Lambda (Anonymous Class):**
```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello");
    }
};
```

**After Lambda:**
```java
Runnable r = () -> System.out.println("Hello");
```

### Common Interview Questions

| Question | Answer |
|---|---|
| What is a Lambda Expression? | A concise way to implement a Functional Interface. |
| What does `->` represent? | The Lambda Operator. |
| Can Lambda Expressions implement multiple abstract methods? | No. |
| Can a Lambda Expression exist without a Functional Interface? | No. |
| Why were Lambda Expressions introduced? | To reduce boilerplate code and make Java more expressive, especially when working with collections and Streams. |

---

## 3. Method References

A **Method Reference** is a shorthand syntax for a Lambda Expression that only calls an existing method.

### Why Do We Need Method References?

**Without Method Reference:**
```java
names.forEach(name -> System.out.println(name));
```

**With Method Reference:**
```java
names.forEach(System.out::println);
```

**Benefits:**
- Same behaviour
- Less code
- Better readability

---

## 4. Stream API

A **Stream** is a sequence of elements that supports operations such as filtering, mapping, sorting, and collecting.

> **Key Point:** A Stream processes data. It does not store data.

### Stream Pipeline

```
Collection
    ↓
 stream()
    ↓
Intermediate Operations (filter, map, sorted...)
    ↓
Terminal Operation (collect, count, forEach...)
    ↓
Result
```

### Important: Streams are Single-Use

Once a Stream has executed a terminal operation, it is consumed and cannot be reused.

```java
Stream<String> stream = names.stream();
stream.forEach(System.out::println);
stream.forEach(System.out::println);  // ❌ Runtime error (IllegalStateException)
```

**Solution:** Create a new Stream each time:
```java
names.stream().forEach(System.out::println);
```

### Collection vs Stream

| Collection | Stream |
|---|---|
| Stores data | Processes data |
| Can be reused | Single-use |
| Eagerly holds elements | Operations are evaluated as the pipeline executes |
| Supports add/remove | No add/remove operations |

---

## 5. Intermediate Operations

Intermediate operations transform a Stream and return another Stream.

### A. Filter

```java
List<String> names = List.of("Java", "Spring", "Docker");

names.stream()
     .filter(name -> name.length() > 4)
     .forEach(System.out::println);
```

**Output:**
```
Spring
Docker
```

### B. Map - Transforms each element from one form to another

**Example 1: Convert to Uppercase**
```java
List<String> names = List.of("Java", "Spring", "Docker");

names.stream()
     .map(String::toUpperCase)
     .forEach(System.out::println);
```

**Output:**
```
JAVA
SPRING
DOCKER
```

**Example 2: Square Numbers**
```java
List<Integer> numbers = List.of(2, 3, 4, 5);

numbers.stream()
       .map(x -> x * x)
       .forEach(System.out::println);
```

**Output:**
```
4
9
16
25
```

### C. FlatMap

`flatMap()` is an Intermediate Operation that transforms each element and then flattens the result into a single Stream.

### D. Distinct, Sort, Limit, Skip

```java
List<Integer> numbers = List.of(10, 20, 30, 40, 50, 60);

numbers.stream()
       .skip(3)
       .forEach(System.out::println);
```

**Output:** `40, 50, 60`

### E. Peek - Debugging Tool

`peek()` is an Intermediate Operation that allows you to look at each element as it flows through the Stream without changing it.

```java
List<String> names = List.of("Java", "Spring", "Docker");

names.stream()
     .peek(System.out::println)
     .forEach(x -> {});
```

**Output:**
```
Java
Spring
Docker
```

---

## 6. Terminal Operations

Terminal operations produce a final result and consume the Stream.

### 1. forEach()

Performs an action on each element.

```java
names.forEach(System.out::println);
```

### 2. collect()

Gathers the elements of a Stream into a desired result.

**Possible Results:**
- List
- Set
- Map
- String
- Grouped Data
- Statistics

**Example: Filter Even Numbers**
```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

List<Integer> even = numbers.stream()
                            .filter(n -> n % 2 == 0)
                            .collect(Collectors.toList());
```

**Result:** `[2, 4]`

### collect() vs forEach()

| collect() | forEach() |
|---|---|
| Returns data | Doesn't return data |
| Creates a List, Set, Map, etc. | Performs an action |
| Used when you need the result later | Used for printing, logging, sending emails |

### 3. count()

Returns the count of elements.

```java
long count = numbers.stream().count();
```

### 4. reduce()

Reduces multiple values into a single value.

```java
int sum = numbers.stream().reduce(0, (a, b) -> a + b);
```

### 5. min() and max()

```java
Optional<Integer> min = numbers.stream().min(Comparator.naturalOrder());
Optional<Integer> max = numbers.stream().max(Comparator.naturalOrder());
```

### 6. anyMatch()

Returns `true` if at least one element matches.

```java
List<Integer> numbers = List.of(5, 10, 15, 20);

boolean result = numbers.stream()
                        .anyMatch(n -> n > 10);
```

**Output:** `true`

### 7. allMatch()

Returns `true` if every element matches.

```java
boolean result = numbers.stream()
                        .allMatch(n -> n > 0);
```

**Output:** `true`

### 8. noneMatch()

Returns `true` if no element matches.

```java
boolean result = numbers.stream()
                        .noneMatch(n -> n < 0);
```

**Output:** `true`

---

## 7. Stream Operations Summary

| Operation | Type | Returns | Uses |
|---|---|---|---|
| filter() | Intermediate | Stream | Select data |
| map() | Intermediate | Stream | Transform data |
| flatMap() | Intermediate | Stream | Flatten nested collections |
| sorted() | Intermediate | Stream | Sort |
| distinct() | Intermediate | Stream | Remove duplicates |
| limit() | Intermediate | Stream | First N elements |
| skip() | Intermediate | Stream | Skip N elements |
| peek() | Intermediate | Stream | Debugging |
| forEach() | Terminal | void | Perform action |
| collect() | Terminal | Collection/Object | Collect results |
| count() | Terminal | long | Count elements |
| reduce() | Terminal | Single value | Sum/Max/Product |
| min() | Terminal | Optional | Minimum |
| max() | Terminal | Optional | Maximum |
| findFirst() | Terminal | Optional | First element |
| findAny() | Terminal | Optional | Any element |
| anyMatch() | Terminal | boolean | At least one matches |
| allMatch() | Terminal | boolean | All match |
| noneMatch() | Terminal | boolean | No matches |

---

# JAVA 8 vs JAVA 17

## 1. Records

A **Record** is a special type of class used to hold immutable data. It automatically generates the constructor, accessor methods, `equals()`, `hashCode()`, and `toString()`.

### Definition

A Record is a special type of class introduced in Java 16 (and widely used in Java 17) that is designed to hold data. Think of it as a lightweight data class.

Instead of writing a full class with fields, constructors, getters, `equals()`, `hashCode()`, and `toString()`, you can simply declare a record.

### Example

```java
public record Employee(int id, String name) {}
```

This single line defines a complete data-holding class.

**Compiler automatically generates:**
- Constructor
- Accessor methods: `id()`, `name()`
- `equals()`
- `hashCode()`
- `toString()`

> **Note:** Records are immutable because fields have the `final` keyword.

### Java 8 POJO vs Java 17 Record

| Java 8 POJO | Java 17 Record |
|---|---|
| Can have setters | No setters |
| Fields may change | Fields are final |
| Mutable by default | Immutable by design |
| Easy to modify state | Create a new instance for changes |

### Code Comparison

**Java 8:**
```java
public class Employee {
    private int id;
    private String name;

    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```

**Java 17:**
```java
public record Employee(int id, String name) {}
```

### Types of Constructors in Records

#### A. Canonical Constructor

The constructor that has exactly the same parameters as the record components.

```java
public record Employee(int id, String name) {
    public Employee(int id, String name) {
        // Validation logic
        if (id <= 0) throw new IllegalArgumentException("ID must be positive");
        this.id = id;
        this.name = name;
    }
}
```

#### B. Compact Constructor

- Omits the parameter list
- The compiler performs the field assignments automatically

```java
public record Employee(int id, String name) {
    public Employee {
        if (id <= 0) throw new IllegalArgumentException("ID must be positive");
    }
}
```

### Interview Tip

**Q: Why do Records have constructors if they already generate one?**

**A:** The generated constructor simply assigns values. We write our own canonical or compact constructor when we need validation, sanitisation, or other initialisation logic before the record is created.
