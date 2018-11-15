---
layout: article
title: Algorithms - Stacks and Queues
key: a04-stacks-and-queues
categories: Algorithms
tags: Stack, Queue
---

# Stacks & Queues

### Facts of Stack

- A stack uses LIFO (last-in first-out) ordering. That is, as in a stack of dinner plates, the most recent item added to the stack is the first item to be removed.
- It uses the operations: push(item), pop(), peek(), and isEmpty().
- Stacks are often useful in certain recursive algorithm. Sometimes you need to push temporary data onto a stack as you recurse, but then remove them as you backtrack.

<!--more-->

### Facts of Queue

- A queue implements FIFO (first-in first-out) ordering. As in a line or queue at a ticket stand, items are removed from the data structure in the same order that they are added.
- It uses the operations: offer(item)/add(item)/enqueue, poll()/remove()/dequeue,  peek(), and isEmpty().
- Queues are often used in breadth-first search or in implementing a cache. e.g. we used a queue to store a list of nodes that we need to process. Each time we process a node, we add its adjacent nodes to the back of the queue. This allows us to process nodes in the order in which they are viewed.


### Facts of Deque
- A *deque*, also called a double-ended queue, is a doubly linked list in which all insertions and deletions are from one of the two ends of the list.
- The preferred way to represent stacks or queues in Java is via the Deque interface. The **ArrayDeque** class is a resizable array that implements this interface, and provides O(1) amortized complexity.
- The methods offerLast("a"), pollFirst(), and peekFirst() are very similar to addLast("a"), removeFirst(), and getFirst(), but they are less prone to throwing exceptions.

## Stacks Boot Camp

### Design Min Stack

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

- push(x) -- Push element x onto stack.
- pop() -- Removes the element on top of the stack.
- top() -- Get the top element.
- getMin() -- Retrieve the minimum element in the stack.

Solution #1: Cache the previous min value alongside!

```java
public class MinStack {
	int min = Integer.MAX_VALUE;
	Deque<Integer> stack = new ArrayDeque<>();

	public void push(int x) {
		if (x <= min) {
			stack.push(min);
			min = x;
		}
		stack.push(x);
	}

	public void pop() {
		if (stack.pop() == min)
			min = stack.pop();
	}

	public int top() {
		return stack.peek();
	}

	public int getMin() {
		return min;
	}
}
```

Solution #2: Use Node to track min value.

```java
public class MinStack {
	Deque<Node> stack = new ArrayDeque<>();

	public void push(int x) {
		int min = stack.isEmpty() ? x : Math.min(x, stack.peek().min);
		stack.push(new Node(min, x));
	}

	public void pop() {
		stack.pop();
	}

	public int top() {
		return stack.peek().value;
	}

	public int getMin() {
		return stack.peek().min;
	}

	class Node {
		int min;
		int value;

		public Node(int min, int value) {
			this.min = min;
			this.value = value;
		}
	}
}
```

### Design Max Stack

Design a max stack that supports push, pop, top, peekMax and popMax.

push(x) -- Push element x onto stack.  
pop() -- Remove the element on top of the stack and return it.  
top() -- Get the element on the top.  
peekMax() -- Retrieve the maximum element in the stack.  
popMax() -- Retrieve the maximum element in the stack, and remove it. If you find more than one maximum elements, only remove the top-most one.  

```
Example 1:
MaxStack stack = new MaxStack();
stack.push(5);
stack.push(1);
stack.push(5);
stack.top(); -> 5
stack.popMax(); -> 5
stack.top(); -> 1
stack.peekMax(); -> 5
stack.pop(); -> 1
stack.top(); -> 5
```
_We can dramatically improve on the time complexity of popping by caching the maximum stored at or below that entry. When we pop, we evict the corresponding cached value._

_The time complexity is O(1). The additional space complexity is O(n). But by observing that if an element E being pushed is smaller than the maximum element already in the stack, then this E can never be the maximum, so we do not need to record it. We can just store the sequence of maximum values in a separate stack, to avoid the possibility of duplicates, we can record the number of occurrences of each maximum value, the space complexity is between O(1) and O(n), depends on how many unique max values._

```java
public class MaxStack {
	private Deque<Integer> elements;
	private Deque<MaxCount> maxCounts;

	public MaxStack() {
		elements = new ArrayDeque<>();
		maxCounts = new ArrayDeque<>();
	}

	public boolean empty() {
		return elements.isEmpty();
	}

	public int top() {
		return elements.peek() == null ? 0 : elements.peek();
	}

	public int peekMax() {
		return maxCounts.peek() == null ? 0 : maxCounts.peek().max;
	}

	public int pop() {
		if (elements.isEmpty())
			throw new IllegalStateException("pop(): empty stack");
		int x = elements.pop();
		if (x == maxCounts.peek().max) {
			maxCounts.peek().count--;
			if (maxCounts.peek().count == 0)
				maxCounts.pop();
		}
		return x;
	}

	public int popMax() {
		if (elements.isEmpty())
			throw new IllegalStateException("popMax(): empty stack");
		MaxCount maxCount = maxCounts.peek();
		// backtrack to closest max and also cache front elements
		List<Integer> cachedItems = new LinkedList<>();
		while (elements.peek() != maxCount.max) {
			cachedItems.add(0, elements.pop());
		}
		elements.pop();
		maxCount.count--;
		// remove it when count down to zero
		if (maxCount.count == 0)
			maxCounts.pop();
		// push back the cached front elements
		for (Integer t : cachedItems) {
			push(t);
		}
		return maxCount.max;
	}

	public void push(int x) {
		elements.push(x);
		if (!maxCounts.isEmpty()) {
			if (maxCounts.peek().max == x)
				maxCounts.peek().count++;
			else if (maxCounts.peek().max < x)
				maxCounts.push(new MaxCount(x, 1));
		} else {
			maxCounts.push(new MaxCount(x, 1));
		}
	}

	private class MaxCount {
		int max;
		int count;

		public MaxCount(int max, int count) {
			this.max = max;
			this.count = count;
		}
	}

	public static void main(String[] args) {
		MaxStack stack = new MaxStack();
		stack.push(5);
		stack.push(1);
		stack.push(5);
		assert stack.top() == 5;
		assert stack.popMax() == 5;
		assert stack.top() == 1;
		assert stack.peekMax() == 5;
		assert stack.popMax() == 5;
		assert stack.peekMax() == 1;
		assert stack.pop() == 1;
	}
}
```

### Daily Temperatures

Given a list of daily temperatures, produce a list that, for each day in the input, tells you how many days you would have to wait until a warmer temperature. If there is no future day for which this is possible, put 0 instead.

For example, given the list temperatures = [73, 74, 75, 71, 69, 72, 76, 73], your output should be [1, 1, 4, 2, 1, 1, 0, 0].

_Use stack to represent strictly increasing temperatures. Time complexity O(N), Space complexity O(W)._

```java
public int[] dailyTemperatures(int[] temperatures) {
	Deque<Integer> stack = new ArrayDeque<>();
	int[] ans = new int[temperatures.length];
	for (int i = temperatures.length - 1; i >= 0; i--) {
		while (!stack.isEmpty() && temperatures[i] >= temperatures[stack.peek()]) {
			stack.pop();
		}
		ans[i] = stack.isEmpty() ? 0 : stack.peek() - i;
		stack.push(i);
	}
	return ans;
}
```

### Flatten Nested List

Given a nested list of integers, implement an iterator to flatten it. Each element is either an integer, or a list whose elements may also be integers or other lists.
Example 1: Given the list [[1,1],2,[1,1]], should return: [1,1,2,1,1].
Example 2: Given the list [1,[4,[6]]], should return: [1,4,6].

_Since we need to access each NestedInteger at a time, we will use a stack to help. In the constructor, we push all the nestedList into the stack from back to front, so when we pop the stack, it returns the very first element. Second, in the hasNext() function, we peek the first element in stack currently, and if it is an Integer, we will return true and pop the element. If it is a list, we will further flatten it. This is iterative version of flatting the nested list. Again, we need to iterate from the back to front of the list._

```java
public class FlattenNestedListIterator implements Iterator<Integer> {
	private Stack<NestedInteger> stack = new Stack<>();

	public FlattenNestedListIterator(List<NestedInteger> nestedList) {
		for (int i = nestedList.size() - 1; i >= 0; i--) {
			stack.push(nestedList.get(i));
		}
	}

	@Override
	public Integer next() {
		return stack.pop().getInteger();
	}

	@Override
	public boolean hasNext() {
		while (!stack.isEmpty()) {
			if (stack.peek().isInteger())
				return true;
			List<NestedInteger> nestedList = stack.pop().getList();
			for (int i = nestedList.size() - 1; i >= 0; i--) {
				stack.push(nestedList.get(i));
			}
		}
		return false;
	}
}
```

### Nested List Weight Sum

Given a nested list of integers, return the sum of all integers in the list weighted by their depth.

Each element is either an integer, or a list -- whose elements may also be integers or other lists.

Example 1:

```
Input: [[1,1],2,[1,1]]
Output: 10
Explanation: Four 1's at depth 2, one 2 at depth 1.
```

```java
public int depthSum(List<NestedInteger> nestedList) {
	return depthSum(nestedList, 1);
}

public int depthSum(List<NestedInteger> list, int depth) {
	int sum = 0;
	for (NestedInteger n : list) {
		if (n.isInteger()) {
			sum += n.getInteger() * depth;
		} else {
			sum += depthSum(n.getList(), depth + 1);
		}
	}
	return sum;
}
```

Now if the weight is increasing from bottom up. i.e., the leaf level integers have weight 1, and the root level integers have the largest weight.

You can either do a brilliant the accumulate sum up, just standard BFS and memorize the depth.

realDepth = maxDepth + 1 - pseudoDepth.

```java
public int depthSumInverse(List<NestedInteger> nestedList) {
	int unweighted = 0, weighted = 0; // accumulate sum
	while (!nestedList.isEmpty()) {
		List<NestedInteger> nextLevel = new ArrayList<>();
		for (NestedInteger ni : nestedList) {
			if (ni.isInteger())
				unweighted += ni.getInteger();
			else
				nextLevel.addAll(ni.getList());
		}
		weighted += unweighted;
		nestedList = nextLevel;
	}
	return weighted;
}
```

### Test Valid Parentheses

Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

The brackets must close in the correct order, "()" and "()[]{}" are all valid but "(]" and "([)]" are not.

```java
public static boolean isValidParentheses(String s) {
  Stack<Character> stack = new Stack<Character>();
  for (char c : s.toCharArray()) {
    if (c == '(')
      stack.push(')');
    else if (c == '{')
      stack.push('}');
    else if (c == '[')
      stack.push(']');
    else if (stack.isEmpty() || stack.pop() != c)
      return false;
  }
  return stack.isEmpty();
}
```

### Remove Invalid Parentheses

Remove the minimum number of invalid parentheses in order to make the input string valid. Return all possible results.

Note: The input string may contain letters other than the parentheses ( and ).

Example: Input: "(a)())()", Output: ["(a)()()", "(a())()"]

Approach #1: Use BFS with visited checking.

```java
public List<String> removeInvalidParentheses(String s) {
	List<String> res = new ArrayList<>();
	if (s == null)
		return res;

	Set<String> visited = new HashSet<>();
	Queue<String> queue = new LinkedList<>();

	queue.add(s);
	visited.add(s);

	boolean found = false;
	while (!queue.isEmpty()) {
		s = queue.poll();

		if (isValid(s)) {
			// found an answer, add to the result
			res.add(s);
			found = true;
		}

		if (found)
			continue;

		// generate all possible states
		for (int i = 0; i < s.length(); i++) {
			// we only try to remove left or right paren
			if (s.charAt(i) != '(' && s.charAt(i) != ')')
				continue;
			String t = s.substring(0, i) + s.substring(i + 1);
			if (!visited.contains(t)) {
				// for each state, if it's not visited, add it to the queue
				queue.add(t);
				visited.add(t);
			}
		}
	}

	return res;
}

// helper function checks if string s contains valid parantheses
boolean isValid(String s) {
	int count = 0;
	for (int i = 0; i < s.length(); i++) {
		char c = s.charAt(i);
		if (c == '(')
			count++;
		if (c == ')' && count-- == 0)
			return false;
	}
	return count == 0;
}
```

Approach #2: Use DFS with backtracking, which is faster!

```java
public List<String> removeInvalidParentheses2(String s) {
	int rmL = 0, rmR = 0; // how many parentheses to remove to make it valid!
	for (int i = 0; i < s.length(); i++) {
		if (s.charAt(i) == '(') {
			rmL++;
		} else if (s.charAt(i) == ')') {
			if (rmL != 0) {
				rmL--;
			} else {
				rmR++;
			}
		}
	}
	Set<String> res = new HashSet<>();
	dfs(s, 0, res, new StringBuilder(), rmL, rmR, 0);
	return new ArrayList<String>(res);
}

public void dfs(String s, int i, Set<String> res, StringBuilder sb, int rmL, int rmR, int open) {
	if (rmL < 0 || rmR < 0 || open < 0) {
		return;
	}
	if (i == s.length()) {
		if (rmL == 0 && rmR == 0 && open == 0)
			res.add(sb.toString());
		return;
	}

	char c = s.charAt(i);
	int len = sb.length();

	if (c == '(') {
		dfs(s, i + 1, res, sb, rmL - 1, rmR, open); // not use (
		dfs(s, i + 1, res, sb.append(c), rmL, rmR, open + 1); // use (

	} else if (c == ')') {
		dfs(s, i + 1, res, sb, rmL, rmR - 1, open); // not use )
		dfs(s, i + 1, res, sb.append(c), rmL, rmR, open - 1); // use )

	} else {
		dfs(s, i + 1, res, sb.append(c), rmL, rmR, open);
	}

	sb.setLength(len); // backtracking
}
```


### Normalize Pathnames

A file or directory can be specified via a string called the pathname. This string may specify an absolute path or relative path to the current working directory. Write a program which takes a pathname, and returns the shortest equivalent pathname.

Test Cases:

```
assert (normalizePathname("123/456").equals("123/456"));
assert (normalizePathname("/123/456").equals("/123/456"));
assert (normalizePathname("../../local").equals("../../local"));
assert (normalizePathname("usr/lib/../bin/gcc").equals("usr/bin/gcc"));
assert (normalizePathname("scripts//./../scripts/awk/././").equals("scripts/awk"));
```

```java
public static String normalizePathname(String path) {
	if (path == null || path.trim().equals(""))
		throw new IllegalArgumentException("Empty string is not a legal path.");

	Deque<String> pathNames = new LinkedList<>();
	if (path.startsWith("/")) // special case: starts with "/"
		pathNames.push("/");

	for (String token : path.split("/")) {
		if (token.equals("..")) {
			if (pathNames.isEmpty() || pathNames.peek().equals(".."))
				pathNames.push(token);
			else {
				if (pathNames.peek().equals("/"))
					throw new IllegalArgumentException("Path error, trying to go up root " + path);
				pathNames.pop();
			}
		} else if (!token.equals(".") && !token.isEmpty()) // must be a name
			pathNames.push(token);
	}

	StringBuilder result = new StringBuilder();
	if (!pathNames.isEmpty()) {
		Iterator<String> it = pathNames.descendingIterator();
		String prev = it.next();
		result.append(prev);
		while (it.hasNext()) {
			if (!prev.equals("/")) {
				result.append("/");
			}
			prev = it.next();
			result.append(prev);
		}
	}
	return result.toString();
}
```


### Evaluate RPN Expressions

Evaluate the value of an arithmetic expression in Reverse Polish Notation. Valid operators are +, -, \*, /. Each operand may be an integer or another expression.

Some examples:  
["2", "1", "+", "3", "\*"] -> ((2 + 1) * 3) -> 9  
["4", "13", "5", "/", "+"] -> (4 + (13 / 5)) -> 6  

```java
public static int evalRPN(String[] tokens) {
  Deque<Integer> stack = new LinkedList<>();
  for (String t : tokens) {
    if (t.equals("+")) {
      stack.push(stack.pop() + stack.pop());
    } else if (t.equals("-")) {
      int a = stack.pop(), b = stack.pop();
      stack.push(b - a);
    } else if (t.equals("*")) {
      stack.push(stack.pop() * stack.pop());
    } else if (t.equals("/")) {
      int a = stack.pop(), b = stack.pop();
      stack.push(b / a);
    } else {
      stack.push(Integer.valueOf(t));
    }
  }
  return stack.pop();
}
```

### Basic Calculator III

Implement a basic calculator to evaluate a simple expression string.

The expression string may contain open ( and closing parentheses ), the plus + or minus sign -, non-negative integers and empty spaces.

The expression string contains only non-negative integers, +, -, \*, / operators , open ( and closing parentheses ) and empty spaces. The integer division should truncate toward zero.

Some examples:  
"1+1" = 2  
"6-4/2" = 4  
"2*(5+5*2)/3+(6/2+8)" = 21  
"(2+6*3+5-(3*14/7+2)\*5)+3"=-12  

_We can use Dijkstra's Two-Stack Algorithm for Expression Evaluation._

```java
public class BasicCalculatorIII {
	public static int calculate(String s) {
		if (s == null || s.length() == 0)
			return 0;
		Deque<Integer> nums = new ArrayDeque<>();
		Deque<Character> ops = new ArrayDeque<>();

		int num = 0;
		for (int i = 0; i < s.length(); i++) {
			char c = s.charAt(i);
			if (c == ' ')
				continue;
			if (Character.isDigit(c)) {
				num = c - '0';
				// convert continuous digits to number
				while (i < s.length() - 1 && Character.isDigit(s.charAt(i + 1))) {
					num = num * 10 + (s.charAt(i + 1) - '0');
					i++;
				}
				nums.push(num);
			} else if (c == '(') {
				ops.push(c);
			} else if (c == ')') {
				// calculate backward until '('
				while (ops.peek() != '(') {
					nums.push(operation(ops.pop(), nums.pop(), nums.pop()));
				}
				ops.pop(); // remove the '('
			} else if (c == '+' || c == '-' || c == '*' || c == '/') {
				// calculate if higher precedence in stack
				while (!ops.isEmpty() && precedence(c, ops.peek())) {
					nums.push(operation(ops.pop(), nums.pop(), nums.pop()));
				}
				ops.push(c);
			}
		}
		while (!ops.isEmpty()) {
			nums.push(operation(ops.pop(), nums.pop(), nums.pop()));
		}
		return nums.pop();
	}

	private static int operation(char op, int b, int a) {
		switch (op) {
		case '+':
			return a + b;
		case '-':
			return a - b;
		case '*':
			return a * b;
		case '/':
			return a / b; // assume b is not 0
		}
		return 0;
	}

	private static boolean precedence(char current, char previous) {
		if (previous == '(' || previous == ')')
			return false;
		if ((current == '*' || current == '/') && (previous == '+' || previous == '-'))
			return false;
		return true;
	}

	public static void main(String[] args) {
		assert calculate(" 6-4 / 2 ") == 4;
		assert calculate("2*(5+5*2)/3+(6/2+8)") == 21;
		assert calculate("(2+6* 3+5- (3*14/7+2)*5)+3") == -12;
	}
}
```

### Largest Rectangle Skyline

Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

_We do not know how far to the right the largest rectangle it supports goes. However, we do know that the largest rectangles supported by earlier buildings whose height is greater than A[i] cannot extend past i, since Building i "blocks" these rectangles._

_When we remove a blocked building from the active pillar set, to find how far to the left its largest supported rectangle extends we simply look for the closest active pillar that has a lower height._

_We maintain a stack, We start with the leftmost bar and keep pushing the current bar's index onto the stack until we get two successive numbers in descending order. Now, we start popping the numbers from stack until we hit a stack which is equal or smaller than the current bar._

```java
public static int largestRectangleArea(int[] heights) {
	int len = heights.length;
	Stack<Integer> s = new Stack<Integer>();
	int maxArea = 0;
	for (int i = 0; i <= len; i++) {
		int h = (i == len ? 0 : heights[i]);
		if (s.isEmpty() || h >= heights[s.peek()]) {
			s.push(i);
		} else {
			int tp = s.pop();
			maxArea = Math.max(maxArea, heights[tp] * (s.isEmpty() ? i : i - 1 - s.peek()));
			i--; // keep trying until! so there be some hollows left.
		}

	}
	return maxArea;
}
```

## Queues Boot Camp

### Design Max Queue

Implement a queue with enqueue, dequeue, and max operations. The max operation returns the maximum element currently stored in the queue.

_Consider an element s in the queue that has the property that it entered the queue before a later element b which is greater than s. Since s will be dequeued before b, s can never in the future become the maximum element stored in the queue._

_To briefly describe how to update the deque (max elements) on queue updates. If the queue is dequeued, and if this element is at the deque's head, pop the head; otherwise no change. When add an entry to the queue, we iteratively evict from the deque's tail until the tail is greater than or equal to the enqueued entry, and then add the new entry to the deque's tail._

_You can even count the occurrences of the max element, like we did with Max Stack, to improve the space complexity._

**This is actually also a sliding window problem.**

```java
public class MaxQueue<T extends Comparable<T>> {
	private Queue<T> entries = new ArrayDeque<>();
	private Deque<T> maxElements = new ArrayDeque<>();

	public void enqueue(T x) {
		entries.add(x);
		while (!maxElements.isEmpty() && maxElements.peekLast().compareTo(x) < 0) {
			// evict this unqualified element
			maxElements.removeLast();
		}
		maxElements.addLast(x);
	}

	public T dequeue() {
		if (!entries.isEmpty()) {
			T entry = entries.remove();
			if (entry.equals(maxElements.peekFirst()))
				maxElements.removeFirst();
			return entry;
		}
		throw new NoSuchElementException();
	}

	public T max() {
		if (!maxElements.isEmpty())
			return maxElements.peekFirst();
		throw new NoSuchElementException();
	}
}
```

_An alternate solution is to use two Max Stacks we designed above to model a queue._

```
private MaxStack enqueue = new MaxStack();
private MaxStack dequeue = new MaxStack();
```

### Implement Queue using Stacks

Implement the following operations of a queue using stacks.

push(x) -- Push element x to the back of queue.
pop() -- Removes the element from in front of queue.
peek() -- Get the front element.
empty() -- Return whether the queue is empty.

Example:
```
MyQueue queue = new MyQueue();

queue.push(1);
queue.push(2);  
queue.peek();  // returns 1
queue.pop();   // returns 1
queue.empty(); // returns false
```

_Use 2 stacks, the newly arrived element is added to stack one first; When pop or peek, first to rotate stack one to stack two, and then operate from stack two. The complexity is O(1) for Push and amortized O(1) for Pop._

```java
public class QueueWithStacks {
	Stack<Integer> stack1, stack2;

	/** Initialize your data structure here. */
	public QueueWithStacks() {
		stack1 = new Stack<>();
		stack2 = new Stack<>();
	}

	/** Push element x to the back of queue. */
	public void push(int x) {
		stack1.push(x);
	}

	/** Removes the element from in front of queue and returns that element. */
	public int pop() {
		rotateStacks();
		return stack2.pop();
	}

	/** Get the front element. */
	public int peek() {
		rotateStacks();
		return stack2.peek();
	}

	/** Returns whether the queue is empty. */
	public boolean empty() {
		return stack1.isEmpty() && stack2.isEmpty();
	}

	private void rotateStacks() {
		if (stack2.isEmpty()) {
			while (!stack1.isEmpty()) {
				stack2.push(stack1.pop());
			}
		}
	}
}
```

### Implement A Circular Queue

Implement a circular queue API using an array for storing elements. Your API should include a constructor function, which takes as argument the initial capacity of the queue, enqueue and dequeue functions, and a function which returns the number of elements stored. Implement dynamic resizing to support storing an arbitrarily large number of elements.

```java
public class CircularQueue {
	private static final int SCALE_FACTOR = 2;
	private int head = 0, tail = 0, size = 0;
	private Integer[] entries;

	public CircularQueue(int capacity) {
		if (capacity < 0)
			throw new IllegalArgumentException();
		entries = new Integer[capacity];
	}

	public void enqueue(Integer x) {
		if (size == entries.length) { // need to resize
			// make the queue elements appear consecutively
			Collections.rotate(Arrays.asList(entries), -head);
			// reset head and tail indices
			head = 0;
			tail = size;
			entries = Arrays.copyOf(entries, size * SCALE_FACTOR);
		}
		entries[tail] = x;
		tail = (tail + 1) % entries.length;
		size++;
	}

	public Integer dequeue() {
		if (size != 0) {
			size--;
			Integer result = entries[head];
			head = (head + 1) % entries.length;
			return result;
		}
		throw new NoSuchElementException("Dequeue called on an empty queue.");
	}

	public int size() {
		return size;
	}
}
```

### Event-driven Simulation

![Event Driven Simulation](/assets/images/algorithms/event-driven-simulation.png)

An event-driven simulation of n colliding particles requires at most n^2 priority queue operations for initialization, and at most n priority queue operations per collision (with one extra priority queue operation for each invalid collision)

```java
public class CollisionSystem {
    private final static double HZ = 0.5;    // number of redraw events per clock tick

    private MinPQ<Event> pq;          // the priority queue
    private double t  = 0.0;          // simulation clock time
    private Particle[] particles;     // the array of particles

    /**
     * Initializes a system with the specified collection of particles.
     * The individual particles will be mutated during the simulation.
     */
    public CollisionSystem(Particle[] particles) {
        this.particles = particles.clone();   // defensive copy
    }

    // updates priority queue with all new events for particle a
    private void predict(Particle a, double limit) {
        if (a == null) return;

        // particle-particle collisions
        for (int i = 0; i < particles.length; i++) {
            double dt = a.timeToHit(particles[i]);
            if (t + dt <= limit)
                pq.insert(new Event(t + dt, a, particles[i]));
        }

        // particle-wall collisions
        double dtX = a.timeToHitVerticalWall();
        double dtY = a.timeToHitHorizontalWall();
        if (t + dtX <= limit) pq.insert(new Event(t + dtX, a, null));
        if (t + dtY <= limit) pq.insert(new Event(t + dtY, null, a));
    }

    // redraw all particles
    private void redraw(double limit) {
        StdDraw.clear();
        for (int i = 0; i < particles.length; i++) {
            particles[i].draw();
        }
        StdDraw.show();
        StdDraw.pause(20);
        if (t < limit) {
            pq.insert(new Event(t + 1.0 / HZ, null, null));
        }
    }


    /**
     * Simulates the system of particles for the specified amount of time.
     */
    public void simulate(double limit) {

        // initialize PQ with collision events and redraw event
        pq = new MinPQ<Event>();
        for (int i = 0; i < particles.length; i++) {
            predict(particles[i], limit);
        }
        pq.insert(new Event(0, null, null));        // redraw event


        // the main event-driven simulation loop
        while (!pq.isEmpty()) {

            // get impending event, discard if invalidated
            Event e = pq.delMin();
            if (!e.isValid()) continue;
            Particle a = e.a;
            Particle b = e.b;

            // physical collision, so update positions, and then simulation clock
            for (int i = 0; i < particles.length; i++)
                particles[i].move(e.time - t);
            t = e.time;

            // process event
            if      (a != null && b != null) a.bounceOff(b);              // particle-particle collision
            else if (a != null && b == null) a.bounceOffVerticalWall();   // particle-wall collision
            else if (a == null && b != null) b.bounceOffHorizontalWall(); // particle-wall collision
            else if (a == null && b == null) redraw(limit);               // redraw event

            // update the priority queue with new collisions involving a or b
            predict(a, limit);
            predict(b, limit);
        }
    }

    private static class Event implements Comparable<Event> {
        private final double time;         // time that event is scheduled to occur
        private final Particle a, b;       // particles involved in event, possibly null
        private final int countA, countB;  // collision counts at event creation


        // create a new event to occur at time t involving a and b
        public Event(double t, Particle a, Particle b) {
            this.time = t;
            this.a    = a;
            this.b    = b;
            if (a != null) countA = a.count();
            else           countA = -1;
            if (b != null) countB = b.count();
            else           countB = -1;
        }

        // compare times when two events will occur
        public int compareTo(Event that) {
            return Double.compare(this.time, that.time);
        }

        // has any collision occurred between when event was created and now?
        public boolean isValid() {
            if (a != null && a.count() != countA) return false;
            if (b != null && b.count() != countB) return false;
            return true;
        }

    }    
}
```

### Task Scheduler

Given a char array representing tasks CPU need to do. It contains capital letters A to Z where different letters represent different tasks.Tasks could be done without original order. Each task could be done in one interval. For each interval, CPU could finish one task or just be idle.

However, there is a non-negative cooling interval n that means between two same tasks, there must be at least n intervals that CPU are doing different tasks or just be idle.

You need to return the least number of intervals the CPU will take to finish all the given tasks.

```
Example 1:
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: A -> B -> idle -> A -> B -> idle -> A -> B.
```

Approach #1: Use a max-heap (queue) to pick the order in which the tasks need to be executed. We need to ensure that the heapification occurs only after the intervals of cooling time, n, as done in last approach.

```java
public int leastInterval(char[] tasks, int n) {
	int[] counts = new int[26];
	for (char c : tasks)
		counts[c - 'A']++;
	Queue<Integer> queue = new PriorityQueue<>(Collections.reverseOrder());
	for (int c : counts) {
		if (c > 0)
			queue.offer(c);
	}
	int time = 0;
	while (!queue.isEmpty()) {
		List<Integer> temp = new ArrayList<>();
		int i = 0;
		while (i <= n) {
			if (!queue.isEmpty()) {
				if (queue.peek() > 1)
					temp.add(queue.poll() - 1);
				else
					queue.poll();
			}
			time++;
			if (queue.isEmpty() && temp.isEmpty()) // last round, no more tasks left!
				break;
			i++;
		}
		for (int t : temp) {
			queue.offer(t);
		}
	}
	return time;
}
```

Approach #2: Calculating Idle slots. If we are able to determine the number of
idle_slots), we can find out the time required to execute all the tasks as idle_slots + total_number_of_tasks.

```java
public int leastInterval2(char[] tasks, int n) {
	int[] counts = new int[26];
	for (char c : tasks)
		counts[c - 'A']++;
	Arrays.sort(counts);
	int maxCount = counts[25] - 1, idleSlots = maxCount * n;
	for (int i = 24; i >= 0 && counts[i] > 0; i--) {
		idleSlots -= Math.min(counts[i], maxCount);
	}
	return idleSlots > 0 ? idleSlots + tasks.length : tasks.length;
}
```

# Reference Resources
- [Source Code on GitHub](https://github.com/codebycase/algorithms-java/blob/master/src/main/java/a04_stacks_queues)
