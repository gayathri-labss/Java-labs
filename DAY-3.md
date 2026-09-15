Comparable vs Comparator

Comparable:
	• Used to define natural sorting.
	• Implemented by the class itself.
	• Uses compareTo() method.

Syntax:
class Student implements Comparable<Student> {

    public int compareTo(Student s) {
        return this.id - s.id;
    }
}


Comparator:
• Used to define custom sorting.
• Implemented in a separate class.
• Uses compare() method.

Syntax:
class NameComparator implements Comparator<Student> {

    public int compare(Student s1, Student s2) {
        return s1.name.compareTo(s2.name);
    }
}

Ex: Collections.sort(studentList, new NameComparator());

Feature	Comparable	Comparator
Package	java.lang	java.util
Method	compareTo()	compare()
Sorting	Natural	Custom
Logic	Inside class	Outside class
Number of sorting options	One	Multiple

Time complexity is o(log n)


Iterator v/s ListIterator :

Iterator:

• Iterator is used to traverse (iterate through) elements in a collection one by one.
• Available in the java.util package.
• Instead of using loops, Iterator provides a standard way to access elements and allows safe removal while iterating.

Syntax: Iterator<String> it = list.iterator();

hasNext()   // Checks if next element exists

next()      // Returns next element

remove()    // Removes current element


Method	Complexity
hasNext()	O(1)
next()	O(1)
remove()	O(1)

• Forward traversal only.
• Works with all Collection types.
•  Cannot move backward.

List Iterator:
• ListIterator is an advanced version of Iterator.
• Works only with List implementations (ArrayList, LinkedList, Vector).

hasNext()

next()

hasPrevious()

previous()

add()

set()

remove()

• Forward traversal.
• Backward traversal.
• Can update elements using set().
•  Can insert elements using add().

Feature	Iterator	ListIterator
Works with	All Collections	Lists only
Forward	✅ Yes	✅ Yes
Backward	❌ No	✅ Yes
add()	❌ No	✅ Yes
set()	❌ No	✅ Yes
remove()	✅ Yes	✅ Yes


Module 6: Generics
Generic allows us to write type safe code. Uses <T>

Without generic:
ArrayList list = new ArrayList();

list.add("Java");
list.add(100);

While retriving: String s = (String) list.get(1); then we get ClassCastException

With generic:
ArrayList<String> list = new ArrayList<>();

list.add("Java");
list.add("Spring");

We can only add string values


Type;
<T>  -> Type

<E>  -> Element

<K>  -> Key

<V>  -> Value

<N>  -> Number

Wildcards
1. <?>
Means any type.
List<?> list;
Accepts:
	• List<String>
	• List<Integer>
	• List<Double>

2. <? extends T>
Upper bound.
Means:
"T or any subclass of T"
Example:
List<? extends Number>
Accepts:
	• Integer
	• Double
	• Float

3. <? super T>
Lower bound.
Means:
"T or any superclass of T"
Example:
List<? super Integer>
Accepts:
	• Integer
	• Number
	• Object

Time Complexity
Generics themselves do not affect time complexity.
They provide compile-time type checking, not performance improvements.


Module 7: Java 8–21 Features

Java -8
A Stream is used to process collections of data (filter, sort, map, count, etc.) in a simple and efficient way.
Note: A Stream does not store data. It only processes data from a source like a List, Set, or array

Why do we need streams?
Before java8

List<Integer> list = Arrays.asList(10,20,30,40);

for(Integer i : list){
    if(i > 20){
        System.out.println(i);
    }
}


Java 8 stream:
list.stream()
    .filter(i -> i > 20)
    .forEach(System.out::println);

• ✅ Streams don't modify the original collection.
• ✅ Streams are processed lazily (intermediate operations run only when a terminal operation is called).
• ✅ A stream can be used only once.
• ✅ Streams can be chained.


Types of Operations
Intermediate Operations
These return another Stream.
Examples:
	• filter()
	• map()
	• sorted()
	• distinct()
	• limit()
	• skip()

Terminal Operations
These produce the final result.
Examples:
	• collect()
	• forEach()
	• count()
	• reduce()
	• findFirst()
	• anyMatch()

Common Stream Methods
filter()
Used to filter data.
list.stream()
    .filter(i -> i > 20);
Output:
30
40

map()
Used to transform data.
list.stream()
    .map(i -> i * 2);
Output:
20
40
60
80

sorted()
Sorts elements.
list.stream()
    .sorted();

distinct()
Removes duplicates.
Arrays.asList(1,1,2,3,3)
↓
distinct()
↓
1,2,3

limit()
Returns the first n elements.
list.stream()
    .limit(3);

count()
Counts elements.
list.stream()
    .count();

collect()
Converts the stream back to a collection.
list.stream()
    .filter(i -> i > 20)
    .collect(Collectors.toList());

6. Time Complexity
Most stream operations process each element once:
	• filter() → O(n)
	• map() → O(n)
	• count() → O(n)
	• sorted() → O(n log n)

Collection	Stream
Stores data	Processes data
Can be reused	Can be used only once
Eager	Lazy (intermediate operations)


Once a terminal operation is executed, the stream cannot be reused.

1. forEach()
Used to perform an action on every element.
list.stream()
    .forEach(System.out::println);
Output:
10
20
30
40

2. collect()
Converts the stream into a collection (List, Set, etc.).
List<Integer> result = list.stream()
                           .filter(i -> i > 20)
                           .collect(Collectors.toList());
Output:
[30, 40]

3. count()
Returns the number of elements.
long count = list.stream().count();
Output:
4

4. findFirst()
Returns the first element.
Optional<Integer> first = list.stream()
                              .findFirst();
Output:
Optional[10]

5. findAny()
Returns any one element.
Mostly used with parallel streams.
Optional<Integer> any = list.stream()
                            .findAny();

6. anyMatch()
Returns true if at least one element matches.
boolean result = list.stream()
                     .anyMatch(i -> i > 30);
Output:
true

7. allMatch()
Returns true if all elements match.
boolean result = list.stream()
                     .allMatch(i -> i > 5);
Output:
true

8. noneMatch()
Returns true if no elements match.
boolean result = list.stream()
                     .noneMatch(i -> i < 0);
Output:
true

9. reduce()
Combines all elements into a single result.
Example: Sum
int sum = list.stream()
              .reduce(0, Integer::sum);
Output:
100
(10 + 20 + 30 + 40)

10. min() / max()
Find smallest or largest element.
list.stream().min(Integer::compareTo);
Output:
10
list.stream().max(Integer::compareTo);
Output:
40

Method	Purpose
forEach()	Print/process each element
collect()	Convert to List/Set
count()	Count elements
findFirst()	First element
findAny()	Any element
anyMatch()	At least one matches
allMatch()	All match
noneMatch()	No elements match
reduce()	Combine into one value
min() / max()	Find smallest/largest





