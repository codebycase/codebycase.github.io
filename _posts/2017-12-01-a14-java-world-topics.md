---
layout: article
title: Algorithms - Java World Topics
key: a14-java-world-topics
categories: Algorithms
tags: Java
---

## Java Language

### Programming

- Private methods are final since we cannot override private methods.
- When a function is static, runtime polymorphism doesn't happen.  

The answer for the code below is: Base::show() called

```java
	class Base {
		public static void show() {
			System.out.println("Base::show() called");
		}
	}

	class Derived extends Base {
		public static void show() {
			System.out.println("Derived::show() called");
		}
	}

	class Main {
		public static void main(String[] args) {
			Base b = new Derived();
			b.show();
		}
	}
```

<!--more-->

- It is not allowed to do **super.super.** We can only access Grandparent's members using Parent.
- Every class is part of some package. If no package is specified, will use a special unnamed package.
- You can import static methods from a class.

```java
// Note static keyword after import.
import static java.lang.System.*;

class StaticImportDemo
{
   public static void main(String args[])
   {      
        out.println("GeeksforGeeks");
   }
}
```
- An interface can contain following type of members:  
  public, static, final fields (i.e., constants)  
  default and static methods with bodies
- Final method can't be overridden, Thus, an abstract function can't be final.

```java
abstract class demo {
  public int a;
  demo() {
    a = 10;
  }
  abstract public void set();
  abstract final public void get(); // final is not allowed here!
}
```

- Parameters are passed by value. You cannot write a standard swap method to swap objects.

"Java manipulates objects 'by reference', but it passes object references to methods 'by value'. Means Java **copies** and passes the reference by value, not the object."

The output is: i = 10, j = 20, The swap method doesn't work as expected.

```java
// This swap it not working as the references are passed by value!
public static void swap(Integer i, Integer j) {
	Integer temp = i;
	i = j;
	j = temp;
}

public static void main(String[] args) {
	Integer i = 10;
	Integer j = 20;
	swap(i, j);
	System.out.println("i = " + i + ", j = " + j); // Answer: i = 10, j = 20
}
```

But you can use a intWrap to swap the values.

```java
class IntWrap {
	int x;
	IntWrap(int x) {
		this.x = x;
	}
}

public class Main {
	public static void main(String[] args) {
		IntWrap i = new IntWrap(10);
		IntWrap j = new IntWrap(20);
		swap(i, j);
		System.out.println("i = " + i.x + ", j = " + j.x);
	}

	public static void swap(IntWrap i, IntWrap j) {
		int temp = i.x;
		i.x = j.x;
		j.x = temp;
	}
}
```

- Java doesn't support default arguments.

This code snippet is wrong:

```
static int fun(int x = 0) {
  return x;
}
```

- Negative numbers are represented by the two's complement of their absolute value.

```
class Test {
    public static void main(String args[])  {
       int x = -1; // 11111111111111111111111111111110
       System.out.println(x>>>29); // 7
       System.out.println(x>>>30); // 3
       System.out.println(x>>>31); // 1
   }   
}
```

- Consider the associativity when multiple operators applied.

```java
public static void main(String args[])  {
    System.out.println(10  +  20 + "GeeksQuiz");
    System.out.println("GeeksQuiz" + 10 + 20);
}
```

The result is:

```
30GeeksQuiz
GeeksQuiz1020
```

- array1.equals(array2) is the same as (array1 == array2), just to compare reference value/address. To compare 2 set of arrays, please use Arrays.equals(array1, array2).

- Unlike class members, local variables of methods must be assigned a value to before they are accessed.

```java
class Main {   
   public static void main(String args[]) {      
         int t;      
         System.out.println(t); // compile error
    }   
}
```

- The default constructor calls super() and initializes all instance variables to default value like 0, null.

- If we write our own constructor, then compiler doesn't create default constructor in Java.

```java
class Point {
	int m_x, m_y;

	public Point(int x, int y) {
		m_x = x;
		m_y = y;
	}

	public static void main(String args[]) {
		Point p = new Point(); // compile error!
	}
}
```

- Static blocks are called before constructors.

```java
public class Test {
	static int a;

	static {
		a = 4;
		System.out.println("inside static block");
		System.out.println("a = " + a);
	}

	Test() {
		System.out.println("inside constructor");
		a = 10;
	}

	public static void func() {
		a = a + 1;
		System.out.println("a = " + a);
	}

	public static void main(String[] args) {
		Test obj = new Test();
		obj.func(); // Alert: The static method should be accessed in a static way.
	}
}
```

The answer is:

```
inside static block
a = 4
inside constructor
a = 11
```

- Checked vs Unchecked Exceptions

**Checked:** are the exceptions that are checked at compile time. The method must either handle (catch) or throw the exception.

```

IOException
FileNotFoundException
ClassNotFoundException
```

**Unchecked:** are the exceptions that are not checked at compile time. It's up to the programers to be civilized, and specify or catch the exceptions.

```
NullPointerException
ArrayIndexOutOfBoundsException
ArithmeticException
IllegalArgumentException
NumberFormatException
```

_In Java exceptions under Error and RuntimeException classes are unchecked exceptions, everything else under throwable is checked._

- When a subclass exception is mentioned after base class exception, then error occurs.

```java
class Test {
	public static void main(String[] args) {
		try {
			int a[] = { 1, 2, 3, 4 };
			for (int i = 1; i <= 4; i++) {
				System.out.println("a[" + i + "]=" + a[i] + "\n");
			}
		} catch (Exception e) {
			System.out.println("error = " + e);
		} catch (ArrayIndexOutOfBoundsException e) { // unreachable!
			System.out.println("ArrayIndexOutOfBoundsException");
		}
	}
}
```

### Nested Classes

Java allows to define a class within another class. Such a class is called a nested class.

Nested classes are divided into two categories: static and non-static. Non-static nested classes are called inner classes. Inner classes have access to other members of the enclosing class, **even if they are private.**

Inner classes have three types: **Inner Class**, **Method-local Inner Class** and **Anonymous Inner Class**.

To create an object for the static nested class:

```java
OuterClass.StaticNestedClass nestedObject = new OuterClass.StaticNestedClass();
```

To create an inner class, you must first instantiate the outer class. Then create the inner object.

```java
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```

### Garbage Collection

In Java, allocation and de-allocation of memory space for objects are done by the garbage collection process in an automated way by the JVM.

The generational garbage collector is based upon the assumption: "the majority of objects that are created are quickly discarded, and objects that are not quickly collected are likely to be around for a while."

**Hotspot JVM Architecture**

![Hotspot JVM Architecture](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide2.png)

There are three components of the JVM that are focused on when tuning performance. The heap is where your object data is stored. This area is then managed by the garbage collector selected at startup. Most tuning options relate to sizing the heap and choosing the most appropriate garbage collector for your situation. The JIT compiler also has a big impact on performance but rarely requires tuning with the newer versions of the JVM.

To determine which objects are no longer in use, the JVM runs a **mark-and-sweep** algorithm (DFS). The algorithm traverses all object references, starting with the GC roots, and marks every object found as alive. All of the heap memory that is not occupied by marked objects is reclaimed.


A simple Java application has the following GC roots:  

- Local variable in the main method  
- The main thread  
- Static variables of the main class  

![GC Roots With Memory Leak](/assets/images/algorithms/gc-roots-with-memory-leak.png)

**Java Heap Memory**

Java Heap Memory is the area of memory used for dynamic allocation. Which is separated into different generations:

![Hotspot Heap Structure](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide5.png)

- The **Young Generation** is where all new objects are allocated and aged. When the young generation fills up, this causes a minor garbage collection. Which is "Stop the World" events!
  1. Eden space
  2. Survivor space (S0 and S1)
- The **Old Generation** is used to store long surviving objects. Typically, a threshold is set for young generation object and when that age is met, the object gets moved to the old generation. Eventually the old generation needs to be collected. This event is called a major garbage collection. Which is also "Stop the World" event and much slower, should be minimized.
- The **Permanent Generation** contains metadata required by the JVM to describe the classes and methods used in the application. Which is populated by the JVM at runtime based on classes in use by the application. (Removed from Java 8).

**Java Garbage Collectors**

- The **Serial GC** is the default for client style machines. Both minor and major garbage collections are done serially (using a single virtual CPU).

> -XX:+UseSerialGC

- The **Parallel GC** uses multiple threads to perform GC. It can use multiple CPUs to speed up application throughput. You can specify threads number: -XX:ParallelGCThreads=<desired number>

> -XX:+UseParallelGC // A multi-thread young generation collector
> -XX:+UseParallelOldGC // Both a multi-thread young and old generation collector

- The **G1 Collector** to replace the CMS Collector. The G1 collector is a parallel, concurrent, and incrementally compacting low-pause garbage collector.

> -XX:+UseG1GC

With Java 8, the new feature -XX:+UseStringDeduplication can take advantage of the facts that the char arrays are internal to strings and final, so JVM can mess around with them.

Whenever the garbage collector visits String objects it takes note of the char arrays. It takes their hash value and store it alongside with a weak reference to the array. As soon as it finds another String which has the same hash code it compares them char by char.

If they match as well, one String will be modified and point to the char array of the second String. The first char array then is no longer referenced anymore and can be garbage collected.

**Monitor JVM Activity**

The Visual VM program is included with the JDK and allows developers to monitor various aspects of a running JVM.

The Visual GC plugin for Visual VM provides a graphical representation of the garbage collectors activity in the JVM.

![Java Visual GC](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/visualvm/Java2Demo03.png)

### Types of References

**Strong References**

We use daily while writing the code. Any object in the memory which has active strong reference is not eligible for garbage collection. Unless you explicitly make reference pointing to null.

**Soft References**

The objects which are softly referenced will not be garbaged (even though they are available for garbage collection) until JVM badly needs memory.

That implies even if you make the strong reference pointing to null. The object could be still in memory and you can use soft reference to retrieve it back before cleaned.

Good for Caching implementation.

**Weak References**

JVM ignores the weak references. They are likely to be garbage collected when JVM runs garbage collector thread.

**Phantom References**

The objects which are being referenced by phantom references are eligible for garbage collection. But, before removing them from the memory, JVM puts them in a queue called ‘reference queue’.

Calling get() method on phantom reference always returns null.

### JavaFX

JavaFX is a software platform for creating and delivering desktop applications, as well as rich internet applications (RIAs) that can run across a wide variety of devices. JavaFX is intended to replace Swing as the standard GUI library for Java SE. JavaFX has support for desktop computers and web browsers on Microsoft Windows, Linux, and macOS.

## Java 8 Features

### Java Interface Changes

**Allow to add default and static methods in Interface.**

One of the major reason for introducing default methods in interfaces is to enhance the Collections API in Java 8 to support lambda expressions.

Liked forEach method:

```java
default void forEach(Consumer<? super T> action) {
      Objects.requireNonNull(action);
      for (T t : this) {
          action.accept(t);
      }
}
```

Java interface static method is similar to default method except that we can’t override them in the implementation classes.

### Java Lambda Expression

An interface with exactly one abstract method is known as **Functional Interface**. It enables us to use lambda expressions to instantiate them.

```java
public static int count(List<Integer> numList, Predicate<Integer> predicate) {
  int sum = 0;
  for (int number : numList) {
    if (predicate.test(number))
      sum++;
  }
  return sum;
}

public static void main(String[] args) {
  List<Integer> numList = new ArrayList<>();

  numList.add(new Integer(10));
  numList.add(new Integer(20));
  numList.add(new Integer(30));
  numList.add(new Integer(40));
  numList.add(new Integer(50));

  // try 4 different predicates (functional interfaces)
  assert count(numList, n -> true) == 5;
  assert count(numList, n -> false) == 0;
  assert count(numList, n -> n < 25) == 2;
  assert count(numList, n -> n % 3 == 0) == 1;
}
```

- A lambda expression can be understood as a kind of anonymous function: it doesn’t have a name, but it has a list of parameters, a body, a return type, and also possibly a list of exceptions that can be thrown.
- Lambda expressions let you pass code concisely.
- A functional interface is an interface that declares exactly one abstract method.
- Lambda expressions can be used only where a functional interface is expected.
- Lambda expressions let you provide the implementation of the abstract method of a functional interface directly inline and treat the whole expression as an instance of a functional interface.
- Java 8 comes with a list of common functional interfaces in the java.util .function package, which includes Predicate<T>, Function<T, R>, Supplier<T>, Consumer<T>, and BinaryOperator<T>, described in table 3.2.
- There are primitive specializations of common generic functional interfaces such as Predicate<T> and Function<T, R> that can be used to avoid boxing operations: IntPredicate, IntToLongFunction, and so on.
- The execute around pattern (that is, you need to execute a bit of behavior in the middle of code that’s always required in a method, for example, resource allocation and cleanup) can be used with lambdas to gain additional flexibility and reusability.
- The type expected for a lambda expression is called the target type.
- Method references let you reuse an existing method implementation and pass it around directly.
- Functional interfaces such as Comparator, Predicate, and Function have several default methods that can be used to combine lambda expressions.

```java
/**
 * Behavior parameterization is the ability for a method to take multiple different behaviors as
 * parameters and use them internally to accomplish different behaviors.
 */

// Executing a block of code with Runnable
public void threadABlockOfCode() {
		Thread thread = new Thread(() -> {
				System.out.println("Hello World!");
		});
		thread.run();
}

// Methods and lambdas as first-class citizens
public void filterAllHiddenFiles() {
		File[] hiddenFiles = new File(".").listFiles(File::isHidden);
		// Arrays.stream(hiddenFiles).forEach((File file) -> System.out.println(file.getPath()));
		Arrays.stream(hiddenFiles).forEach(System.out::println);
}

/**
 * You can use a lambda expression in the context of a functional interface. A functional
 * interface is an interface that specifies exactly one abstract method. But they can have
 * multiple default methods. Also these interfaces will be annotated as
 * {@code @FunctionalInterface}
 *
 * <br>
 *
 * {@code java.util.Comparator} <br>
 * {@code java.lang.Runnable} <br>
 * {@code java.util.function.Consumer} <br>
 * {@code java.util.function.Function} <br>
 * {@code java.util.function.Predicate} <br>
 * {@code java.awt.event.ActionListener} <br>
 * {@code java.util.concurrent.Callable} <br>
 * {@code java.security.PriviledgedAction}
 */
public void useLambdaFunctions() {
		Stream<String> stream = Stream.of("Java 8", "Lambdas", "In", "Action");
		stream.map(String::toUpperCase).forEach(System.out::println);
}

/**
 * Boxed values are essentially a wrapper around primitive types and are stored on the heap.
 * Therefore, boxed values use more memory and require additional memory lookups to fetch the
 * wrapped primitive value.
 *
 * Java8 defines a list of named functional interfaces those use appropriate primitive types
 * directly!
 *
 */
public void methodComparation() {
		List<String> str = Arrays.asList("a", "b", "A", "B");
		str.sort((a, b) -> a.compareToIgnoreCase(b));
		System.out.println(str);
		str = Arrays.asList("a", "b", "A", "B");
		str.sort(String::compareToIgnoreCase);
		System.out.println(str);
}

public void appleSamples() {
		// filtering with lambdas
		List<Apple> inventory = Arrays.asList(new Apple(80, "green"), new Apple(155, "green"), new Apple(120, "red"));
		inventory.stream().filter(a -> "green".equals(a.getColor())).forEach(System.out::println);

		inventory.sort(new Comparator<Apple>() {
				@Override
				public int compare(Apple o1, Apple o2) {
						return o1.getWeight().compareTo(o2.getWeight());
				}
		});

		// Functional Interface
		inventory.sort((a, b) -> (a.getWeight().compareTo(b.getWeight())));
		System.out.println(inventory);

		// Static Assistant Method
		inventory.sort(Comparator.comparing(a -> a.getWeight()));
		System.out.println(inventory);

		// Method Reference
		inventory.sort(Comparator.comparing(Apple::getWeight).reversed().thenComparing(Apple::getColor));
		System.out.println(inventory);
}

public void andThenCompose() {
		Function<Integer, Integer> f = x -> x + 1;
		Function<Integer, Integer> g = x -> x * 2;
		Function<Integer, Integer> h = f.andThen(g);
		System.out.println(f.andThen(g).apply(1));
		System.out.println(f.compose(g).apply(1));
		System.out.println(f.andThen(g).apply(3)); // (3 + 1) * 2 = 8
		System.out.println(f.compose(g).apply(3)); // (3 * 2) + 1 = 7
}

public void transformLetters() {
		Function<String, String> addHeader = Letter::addHeader;
		addHeader.andThen(Letter::checkSpelling).andThen(Letter::addFooter);

}

public static class Letter {
		public static String addHeader(String text) {
				return "From Raoul, Mario and Alan: " + text;
		}

		public static String addFooter(String text) {
				return text + " Kind regards";
		}

		public static String checkSpelling(String text) {
				return text.replaceAll("labda", "lambda");
		}
}
```


### Java Stream API

Especially helpful for Bulk Data Operations on Collections.

A new java.util.stream has been added in Java 8 to perform filter/map/reduce like operations with the collection. Stream API will allow sequential as well as parallel execution.

- The Streams API lets you express complex data processing queries.
- You can filter and slice a stream using the filter, distinct, skip, and limit methods.
- You can extract or transform elements of a stream using the map and flatMap methods.
- You can find elements in a stream using the findFirst and findAny methods. You can match a given predicate in a stream using the allMatch, noneMatch, and anyMatch methods.
- These methods make use of short-circuiting: a computation stops as soon as a result is found; there’s no need to process the whole stream.
- You can combine all elements of a stream iteratively to produce a result using the reduce method, for example, to calculate the sum or find the maximum of a stream.
- Some operations such as filter and map are stateless; they don’t store any state. Some operations such as reduce store state to calculate a value. Some operations such as sorted and distinct also store state because they need to buffer all the elements of a stream before returning a new stream. Such operations are called stateful operations.
- There are three primitive specializations of streams: IntStream, DoubleStream, and LongStream. Their operations are also specialized accordingly.
- Streams can be created not only from a collection but also from values, arrays, files, and specific methods such as iterate and generate.
- An infinite stream is a stream that has no fixed size.

```java
public class Java8Stream {
	public static void main(String... args) {
		Trader raoul = new Trader("Raoul", "Cambridge");
		Trader mario = new Trader("Mario", "Milan");
		Trader alan = new Trader("Alan", "Cambridge");
		Trader brian = new Trader("Brian", "Cambridge");

		List<Transaction> transactions = Arrays.asList(new Transaction(brian, 2011, 300), new Transaction(raoul, 2012, 1000),
				new Transaction(raoul, 2011, 400), new Transaction(mario, 2012, 710), new Transaction(mario, 2012, 700),
				new Transaction(alan, 2012, 950));

		// Query 1: Find all transactions from year 2011 and sort them by value (small to high).
		List<Transaction> tr2011 = transactions.stream()
				.filter(transaction -> transaction.getYear() == 2011)
				.sorted(comparingInt(Transaction::getValue))
				.collect(toList());
		System.out.println(tr2011);

		// Query 2: What are all the unique cities where the traders work?
		List<String> cities = transactions.stream()
				.map(transaction -> transaction.getTrader()
						.getCity())
				.distinct()
				.collect(toList());
		System.out.println(cities);

		// Query 3: Find all traders from Cambridge and sort them by name.
		List<Trader> traders = transactions.stream()
				.map(Transaction::getTrader)
				.filter(trader -> trader.getCity()
						.equals("Cambridge"))
				.distinct()
				.sorted(comparing(Trader::getName))
				.collect(toList());
		System.out.println(traders);

		// Query 4: Return a string of all traders’ names sorted alphabetically.
		String traderStr = transactions.stream()
				.map(transaction -> transaction.getTrader()
						.getName())
				.distinct()
				.sorted()
				.collect(joining(", "));
		System.out.println(traderStr);

		// Query 5: Are there any trader based in Milan?
		boolean milanBased = transactions.stream()
				.anyMatch(transaction -> transaction.getTrader()
						.getCity()
						.equals("Milan"));
		System.out.println(milanBased);

		// Query 6: Update all transactions so that the traders from Milan are set to Cambridge.
		transactions.stream()
				.map(Transaction::getTrader)
				.filter(trader -> trader.getCity()
						.equals("Milan"))
				.forEach(trader -> trader.setCity("Cambridge"));
		System.out.println(transactions);

		// Query 7: What's the highest value in all the transactions?
		int highestValue = transactions.stream()
				.map(Transaction::getValue)
				.reduce(0, Integer::max);
		System.out.println(highestValue);

		String[] arrayOfWords = { "Hello", "World" };
		List<String> result = Arrays.stream(arrayOfWords)
				.map(w -> w.split(""))
				.flatMap(Arrays::stream) // flat all arrays to a single stream!
				.distinct()
				.collect(toList());
		System.out.println(result);
	}

	static class Trader {
		private String name;
		private String city;

		public Trader(String n, String c) {
			this.name = n;
			this.city = c;
		}

		public String getName() {
			return this.name;
		}

		public String getCity() {
			return this.city;
		}

		public void setCity(String newCity) {
			this.city = newCity;
		}

		public String toString() {
			return "Trader:" + this.name + " in " + this.city;
		}
	}

	static class Transaction {
		private Trader trader;
		private int year;
		private int value;

		public Transaction(Trader trader, int year, int value) {
			this.trader = trader;
			this.year = year;
			this.value = value;
		}

		public Trader getTrader() {
			return this.trader;
		}

		public int getYear() {
			return this.year;
		}

		public int getValue() {
			return this.value;
		}

		public String toString() {
			return "{" + this.trader + ", " + "year: " + this.year + ", " + "value:" + this.value + "}";
		}
	}
}
```

### Java Collector API

- collect is a terminal operation that takes as argument various recipes (called collectors) for accumulating the elements of a stream into a summary result.
- Predefined collectors include reducing and summarizing stream elements into a single value, such as calculating the minimum, maximum, or average.
- Predefined collectors let you group elements of a stream with groupingBy and partition elements of a stream with partitioningBy.
- Collectors compose effectively to create multilevel groupings, partitions, and reductions.
- You can develop your own collectors by implementing the methods defined in the Collector interface.

```java
private static Map<Dish.Type, List<Dish>> groupDishesByType() {
	return menu.stream()
			.collect(groupingBy(Dish::getType));
}

private static Map<Dish.Type, List<String>> groupDishNamesByType() {
	return menu.stream()
			.collect(groupingBy(Dish::getType, mapping(Dish::getName, toList())));
}

private static Map<Dish.Type, Set<String>> groupDishTagsByType() {
	return menu.stream()
			.collect(groupingBy(Dish::getType, mapping(Dish::getName, toSet())));
}

private static Map<Dish.Type, List<Dish>> groupCaloricDishesByType() {
	return menu.stream()
			.filter(dish -> dish.getCalories() > 500)
			.collect(groupingBy(Dish::getType));
}

private static Map<CaloricLevel, List<Dish>> groupDishesByCaloricLevel() {
	return menu.stream()
			.collect(groupingBy(dish -> {
				if (dish.getCalories() <= 400)
					return CaloricLevel.DIET;
				else if (dish.getCalories() <= 700)
					return CaloricLevel.NORMAL;
				else
					return CaloricLevel.FAT;
			}));
}

private static Map<Dish.Type, Map<CaloricLevel, List<Dish>>> groupDishedByTypeAndCaloricLevel() {
	return menu.stream()
			.collect(groupingBy(Dish::getType, groupingBy((Dish dish) -> {
				if (dish.getCalories() <= 400)
					return CaloricLevel.DIET;
				else if (dish.getCalories() <= 700)
					return CaloricLevel.NORMAL;
				else
					return CaloricLevel.FAT;
			})));
}

private static Map<Dish.Type, Long> countDishesInGroups() {
	return menu.stream()
			.collect(groupingBy(Dish::getType, counting()));
}

private static Map<Dish.Type, Optional<Dish>> mostCaloricDishesByType() {
	return menu.stream()
			.collect(groupingBy(Dish::getType, reducing((Dish d1, Dish d2) -> d1.getCalories() > d2.getCalories() ? d1 : d2)));
}

private static Map<Dish.Type, Dish> mostCaloricDishesByTypeWithoutOprionals() {
	return menu.stream()
			.collect(groupingBy(Dish::getType,
					collectingAndThen(reducing((d1, d2) -> d1.getCalories() > d2.getCalories() ? d1 : d2), Optional::get)));
}

private static Map<Dish.Type, Integer> sumCaloriesByType() {
	return menu.stream()
			.collect(groupingBy(Dish::getType, summingInt(Dish::getCalories)));
}

private static Map<Dish.Type, Set<CaloricLevel>> caloricLevelsByType() {
	return menu.stream()
			.collect(groupingBy(Dish::getType, mapping(dish -> {
				if (dish.getCalories() <= 400)
					return CaloricLevel.DIET;
				else if (dish.getCalories() <= 700)
					return CaloricLevel.NORMAL;
				else
					return CaloricLevel.FAT;
			}, toSet())));
}

private static Map<Boolean, List<Dish>> partitionByVegeterian() {
	return menu.stream()
			.collect(partitioningBy(Dish::isVegetarian));
}

private static Map<Boolean, Map<Dish.Type, List<Dish>>> vegetarianDishesByType() {
	return menu.stream()
			.collect(partitioningBy(Dish::isVegetarian, groupingBy(Dish::getType)));
}

private static Object mostCaloricPartitionedByVegetarian() {
	return menu.stream()
			.collect(partitioningBy(Dish::isVegetarian, collectingAndThen(maxBy(comparingInt(Dish::getCalories)), Optional::get)));
}

private static int calculateTotalCaloriesUsingSum() {
	return menu.stream()
			.mapToInt(Dish::getCalories)
			.sum();
}

private static long howManyDishes() {
	return menu.stream()
			.collect(counting());
}

private static Dish findMostCaloricDish() {
	return menu.stream()
			.collect(reducing((d1, d2) -> d1.getCalories() > d2.getCalories() ? d1 : d2))
			.get();
}

private static Dish findMostCaloricDishUsingComparator() {
	Comparator<Dish> dishCaloriesComparator = Comparator.comparingInt(Dish::getCalories);
	BinaryOperator<Dish> moreCaloricOf = BinaryOperator.maxBy(dishCaloriesComparator);
	return menu.stream()
			.collect(reducing(moreCaloricOf))
			.get();
}

private static int calculateTotalCalories() {
	return menu.stream()
			.collect(summingInt(Dish::getCalories));
}

private static Double calculateAverageCalories() {
	return menu.stream()
			.collect(averagingInt(Dish::getCalories));
}

private static IntSummaryStatistics calculateMenuStatistics() {
	return menu.stream()
			.collect(summarizingInt(Dish::getCalories));
}

private static String getShortMenu() {
	return menu.stream()
			.map(Dish::getName)
			.collect(joining());
}

private static String getShortMenuCommaSeparated() {
	return menu.stream()
			.map(Dish::getName)
			.collect(joining(", "));
}
```

### Java Parallel Stream

- Stream sources and decomposability: Excellent (ArrayList, IntStream.range); Good (HashSet, TreeSet); Poor (LinkedList, Stream.iterate).
- The fork/join framework was designed to recursively split a parallelizable task into smaller tasks and then combine the results of each subtask to produce the overall result.
- Internal iteration allows you to process a stream in parallel without the need to explicitly use and coordinate different threads in your code.
- Even if processing a stream in parallel is so easy, there’s no guarantee that doing so will make your programs run faster under all circumstances.
- Behavior and performance of parallel software can sometimes be counterintuitive, and for this reason it’s always necessary to measure them and be sure that you’re not actually slowing your programs down.
- Parallel execution of an operation on a set of data, as done by a parallel stream, can provide a performance boost, especially when the number of elements to be processed is huge or the processing of each single element is particularly time consuming.
- From a performance point of view, using the right data structure, for instance, employing primitive streams instead of nonspecialized ones whenever possible, is almost always more important than trying to parallelize some operations.
- The fork/join framework lets you recursively split a parallelizable task into smaller tasks, execute them on different threads, and then combine the results of each subtask in order to produce the overall result.
- Spliterators define how a parallel stream can split the data it traverses.

**Work Stealing**

The tasks are more or less evenly divided on all the threads in the ForkJoinPool. Each of these threads holds a doubly linked queue of the tasks assigned to it, and as soon as it completes a task it pulls another one from the head of the queue and starts executing it. For the reasons we listed previously, one thread might complete all the tasks assigned to it much faster than the others, which means its queue will become empty while the other threads are still pretty busy. In this case, instead of becoming idle, the thread randomly chooses a queue of a different thread and “steals” a task, taking it from the tail of the queue. This process continues until all the tasks are executed, and then all the queues become empty. That’s why having many smaller tasks, instead of only a few bigger ones, can help in better balancing the workload among the worker threads.

```java
public class Java8ParallelStream {
	private static final long N = 10_000_000L;
	private static final ForkJoinPool FORK_JOIN_POOL = new ForkJoinPool();

	public static <T, R> long measurePerf(Function<T, R> function, T input) {
		long fastest = Long.MAX_VALUE;
		for (int i = 0; i < 2; i++) {
			long start = System.nanoTime();
			R result = function.apply(input);
			long duration = (System.nanoTime() - start) / 1_000_000;
			System.out.println("Result: " + result);
			if (duration < fastest)
				fastest = duration;
		}
		return fastest;
	}

	static class ParallelStreams {
		public static long iterativeSum(long n) {
			long result = 0;
			for (long i = 0; i <= n; i++) {
				result += i;
			}
			return result;
		}

		public static long sequentailSum(long n) {
			return Stream.iterate(1L, i -> i + 1).limit(n).reduce(Long::sum).get();
		}

		public static long parallelSum(long n) {
			return Stream.iterate(1L, i -> i + 1).limit(n).parallel().reduce(Long::sum).get();
		}

		public static long rangedSum(long n) {
			return LongStream.rangeClosed(1, n).reduce(Long::sum).getAsLong();
		}

		public static long parallelRangedSum(long n) {
			return LongStream.rangeClosed(1, n).parallel().reduce(Long::sum).getAsLong();
		}

		public static long forkJoinSum(long n) {
			long[] numbers = LongStream.rangeClosed(1, n).toArray();
			ForkJoinTask<Long> task = new ForkJoinSumCalculator(numbers);
			return FORK_JOIN_POOL.invoke(task);
		}

		public static long sideEffectSum(long n) {
			Accumulator accumulator = new Accumulator();
			LongStream.rangeClosed(1, n).forEach(accumulator::add);
			return accumulator.getTotal();
		}

		public static long sideEffectParrallelSum(long n) {
			Accumulator accumulator = new Accumulator();
			LongStream.rangeClosed(1, n).parallel().forEach(accumulator::add);
			return accumulator.getTotal();
		}
	}

	static class ForkJoinSumCalculator extends RecursiveTask<Long> {
		private static final long serialVersionUID = -2754919233589478904L;

		private static final long THRESHOLD = 10_000;
		private final long[] numbers;
		private final int start, end;

		public ForkJoinSumCalculator(long[] numbers) {
			this(numbers, 0, numbers.length);
		}

		private ForkJoinSumCalculator(long[] numbers, int start, int end) {
			this.numbers = numbers;
			this.start = start;
			this.end = end;
		}

		@Override
		protected Long compute() {
			int length = end - start;
			if (length <= THRESHOLD) {
				return computeSequentially();
			}
			ForkJoinSumCalculator leftTask = new ForkJoinSumCalculator(numbers, start, start + length / 2);
			leftTask.fork();
			ForkJoinSumCalculator rightTask = new ForkJoinSumCalculator(numbers, start + length / 2, end);
			Long rightResult = rightTask.compute();
			Long leftResult = leftTask.join();
			return leftResult + rightResult;
		}

		private long computeSequentially() {
			long sum = 0;
			for (int i = start; i < end; i++) {
				sum += numbers[i];
			}
			return sum;
		}
	}

	static class Accumulator {
		private long total = 0;

		public void add(long value) {
			total += value;
		}

		public long getTotal() {
			return total;
		}
	}

	public static void main(String[] args) {
		System.out.println("Iterative Sum done in: " + measurePerf(ParallelStreams::iterativeSum, N) + " msecs");
		System.out.println("Sequential Sum done in: " + measurePerf(ParallelStreams::sequentailSum, N) + " msecs");
		System.out.println("Parallel forkJoinSum done in: " + measurePerf(ParallelStreams::parallelSum, N) + " msecs");
		System.out.println("Range forkJoinSum done in: " + measurePerf(ParallelStreams::rangedSum, N) + " msecs");
		System.out.println("Parallel range forkJoinSum done in: " + measurePerf(ParallelStreams::parallelRangedSum, N) + " msecs");
		System.out.println("ForkJoin sum done in: " + measurePerf(ParallelStreams::forkJoinSum, N) + " msecs");
		System.out.println("SideEffect sum done in: " + measurePerf(ParallelStreams::sideEffectSum, N) + " msecs");
		System.out.println("SideEffect parallel sum done in: " + measurePerf(ParallelStreams::sideEffectParrallelSum, N) + " msecs");
	}
}
```

### Java Default Methods

- Interfaces in Java 8 can have implementation code through default methods and static methods.
- Default methods start with a default keyword and contain a body like class methods do.
- Adding an abstract method to a published interface is a source incompatibility.
- Default methods help library designers evolve APIs in a backward-compatible way.
- Default methods can be used for creating optional methods and multiple inheritance of behavior.
- There are resolution rules to resolve conflicts when a class inherits from several default methods with the same signature.
- A method declaration in the class or a superclass takes priority over any default method declaration. Otherwise, the method with the same signature in the most specific default-providing interface is selected.
- When two methods are equally specific, a class can explicitly override a method and select which one to call.

### Java Optional vs. null

- Java 8 introduces the class java.util.Optional<T> to model the presence or absence of a value.
- You can create Optional objects with the static factory methods Optional.empty, Optional.of, and Optional.ofNullable.
- The Optional class supports many methods such as map, flatMap, and filter, which are conceptually similar to the methods of a stream.
- Using Optional forces you to actively unwrap an optional to deal with the absence of a value; as a result, you protect your code against unintended null pointer exceptions.
- Using Optional can help you design better APIs in which, just by reading the signature of a method, users can tell whether to expect an optional value.

```java
public int readDuration(Properties props, String name) {
	return Optional.ofNullable(props.getProperty(name))
			.flatMap(OptionalUtility::stringToInt)
			.filter(i -> i > 0)
			.orElse(0);
}
```

### Java CompletableFuture



### Java 8 Refactor/Test/Debug

- Lambda expressions can make your code more readable and flexible.
- Consider converting anonymous classes to lambda expressions, but be wary of subtle semantic differences such as the meaning of the keyword this and shadowing of variables.
- Method references can make your code more readable compared to lambda expressions.
- Consider converting iterative collection processing to use the Streams API.
- Lambda expressions can help remove boilerplate code associated with several object-oriented design patterns such as strategy, template method, observer, chain of responsibility, and factory.
- Lambda expressions can be unit tested, but in general you should focus on testing the behavior of the methods where the lambda expressions appear.
- Consider extracting complex lambda expressions into regular methods.
- Lambda expressions can make stack traces less readable.
- The peek method of a stream is useful to log intermediate values as they flow past at certain points in a stream pipeline.

```java
public class Java8Refactor {
	private Logger logger = Logger.getAnonymousLogger();

	interface Task {
		public void execute();
	}

	public static void doSomething(Runnable r) {
		r.run();
	}

	public static void doSomething(Task t) {
		t.execute();
	}

	// Lambda can help to defer the construction of message
	public void log(Level level, Supplier<String> msgSupplier) {
		if (logger.isLoggable(level)) {
			logger.log(level, msgSupplier.get());
		}
	}

	// Execute around
	public static String processFile(BufferedReaderProcessor p) throws IOException {
		try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
			return p.process(br);
		}
	}

	@FunctionalInterface
	interface BufferedReaderProcessor {
		String process(BufferedReader b) throws IOException;
	}

	// Strategy Pattern
	@FunctionalInterface
	public interface ValidationStrategy {
		boolean execute(String s);
	}

	public static class Validator {
		private final ValidationStrategy strategy;

		public Validator(ValidationStrategy v) {
			this.strategy = v;
		}

		public boolean validate(String s) {
			return strategy.execute(s);
		}
	}

	public static void debugWithPeekLog() {
		List<Integer> numbers = Arrays.asList(2, 3, 4, 5, 6, 7, 8, 9);
		List<Integer> result =
				  numbers.stream()
				         .peek(x -> System.out.println("from stream: " + x))
				         .map(x -> x + 17)
				         .peek(x -> System.out.println("after map: " + x))
				         .filter(x -> x % 2 == 0)
				         .peek(x -> System.out.println("after filter: " + x))
				         .limit(3)
				         .peek(x -> System.out.println("after limit: " + x))
				         .collect(Collectors.toList());
		System.out.println(result);
	}

	public static void main(String[] args) throws Exception {

		// anonymous class
		doSomething(new Task() {
			@Override
			public void execute() {
				System.out.println("Danger danger!!");
			}
		});
		// lambda expression
		doSomething((Task) () -> System.out.println("Danger danger!!"));
		// execute around
		// processFile(b -> b.readLine());
		// strategy pattern
		Validator validator = new Validator(s -> s.matches("[a-z]+"));
		System.out.println(validator.validate("aaaaa"));
		// chain of responsiblity
		// Chain of responsibility
		UnaryOperator<String> headerProcessing = (String text) -> "From Raoul, Mario and Alan: " + text;
		UnaryOperator<String> spellCheckerProcessing = (String text) -> text.replaceAll("labda", "lambda");
		Function<String, String> pipeline = headerProcessing.andThen(spellCheckerProcessing);
		System.out.println(pipeline.apply("Aren't labdas really sexy?!!"));
		debugWithPeekLog();
	}
}
```


### Java Time API


## Java Application

### Memory Leak

**How to Detect Java Memory Leaks?**

- Use Eclipse memory leak warnings. or plugin Memory Analyzer.
- Java Visual VM or jconsole, connect with JMX, provides memory and CPU profiling, heap dump analysis, memory leak detection, access to MBeans, and garbage collection.
- Java Profilers, help you monitor different JVM parameters: object creation, thread execution, method execution and garbage collection.
- Using heap dumps, allow you to see the number of instances open and how much space these instances take up.
- If your app just crashes without returning an OutOfMemoryError message, you can check the fatal log error or the crash dump to see what went wrong.
- JVM settings: enable verbose garbage collection.
> $JAVA_OPTS=“-DLauncher=PRIME -DinstanceID=1 -Xms1024M -Xmx23552M -XX:+UseLargePages -XX:PermSize=256M -XX:MaxPermSize=1286M -XX:+PrintGCDateStamps -verbose:gc -XX:+PrintGCDetails -Xloggc:\“gclog.txt\” -DloggerImplName=PclnLogger -Dcom.sun.management.jmxremote.port=4321 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false”;

**How to Avoid Java Memory Leaks?**

1. Use reference objects to avoid memory leaks.
2. Avoid memory leaks related to a WebApp classloader. (static fields)
3. Other specific steps.
  - Release resources and sessions when it's no longer need.
  - Use StringBuffer. A large number of temporary objects will slow down performance.
  - Use PreparedStatement object and parameterized sql.
  - Close stream, connection, statement in the finally block.
4. Optimize your code to bath processing data.
5. Even if you specify System.gc(), the garbage collector might not run until the memory runs low.

**What to do with Memory Leaks?**

- Java heap space: Means that memory resources could not be allocated for a particular object in the Java heap. This can mean several things, including a memory leak, or the specified heap size is lower than what the application needs. It could also mean that your program is using a lot of finalizers.
- PermGen space: This means that the permanent generation area is already full. This area is where the method and class objects are stored. You can easily correct this by increasing the space via -XX:MaxPermSize. (Removed from Java 8)
- Requested array size exceeds VM limit: This means that the program is trying to assign an array that is > than the heap size. You might need to optimize your code and use batch or pagination.
- Request \<size\> bytes for \<reason\>. Out of swap space?: This means that an allocation using the local heap did not succeed, or the native heap is close to being all used up.
- \<Reason\> \<stack trace\> (Native method): This means that a native method was not allocated the required memory.

# Reference Resources
- [Source Code on GitHub](https://github.com/codebycase/algorithms-java/tree/master/src/main/java/a014_java_world_topics)
- [Java Garbage Collection Basics](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)
