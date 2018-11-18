---
layout: article
title: Coding - Scala Programming Language
key: c05-scala-programming-language
categories: Coding
tags: Scala
---

# Scala Tutorial

Scala is a general-purpose programming language providing support for functional programming and a strong static type system. Designed to be concise, many of Scala's designs aimed to address the criticisms of Java.

- Scala is static type system (do type checking at compile-time), and also offer some form of type inference, the capability of the type system to deduce the type of a variable.
- Scala source code is intended to be compiled to Java bytecode, runs on JVM. Scala provides language interoperability with Java.
- Scala.js is a Scala compiler that complies to JavaScript, and making it possible to write Scala programs that run in web browsers.

<!--more-->

### Java vs. Scala Sample

```java
// Java:
public class Point {
  private final double x, y;

  public Point(final double x, final double y) {
    this.x = x;
    this.y = y;
  }

  public Point(
    final double x, final double y,
    final boolean addToGrid
  ) {
    this(x, y);

    if (addToGrid)
      grid.add(this);
  }

  public Point() {
    this(0.0, 0.0);
  }

  public double getX() {
    return x;
  }

  public double getY() {
    return y;
  }

  double distanceToPoint(final Point other) {
    return distanceBetweenPoints(x, y,
      other.x, other.y);
  }

  private static Grid grid = new Grid();

  static double distanceBetweenPoints(
      final double x1, final double y1,
      final double x2, final double y2
  ) {
    return Math.hypot(x1 - x2, y1 - y2);
  }
}
```

```scala
// Scala
class Point(
  val x: Double, val y: Double,
  addToGrid: Boolean = false) {
  import Point._

  if (addToGrid)
    grid.add(this)

  def this() = this(0.0, 0.0)

  def distanceToPoint(other: Point) =
    distanceBetweenPoints(x, y, other.x, other.y)
}

object Point {
  private val grid = new Grid()

  def distanceBetweenPoints(x1: Double, y1: Double,
                            x2: Double, y2: Double) = {
    math.hypot(x1 - x2, y1 - y2)
  }
}
```

### Syntactic Differences

- Scala does not require semicolons to end statements.
- Value types are capitalized: `Int, Double, Boolean` instead of `int, double, boolean`.
- Parameter and return types follow, as in Pascal, rather than precede as in C.
- Methods must be preceded by `def`.
- Local or class variables must be preceded by `val` (immutable variable) or `var` (mutable variable).
- The `return` operator is unnecessary in a function; the value of the last executed statement or expression is normally the function's value.
- Instead of the Java cast operator `(Type) foo`, Scala uses `foo.asInstanceOf[Type]`, or a specialized function such as `toDouble` or `toInt`.
- Instead of Java's `import foo.*`; Scala uses `import foo._`.
- Function or method `foo()` can also be called as just `foo`; method `thread.send(signo)` can also be called as just `thread send signo`; and method `foo.toString()` can also be called as just `foo toString`.
- Array references are written like function calls, e.g. `array(i)` rather than `array[i]`. (Internally in Scala, both arrays and functions are conceptualized as kinds of mathematical mappings from one object to another.)
- Generic types are written as e.g. `List[String]` rather than Java's `List<String>`.
- Instead of the pseudo-type `void`, Scala has the actual singleton class `Unit`.

### Conceptual Differences

- Scala has no static variables or methods. Instead, it has _singleton objects_ which are essentially classes with only one object in the class. It is common to place static variables and methods in a singleton object with the same name as the class name, which is then known as a companion object. (The underlying class for the singleton object has a `$` appended, Hence, for `class Foo` with companion object `object Foo`, under the hood there's a class `Foo$` containing the companion object's code, and one object of this class is created, using the singleton pattern).
- In place of constructor parameters, Scala has _class parameters_, which are placed on the class, similar to parameters to a function. When declared with a `val` or `var` modifier, fields are also defined with the same name, and automatically initialized from the class parameters. (Under the hood, external access to public fields always goes through accessor (getter) and mutator (setter) methods, which are automatically created. The accessor function has the same name as the field, which is why it's unnecessary in the above example to explicitly declare accessor methods.) Node that alternative constructors can also be declared, as in Java. Code that would go into the default constructor (other than initializing the member variables) goes directly at class level.
- Default visibility in Scala is public.

### Syntactic Flexibility

- Semicolons are unnecessary; lines are automatically joined if they begin or end with a token that cannot normally come in this position, or if they are unclosed parentheses or brackets.
- Any method can be used as an infix operator, e.g. `"%d apples".format(num)` and `"%d apples" format num` are equivalent. In fact, arithmetic operators like `+` and `<<` are treated just like any other methods, since function names are allowed to consist of sequences of arbitrary symbols (with a few exceptions made for things like parens, brackets and braces that must be handled specially); the only special treatment that such symbol-named methods undergo concerns the handling of precedence.
- Methods apply and update have syntactic short forms. `foo()` -- Where `foo` is a value (singleton object or class instance) -- is short for `foo.apply()`, and `foo() = 42` is short for `foo.update(42)`. Similarly, `foo(42)` is short for `foo.apply(42)`, and `foo(4) = 2` is short for `foo.update(4, 2)`. This is used for collection classes and extends to many other cases, such as STM cells.
- Scala distinguishes between no-parens (`def foo = 42`) and empty-parens (`def foo() = 42`) methods. When calling an empty-parens method, the parentheses may be omitted, which is useful when calling into Java libraries that do not know this distinction, e.g., using `foo.toString` instead of `foo.toString()`. By convention, a method should be defined with empty-parens when it performs side effects.
- Method names ending in colon (`:`) expect the argument on the left-hand-side and receiver on the right-hand-side. For example, the `4::2::Nil` is the same as `Nil.::(2).::(4)`, the first form corresponding visually to the result (a list with first element 4 and second element 2).
- Class body variables can transparently implemented as separate getter and setter methods. For `trait FooLike { var bar: Int }`, an implementation may be `object Foo extends FooLike { private var x = 0; def bar = x; def bar_=(value: Int) { x = value } }`. The call side will still be able to use a concise `foo.bar = 42`.
- The use of curly braces instead of parentheses is allowed in method calls. This allows pure library implementations of new control structures. For example, `breakable { ... if (...) break() ... }` looks as if `breakable` was a language defined keyword, but really is just a method taking a thunk argument. Methods that take thunks or functions often place these in a second parameter list, allowing to mix parentheses and curly braces syntax: `Vector.fill(4) { math.random }` is the same as `Vector.fill(4) ( math.random )`. The curly braces variant allows the expression to span multiple lines.
- For-expressions can accommodate any type that defines monadic methods such as `map`, `flatMap` and `filter`.
- *Unified type system*. In Scala, all types inherit from a top-level class Any, whose immediate children are `AnyVal` (value types, such as `Int` and `Boolean`) and `AnyRef` (reference types, as in Java). This means that the Java distinction between primitive types and boxed types (e.g. `int` vs. `Integer`) is not present in Scala; boxing and unboxing is completely transparent to the user.

### For-expressions

Scala has a much more powerful concept of for-expressions.

```java
val s = for (x <- 1 to 25 if x * x > 50) yield 2 * x
```
```
Vector(16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48, 50)
```

Below example shows expression `(mention, times) <- mentions` is an example of pattern matching. Iterating over a map returns a set of key-value tuples which can be easily destructured into separate variables for the key and value.

```java
// Given a map specifying Twitter users mentioned in a set of tweets,
// and number of times each user was mentioned, look up the users
// in a map of known politicians, and return a new map giving only the
// Democratic politicians (as objects, rather than strings).
val dem_mentions = for {
  (mention, times) <- mentions
  account <- accounts.get(mention)
  if account.party == "Decoratic"
} yield (account, times)
```

### Functional Tendencies

While supporting all of the object-oriented features available in Java, Scala also provides a large number of capabilities that are normally found only in functional programming languages.

- No distinction between statements and expressions

_All statements are in fact expressions that evaluate to some value. Return the type Unit or Nothing._

```java
// Java:
int hexDigit = x >= 10 ? x + 'A' - 10 : x + '0';
```
```js
// Scala:
val hexDigit = if (x >= 10) x + 'A' - 10 else x + '0'
```

All expressions are functions, even methods that return `Unit` are written with an equals sign.

```java
def printValue(x: String): Unit = {
  println("I ate a %s".format(x))
}
```
```js
def printValue(x: String) = println("I ate a %s" format x)
```

- Type inference

Due to type interface, the type of variables, function return values, and many other expressions can typically be omitted, as the compiler can deduce it. And certain types still need to be declared (most notably, function parameters, and the return types of recursive functions).

```java
def formatApples(x: Int) = "I ate %d apples".format(x)

def factorial(x: Int): Int =
  if (x == 0)
    1
  else
    x * factorial(x - 1)
```

- Anonymous functions with capturing semantics (i.e., closures)

In Scala, functions are objects, and a convenient syntax exists for specifying anonymous functions. An example is the expression `x => x < 2`. An even shorter form of anonymous function uses placeholder variables: `list map { x => sqrt(x) }` can be written more concisely as `list map { sqrt(_) }` or even list map sqrt.

- Immutable variables and objects

The immutable variants allow for very easy concurrency - no locks are needed as no shared objects are ever modified. Immutable structures are also constructed efficiently, in the sense that modified instances reuses most of old instance data and unused parts are collected by GC.

- Lazy (non-strict) evaluation

Scala evaluates expressions as soon as they are available, rather than as needed. However, it is possible to declare a variable non-strict ("lazy") with the `lazy` keyword, meaning that the code to produce the variable's value will not be evaluated until the first time the variable is referenced.

- Delimited continuations
- Higher-order functions
- Nested functions
- Currying
- Pattern matching

```java
def qsort(list: List[Int]): List[Int] = list match {
  case Nil => Nil // only matches the literal object Nil
  case pivot :: tail => // matches a non-empty lists
    val (smaller, rest) = tail.partition(_ < pivot)
    qsort(smaller) ::: pivot :: qsort(rest)
}
```

In the pattern-matching example above, the body of the `match` operator is a partial function, which consists of a series of `case` expressions. As below, we a ready-only _variable_ is declared whose type is a function from lists of integers to lists of integers, and bind it to a partial function.

```java
val qsort: List[Int] => List[Int] = {
  case Nil => Nil
  case pivot :: tail =>
    val (smaller, rest) = tail.partition(_ < pivot)
    qsort(smaller) ::: pivot :: qsort(rest)
}
```

- Algebraic data types (through case classes)
- Tuples

### Object-oriented Extensions

Scala is a pure object-oriented language in the sense that every value is an object. Data types and behaviors of objects are described by classes and traits.

Traits are Scala's replacement for Java's interfaces.

```js
abstract class Window {
  // abstract
  def draw()
}

class SimpleWindow extends Window {
  def draw() {
    println("in SimpleWindow")
    // draw a basic window
  }
}

trait WindowDecoration extends Window {}

trait HorizontalScrollbarDecoration extends WindowDecoration {
  // "abstract override" is needed here in order for "super()" to work because the parent
  // function is abstract. If it were concrete, regular "override" would be enough.
  abstract override def draw() {
    println("in HorizontalScrollbarDecoration");
    super.draw()
    // now draw a horizontal scroll bar
  }

  trait VerticalScrollbarDecoration extends WindowDecoration {
    abstract override def draw() {
      println("in VerticalScrollbarDecoration")
      super.draw()
      // now draw a vertical scrollbar
    }
  }

  trait TitleDecoration extends WindowDecoration {
    abstract override def draw() {
      println("in TitleDecoration");
      super.draw()
      // now draw the title bar
    }
  }

  // A variable may be declared thus:
  val mywin = new SimpleWindow with VerticalScrollbarDecoration with HorizontalScrollbarDecoration with TitleDecoration
}
```

The result of calling `mywin.draw()` is

```
in TitleDecoration
in HorizontalScrollbarDecoration
in VerticalScrollbarDecoration
in SimpleWindow
```

### Type Enrichment

This technique allows new methods to be added to an existing class using an add-on library such that only code the imports the add-on library gets the new functionality, and all other code is unaffected.

```js
object MyExtensions {
  implicit class IntPredicates(i: Int) {
    def isEven = i % 2 == 0
    def isOdd = !isEven
  }
}

import MyExtensions._ // bring implicit enrichment into scope
println(4.isEven) // -> true
```

### Concurrency

Scala standard library includes support for the actor model, in addition to the standard Java concurrency APIs. Akka is a seperate open source framework that provides actor-based concurrency.

Scala also comes with built-in support for data-parallel programming in the form of Parallel Collections.

```js
val urls = List("http://scala-lang.org", "https://github.com/scala/scala")

def fromURL(url: String) = scala.io.Source.fromURL(url).getLines().mkString("\n")

val t = System.currentTimeMillis()
urls.par.map(fromURL(_))
println("time: " + (System.currentTimeMillis - t) + "ms");
```

### Custer Computing

The most well-known open-source cluster computing solution written in Scala is **Apache Spark** and **Apache Kafka**.



# Reference Resources
- [Scala (programming language)](https://en.wikipedia.org/wiki/Scala_(programming_language))
