JAVA-8:

	Functional programming:
Functional Programming (FP) is a programming style where behaviour (functions) can be passed around and composed to solve problems in a concise way.
Java supports this through:
	• Functional Interfaces
	• Lambda Expressions
	• Method References
	• Streams
	Java is not a pure Functional Programming language like Haskell. It is primarily an Object-Oriented language that added Functional Programming features in Java 8.


Object-Oriented Programming	Functional Programming
Focuses on objects	Focuses on behaviour/functions
More boilerplate	Less boilerplate
Often changes object state	Encourages fewer side effects
Classes are central	Functions are central

F2. Functional Interface

Definition:
An interface with exactly one abstract method.

Purpose:
Supports Lambda Expressions.

Rules:
- One abstract method
- Multiple default methods allowed
- Multiple static methods allowed
- @FunctionalInterface is optional but recommended

Examples:
Runnable
Callable
Comparator
Predicate
Function
Consumer
Supplier


	3. Lambda Expression:
	A Lambda Expression is a concise way of implementing a Functional Interface.
	
	Syntax:
	(parameters) -> {
	    statements;
	}
	
	
	Ex;
	Runnable r = new Runnable() {
	    @Override
	    public void run() {
	        System.out.println("Hello");
	    }
	};
	
	
	Q1. What is a Lambda Expression?
	A concise way to implement a Functional Interface.
	
	Q2. What does -> represent?
	The Lambda Operator.
	
	Q3. Can Lambda Expressions implement multiple abstract methods?
	No.
	
	Q4. Can a Lambda Expression exist without a Functional Interface?
	No.
	
	Q5. Why were Lambda Expressions introduced?
	To reduce boilerplate code and make Java more expressive, especially when working with collections and Streams.
	
	
	4. Method References:
	A Method Reference is a shorthand syntax for a Lambda Expression that only calls an existing method.

Why do we need Method References?
Suppose you have:
names.forEach(name -> System.out.println(name));
Here, the lambda doesn't contain any extra logic. It simply calls System.out.println(name).
Java lets you shorten it:
names.forEach(System.out::println);
Same behaviour.
Less code.
Better readability.


	5. Stream API:
	A Stream is a sequence of elements that supports operations such as filtering, mapping, sorting, and collecting.
	A Stream processes data. It does not store data.
	
	Collection
	      │
	      ▼
	 stream()
	      │
	      ▼
	Intermediate Operations
	(filter, map, sorted...)
	      │
	      ▼
	Terminal Operation
	(collect, count, forEach...)
	      │
	      ▼
	Result
	This is called as Stream pipeline
	
	Streams are single-use
	This is an important interview question.
	Stream<String> stream = names.stream();
	stream.forEach(System.out::println);
	stream.forEach(System.out::println);
	❌ Runtime error (IllegalStateException).
	Once a Stream has executed a terminal operation, it is consumed and cannot be reused.
	To process again, create a new Stream:
	names.stream().forEach(System.out::println);
	
	
	Collection	Stream
	Stores data	Processes data
	Can be reused	Single-use
	Eagerly holds elements	Operations are evaluated as the pipeline executes
	Supports add/remove	No add/remove operations
	
	
	Intermediate Operations:
	A. Filters
	B. Map - Transforms each element from one form to another
	Convert to Uppercase
	names.stream()
     .map(String::toUpperCase)
     .forEach(System.out::println);
	Output
	JAVA
SPRING
DOCKER
	
	
	Square Numbers
	List<Integer> numbers =
        List.of(2,3,4,5);
	numbers.stream()
       .map(x -> x * x)
       .forEach(System.out::println);
	Output
	4
9
16
25
	
	
	C. Flatmap(): flatMap() is an Intermediate Operation that transforms each element and then flattens the result into a single Stream.
	D. Distinct, sort, limit, skip
		List<Integer> numbers =
		        List.of(10,20,30,40,50,60);
		
		numbers.stream()
		       .skip(3)
		       .forEach(System.out::println);
		
		Outpur; 40,50,60
		
		
	E. Peek(): peek() is an Intermediate Operation that allows you to look at each element as it flows through the Stream without changing it.
List<String> names =
        List.of("Java", "Spring", "Docker");

names.stream()
     .peek(System.out::println)
     .forEach(x -> {});


Output: 
Java
Spring
Docker


Terminal Operation:
	1. forEach(): 
	2. Collect() : 
	collect() is a Terminal Operation that gathers the elements of a Stream into a desired result.
	The result could be:
		• List
		• Set
		• Map
		• String
		• Grouped Data
		• Statistics
	Think of it as "Take everything from the Stream and put it into something useful."
	
	
	Simplest Example
	List<Integer> numbers =
        List.of(1,2,3,4,5);
	Suppose we want even numbers.
	List<Integer> even =
        numbers.stream()
               .filter(n -> n % 2 == 0)
               .collect(Collectors.toList());
	Result
	[2,4]
	Now we have a List again.
	
	
	collect()	forEach()
	Returns data	Doesn't return data
	Creates a List, Set, Map, etc.	Performs an action
	Used when you need the result later	Used for printing, logging, sending emails, etc.
	3. Count()
	4. Reduce(): used to reduce multiple value into a single value
	5. Min, max
	6. Anymatch()
Returns true if at least one element matches.
boolean result =
numbers.stream()
       .anyMatch(n -> n > 10);

Output: true

7 allmatch():
allMatch()
Returns true if every element matches.
boolean result =
numbers.stream()
       .allMatch(n -> n > 0);
Output
true


8.
noneMatch()
Returns true if no element matches.
boolean result =
numbers.stream()
       .noneMatch(n -> n < 0);
Output
True


Operation	Type	Returns	Uses
filter()	Intermediate	Stream	Select data
map()	Intermediate	Stream	Transform data
flatMap()	Intermediate	Stream	Flatten nested collections
sorted()	Intermediate	Stream	Sort
distinct()	Intermediate	Stream	Remove duplicates
limit()	Intermediate	Stream	First N
skip()	Intermediate	Stream	Skip N
peek()	Intermediate	Stream	Debugging
forEach()	Terminal	void	Perform action
collect()	Terminal	Collection/Object	Collect results
count()	Terminal	long	Count elements
reduce()	Terminal	Single value	Sum/Max/Product
min()	Terminal	Optional	Minimum
max()	Terminal	Optional	Maximum
findFirst()	Terminal	Optional	First element
findAny()	Terminal	Optional	Any element
anyMatch()	Terminal	boolean	At least one matches
allMatch()	Terminal	boolean	All match
noneMatch()	Terminal	boolean	No matches


Java 8 v/s Java 17:
	1. Record:
A Record is a special type of class used to hold immutable data. It automatically generates the constructor, accessor methods, equals(), hashCode(), and toString().

	A Record is a special type of class introduced in Java 16 (and widely used in Java 17) that is designed to hold data.
	Think of it as a lightweight data class.
	Instead of writing a full class with fields, constructors, getters, equals(), hashCode(), and toString(), you can simply declare a record.
	
	public record Employee(int id, String name) {}
	This single line defines a complete data-holding class.
	
	They areimmutable, because fields setter/ getter are final keyword
	
	Java 8 POJO	Java 17 Record
	Can have setters	No setters
	Fields may change	Fields are final
	Mutable by default	Immutable by design
	Easy to modify state	Create a new instance for changes
	
	Type of constructors:
	A.  canonical constructor is the constructor that has exactly the same parameters as the record components.
	B. Compact constrcutor:
		• Omits the parameter list.
		• The compiler performs the field assignments automatically.

Interview Tip
If you're asked:
	"Why do Records have constructors if they already generate one?"
A strong answer is:
	"The generated constructor simply assigns values. We write our own canonical or compact constructor when we need validation, sanitisation, or other initialisation logic before the record is created."


Java8:
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


Java17:
public record Employee(int id, String name) {}

hat's it.
The compiler automatically generates:
	• Constructor
	• Accessor methods (id(), name())
	• equals()
	• hashCode()
	• toString()

	


