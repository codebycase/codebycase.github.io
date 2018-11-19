---
title: Algorithms 16 - Tricky Java Snippets
key: a16-tricky-code-snippets
tags: Java
---

Collect a list of java code snippets those are tricky and brilliant.

<!-- more -->

### Split a paragraph to words

```java
// Print words and ignore space and punctuations.
StringBuilder word = new StringBuilder();
for (char c : paragraph.toCharArray()) {
	if (Character.isLetter(c)) {
		word.append(c);
	} else if (word.length() > 0) {
		System.out.println(word);
		word.setLength(0);
	}
}
```

<!--more-->

### Count The Repetitions

```java
private int countS2inS1(final char[] s1, final char[] s2) {
	int count = 0;
	int s2Pointer = 0;

	for (final char c1 : s1) {
		if (c1 == s2[s2Pointer]) {
			s2Pointer++;
			if (s2Pointer == s2.length) {
				s2Pointer = 0;
				count++;
			}
		}
	}

	return count;
}
```

### Most Profit with Two Pointers

```java
Arrays.sort(jobs, (a, b) -> (a.difficulty - b.difficulty));
Arrays.sort(works);
// Use a "two pointers" approach to process jobs in order and keep track of best
int ans = 0, i = 0, best = 0;
for (int skill : works) {
	while (i < N && skill >= jobs[i].difficulty) {
		best = Match.max(best, jobs[i++].profit);
	}
	ans += best;
}
```

### Range Sum Query - Immutable

```java
class RangeSumQuery {
	private int[] sum; // cache

	public RangeSumQuery(int[] nums) {
		sum = new int[nums.length + 1];
		for (int i = 0; i < nums.length; i++) {
			sum[i + 1] = sum[i] + nums[i];
		}
	}

	public int sumRange(int i, int j) {
		return sum[j + 1] - sum[i];
	}
}
```

### Integer Break

```java
// e.g. given n = 10, return 36 (10 = 3 + 3 + 4).
// We should choose integers that are closer to e.
// The potential candidates are 3 and 2 since 3 > e > 2
public int integerBreak(int n) {
	if (n == 2)
		return 1;
	if (n == 3)
		return 2;
	if (n == 4)
		return 4;
	int ans = 1;
	while (n > 4) {
		n = n - 3;
		ans = ans * 3;
	}
	return ans * n;
}
```

### Largest Number

Given a list of non negative integers, arrange them such that they form the largest number.

e.g. Input: [3,30,34,5,9]; Output: "9534330"

```java
public String largestNumber(int[] nums) {
	return Arrays.stream(nums).mapToObj(String::valueOf).sorted((a, b) -> (b + a).compareTo(a + b))
			.reduce((a, b) -> "0".equals(a) ? "0" : a + b).get();
}
```

### Maximum Number

Given a list of non negative integers of length `m`, create the maximum number of length `k <= m`.

e.g. Input num = [9, 1, 2, 5, 8, 3], k = 3; Output: 985

```java
// as long as there were enough nums left for left number
public int[] maxNumberArray(int[] nums, int k) {
	int n = nums.length;
	int[] ans = new int[k];
	int j = 0; // pointer of the ans array
	for (int i = 0; i < n; ++i) {
		// assign so-far-max num as front as possible
		while (n - i + j > k && j > 0 && ans[j - 1] < nums[i])
			j--;
		if (j < k)
			ans[j++] = nums[i];
	}
	return ans;
}
```

### Maximum swap

Given a non-negative integer, you could swap two digits at most once to get the maximum valued number. Return the maximum valued number you could get.

Example 1: Input: 2736 Output: 7236 Explanation: Swap the number 2 and the number 7.

```java
public int maximumSwap(int num) {
	char[] A = Integer.toString(num).toCharArray();
	int[] last = new int[10];
	for (int i = 0; i < A.length; i++) {
		last[A[i] - '0'] = i;
	}

	for (int i = 0; i < A.length; i++) {
		for (int d = 9; d > A[i] - '0'; d--) {
			if (last[d] > i) {
				char tmp = A[i];
				A[i] = A[last[d]];
				A[last[d]] = tmp;
				return Integer.valueOf(new String(A));
			}
		}
	}
	return num;
}
```

### String to Integer

"42" -> 42
"    -42" -> -42
"4193 with words" -> 4139
"words and 987" -> 0
"-91283472332" -> -2147483648

```java
public int myAtoi(String str) {
		if (str == null || str.length() == 0)
				return 0;
		str = str.trim();
		int sign = 1, start = 0;
		long sum = 0;
		if (str.charAt(start) == '+') {
				sign = 1;
				start++;
		} else if (str.charAt(start) == '-') {
				sign = -1;
				start++;
		}

		for (int i = start; i < str.length(); i++) {
				if (!Character.isDigit(str.charAt(i)))
						return (int) sum * sign;
				sum = sum * 10 + str.charAt(i) - '0';
				if (sign == 1 && sum > Integer.MAX_VALUE)
						return Integer.MAX_VALUE;
				if (sign == -1 && sign * sum < Integer.MIN_VALUE)
						return Integer.MIN_VALUE;
		}

		return (int) sum * sign;
}
```

### Scan Singly-linked List Conditionally

```java
// Solution One
ListNode node = head;
while (node != null) {
	if (set.contains(node.val) && (node.next == null || !set.contains(node.next.val))) {
		count++;
	}
	node = node.next;
}
// Solution Two
ListNode node = head;
while (node != null) {
	if (set.contains(node.val)) {
		count++;
		// scan to the end of current connected subset component
		while (node.next != null && set.contains(node.next.val))
			node = node.next;
	}
	node = node.next;
}
```

### Binary Tree (Depth-First) Pruning

```java
public TreeNode pruneTree(TreeNode root) {
		return containsNode(root) ? root : null;
}

// remove subtree which is not containing a 1
private boolean containsNode(TreeNode node) {
		if (node == null)
				return false;
		boolean a1 = containsNode(node.left);
		boolean a2 = containsNode(node.right);
		if (!a1) node.left = null;
		if (!a2) node.right = null;
		return node.val == 1 || a1 || a2;
}
```

### Build a Trie Tree with Words

```java
private Node buildTrie(String[] words) {
	Node root = new Node();
	for (String word : words) {
		Node node = root;
		for (char c : word.toCharArray()) {
			int i = c - 'a';
			if (node.next[i] == null) {
				node.next[i] = new Node();
				node.count++;
			}
			node = node.next[i];
		}
		node.word = word;
	}
	return root;
}

class Node {
	Node[] next = new Node[26];
	int count = 0; // count children nodes, zero means a leave
	String word = null;
}
```

### Run Length Encoding

```java
// For "abbcccaaa", we'll write the "key" of "abca", and the "counts" as [1,2,3,3].
class RLE {
	String key;
	List<Integer> counts;

	public RLE(String S) {
		StringBuilder sb = new StringBuilder();
		counts = new ArrayList<>();

		char[] ca = S.toCharArray();
		int N = ca.length;
		int prev = -1;
		for (int i = 0; i < N; ++i) {
			if (i == N - 1 || ca[i] != ca[i + 1]) {
				sb.append(ca[i]);
				counts.add(i - prev);
				prev = i;
			}
		}

		key = sb.toString();
	}
}
```

### Skyline of Row and Col

```java
int N = grid.length;
int[] rowMaxes = new int[N];
int[] colMaxes = new int[N];
for (int r = 0; r < N; ++r) {
	for (int c = 0; c < N; ++c) {
		rowMaxes[r] = Math.max(rowMaxes[r], grid[r][c]);
		colMaxes[c] = Math.max(colMaxes[c], grid[r][c]);
	}
}
```

### Greatest Common Divisor

```java
public static int gcd(int a, int b) {
	if (b == 0)
		return a;
	return gcd(b, a % b);
}
```

### Longest Common Prefix

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string "".

Given ["flower","flow","flight"], The answer is "fl".

_Use vertical scanning, the complexity is O(n*minLen) where minLen is the length of the shortest string in the array._

```java
public String longestCommonPrefix(String[] strs) {
	if (strs == null || strs.length == 0)
		return "";
	for (int i = 0; i < strs[0].length(); i++) {
		char c = strs[0].charAt(i);
		for (int j = 1; j < strs.length; j++) {
			if (i == strs[j].length() || strs[j].charAt(i) != c)
				return strs[0].substring(0, i);
		}
	}
	return strs[0];
}
```

### Shorthand RGB Color

A shorthand hexadecimal RGB color "#1e6" = "#11ee66" = "0x11 * (1 << 16) + 0xee * (1 << 8) + 0x66" = "17 * 1 * (1 << 16) + 17 * 14 * (1 << 8) + 17 * 6" = "1175142"

The reason for 17 is because 0x22 = 2 * 16 + 2 * 1 = 2 * 17.

### Rotate String & Compare

For each rotate of A, let's check if it equals B. After rotate A by s, we should check that A[s] == B[0], A[s + 1] == B[1], A[s + 2] == B[2], etc.

```java
public boolean rotateString(String A, String B) {
	if (A.length() != B.length())
		return false;
	if (A.length() == 0)
		return true;
	search: for (int i = 0; i < A.length(); i++) {
		for (int j = 0; j < A.length(); j++) {
			if (A.charAt((i + j) % A.length()) != B.charAt(j))
				continue search;
		}
		return true;
	}
	return false;
}
```

We can also simply check wether A.length() == B.length() and also B is a substring of A + A.

```java
public boolean rotateString(String A, String B) {
	return (A.length() == B.length()) && ((A + A).contains(B));
}
```

### Recursively Check Last Digit

```java
// Return true if n is good.
// The flag is true iff we have an occurrence of 2, 5, 6, 9.
public boolean goodRotatedDigits(int n, boolean flag) {
	if (n == 0)
		return flag;

	int d = n % 10;
	if (d == 3 || d == 4 || d == 7)
		return false;
	if (d == 0 || d == 1 || d == 8)
		return goodRotatedDigits(n / 10, flag);
	return goodRotatedDigits(n / 10, true);
}
```

### Subarrays with Bounded Max

Subarrys of [2, 1, 4, 3] with bounded max 3 is: [2], [1], [2, 1], [3]

```java
public int countByBoundedMax(int[] A, int bound) {
	int ans = 0, cur = 0;
	for (int x : A) {
		cur = x <= bound ? cur + 1 : 0;
		ans += cur;
	}
	return ans;
}

// Another way to calculate based on bounds
public int numSubarrayBoundedMax(int[] A, int min, int max) {
	int i = 0, count = 0, result = 0;
	for (int j = 0; j < A.length; j++) {
		if (A[j] >= min && A[j] <= max) {
			count = (j - i) + 1;
			result += count;
		} else if (A[j] < min) {
			result += count;
		} else {
			i = j + 1;
			count = 0;
		}

	}
	return result;
}
```

### Unique Paths in Grid

```java
public int findHowManyUniquePathsInGrid2(int m, int n) {
	int[][] grid = new int[m][n];
	for (int i = 0; i < m; i++) {
		for (int j = 0; j < n; j++) {
			if (i == 0 || j == 0)
				grid[i][j] = 1;
			else
				grid[i][j] = grid[i - 1][j] + grid[i][j - 1];
		}
	}
	return grid[m - 1][n - 1];
}
```

### If 2 Words Are Scrambled

A scrambled string of "great" could be "rgeat" or "rgtae".

```java
public boolean isScramble(String s1, String s2) {
	if (s1.equals(s2))
		return true;


	int[] count = new int[26];
	for (int i = 0; i < s1.length(); i++) {
		count[s1.charAt(i) - 'a']++;
		count[s2.charAt(i) - 'a']--;
	}
	for (int i = 0; i < count.length; i++) {
		if (count[i] != 0)
			return false;
	}

	for (int i = 1; i < s1.length(); i++) {
		if (isScramble(s1.substring(0, i), s2.substring(0, i))
				&& isScramble(s1.substring(i), s2.substring(i)))
			return true;
		if (isScramble(s1.substring(0, i), s2.substring(s2.length() - i))
				&& isScramble(s1.substring(i), s2.substring(0, s2.length() - i)))
			return true;
	}

	return false;
}
```

### Nth Ugly Number

```java
// Ugly numbers are positive numbers whose prime factors only include 2, 3, 5.
public int nthUglyNumber(int n) {
	if (n == 1)
		return 1;

	Queue<Long> q = new PriorityQueue<>();
	q.add(1l);

	for (long i = 1; i < n; i++) {
		long tmp = q.poll();
		// take care the duplicates
		while (!q.isEmpty() && q.peek() == tmp)
			tmp = q.poll();
		q.add(tmp * 2);
		q.add(tmp * 3);
		q.add(tmp * 5);
	}

	return q.poll().intValue();
}
```

### Return Trie Keys with Prefix

```java
/**
 * Returns all of the keys in the Trie tree that start with prefix.
 */
public Iterable<String> keysWithPrefix(String prefix) {
	Queue<String> results = new LinkedList<String>();
	Node x = get(root, prefix, 0);
	collect(x, new StringBuilder(prefix), results);
	return results;
}

// Recursion
private Node get(Node x, String prefix, int d) {
	if (x == null)
		return null;
	if (d == prefix.length())
		return x;
	char c = prefix.charAt(d);
	return get(x.next[c], prefix, d + 1);
}

// Iteration
private Node get(Node x, String prefix) {
	Node node = x;
	for (char c : prefix.toCharArray()) {
		node = node.next[c];
		if (node == null)
			return null;
	}
	return node;
}

private void collect(Node x, StringBuilder prefix, Queue<String> results) {
	if (x == null)
		return;
	if (x.isString)
		results.offer(prefix.toString());
	for (char c = 0; c < R; c++) {
		prefix.append(c);
		collect(x.next[c], prefix, results);
		prefix.deleteCharAt(prefix.length() - 1);
	}
}
```

### Rotate Image

You are given an n x n 2D matrix representing an image.

Rotate the image by 90 degrees (clockwise).

Example:

```
Given input matrix =
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

rotate the input matrix in-place such that it becomes:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```

```java
public void rotate(int[][] matrix) {
	int m = matrix.length;
	int n = matrix[0].length;
	// first swap symmetry
	for (int i = 0; i < m; i++) {
		for (int j = i; j < n; j++) {
			int temp = matrix[i][j];
			matrix[i][j] = matrix[j][i];
			matrix[j][i] = temp;
		}
	}
	// second reverse left to right
	for (int i = 0; i < m; i++) {
		for (int j = 0; j < n / 2; j++) {
			int temp = matrix[i][j];
			matrix[i][j] = matrix[i][n - 1 - j];
			matrix[i][m - 1 - j] = temp;
		}
	}
}
```

### Valid Anagram

Given two strings s and t , write a function to determine if t is an anagram of s.

Example 1: Input: s = "anagram", t = "nagaram" Output: true
Example 2: Input: s = "rat", t = "car" Output: false

_Either use 26 prime numbers, or count to int[26]._

```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length())
        return false;
    int[] table = new int[26];
    for (int i = 0; i < s.length(); i++)
        table[s.charAt(i) - 'a']++;
    for (int i = 0; i < t.length(); i++) {
        table[t.charAt(i) - 'a']--;
				// return when more chars found
        if (table[t.charAt(i) - 'a'] < 0)
            return false;
    }
    return true;
}
```

### Sum of Square Numbers

Given a non-negative integer c, your task is to decide whether there're two integers a and b such that a2 + b2 = c.

Complexity is: O(sqrt(c)log(c))

```java
public boolean judgeSquareSum(int c) {
		for (long a = 0; a * a <= c; a++) {
				double b = Math.sqrt(c - a * a);
				if (b == (int) b)
						return true;
		}
		return false;
}
```

### Sparse Matrix Multiplication

Example:

```
Input:

A = [
  [ 1, 0, 0],
  [-1, 0, 3]
]

B = [
  [ 7, 0, 0 ],
  [ 0, 0, 0 ],
  [ 0, 0, 1 ]
]

Output:

     |  1 0 0 |   | 7 0 0 |   |  7 0 0 |
AB = | -1 0 3 | x | 0 0 0 | = | -7 0 3 |
                  | 0 0 1 |
```

```java
public int[][] matrixMultiply(int[][] A, int[][] B) {
	int rowsA = A.length, colsA = A[0].length, colsB = B[0].length;
	int[][] result = new int[rowsA][colsB];
	for (int xA = 0; xA < rowsA; xA++) {
		for (int yA = 0; yA < colsA; yA++) {
			if (A[xA][yA] != 0) {
				for (int yB = 0; yB < colsB; yB++) {
					result[xA][yB] += A[xA][yA] * B[yA][yB];
				}
			}
		}
	}
	return result;
}
```

### Find the Celebrity

```java
// two pass
public int findCelebrity(int n) {
		int candidate = 0;
		for(int i = 1; i < n; i++){
				if(knows(candidate, i))
						candidate = i;
		}
		for(int i = 0; i < n; i++){
				if(i != candidate && (knows(candidate, i) || !knows(i, candidate))) return -1;
		}
		return candidate;
}
```

### Find Pivot Index

Given an array of integers nums, write a method that returns the "pivot" index of this array.

We define the pivot index as the index where the sum of the numbers to the left of the index is equal to the sum of the numbers to the right of the index.

If no such index exists, we should return -1. If there are multiple pivot indexes, you should return the left-most pivot index.

Example 1:

```
Input:
nums = [1, 7, 3, 6, 5, 6]
Output: 3
Explanation:
The sum of the numbers to the left of index 3 (nums[3] = 6) is equal to the sum of numbers to the right of index 3.
Also, 3 is the first index where this occurs.
```

```java
public int pivotIndex(int[] nums) {
		int sum = Arrays.stream(nums).sum();
		int leftSum = 0;
		for (int i = 0; i < nums.length; i++) {
				if (leftSum == sum - leftSum - nums[i])
						return i;
				leftSum += nums[i];
		}
		return -1;
}
```

## Other Leetcode Questions:

- [Image Overlap](https://leetcode.com/problems/image-overlap/description/) *
- [Shortest Distance to a Character](https://leetcode.com/problems/shortest-distance-to-a-character/description/) *
- [Short Encoding of Words](https://leetcode.com/problems/short-encoding-of-words/solution/)
- [Find And Replace in String](https://leetcode.com/problems/find-and-replace-in-string/description/) **
- [Consecutive Numbers Sum](https://leetcode.com/problems/consecutive-numbers-sum/description/) **
- [Unique Letter String](https://leetcode.com/problems/unique-letter-string/solution/) **
- [Binary Trees With Factors](https://leetcode.com/problems/binary-trees-with-factors/description/) *****
  - Dynamic programming with index lookup, use long to avoid overflow.
- [Friends Of Appropriate Ages](https://leetcode.com/problems/friends-of-appropriate-ages/description/) ***
  - Bucket ages and compare between buckets, also deduct the same age case.
- [Sum of Distances in Tree](https://leetcode.com/problems/sum-of-distances-in-tree/description/) ****
  - Find answer for root first, use the second pre-order traversal to update others


# Reference Resources
- [Source Code on GitHub](https://github.com/codebycase/algorithms-java/tree/master/src/main/java/a19_tricky_java_snippets)
