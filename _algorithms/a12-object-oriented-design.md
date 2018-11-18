---
layout: article
title: Algorithms - Object-Oriented Design
key: a12-object-oriented-design
cover: /assets/images/articles/computer-science.png
categories: Algorithms
tags: Design
sharing: true
aside:
  toc: true
show_edit_on_github: true
pageview: true
---

# Object-Oriented Design

- A class is an encapsulation of data and methods that operate on that data, which reduces the conceptual burden of writing code, and enable code reuse, through the use of inheritance and polymorphism.

- A design pattern is a general repeatable solution to a commonly occurring problem, or it is a description of how to solve a problem that arises in many different situations.

<!--more-->

### Template Method vs. Strategy

Both the template method and strategy are behavioral patterns, and used to make algorithm reusable.

However, they differ in the following key way:

- In the template method, a skeleton algorithm is provided in superclass. Subclasses can override methods to specialize the algorithm.

- The strategy pattern is typically applied when a family of algorithms implements a common interface. These algorithms can then be selected by clients.

- In the template method pattern, the superclass algorithm may have "hooks" -- calls to placeholder methods that can be overridden by subclasses to provide additional functionality. Sometimes a hook is not implemented, thereby forcing the subclasses to implement; some times it offers a "no-operation" or some baseline functionality. _There is no analog to a hook in a strategy pattern._

As a concrete example, consider a sorting algorithm like quicksort, which is a good example of a template method: subclasses can implement their own pivot selection and partitioning method. But there may be multiple ways to sort elements, thereof, it's natural to pass quicksort an object that implements a compare method (comparable interface). These objects constitute an example of the strategy pattern.

### Observer (Listener) Pattern

- The observer pattern defines a one-to-many dependency between objects so that when one object changes state all its dependents are notified and updated automatically.

- The observed object must implement the methods: Register an observer, Remove an observer, Notify all observers; The observer object must implement the method: Update the observer.

- As a concrete example, consider a service keeps track of the top 10 most visited pages. There may be multiple client applications that use this information. Or start/shutdown application listeners.

### Singleton and Flyweight

Both keep a single copy of an object. But there are several key differences:

- Flyweights are used to save memory. Singletons are used to ensure all clients see the same object.

- A singleton is used where there is a single shared object, e.g., a database connection, server configurations, a logger, etc. A flyweight is used where there is a family of shared objects. e.g., objects describing character fonts, or nodes shared across multiple binary search trees.

- Flyweight objects are invariable immutable. Singleton objects are usually not immutable, e.g., request can be added to the database connection object.

- The singleton pattern is a creational pattern, whereas the flyweight is a structural pattern.

In summary, a singleton is like a global variable, whereas a flyweight is like a pointer to a canonical representation.

### Adapters

The adapter pattern allows the interface of an existing class to be used from another interface. It is often used to make existing classes work with others without modifying their source code.

There are two ways to build an adapter: via subclassing (the class adapter pattern) and composition (the object adapter pattern). In the class adapter pattern, the adapter inherits both the interface that is expected and the interface that is pre-existing. In the object adapter pattern, adapter contains an instance of the class if wraps and the adapter makes calls to the instance of the wrapped object.

As a concrete example of an object adapter, suppose we have legacy code that returns objects of type stack. Newer code expects inputs of type deque. We could create a new type, stack-adapter, which implements the deque methods, and has a field of type stack -- this is a referred to as object composition.

### Creational Patterns

- **Builder**, the builder pattern is to build a complex object in phases. It breaks down the construction process, and can give names to steps, which is using an mutable inner class that has a build method that returns the desired object. It deals far better with optional parameters and when the parameter list is very long.

- **Static Factory**, a static factory is a function for construction of objects. the function's name can make what it's doing much clearer compared to a call to a constructor. The function is not obliged to create a new object - in particular, it can return a flyweight, from the cache map. Integer.valueOf("123") is a good example, it caches values in the range [-128, 127] that are already exists, thereby reducing memory footprint and construction time.

- **Factory Method**, a factory method defines interface for creation an object, but lets subclasses decide which class to instantiate. A drawback of the factory method pattern is that it makes subclassing challenging.

```java
abstract protected Room makeRoom();
```

- **Abstract Factory**, a abstract factory provides an interface for creating families of related objects without specifying their concrete classes. For example, a class DocumentCreator could provide interfaces to create a number of documents, such as createLetter() and createResume(). Use this pattern makes it possible to interchange concrete implementations without changing the code that uses them, even at runtime.

### Libraries and Design Patterns

- Libraries provide the implementations of algorithms. In contrast, design patterns provide a higher level understanding or descriptions of how to structure classes and objects to solve specific types of problems.

- Another differences is that it's often necessary to use combinations of different patterns to solve a problem. e.g. MVC incorporates with Observer, Strategy, and Composite patterns.

- Many libraries take advantage of design patterns in their implementations: sorting and searching algorithms use the template method pattern, custom comparison functions illustrate the strategy pattern. typed-I/O shows off the decorator pattern, etc.

# Reference Resources
- [Source Code on GitHub](https://github.com/codebycase/algorithms-java/tree/master/src/main/java/a12_object_oriented_design)
