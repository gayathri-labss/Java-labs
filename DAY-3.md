# Day 3 — Comparable, Comparator, Iterators, Generics, and Streams

## Comparable vs Comparator

### Comparable
- Used to define natural ordering.
- Implemented by the class itself.
- Uses `compareTo()` method.

Syntax:

```java
class Student implements Comparable<Student> {
    @Override
    public int compareTo(Student s) {
        return this.id - s.id;
    }
}
```

### Comparator
- Used to define custom ordering.
- Implemented in a separate class.
- Uses `compare()` method.

Syntax:

```java
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return s1.name.compareTo(s2.name);
    }
}
```

Example:

```java
Collections.sort(studentList, new NameComparator());
```

| Feature | Comparable | Comparator |
|---|---:|---:|
| Package | `java.lang` | `java.util` |
| Method | `compareTo()` | `compare()` |
| Sorting type | Natural | Custom |
| Logic location | Inside class | Outside class |
| Number of sorting options | One | Multiple |

> Note: Sorting algorithms used by Collections.sort are typically O(n log n) time complexity (not `o(log n)`).

---

## Iterator vs ListIterator

### Iterator
- Used to traverse elements in a Collection one by one.
- Available in `java.util`.
- Allows safe removal during iteration.

Syntax:

```java
Iterator<String> it = list.iterator();

while (it.hasNext()) {
    String s = it.next();
    // ...
    it.remove(); // optional
}
```

Important methods:
- `hasNext()` — checks if a next element exists (O(1))
- `next()` — returns the next element (O(1))
- `remove()` — removes the current element (O(1))

Characteristics:
- Forward traversal only.
- Works with all `Collection` types.
- Cannot move backward.

### ListIterator
- Advanced iterator for `List` implementations (`ArrayList`, `LinkedList`, `Vector`).
- Supports bidirectional traversal and modification while iterating.

Key methods:
- `hasNext()`, `next()`
- `hasPrevious()`, `previous()`
- `add(E e)` — insert element
- `set(E e)` — replace last returned element
- `remove()` — remove last returned element

Characteristics:
- Forward and backward traversal.
- Can update elements using `set()`.
- Can insert elements using `add()`.

| Feature | Iterator | ListIterator |
|---|---:|---:|
| Works with | All Collections | Lists only |
| Forward traversal | ✅ Yes | ✅ Yes |
| Backward traversal | ❌ No | ✅ Yes |
| add() | ❌ No | ✅ Yes |
| set() | ❌ No | ✅ Yes |
| remove() | ✅ Yes | ✅ Yes |

---

## Module 6: Generics
Generics allow writing type-safe code using type parameters like `<T>`.

Without generics:

```java
ArrayList list = new ArrayList();
list.add("Java");
list.add(100);

// While retrieving:
String s = (String) list.get(1); // ClassCastException at runtime
```

With generics:

```java
ArrayList<String> list = new ArrayList<>();
list.add("Java");
list.add("Spring");
// Only String values allowed
```

Common type parameter names:
- `<T>` — Type
- `<E>` — Element
- `<K>` — Key
- `<V>` — Value
- `<N>` — Number

### Wildcards
1. `<?>` — Unbounded wildcard (means any type)
   - `List<?> list;` accepts `List<String>`, `List<Integer>`, etc.

2. `<? extends T>` — Upper-bounded wildcard (T or any subclass of T)
   - Example: `List<? extends Number>` accepts `List<Integer>`, `List<Double>`, etc.

3. `<? super T>` — Lower-bounded wildcard (T or any superclass of T)
   - Example: `List<? super Integer>` accepts `List<Integer>`, `List<Number>`, `List<Object>`, etc.

Generics provide compile-time type checking; they do not change runtime time complexity.

---

## Module 7: Java 8–21 Features — Streams
A Stream is used to process collections of data (filter, map, sort, count, etc.) in a simple and expressive way.

Notes about Streams:
- A Stream does not store data; it processes data from a source (List, Set, array).
- Streams do not modify the original collection.
- Streams are processed lazily — intermediate operations run only when a terminal operation is called.
- A Stream can be used only once.
- Stream operations can be chained.

Example — before Java 8:

```java
List<Integer> list = Arrays.asList(10, 20, 30, 40);
for (Integer i : list) {
    if (i > 20) {
        System.out.println(i);
    }
}
```

Example — with Java 8 Stream:

```java
list.stream()
    .filter(i -> i > 20)
    .forEach(System.out::println);
```

### Types of Stream Operations
- Intermediate operations: return another `Stream` (lazy)
  - `filter()`, `map()`, `sorted()`, `distinct()`, `limit()`, `skip()`
- Terminal operations: produce the final result (eager)
  - `collect()`, `forEach()`, `count()`, `reduce()`, `findFirst()`, `anyMatch()`

### Common Stream Methods (and complexity where applicable)
- `filter()` — O(n)
- `map()` — O(n)
- `count()` — O(n)
- `sorted()` — O(n log n)

Examples and usage:

- forEach()
```java
list.stream().forEach(System.out::println);
```

- collect()
```java
List<Integer> result = list.stream()
                            .filter(i -> i > 20)
                            .collect(Collectors.toList());
```

- count()
```java
long count = list.stream().count();
```

- findFirst()
```java
Optional<Integer> first = list.stream().findFirst();
```

- findAny() — useful with parallel streams
```java
Optional<Integer> any = list.stream().findAny();
```

- anyMatch()/allMatch()/noneMatch()
```java
boolean any = list.stream().anyMatch(i -> i > 30);
boolean all = list.stream().allMatch(i -> i > 5);
boolean none = list.stream().noneMatch(i -> i < 0);
```

- reduce() — combine elements (example: sum)
```java
int sum = list.stream().reduce(0, Integer::sum);
```

- min()/max()
```java
Optional<Integer> min = list.stream().min(Integer::compareTo);
Optional<Integer> max = list.stream().max(Integer::compareTo);
```

Summary table — Collections vs Stream

| Collection | Stream |
|---|---|
| Stores data | Processes data |
| Can be reused | Can be used only once |
| Eager | Lazy (intermediate operations) |

---

End of notes.
