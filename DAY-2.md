# DAY-2: ADVANCED JAVA CONCEPTS

## 1. EXCEPTION HANDLING

### What is an Exception?

An exception is an unexpected event that occurs while a program is running. Instead of crashing the entire program, Java allows us to handle the problem gracefully.

When an exception occurs, Java immediately stops executing the remaining statements inside the try block and jumps to the matching catch block.

### TRY-CATCH BLOCK

```java
try {
    // Code that may throw an exception
} catch (ExceptionType e) {
    // Handle the exception
}
```

### FINALLY BLOCK

`finally` is used for cleanup operations.

**Examples:**
- Close a database connection
- Close a file
- Release a network connection
- Release a lock

These tasks should happen whether the operation succeeds or fails.

**Valid Combinations:**
```java
try { }
catch (...) { }

try { }
finally { }

try { }
catch (...) { }
finally { }
```

**Invalid:**
```java
try {
    // code
}
// ❌ Java requires a catch or finally block
```

---

## 2. THROW vs THROWS

### THROW

`throw` is a keyword used to manually create and throw an exception.

**Example:**
```java
public class Main {
    public static void main(String[] args) {
        int age = 15;
        
        if (age < 18) {
            throw new IllegalArgumentException("Age must be 18 or above.");
        }
        
        System.out.println("Welcome!");
    }
}
```

**Output:**
```
Exception in thread "main"
java.lang.IllegalArgumentException:
Age must be 18 or above.
```

### THROWS

`throws` is a keyword used in a method declaration to indicate that the method may throw an exception, and the caller is responsible for handling it. `throws` does NOT create an exception.

**Example:**
```java
import java.io.IOException;

public class Main {
    static void readFile() throws IOException {
        System.out.println("Reading File...");
    }
    
    public static void main(String[] args) {
        System.out.println("Program Started");
    }
}
```

The caller of `IOException` will handle it.

### THROW vs THROWS Comparison

| Aspect | THROW | THROWS |
|--------|-------|--------|
| **Used** | Inside a method | In a method declaration |
| **Purpose** | Creates an exception | Declares a possible exception |
| **Responsibility** | Manually raise an exception | Pass responsibility to caller |

---

## 3. MULTIPLE CATCH BLOCKS

### ❌ WRONG WAY

```java
try {
    int x = 10 / 0;
} catch (Exception e) {
    System.out.println("Exception");
} catch (ArithmeticException e) {
    System.out.println("Arithmetic");
}
```

This causes a **compilation error** because `Exception` will catch `ArithmeticException`, making the second catch block unreachable.

### ✅ CORRECT WAY

```java
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Arithmetic");
} catch (Exception e) {
    System.out.println("Exception");
}
```

**Rule**: Always catch specific exceptions first, then general exceptions.

---

## 4. CUSTOM EXCEPTIONS

A user-defined exception created by extending `Exception` (or another exception class) to represent application-specific errors.

**Why create custom exceptions?**
- Better readability
- Better debugging
- Better business logic
- Easier maintenance

⚠️ **Important**: `Exception` should be the last catch block because it is the parent class. If placed first, the more specific catch blocks become unreachable.

---

## 5. STACK vs HEAP MEMORY

### What is Memory?

Memory is a place where we store variables, objects, methods, and references.

### STACK MEMORY

- Stores local variables, method calls, and references
- Temporary memory
- Automatically freed when method exits
- Smaller size
- Faster access

### HEAP MEMORY

- Stores objects, arrays, strings, instance variables
- Longer lifetime
- Freed by Garbage Collection
- Larger size
- Slightly slower access

---

### EXAMPLE 1: Single Object

```java
Student s = new Student();
```

**Answer:**
- `s` → Stored in Stack
- Student Object → Stored in Heap

---

### EXAMPLE 2: Two References to Same Object

```java
Student s1 = new Student();
Student s2 = s1;
```

**Answer**: One Student object in Heap with two references (`s1`, `s2`) pointing to it.

```
STACK                          HEAP

s1  -----------\
                \
                 ---> Student Object
                /
s2  -----------/
```

There is still only **ONE** Student object in the Heap.
Both `s1` and `s2` point to the same object.

---

### EXAMPLE 3: Two Separate Objects

```java
Student s1 = new Student();
Student s2 = new Student();
```

**Answer**: **TWO** separate objects in Heap, each with its own reference.

```
STACK                      HEAP

s1  ------------> Student Object 1

s2  ------------> Student Object 2
```

Every time you see `new`, Java creates a new object in the Heap.

---

### INTERVIEW TIP: == vs .equals()

For objects:
- `==` checks whether both references point to the **SAME object** in memory.

**Example:**
```java
Student s1 = new Student();
Student s2 = s1;
s1 == s2?  // true (both point to same object)

Student s1 = new Student();
Student s2 = new Student();
s1 == s2?  // false (different objects)
```

---

## 6. GARBAGE COLLECTION

### What is Garbage Collection?

Garbage Collection (GC) is the process by which the JVM automatically removes objects from the Heap that are no longer being used.

**Example:**
```java
Student s1 = new Student();
Student s2 = s1;
s1 = null;
s2 = null;
```

```
STACK: s1 = null, s2 = null
HEAP: Student Object (no references)
```

**Result**: Now no references point to the object.
✅ It becomes eligible for Garbage Collection.

### How Does the JVM Know?

The JVM starts from special references called **GC Roots** (such as local variables on the stack). If an object can be reached from these roots, it is considered alive. If it cannot be reached, it is considered eligible for collection.

**Remember:**
- Reachable = Alive
- Not Reachable = Eligible for GC

---

## 7. STRINGS IN JAVA

⭐ **MOST ASKED JAVA QUESTIONS** ⭐

### What is a String?

A String is an object in Java that represents a sequence of characters.

**Characteristics:**
- Objects
- Used to store text
- Created from the String class
- **Immutable**

---

### Breaking Down a String Declaration

```java
String city = "Bengaluru";
```

**Explanation:**
- `String` → Class (Data type)
- `city` → Reference variable
- `"Bengaluru"` → String object (also called a String literal)

---

### WHY ARE STRINGS IMMUTABLE?

An immutable object is an object whose state (data/content) cannot be changed after it is created.

**Example:**
```java
String name = "Gayathri";
name = "Rahul";
```

**What happens:**
- ❌ **WRONG**: The object "Gayathri" changed into "Rahul"
- ✅ **CORRECT**:
  1. Java creates a String object "Gayathri"
  2. Java creates another String object "Rahul"
  3. The reference variable `name` now points to "Rahul"
  4. The original "Gayathri" object is NEVER modified

---

### STRING CONCATENATION - EXAMPLE 1

```java
String s = "Java";
s.concat(" Programming");
System.out.println(s);
```

**Output:** `Java`

**Explanation:**
```
STACK                      HEAP

s --------------------> "Java"

                      "Java Programming"
                       (No reference)
```

Since there is no reference to "Java Programming", it goes to Garbage Collection.

---

### STRING CONCATENATION - EXAMPLE 2

```java
String s = "Java";
s = s.concat(" Programming");
System.out.println(s);
```

**Output:** `Java Programming`

**Explanation:**
Now we **ASSIGN** the result back to `s`, so `s` points to the new String object.

---

### ⭐ INTERVIEW RULE

Methods like:
- `concat()`
- `replace()`
- `substring()`
- `toUpperCase()`
- `toLowerCase()`

Do **NOT** modify the original String. They return a **NEW** String.

If you don't assign the result, the original String remains unchanged.

---

### WHY DID JAVA MAKE STRINGS IMMUTABLE?

There are four main reasons:
1. 🔒 **Security**
2. 💾 **String Pool**
3. 🧵 **Thread Safety**
4. ⚡ **Performance**

**Real-Life Example:**
Think of your Aadhaar number or PAN number. Once issued, you don't want anyone to silently change it.

Strings used for things like:
- Passwords
- URLs
- Database names
- File paths
- Account numbers

should remain unchanged after they're created.

---

## 8. STRING POOL

### What is String Pool?

The String Pool is a memory area where Java stores only **ONE** copy of identical String literals to save memory.

**Example:**
```java
String s1 = "Java";
String s2 = "Java";
```

```
STACK                          HEAP (String Pool)

s1 -----------------------\
                            \
                             ---> "Java"
                            /
s2 -----------------------/
```

Both `s1` and `s2` point to the **SAME** String object in the String Pool.

```java
System.out.println(s1 == s2);
```

**Output:** `true` (both point to same object)

---

### With new Keyword

```java
String s1 = new String("Java");
String s2 = new String("Java");
s1 == s2?  // false
```

**Why?** Because `new` creates NEW objects. Each variable references a different object.

---

### == vs .equals()

| Operator | Method |
|----------|--------|
| **==** → Compares references (memory address) | **.equals()** → Compares contents (values) |
| Checks if both point to SAME object | Checks if both have SAME content |

**Example:**
```java
String s1 = "Java";
String s2 = "Java";
String s3 = new String("Java");

s1 == s2?        // true (same object in String Pool)
s1 == s3?        // false (different objects)
s1.equals(s3)?   // true (same content)
```

---

## 9. StringBuilder AND StringBuffer

### What is StringBuilder?

`StringBuilder` is a mutable class used to store and modify strings efficiently.
Mutable means its contents **CAN** be changed without creating a new object.

**Example:**
```java
StringBuilder sb = new StringBuilder("Java");
sb.append(" Programming");
System.out.println(sb);
```

**Output:** `Java Programming`
(Points to SAME object, not a new one)

---

### What is StringBuffer?

`StringBuffer` is also a mutable class like `StringBuilder`, but it is **THREAD-SAFE**.
It is used when multiple threads may modify the same string.

---

### Why is StringBuilder Faster?

Because it modifies the **SAME** object instead of creating new ones:
- Less memory usage
- Less Garbage Collection
- Better performance when repeatedly changing text

---

### String vs StringBuilder vs StringBuffer

| Aspect | String | StringBuilder | StringBuffer |
|--------|--------|---------------|--------------|
| **Mutability** | Immutable | Mutable | Mutable |
| **Performance** | Slower (creates new objects) | Faster | Synchronized (slower) |
| **Thread-Safe** | Not thread-safe | Not thread-safe | Thread-safe |

---

## 10. WRAPPER CLASSES

### What is a Wrapper Class?

A Wrapper Class is a class that wraps (encapsulates) a primitive data type into an object.

**Example:**
```java
int age = 22;              // Primitive
Integer age = 22;          // Wrapper Class object
```

---

### Why Do We Need Wrapper Classes?

Some Java features **ONLY** work with objects, not primitives.

**Example:**
```java
ArrayList<int> numbers = new ArrayList<>();      // ❌ Compilation Error
ArrayList<Integer> numbers = new ArrayList<>();  // ✅ Works perfectly
```

`ArrayList` stores objects, not primitives. This is one of the biggest reasons Wrapper Classes exist.

---

### PRIMITIVE TO WRAPPER CLASS MAPPING

| Primitive | Wrapper Class |
|-----------|---------------|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

---

### WHY NOT ALWAYS USE WRAPPER CLASSES?

**Primitives:**
- Faster
- Use less memory

**Wrapper Classes:**
- Need more memory
- Slightly slower (they are objects)

Use Wrapper Classes **ONLY** when you need objects.

---

## 11. AUTOBOXING AND UNBOXING

### AUTOBOXING

Autoboxing is the automatic conversion of a primitive data type into its corresponding Wrapper Class object by the Java compiler.

**Example:**
```java
Integer num = 20;  // You wrote this
```

Internally, compiler treats it as:
```java
Integer num = Integer.valueOf(20);
```

---

### UNBOXING

Unboxing is the automatic conversion of a Wrapper Class object into its corresponding primitive data type.

**Example:**
```java
Integer i = 10;
int x = i;         // Automatic unboxing
```

---

### AUTOBOXING vs UNBOXING

| Aspect | Autoboxing | Unboxing |
|--------|-----------|----------|
| **Conversion** | Primitive → Wrapper | Wrapper → Primitive |
| **Automatic** | Yes | Yes |
| **Example** | `Integer i = 10;` | `int x = i;` |

---

### ⚠️ NULL POINTER EXCEPTION IN UNBOXING

**Interview Question:**
"What happens when you unbox a null Integer?"

**Good Answer:**
A `NullPointerException` is thrown because Java tries to call `intValue()` on a null reference during unboxing.

---

## 12. INTEGER CACHING

### Example 1: Within Cache Range

```java
Integer a = 10;
Integer b = 10;
System.out.println(a == b);  // true
```

**Why?** Java caches Integer objects from **-128 to 127**. Both `a` and `b` reference the SAME cached object.

```
STACK                    INTEGER CACHE

a --------------------\
                       \
                        ---> Integer(10)
                       /
b --------------------/
```

---

### Example 2: Outside Cache Range

```java
Integer c = 200;
Integer d = 200;
c == d?  // false
```

**Why?** `200` is outside the cache range (-128 to 127), so Java creates **TWO** separate objects.

---

### ⭐ INTERVIEW QUESTION

**Question:**
"Why is `Integer a = 100; Integer b = 100; a == b` true, but `Integer c = 200; Integer d = 200; c == d` false?"

**Strong Answer:**
Java caches Integer objects from -128 to 127. The value 100 comes from the cache, so both variables reference the same object, making `a == b` true. The value 200 is outside the cache range, so Java creates two separate objects, making `c == d` false.

---

## 13. COLLECTIONS FRAMEWORK

### What is a Collection?

A Collection is an object that stores and manages a group of objects.

---

### Why Do We Need Collections?

**Without Collections:**
```java
String emp1 = "Rahul";
String emp2 = "Amit";
String emp3 = "Priya";
...
String emp100 = "John";
```
😵 This is difficult to manage.

**With a Collection:**
```java
ArrayList<String> employees = new ArrayList<>();
```
Much cleaner and easier!

---

### Uses of Collections

- Store multiple objects
- Add elements
- Remove elements
- Search elements
- Sort elements

---

### COLLECTION vs COLLECTIONS

**Collection (Interface):**
- Represents a group of objects
- Defines operations like: `add()`, `remove()`, `size()`, `isEmpty()`
- Classes like ArrayList, LinkedList, and HashSet implement this interface

**Collections (Utility Class):**
- Provides static methods to work with collections
- Contains helper methods: `sort()`, `reverse()`, `shuffle()`, `max()`, `min()`

---

## 14. LIST IMPLEMENTATIONS

### A. ARRAYLIST

`ArrayList` is a class that implements the List interface and stores objects in a dynamic array.

**Problem with Arrays:**
```java
int[] numbers = new int[3];
numbers[0] = 10;
numbers[1] = 20;
numbers[2] = 30;
numbers[3] = 40;  // ❌ IndexOutOfBoundsException
```

Arrays have fixed size. Once created, you cannot add more elements.

**Solution - ArrayList:**
```java
ArrayList<Integer> numbers = new ArrayList<>();
numbers.add(10);
numbers.add(20);
numbers.add(30);
numbers.add(40);  // ✅ Works! ArrayList grows automatically
```

---

#### SIZE vs CAPACITY

- **Size**: The number of elements currently stored
- **Capacity**: The total number of elements the internal array can hold before resizing

---

#### CAN ARRAYLIST STORE DUPLICATES?

```java
list.add(10);
list.add(10);
```

**Answer**: ✅ **Yes**. ArrayList allows duplicates.

---

#### DOES ARRAYLIST MAINTAIN INSERTION ORDER?

```java
list.add("Apple");
list.add("Banana");
list.add("Mango");
```

**Output:**
```
Apple
Banana
Mango
```

**Answer**: ✅ **Yes**. ArrayList maintains insertion order.

---

#### ARRAYLIST METHODS

| Method | Purpose |
|--------|---------|
| `add()` | Add element |
| `add(index, element)` | Add at specific position |
| `get()` | Get element at index |
| `set()` | Update element at index |
| `remove(index)` | Remove by index |
| `remove(object)` | Remove by value |
| `contains()` | Check if element exists |
| `size()` | Get number of elements |
| `isEmpty()` | Check if empty |
| `clear()` | Remove all elements |
| `indexOf()` | First occurrence of element |
| `lastIndexOf()` | Last occurrence of element |
| `addAll()` | Add another collection |
| `removeAll()` | Remove matching elements |
| `retainAll()` | Keep only common elements |
| `sort()` | Sort elements |
| `toArray()` | Convert to array |
| `clone()` | Create a copy |

---

#### EXAMPLE: Remove by Value

```java
ArrayList<Integer> list = new ArrayList<>();
list.add(10);
list.add(20);
list.add(30);

list.remove(Integer.valueOf(20));
System.out.println(list);
```

**Output:** `[10, 30]`

---

### B. LINKEDLIST

`LinkedList` is a class that implements the List interface and stores elements as a chain of nodes instead of a dynamic array.

**Structure:**
```
+------+      +------+      +------+
|  10  | ---> |  20  | ---> |  30  | ---> null
+------+      +------+      +------+

Each node contains:
    • The data (10, 20, 30)
    • A reference to the next node
```

---

#### ARRAYLIST vs LINKEDLIST

| Feature | ArrayList | LinkedList |
|---------|-----------|------------|
| **Internal Structure** | Dynamic Array | Doubly Linked List |
| **Random Access get()** | ⭐ O(1) Fast | ❌ O(n) Slow |
| **Insert at End** | O(1) (amortized) | O(1) |
| **Insert in Middle** | O(n) | O(n)* |
| **Remove from Middle** | O(n) | O(n)* |
| **Memory Usage** | Less | More (extra refs) |
| **Best Use Case** | Frequent reading | Frequent insertions/deletions |

---

### C. VECTOR

`Vector` is a class that implements the List interface and stores elements in a dynamic array, just like ArrayList, but it is **THREAD-SAFE**.

**Real-Life Example:**
Imagine a banking application. Two employees are updating the same customer record at the same time.

Without protection:
- Employee A → Adds transaction
- Employee B → Removes transaction
- Result: ❌ Wrong data, Lost updates, Unexpected errors

With Vector:
- Vector prevents this by allowing only **ONE** thread at a time to modify the list.

---

#### SYNCHRONIZATION

Synchronization is a mechanism that allows only one thread to execute a critical section of code at a time.

**Process:**
```
Lock
  ↓
Perform operation
  ↓
Unlock
```

Extra work means extra time. That's why **Vector is generally SLOWER** than ArrayList.

---

## 15. QUEUE IMPLEMENTATIONS

### A. STACK (LIFO - Last In, First Out)

`Stack` is a class that stores elements using the **LIFO** principle.

**Real-World Examples:**
- Undo feature in MS Word
- Browser back button
- Function calls in Java (Call Stack)
- Expression evaluation
- Parentheses matching: `()[]{}`

**Methods:**

| Operation | Time Complexity | Description |
|-----------|-----------------|-------------|
| `push()` | O(1) | Add to top |
| `pop()` | O(1) | Remove from top |
| `peek()` | O(1) | View top without removing |
| `empty()` | O(1) | Check if empty |
| `search()` | O(n) | Search for element |

---

### B. QUEUE (FIFO - First In, First Out)

`Queue` is an interface that stores elements using the **FIFO** principle.

⚠️ **Important**: Queue is an **INTERFACE**, not a class.

```java
Queue<Integer> q = new Queue<>();          // ❌ Compilation Error
Queue<Integer> q = new LinkedList<>();     // ✅ Correct
```

**Real-World Examples:**
- 🖨️ Printer jobs
- 🎟️ Ticket booking
- ☎️ Customer support calls
- 📩 Message processing
- 💻 CPU task scheduling

---

#### QUEUE STRUCTURE

```
Front                    Rear

A  →  B  →  C
```

- New elements are added at the **REAR**
- Elements are removed from the **FRONT**

---

#### QUEUE METHODS

| Method | Purpose |
|--------|---------|
| `offer()` | Add element to rear |
| `poll()` | Remove and return front element |
| `peek()` | Return front without removing |

---

#### QUEUE METHOD COMPARISON

| Method | Time Complex | Returns null if empty | Throws exception |
|--------|-------------|----------------------|------------------|
| `offer()` | O(1) | No | No |
| `poll()` | O(1) | Yes | No |
| `remove()` | O(1) | No | Yes |
| `peek()` | O(1) | Yes | No |
| `element()` | O(1) | No | Yes |

---

#### ⚠️ IMPORTANT DIFFERENCE

- **poll()** - Removes front element. If queue is empty, returns **null** (safe)
- **remove()** - Removes front element. If queue is empty, throws **NoSuchElementException**

- **peek()** - Returns front element without removing. If empty, returns **null** (safe)
- **element()** - Returns front element. If empty, throws **exception**

**Safe methods**: `poll()`, `peek()` → return null
**Strict methods**: `remove()`, `element()` → throw exception

---

### C. PRIORITY QUEUE

`PriorityQueue` is a class that implements the Queue interface and orders elements according to their **priority** instead of insertion order.

**Real-World Examples:**
- 🏥 Hospital emergency systems
- 💻 CPU process scheduling
- 🌐 Network packet routing
- 📍 GPS shortest path algorithms (Dijkstra's)
- 📊 Task schedulers

---

#### PRIORITYQUEUE METHODS

| Method | Purpose |
|--------|---------|
| `offer()` | Add element |
| `peek()` | Show smallest element (no remove) |
| `poll()` | Remove smallest element |

**Time Complexity:**
- `offer()` → O(log n)
- `poll()` → O(log n)
- `peek()` → O(1)

**Internal Structure:**
Priority Queue uses a **HEAP** data structure internally. By default, it stores the **SMALLEST** element at the root.

---

### D. DEQUE (Double-Ended Queue)

`Deque` (pronounced "deck") stands for Double-Ended Queue.
It is an interface that allows you to add and remove elements from **BOTH** the front and the rear.

**Combination of both Queue and Stack!**

**Syntax:**
```java
Deque<Integer> deque = new ArrayDeque<>();
```

**Note:**
- `Deque` → Interface
- `ArrayDeque` → Class

---

#### DEQUE FEATURE COMPARISON

| Feature | Queue | Stack | Deque |
|---------|-------|-------|-------|
| **Add Front** | ❌ | ❌ | ✅ |
| **Add Rear** | ✅ | ❌ | ✅ |
| **Remove Front** | ✅ | ❌ | ✅ |
| **Remove Rear** | ❌ | ✅ | ✅ |
| **FIFO** | ✅ | ❌ | Can support FIFO |
| **LIFO** | ❌ | ✅ | Can support LIFO |

---

#### WHY USE ARRAYDEQUE OVER STACK?

**Answer:**
- Stack is a **legacy class** (introduced in early Java and extends Vector)
- Because Stack extends Vector, its operations are **synchronized**, which adds unnecessary overhead in single-threaded use cases
- **ArrayDeque is generally FASTER** and is the recommended choice for implementing stack behavior in modern Java

---

#### LIST IMPLEMENTATIONS SUMMARY

| Collection | Order | Duplicates | Access Pattern |
|-----------|-------|-----------|-----------------|
| **ArrayList** | Maintains insertion order | ✅ Yes | Fast random access |
| **LinkedList** | Maintains insertion order | ✅ Yes | Good for sequential access |
| **Vector** | Maintains insertion order | ✅ Yes | Like ArrayList but synchronized |
| **Stack** | LIFO | ✅ Yes | Last In, First Out |
| **Queue** | FIFO | ✅ Yes | First In, First Out |
| **PriorityQueue** | Priority order | ✅ Yes | Smallest element first (default) |
| **Deque** | Double-ended | ✅ Yes | Stack + Queue |

---

## 16. SET INTERFACE

### What is a Set?

`Set` is an interface that stores **UNIQUE** elements. It does **NOT** allow duplicate values.

**Example:**
```java
Set<String> names = new HashSet<>();
names.add("A");
names.add("B");
names.add("A");  // Duplicate, will be ignored

Output: A, B
```

⚠️ **Important**: Set has **NO INDEXING**.

---

### SET OPERATIONS TIME COMPLEXITY

| Operation | Time Complexity |
|-----------|-----------------|
| `add()` | O(1) average |
| `remove()` | O(1) average |
| `contains()` | O(1) average |

---

### SET IMPLEMENTATIONS

**1. HashSet** ⭐⭐⭐⭐⭐ (Most Used)
- No duplicates
- No guaranteed order
- Fastest for general use

**2. LinkedHashSet**
- No duplicates
- Maintains insertion order

**3. TreeSet**
- No duplicates
- Stores elements in sorted order

---

### SPECIAL QUESTION: Can a HashSet contain null?

**Answer**: ✅ **Yes**, a HashSet allows **ONE** null element.

---

## 17. HASHING CONCEPT

### What is Hashing?

Hashing is the process of converting an object into a unique integer value called a **HASH CODE**, which Java uses to quickly store and find objects.

A **bucket** is a storage unit used to store the hash code.

---

### HASH COLLISION

A hash collision occurs when two **different** objects produce the **SAME** hash code and are assigned to the **same** bucket.

**Example:**
```
"Java" → Bucket 3
"Spring" → Bucket 3 (same bucket, different objects)
```

**Should Java reject the second object?**
- ❌ **No**. Because `"Java"` ≠ `"Spring"`. Same bucket, but different objects.
- Both should be stored.

**To resolve collisions:**
1. `hashCode()` → Find the correct bucket
2. `equals()` → Check if the object already exists in that bucket

---

### HASHSET INTERNAL WORKING

HashSet stores unique elements and uses a HashMap internally.

**add(element) Flow:**
```
add(element)
    ↓
Calculate hashCode()
    ↓
Find bucket
    ↓
Compare using equals()
    ↓
Already exists?
    • Yes → Ignore (No duplicates)
    • No → Add element
```

---

## 18. HASHSET vs LINKEDHASHSET vs TREESET

| Feature | HashSet | LinkedHashSet | TreeSet |
|---------|---------|---|---|
| **Duplicates** | ❌ No | ❌ No | ❌ No |
| **Order** | No guarantee | Insertion order | Sorted order |
| **Internal Structure** | HashMap | LinkedHashMap | Red-Black Tree |
| **Search** | O(1) | O(1) | O(log n) |
| **Null** | 1 allowed | 1 allowed | Not allowed |

---

## 19. MAP INTERFACE

### What is a HashMap?

`HashMap` is a class that stores data as **KEY-VALUE PAIRS** and uses hashing internally for fast insertion, searching, and deletion.

**HashMap Operations Time Complexity:**

| Operation | Average Time |
|-----------|--------------|
| `put()` | O(1) |
| `get()` | O(1) |
| `remove()` | O(1) |
| `containsKey()` | O(1) |

---

### HASHMAP vs HASHSET

| HashMap | HashSet |
|---------|---------|
| Stores key-value pairs | Stores only values |
| Keys are unique | Elements are unique |
| Uses `put()` | Uses `add()` |
| Uses `get(key)` | No `get(index)` |

---

### HASHMAP INTERNAL STRUCTURE

HashMap uses an **ARRAY OF BUCKETS** where each bucket stores a **NODE** object.

**Node consists of:**
- `key`
- `value`
- `hashCode`
- `next` element (for collision handling)

---

### put(key, value) FLOW

```
put(key, value)
    ↓
Calculate hashCode()
    ↓
Find bucket index
    ↓
Bucket empty?
    • Yes → Insert new Node
    • No → Compare keys using equals()
        ○ Same key → Replace value
        ○ Different key → Add new Node (collision)
```

---

### get(key) FLOW

```
get(key)
    ↓
Calculate hashCode()
    ↓
Find bucket
    ↓
Compare keys using equals()
    ↓
Return value
```

---

### HASH COLLISIONS IN HASHMAP

Collision occurs when two **KEYS** map to the **SAME** bucket.
Java stores both nodes in the **SAME** bucket.

---

### HASHMAP RULES

✅ Keys must be unique
✅ Values can be duplicated
✅ Uses `hashCode()` and `equals()`
✅ Not thread-safe
✅ Allows **ONE** null key and **MULTIPLE** null values

---

### LINKEDHASHMAP

`LinkedHashMap` is similar to `HashMap` but maintains insertion order.

---

## END OF DAY-2 ✅
