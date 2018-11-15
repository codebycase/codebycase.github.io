---
layout: article
title: Algorithms - Recursion, Greedy, Invariant
key: a10-recursion-greedy-invariant
categories: Algorithms
tags: Recursion Greedy Invariants
---

## Recursion

- Recursion is a good choice for search, enumeration, and divide-and-conquer.

- If you are asked to remove recursion from a program, consider mimicking call stack with the stack data structure.

- Use recursion as alternative to deeply nested iteration loops. For example, recursion is much better when you have an undefined number of levels.

- If a recursion function may end up being called with the same arguments more than once, cache the results - this is the idea behind Dynamic Programming.

- Two key ingredients to a successful use of recursion are: identifying the base cases which are to be solved directly, and ensuring progress that is the recursion converges to the solution.

<!--more-->

- A divide-and-conquer algorithm works by repeatedly decomposing a problem into two or more smaller independent subproblems of the same kind, until it gets to instances that are simple enough to be solved directly. (Top-Down)

### Recursion Math

Consider the following recursive function:

```java
public static int mystery(int a, int b) {
	if (b == 0) return 1;
	if (b % 2 == 0) return mystery(a + a, b / 2);
	return mystery(a + a, b / 2) + a;
}
```

What are the values of mystery(2, 25) and mystery(3, 11)?

```
mystery(2, 25) <= 51
	-> mystery(4, 12) + 2 <= 49 + 2
		-> mystery(8, 6) <= 49
			-> mystery(16, 3) <= 49
				-> mystery(32, 1) + 16 <= 33 + 16
					-> mystery(48, 0) + 32 <= 1 + 32

mystery(3, 11) <= 34
	-> mystery(6, 5) + 3 <= 31 + 3
		-> mystery(12, 2) + 6 <= 25 + 6
			-> mystery(24, 1) <= 25
				-> mystery(48, 0) + 24 <= 1 + 24
```

### Compute Gray Code

The gray code is a binary numeral system where two successive values differ in only one bit.

Given a non-negative integer n representing the total number of bits in the code, print the sequence of gray code. A gray code sequence must begin with 0.

For example, given n = 2, return [0,1,3,2]. Its gray code sequence is:

```
00 - 0
01 - 1
11 - 3
10 - 2
```

```
000 - 0
001 - 1
011 - 3
010 - 2
```
+

```
110 - 6
111 - 7
101 - 5
100 - 4
```

Note:

For a given n, a gray code sequence is not uniquely defined.

For example, [0,2,3,1] is also a valid gray code sequence according to the above definition.

_The idea is to generate the sequence iteratively. For example, when n = 3, we can get the result based on n = 2. 00,01,11,10 <=> 000,001,011,010, prepend 1 to get (110,111,101,100). The middle two numbers only differ at their highest bit, while the rest numbers of part two are exactly symmetric of part one. It is easy to see its correctness._

```java
public List<Integer> grayCode(int n) {
	List<Integer> result = new ArrayList<>();
	result.add(0);
	for (int i = 0; i < n; i++) {
		int size = result.size();
		for (int k = size - 1; k >= 0; k--) {
			result.add(result.get(k) | (1 << i));
		}
	}
	return result;
}
```

### The Hanoi Towers

In the classic problem of the Towers of Hanoi, you have 3 towers and N disks of different sizes which can slide onto any tower. How can you transfers N rings from one peg to another.
The only operation you can perform is taking a single ring from the top of one peg and placing it on the top of another peg. You must never place a larger ring above a smaller ring.

_Below shows the operations for 3 rings. One way to see the complexity is to "unwrap" the recurrence: $$T(n) = 1 + 2 + 4 + ... + 2^kT(n-k) = O(2^n)$$_

![Towers Of Hanoi](/assets/images/algorithms/towers-of-hanoi.png)

This approach leads to a nature recursive algorithm. In each part, we are doing the following steps, outlined below with pseudocode:

```java
moveDisks(int n, Tower origin, Tower destination, Tower buffer) {
	// Base Case
	if (n <= 0)
		return;

	// Move top n - 1 disks from origin to buffer, using destination as a buffer.
		moveDisk(n - 1, origin, buffer, destination)

	// Move top (the last one) from origin to destination
		moveTop(origin, destination)

	// Move top n - 1 disks from buffer to destination, using origin as a buffer.
		moveDisks(n - 1, buffer, destination, origin)
}
```

The following code provides the java implementation of this algorithm.

```java
public class HanoiTower {
	private static final int NUM_PEGS = 3;

	class Tower {
		private Stack<Integer> disks;
		private int index;

		public Tower(int i) {
			disks = new Stack<Integer>();
			index = i;
		}

		public int index() {
			return index;
		}

		public void add(int disk) {
			if (!disks.isEmpty() && disks.peek() <= disk)
				System.err.println("Error placing disk " + disk);
			else
				disks.push(disk);
		}

		public void moveTopTo(Tower tower) {
			int top = disks.pop();
			tower.add(top);
		}

		public void moveDisks(int n, Tower destination, Tower buffer) {
			if (n <= 0)
				return;
			moveDisks(n - 1, buffer, destination);
			moveTopTo(destination);
			System.out.println("Moved top from " + this.index + " to " + destination.index);
			buffer.moveDisks(n - 1, destination, this);
		}

		public String toString() {
			return disks.toString();
		}
	}

	public Tower printHanoiTower(int numRings) {
		Tower[] towers = new Tower[NUM_PEGS];
		for (int i = 0; i < towers.length; i++) {
			towers[i] = new Tower(i);
		}
		for (int i = numRings; i >= 1; i--) {
			towers[0].add(i);
		}
		towers[0].moveDisks(numRings, towers[2], towers[1]);
		return towers[2];
	}

	public static void main(String[] args) {
		HanoiTower solution = new HanoiTower();
		Tower tower = solution.printHanoiTower(5);
		System.out.println("Tower: " + tower.toString());
		assert tower.toString().equals("[5, 4, 3, 2, 1]");
	}
}
```

### The Tree's Diameter

The diameter of a tree is defined to be the length of a longest path in the tree.

_Consider a longest path in the tree. Either it passes through the root or it does not pass through the root. We can keep tracking the max two distances for the children of a root, the diameter would be distances[0] + distances[1]._

```java
public class TreeDiameter {
	public static class TreeNode {
		List<Edge> edges = new ArrayList<>();
	}

	private static class Edge {
		public TreeNode node;
		public Double length;

		public Edge(TreeNode node, Double length) {
			this.node = node;
			this.length = length;
		}
	}

	private static class DistanceAndDiameter {
		public Double distance;
		public Double diameter;

		public DistanceAndDiameter(Double distance, Double diameter) {
			this.distance = distance;
			this.diameter = diameter;
		}
	}

	public static double computeDiameter(TreeNode T) {
		return T != null ? computeDistanceAndDiameter(T).diameter : 0.0;
	}

	private static DistanceAndDiameter computeDistanceAndDiameter(TreeNode r) {
		double diameter = Double.MIN_VALUE;
		// Stores the max two distances. distance[0] is the max distance
		double[] distances = { 0.0, 0.0 };
		for (Edge e : r.edges) {
			DistanceAndDiameter distanceDiameter = computeDistanceAndDiameter(e.node);
			if (distanceDiameter.distance + e.length > distances[0]) {
				// Shift and store the max two distances
				distances = new double[] { distanceDiameter.distance + e.length, distances[0] };
			} else if (distanceDiameter.distance + e.length > distances[1]) {
				// When only greater than the second distance
				distances[1] = distanceDiameter.distance + e.length;
			}
			diameter = Math.max(diameter, distanceDiameter.diameter);
		}
		return new DistanceAndDiameter(distances[0], Math.max(diameter, distances[0] + distances[1]));
	}
}
```

### Network Delay Time

There are N network nodes, labelled 1 to N.

Given times, a list of travel times as directed edges times[i] = (u, v, w), where u is the source node, v is the target node, and w is the time it takes for a signal to travel from source to target.

Now, we send a signal from a certain node K. How long will it take for all nodes to receive the signal? If it is impossible, return -1.

Note:

- N will be in the range [1, 100].
- K will be in the range [1, N].
- The length of times will be in the range [1, 6000].
- All edges times[i] = (u, v, w) will have 1 <= u, v <= N and 1 <= w <= 100.

_Dijkstra's algorithm is based on repeatedly making the candidate move that has the least distance travelled. We can use this algorithm to find the shortest path from our source to all targets. The time complexity is O(ElogE) where E is length of times._

```java
public int networkDelayTime(int[][] times, int N, int K) {
	List<List<int[]>> graph = new ArrayList<>();
	// label from 1 to N!
	for (int i = 0; i <= N; i++) {
		graph.add(new ArrayList<>());
	}
	for (int[] edge : times) {
		graph.get(edge[0]).add(new int[] { edge[1], edge[2] });
	}

	// int[] has node label and delay time
	Queue<int[]> heap = new PriorityQueue<>((a, b) -> a[1] - b[1]);
	heap.offer(new int[] { K, 0 }); // node -> distance

	// distance of shortest s -> v path
	Map<Integer, Integer> distances = new HashMap<>();
	while (!heap.isEmpty()) {
		int[] item = heap.poll();
		int node = item[0], delay = item[1];
		if (distances.containsKey(node))
			continue;
		distances.put(node, delay);
		for (int[] edge : graph.get(node)) {
			int node2 = edge[0], delay2 = edge[1];
			if (!distances.containsKey(node2))
				heap.offer(new int[] { node2, delay + delay2 });
		}
	}

	if (distances.size() != N)
		return -1;
	int answer = 0;
	for (int delay : distances.values())
		answer = Math.max(answer, delay);
	return answer;
}
```

### Cheapest Flights with K Stops

There are n cities connected by m flights. Each fight starts from city u and arrives at v with a price w.

Now given all the cities and fights, together with starting city src and the destination dst, your task is to find the cheapest price from src to dst with up to k stops. If there is no such route, output -1.

```java
public int findCheapestPrice(int n, int[][] flights, int src, int dst, int K) {
	List<List<int[]>> graph = new ArrayList<>(n);
	for (int i = 0; i < n; i++) {
		graph.add(new ArrayList<>());
	}
	for (int[] flight : flights) {
		graph.get(flight[0]).add(flight);
	}

	PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> (a[1] - b[1]));
	// city, cost, stop
	pq.add(new int[] { src, 0, -1 });

	while (!pq.isEmpty()) {
		int[] curt = pq.poll();
		int city = curt[0];
		int cost = curt[1];
		int stop = curt[2] + 1;

		if (city == dst) {
			return curt[1];
		}

		for (int[] flight : graph.get(city)) {
			if (stop > K)
				continue;
			pq.add(new int[] { flight[1], flight[2] + cost, stop });
		}
	}

	return -1;
}
```

### N Pairs of Parentheses

[N Pairs of Parentheses](/algorithms/2017/11/15/a08-dynamic-programming.html#n-pairs-of-parentheses)

### N Queens Chessboard

[N Queens Chessboard](/algorithms/2017/11/15/a08-dynamic-programming.html#n-queens-chessboard)

### Compute Permutations

[Compute Permutations](/algorithms/2017/11/15/a08-dynamic-programming.html#compute-permutations)

### Compute Subsets

[Compute Permutations](/algorithms/2017/11/15/a08-dynamic-programming.html#subset-with-k-size)

### Sudoku Solver

[Sudoku Solver](/algorithms/2017/11/11/a02-arrays-and-strings.html#sudoku-solver)

## Greedy Algorithms

- A greedy algorithm is an algorithm that computes a solution in steps; at each step the algorithm makes a decision that is locally optimum, and it never changes that decision.

- It's often easier to conceptualized a greedy algorithm recursively, and then implement it using iteration for higher performance.

- A greedy algorithm is often the right choice for an optimization problem where there's a natural set of choices to select from.

### Assignment of Tasks

Design an algorithm that takes as input a set of tasks and return an optimum assignment.

_In summary, we sort the set of task durations, and pair the shortest, second shortest, third shortest, etc. tasks with the longest, second longest, third longest, etc. tasks. For example, if the durations are 5, 2, 1, 6, 4, 4, then on sorting we get 1, 2, 4, 4, 5, 6, and the pairing are (1, 6), (2, 5), and (4, 4). O(nlog(n))._

### Minimize Waiting Time

Given service times for a set of queries, compute a schedule for processing the queries that minimizes the total waiting time. For example, if the service times are <2, 5, 1, 3>.

_The best schedule processes queries in increasing order of service times. It has a total waiting time of 0 + (1) + (1 + 2) + (1 + 2 + 3) = 10._

```java
public static int minimumTotalWaitingTime(List<Integer> serviceTimes) {
	Collections.sort(serviceTimes);
	int totalWaitingTime = 0;
	for (int i = 0; i < serviceTimes.size(); i++) {
		// exclude the last one!
		int numRemainingQueries = serviceTimes.size() - (i + 1);
		totalWaitingTime += serviceTimes.get(i) * numRemainingQueries;
	}
	return totalWaitingTime;
}
```

### Interval Covering Problem

You are given a set of closed intervals. Design an efficient algorithm for finding a minimum sized set of numbers that covers all the intervals.

_Let's focus on the extreme cases. In particular, consider the interval that ends first and whose right endpoint is also minimum. To cover it, we must pick a number that appears in it. Furthermore, we should pick its right endpoint, since any other intervals covered by a number in the interval will continue to be covered if we pick the right endpoint._

```java
public static Integer findMinimumVisits(List<Interval> intervals) {
	Collections.sort(intervals, (a, b) -> (a.right - b.right));
	int numVisits = 0;
	int lastVisitTime = 0;
	for (Interval interval : intervals) {
		if (interval.left > lastVisitTime) {
			lastVisitTime = interval.right;
			numVisits++;
		}
	}
	return numVisits;
}
```

## Invariants

A common approach to design an efficient algorithm is to use invariants. An invariant is a condition that is true during execution of a program. This condition may be on the values of the variables of the program, or on the control logic.

Identifying the right invariant is an art. The key strategy to determine whether to use an invariant when designing an algorithm is to work on small examples to hypothesize the invariant.

### Majority Element

Let's say the majority element occurred m times out of n entries. By the definition of majority element, $$\frac{m}{n} > \frac{1}{2}$$. At most one of the two distinct entries that are discarded can be the majority element. Hence, after discarding them, the ratio is either $$\frac{m}{(n-2)}$$ or $$\frac{m-1}{n-2}$$, It is easy to tell both $$\frac{m}{n-2} > \frac{1}{2}$$ and $$\frac{m-1}{n-2} > \frac{1}{2}$$.

```java
public static String majoritySearch(Iterator<String> sequence) {
	String candidate = null;
	int candidateCount = 0;
	while (sequence.hasNext()) {
		String current = sequence.next();
		if (candidateCount == 0) {
			candidate = current;
			candidateCount = 1;
		} else {
			if (current.equals(candidate))
				candidateCount++;
			else
				candidateCount--;
		}
	}
	return candidate;
}
```

### Heavy Hitter Tokens

In practice we may not be interested in a majority token but all tokens whose count exceeds say 1% of the total token count.

Given you are reading a sequence of strings separated by whitespace. You are allowed to read the sequence twice. Devise an algorithm that uses O(k) memory to identify the words that occur more than n/k times, where n is the length of the sequence.

_Here instead of discarding two distinct words, we discard k distinct words at any given time and we are guaranteed that all the words that occurred more than n/k continue to appear in the remaining sequence._

```java
public static List<String> searchFrequentItems(Iterable<String> stream, int k) {
	// Finds the candidates which may occur > n / k times
	Map<String, Integer> table = new HashMap<>();
	int count = 0; // Counts the number of items

	Iterator<String> sequence = stream.iterator();
	while (sequence.hasNext()) {
		String item = sequence.next();
		table.put(item, table.getOrDefault(item, 0) + 1);
		count++;
		// Detecting k items in table, at least one of them must have exactly one in it, We will
		// discard those k items by one for each
		if (table.size() == k) {
			List<String> delKeys = new ArrayList<>();
			for (Map.Entry<String, Integer> entry : table.entrySet()) {
				if (entry.getValue() - 1 == 0)
					delKeys.add(entry.getKey());
				else
					table.put(entry.getKey(), entry.getValue() - 1);
			}
			for (String delKey : delKeys) {
				table.remove(delKey);
			}
		}
	}

	// Reset table for the following counting.
	for (String key : table.keySet()) {
		table.put(key, 0);
	}

	// Counts the occurence of each candidate word.
	sequence = stream.iterator();
	while (sequence.hasNext()) {
		String item = sequence.next();
		if (table.containsKey(item)) {
			table.put(item, table.get(item) + 1);
		}
	}

	// Selects the word which occurs > n/k times
	List<String> result = new ArrayList<>();
	for (Map.Entry<String, Integer> it : table.entrySet()) {
		if (count * 1.0 / k < (double) it.getValue()) {
			result.add(it.getKey());
		}
	}

	return result;
}
```

### Gas Stations

There are N gas stations along a circular route, where the amount of gas at station i is gas[i].

You have a car with an unlimited gas tank and it costs cost[i] of gas to travel from station i to its next station (i+1). You begin the journey with an empty tank at one of the gas stations.

Return the starting gas station's index if you can travel around the circuit once, otherwise return -1.

_The solution is based on follow two ideas: If car starts at A and can not reach B. Any station between A and B can not reach B; If the total number of Gas is bigger than the total number of Cost, There must be a solution._

```java
public static int canCompleteCircuit(int[] gas, int[] cost) {
	int sumGas = 0, sumCost = 0;
	int start = 0, tank = 0;
	for (int i = 0; i < gas.length; i++) {
		sumGas += gas[i];
		sumCost += cost[i];
		tank += gas[i] - cost[i]; // track tank's gas left!
		if (tank < 0) {
			start = i + 1;
			tank = 0;
		}
	}
	return sumGas < sumCost ? -1 : start;
}
```

### 3/4 Sum Problems

The following algorithms demonstrate how to calculate 3 or 4 sums to equal, closest or smaller etc. All starts with sorting the array first which complexity is n(log(n)). But overall complexity is O(n^2) due to the 2 layers iteration.

```java
public class ThreeFourSumProblems {
	/**
	 * Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0? Find
	 * all unique triplets in the array which gives the sum of zero.
	 *
	 * Note: The solution set must not contain duplicate triplets.
	 *
	 * <pre>
	For example, given array S = [-1, 0, 1, 2, -1, -4],

	A solution set is:
	[
	  [-1, 0, 1],
	  [-1, -1, 2]
	]
	 * </pre>
	 *
	 */
	public static List<List<Integer>> threeSumEquals(int[] nums, int target) {
		List<List<Integer>> result = new ArrayList<>();
		if (nums == null || nums.length < 3)
			return result;
		Arrays.sort(nums);
		for (int i = 0; i < nums.length - 2; i++) {
			// skip duplicates
			if (i > 0 && nums[i] == nums[i - 1])
				continue;
			int lo = i + 1, hi = nums.length - 1;
			while (lo < hi) {
				int sum = nums[i] + nums[lo] + nums[hi];
				if (sum == target) {
					result.add(Arrays.asList(nums[i], nums[lo], nums[hi]));
					// skip duplicates
					while (lo < hi && nums[lo] == nums[lo + 1])
						lo++;
					// skip duplicates
					while (lo < hi && nums[hi] == nums[hi - 1])
						hi--;
					lo++;
					hi--;
				} else if (sum > target) {
					hi--;
				} else {
					lo++;
				}
			}
		}

		return result;
	}

	/**
	 * Given an array S of n integers, find three integers in S such that the sum is closest to a
	 * given number, target. Return the sum of the three integers. You may assume that each input
	 * would have exactly one solution.
	 *
	 * For example, given array S = {-1 2 1 -4}, and target = 1.
	 *
	 * The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
	 *
	 */
	public static int threeSumClosest(int[] nums, int target) {
		if (nums == null || nums.length < 3)
			return 0;
		Arrays.sort(nums);
		int result = nums[0] + nums[1] + nums[2];
		for (int i = 0; i < nums.length - 2; i++) {
			int lo = i + 1, hi = nums.length - 1;
			while (lo < hi) {
				int sum = nums[i] + nums[lo] + nums[hi];
				if (sum > target)
					hi--;
				else
					lo++;
				if (Math.abs(sum - target) < Math.abs(result - target))
					result = sum;
			}
		}
		return result;
	}

	/**
	 * Given an array of n integers nums and a target, find the number of index triplets i, j, k
	 * with 0 <= i < j < k < n that satisfy the condition nums[i] + nums[j] + nums[k] < target.
	 *
	 * <pre>
	For example, given nums = [-2, 0, 1, 3], and target = 2.

	Return 2. Because there are two triplets which sums are less than 2:

	[-2, 0, 1]
	[-2, 0, 3]
	 * </pre>
	 */
	public static int threeSumSmaller(int[] nums, int target) {
		if (nums == null || nums.length < 3)
			return 0;

		Arrays.sort(nums);

		int result = 0;
		for (int i = 0; i < nums.length - 2; i++) {
			int lo = i + 1, hi = nums.length - 1;
			while (lo < hi) {
				int sum = nums[i] + nums[lo] + nums[hi];
				if (sum < target) {
					result += hi - lo;
					lo++;
				} else {
					hi--;
				}
			}
		}

		return result;
	}

	/**
	 * <pre>
	Given an array S of n integers, are there elements a, b, c, and d in S such that a + b + c + d = target? Find all unique quadruplets in the array which gives the sum of target.

	Note: The solution set must not contain duplicate quadruplets.

	For example, given array S = [1, 0, -1, 0, -2, 2], and target = 0.

	A solution set is:
	[
	[-1,  0, 0, 1],
	[-2, -1, 1, 2],
	[-2,  0, 0, 2]
	]
	 * </pre>
	 *
	 */
	public List<List<Integer>> fourSum(int[] nums, int target) {
		List<List<Integer>> result = new ArrayList<>();
		if (nums == null || nums.length < 4)
			return result;
		Arrays.sort(nums);
		int len = nums.length;

		for (int i = 0; i < nums.length - 3; i++) {
			if (nums[i] + nums[i + 1] + nums[i + 2] + nums[i + 3] > target)
				break;
			if (nums[i] + nums[len - 1] + nums[len - 2] + nums[len - 3] < target)
				continue;
			if (i > 0 && nums[i] == nums[i - 1])
				continue;
			for (int j = i + 1; j < nums.length - 2; j++) {
				if (nums[i] + nums[j] + nums[j + 1] + nums[j + 2] > target)
					break;
				if (nums[i] + nums[j] + nums[len - 1] + nums[len - 2] < target)
					continue;
				if (j > i + 1 && nums[j] == nums[j - 1])
					continue;
				int lo = j + 1, hi = nums.length - 1;
				while (lo < hi) {
					int sum = nums[i] + nums[j] + nums[lo] + nums[hi];
					if (sum == target) {
						result.add(Arrays.asList(nums[i], nums[j], nums[lo], nums[hi]));
						while (lo < hi && nums[lo] == nums[lo + 1])
							lo++;
						while (lo < hi && nums[hi] == nums[hi - 1])
							hi--;
						lo++;
						hi--;
					} else if (sum > target)
						hi--;
					else
						lo++;
				}
			}
		}

		return result;
	}

	/**
	 * Given four lists A, B, C, D of integer values, compute how many tuples (i, j, k, l) there are
	 * such that A[i] + B[j] + C[k] + D[l] is zero.
	 *
	 * To make problem a bit easier, all A, B, C, D have same length of N where 0 ≤ N ≤ 500. All
	 * integers are in the range of -228 to 228 - 1 and the result is guaranteed to be at most 231 -
	 * 1.
	 *
	 */
	public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
		Map<Integer, Integer> map = new HashMap<>();

		for (int i = 0; i < C.length; i++) {
			for (int j = 0; j < D.length; j++) {
				int sum = C[i] + D[j];
				map.put(sum, map.getOrDefault(sum, 0) + 1);
			}
		}

		int result = 0;
		for (int i = 0; i < A.length; i++) {
			for (int j = 0; j < B.length; j++) {
				int sum = A[i] + B[j];
				result += map.getOrDefault(-1 * sum, 0);
			}
		}

		return result;
	}

	public static void main(String[] args) {
		int[] nums = { -1, 0, 1, 2, -1, -4 };
		List<List<Integer>> result = threeSumEquals(nums, 0);
		assert result.toString().equals("[[-1, -1, 2], [-1, 0, 1], [-1, 0, 1]]");
		nums = new int[] { -1, 2, 1, -4 };
		assert threeSumClosest(nums, 1) == 2;
		nums = new int[] { -2, 0, 1, 3 };
		assert threeSumSmaller(nums, 2) == 2;
	}
}
```

### Largest Rectangle in Histogram

Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

_We maintain a stack, We start with the leftmost bar and keep pushing the current bar's index onto the stack until we get two successive numbers in descending order. Now, we start popping the numbers from stack until we hit a stack which is equal or smaller than the current bar._

```java
public int largestRectangleArea(int[] heights) {
	 Stack<Integer> stack = new Stack<>();
	 int maxArea = 0;
	 // loop one more time to process the rest heights
	 for (int i = 0; i <= heights.length; ++i) {
			 int height = i == heights.length ? 0 : heights[i];
			 while (!stack.isEmpty() && heights[stack.peek()] >= height) {
					 maxArea = Math.max(maxArea, heights[stack.pop()] * (stack.isEmpty() ? i : i - 1 - stack.peek()));
			 }
			 stack.push(i);
	 }
	 return maxArea;	 
}
```

### Trapping Water in Lines

Write a program which takes as an input an integer array and returns the pair of entries that trap the maximum amount of water.

Also called "Container With Most Water".

```java
public int maxTrappedWaterInLines(List<Integer> heights) {
	int i = 0, j = heights.size() - 1, maxWater = 0;
	while (i < j) {
		int width = j - i;
		maxWater = Math.max(maxWater, width * Math.min(heights.get(i), heights.get(j)));
		if (heights.get(i) > heights.get(j))
			j--;
		else
			i++;
	}
	return maxWater;
}
```

### Trapping Water in Histogram

Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining. Also called Trapping Rain Water.

For example,
Given [0,1,0,2,1,0,1,3,2,1,2,1], return 6.

![Rain Water Trap](/assets/images/algorithms/rain-water-trap.png)

_We can do it in one iteration using 2 pointers, maintain the leftMax and rightMax. Time complexity is O(n), space is O(1)._

```java
public static int computeHistogramVolume(int[] heights) {
	int a = 0;
	int b = heights.length - 1;
	int max = 0;
	int leftMax = 0;
	int rightMax = 0;
	while (a <= b) {
		leftMax = Math.max(leftMax, heights[a]);
		rightMax = Math.max(rightMax, heights[b]);
		if (leftMax < rightMax) {
			max += (leftMax - heights[a]);
			a++;
		} else {
			max += (rightMax - heights[b]);
			b--;
		}
	}
	return max;
}
```

_We can also use stack to keep track of the bars that are bounded by longer bars and hence, may store water. It's opposite to the largest rectangle problem. Time and space complexity are both O(n)._

```java
public static int computeHistogramVolume2(int[] heights) {
	Deque<Integer> stack = new ArrayDeque<>();
	int max = 0;
	int current = 0;
	while (current < heights.length) {
		while (!stack.isEmpty() && heights[current] > heights[stack.peek()]) {
			int top = stack.pop();
			if (stack.isEmpty())
				break;
			int distance = current - stack.peek() - 1;
			int height = Math.min(heights[current], heights[stack.peek()]) - heights[top];
			max += height * distance;
		}
		stack.push(current++);
	}
	return max;
}
```

### Trapping Water in 2D Matrix

Given an m x n matrix of positive integers representing the height of each unit cell in a 2D elevation map, compute the volume of water it is able to trap after raining.

Example:

```
Given the following 3x6 height map:
[
  [1,4,3,1,3,2],
  [3,2,1,3,2,4],
  [2,3,3,2,3,1]
]

Return 4.
```

![Rain Water in 2D Matrix](https://leetcode.com/static/images/problemset/rainwater_fill.png)

```java
public class TrappingRainWaterII {
    public class Cell {
        int row;
        int col;
        int height;

        public Cell(int row, int col, int height) {
            this.row = row;
            this.col = col;
            this.height = height;
        }
    }

    public int trapRainWater(int[][] heights) {
        if (heights == null || heights.length == 0 || heights[0].length == 0)
            return 0;

        Queue<Cell> queue = new PriorityQueue<>(1, new Comparator<Cell>() {
            public int compare(Cell a, Cell b) {
                return a.height - b.height;
            }
        });

        int m = heights.length;
        int n = heights[0].length;
        boolean[][] visited = new boolean[m][n];

        // Initially, add all the Cells which are on borders to the queue.
        for (int i = 0; i < m; i++) {
            visited[i][0] = true;
            visited[i][n - 1] = true;
            queue.offer(new Cell(i, 0, heights[i][0]));
            queue.offer(new Cell(i, n - 1, heights[i][n - 1]));
        }

        for (int i = 0; i < n; i++) {
            visited[0][i] = true;
            visited[m - 1][i] = true;
            queue.offer(new Cell(0, i, heights[0][i]));
            queue.offer(new Cell(m - 1, i, heights[m - 1][i]));
        }

        // from the borders, pick the shortest cell visited and check its neighbors:
        // if the neighbor is shorter, collect the water it can trap and update its height as its height plus the water trapped
        // add all its neighbors to the queue.
        int[][] dirs = new int[][] { { -1, 0 }, { 1, 0 }, { 0, -1 }, { 0, 1 } };
        int result = 0;
        while (!queue.isEmpty()) {
            Cell cell = queue.poll();
            for (int[] dir : dirs) {
                int row = cell.row + dir[0];
                int col = cell.col + dir[1];
                if (row >= 0 && row < m && col >= 0 && col < n && !visited[row][col]) {
                    visited[row][col] = true;
                    result += Math.max(0, cell.height - heights[row][col]);
                    queue.offer(new Cell(row, col, Math.max(heights[row][col], cell.height)));
                }
            }
        }

        return result;
    }
}
```

### Pour Water in Histogram

We are given an elevation map, heights[i] representing the height of the terrain at that index. The width at each index is 1. After V units of water fall at index K, how much water is at each index?

```
Input: heights = [2,1,1,2,1,2,2], units = 4, index = 3

#       #
#   w   #
##ww#w###
#########
 0123456  

The final answer is [2,2,2,3,2,2,2]:
```

```java
public int[] pourWaterInHistogram(int[] heights, int units, int index) {
	while (units-- > 0)
		droplet: {
			for (int dir : new int[] { -1, 1 }) {
				// two pointers shift together
				int i = index, j = index + dir, best = index;
				while (0 <= j && j < heights.length && heights[j] <= heights[i]) {
					if (heights[j] < heights[i])
						best = j;
					i = j;
					j += dir;
				}
				// break if found the best
				if (best != index) {
					heights[best]++;
					break droplet;
				}
			}
			heights[index]++;
		}
	return heights;
}

public static void printTerrian() {
		int[] terrain = { 5, 4, 2, 1, 2, 3, 2, 1, 0, 1, 2, 4 };
		int m = Arrays.stream(terrain).max().getAsInt() + 1;
		int n = terrain.length;
		char[][] grid = new char[m][n];
		for (int i = 0; i < m; i++) {
				for (int j = 0; j < n; j++) {
						if (i >= m - terrain[j] - 1)
								grid[i][j] = '+';
						else
								grid[i][j] = ' ';
				}
		}
		for (char[] row : grid) {
				System.out.println(Arrays.toString(row));
		}
}
```

### Candy (Giving Candies)

There are N children standing in a line. Each child is assigned a rating value.

You are giving candies to these children subjected to the following requirements:

Each child must have at least one candy.
Children with a higher rating get more candies than their neighbors.
What is the minimum candies you must give?

```
Example 1:
Input: [1,0,2]
Output: 5
Explanation: You can allocate to the first, second and third child with 2, 1, 2 candies respectively.

Example 2:
Input: [1,2,2]
Output: 4
Explanation: You can allocate to the first, second and third child with 1, 2, 1 candies respectively.
             The third child gets 1 candy because it satisfies the above two conditions.
```

Solution 1: We can make use of a single array candies to keep the count of the number of candies to be allocated to the current student.

```java
public int candy(int[] ratings) {
	int[] candies = new int[ratings.length];
	Arrays.fill(candies, 1);
	for (int i = 1; i < ratings.length; i++) {
		if (ratings[i] > ratings[i - 1]) {
			candies[i] = candies[i - 1] + 1;
		}
	}
	int sum = candies[ratings.length - 1];
	for (int i = ratings.length - 2; i >= 0; i--) {
		if (ratings[i] > ratings[i + 1]) {
			// makes it satisfy both left and right neighbor criteria
			candies[i] = Math.max(candies[i], candies[i + 1] + 1);
		}
		sum += candies[i];
	}
	return sum;
}
```

Solution 2: Single Pass Approach with Constant Space.

```java
public int candy2(int[] ratings) {
	if (ratings == null || ratings.length == 0)
		return 0;
	int start = 0, sum = 0, len = ratings.length;
	while (start < len) {
		if (start + 1 < len && ratings[start] == ratings[start + 1]) {
			sum += 1;
			start++;
			continue;
		}
		// left means the left part of the mountain,right means the right part of the mountain
		int left = 0, right = 0;
		// climbing up
		while (start + 1 < len && ratings[start] < ratings[start + 1]) {
			start++;
			left++;
		}
		// falling down
		while (start + 1 < len && ratings[start] > ratings[start + 1]) {
			start++;
			right++;
		}
		// break for flat point
		if (left == 0 && right == 0) {
			sum += 1;
			break;
		}
		// calculate for mountain
		int max = Math.max(left, right) + 1;
		int leftSum = (1 + left) * left / 2;
		int rightSum = (1 + right) * right / 2 - 1;
		sum += max + leftSum + rightSum;
	}
	return sum;
}
```

### Jump Game

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

Example 1:

```
Input: [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

```java
public boolean canJump(int[] nums) {
	int pos = 0;
	for (int i = 0; i < nums.length; i++) {
		if (pos >= nums.length - 1) {
			return true;
		} else if (i > pos) {
			// can't jump to next
			return false;
		} else {
			pos = Math.max(pos, nums[i] + i);
		}
	}
	return false;
}
```

### Jump Game II

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

Example:

```
Input: [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2.
    Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

```java
public int jump(int[] A) {
	if (A.length < 2)
		return 0;
	int jumps = 0, curEnd = 0, maxEnd = 0;
	for (int i = 0; i < A.length - 1; i++) {
		if (i + A[i] > maxEnd) {
			maxEnd = i + A[i];
			// break it!
			if (maxEnd >= A.length - 1)
				return jumps + 1;
		}
		if (i == curEnd) {
			jumps++;
			curEnd = maxEnd;
		}
	}
	return jumps;
}
```

### Frog Jump

A frog is crossing a river. The river is divided into x units and at each unit there may or may not exist a stone. The frog can jump on a stone, but it must not jump into the water.

Given a list of stones' positions (in units) in sorted ascending order, determine if the frog is able to cross the river by landing on the last stone. Initially, the frog is on the first stone and assume the first jump must be 1 unit.

If the frog's last jump was k units, then its next jump must be either k - 1, k, or k + 1 units. Note that the frog can only jump in the forward direction.

```
Example 1:

[0,1,3,5,6,8,12,17]

There are a total of 8 stones.
The first stone at the 0th unit, second stone at the 1st unit,
third stone at the 3rd unit, and so on...
The last stone at the 17th unit.

Return true. The frog can jump to the last stone by jumping
1 unit to the 2nd stone, then 2 units to the 3rd stone, then
2 units to the 4th stone, then 3 units to the 6th stone,
4 units to the 7th stone, and 5 units to the 8th stone.
```

Solution 1: Using Memorization with Binary Search, Time complexity is O(n^2*log(n))

```java
public boolean canCross(int[] stones) {
	int[][] memo = new int[stones.length][stones.length];
	for (int[] row : memo) {
		Arrays.fill(row, -1);
	}
	return canCross(stones, 0, 0, memo) == 1;
}

public int canCross(int[] stones, int index, int jumpsize, int[][] memo) {
	if (memo[index][jumpsize] >= 0) {
		return memo[index][jumpsize];
	}
	int ind1 = Arrays.binarySearch(stones, index + 1, stones.length, stones[index] + jumpsize);
	if (ind1 >= 0 && canCross(stones, ind1, jumpsize, memo) == 1) {
		memo[index][jumpsize] = 1;
		return 1;
	}
	int ind2 = Arrays.binarySearch(stones, index + 1, stones.length, stones[index] + jumpsize - 1);
	if (ind2 >= 0 && canCross(stones, ind2, jumpsize - 1, memo) == 1) {
		memo[index][jumpsize - 1] = 1;
		return 1;
	}
	int ind3 = Arrays.binarySearch(stones, index + 1, stones.length, stones[index] + jumpsize + 1);
	if (ind3 >= 0 && canCross(stones, ind3, jumpsize + 1, memo) == 1) {
		memo[index][jumpsize + 1] = 1;
		return 1;
	}
	memo[index][jumpsize] = ((index == stones.length - 1) ? 1 : 0);
	return memo[index][jumpsize];
}
```

Solution 2: Using Dynamic Programming, Time complexity is O(n^2), Space complexity can grow up to O(n^2).

We make use of a hashmap which contains key:value pairs such that key refers to the position at which a stone is present and value is a set containing the jump size which can lead to the current stone position.

```java
public boolean canCross2(int[] stones) {
	Map<Integer, Set<Integer>> map = new HashMap<>();
	for (int i = 0; i < stones.length; i++) {
		map.put(stones[i], new HashSet<Integer>());
	}
	map.get(0).add(0);
	for (int i = 0; i < stones.length; i++) {
		// for each stone, calculate next stones it can reach
		for (int k : map.get(stones[i])) {
			for (int step = k - 1; step <= k + 1; step++) {
				if (step > 0 && map.containsKey(stones[i] + step)) {
					map.get(stones[i] + step).add(step);
				}
			}
		}
	}
	return map.get(stones[stones.length - 1]).size() > 0;
}
```



# Reference Resources
- [Source Code on GitHub](https://github.com/codebycase/algorithms-java/blob/master/src/main/java/a10_recursion_greedy_invariant)
