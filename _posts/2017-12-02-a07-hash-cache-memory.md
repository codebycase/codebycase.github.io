---
layout: article
title: Algorithms - Hash/Cache and Memory
key: a07-hash-cache-memory
categories: Algorithms
tags: Hash, Cache, Memory
---

## Hash Tables

A hash table is a data structure that maps keys to values for highly efficient lookups, inserts and deletes (average **O(1 + n/m)** time complexity).

This is a simple implementation by using an array of linked lists and a hash code function:

1. First, use a hash function to compute the hash code from the key, which will usually be an int or long. Note that two different keys could have the same hash code, as there may be an infinite number of keys and a finite number of ints.
  - It should be consistent - equal key must produce the same hash value.
  - It should be efficient to compute.
  - It should uniformly distribute the set of keys.

2. Then, map the hash code to an index in the array. This could be done with something like _hash(key) % array_length_. Two different hash codes could, of course, map to the same index (bucket).

3. If two keys map to the same location/slot/index, a **collision** is said to occur. To deal with collision, at this index, there is a singly linked list of entries which have the keys and values. (_with Java8, large linked list will be dynamically replaced with a balanced binary search tree (like red-black BST) to gain the lookup time from O(n) to O(log(n))_)

4. To retrieve the value pair by its key, we need to repeat this process. Compute the hash code from the key, and then compute the index from the hash code. Then search through the linked list for the value with this key.

<!--more-->

- If the hash function does a good job of spreading objects across the underlying array and take O(1) time to compute, on average, lookups, insertions, and deletions have O(1 + n/m) time complexity. where n is the number of stored items and m is the array's size. If the load factor *n/m* exceeds some threshold (e.g. 0.75), rehashing can be applied to the hash table. A new array with a larger number of locations is allocated, and the objects are moved to the new array. *Rehashing* is expensive (**O(n + m)** time), but if it is done infrequently (e.g. whenever the number of entries doubles). its amortized cost is low. _Even for realtime systems, a separate thread can do the rehashing_.

- A hash function has one hard requirement--equal keys should have equal hash codes; a softer requirement is it should spread keys uniformly distributed across underlying array, plus should be efficient to compute. As a rule, you should avoid using mutable objects as keys. _If you have to update a key, first remove it, then update it, and finally add it back_--this ensures it's moved to the correct array location.

There are various ways of collision resolution. Basically, there are two different strategies:  

- Closed addressing (open hashing). Each slot of the hash table contains a linked list, which stores key-value pairs with the same hash. When collision occurs, this data structure is searched for key-value pair, which matches the key.  
- Open addressing (closed hashing). Each slot actually contains a key-value pair. When collision occurs, open addressing algorithm calculates another location (i.e. next one) to locate a free slot. This strategy might experience drastic performance decrease, when table is tightly filled (load factor is 0.7 or more). e.g. LinearProbingHashST.

Alternatively, we can implement the hash table with a balanced binary search tree. This give us an O(logN) insert/search/delete time. The advantage of this is potentially using less space, since we no longer allocate a large array. We can also iterate through the keys in order, which can be useful sometimes.

  - In Java libraries, TreeMap uses a red-black BST, their implementation maintains three pointers (two children and parent) for each node.
  - HashMap uses a hash table with separate chaining (linked list). The table size is a power of 2 (instead of a prime), this replaces a relatively expensive % M operation with AND. Default load factor = 0.75. To guard against some poorly written hash function.
  - IdentifyHashMap uses reference-equality in place of object-equality, it is roughly equivalent to our LinearProbingHashST with a load factor of 2/3.

The memory usage for a hashing algorithm with SequentialSearchST is **48n + 24** bytes. The 24 bytes arises from the usual 16 bytes of object overhead plus one 8-byte reference (first). There are also a total of n Node objects, each with the usual 16 bytes of object overhead, 8 bytes of extra object overhead (because Node is a non-static nested class), and 24 bytes for 3 references (key, value, and next).

The memory usage (ignoring the memory for the keys and values) for a hashing algorithm with RedBlackBST implementation is **~64n** bytes. There are a total of n Node objects, each with the usual 16 bytes of object overhead, 8 bytes of extra object overhead (because Node is a non-static nested class), 32 bytes for 4 references (key, value, left, and right), 4 bytes for the subtree count n, 1 byte for the color bit color, plus 3 bytes of padding.

The advantages of hashing over BST implementations are that the code is simpler and search times are optimal (constant). If the keys are of a standard type or are sufficiently simple that we can be confident of using hashing. The advantages of BSTs over hashing are that they are based on a simpler abstract interface (no hash function need be designed); red-blank BSTs can provide guaranteed worst-case performance; also support a wider range of operations (rank, select, sort and range search).

|algorithm (data structure)|search (worst)|insert (worst)|search (average)|insert (average)|key interface|memory (bytes)|
|:-----------------------------:|:------------:|:-----------:|:--------------:|:---------------:|:-----------:|:------------:|
|sequential search (unordered list)|$$n$$|$$n$$|$$n/2$$|$$n$$|equals()|$$48n$$|
|binary search (ordered array)|$$\lg n$$|$$2n$$|$$\lg n$$|$$n/2$$|compareTo()|$$16n$$|
|binary tree search (BST)|$$n$$|$$n$$|$$1.39\lg n$$|$$1.39\lg n$$|compareTo()|$$64n$$|
|2-3 tree search (red-black BST)|$$2\lg n$$|$$2\lg n$$|$$1.00\lg n$$|$$1.00\lg n$$|compareTo()|$$64n$$|
|separate chaining (array of lists)|$$n$$|$$n$$|$$n/(2m)$$|$$n/m$$|equals() hashCode()|$$48n + 32m$$|
|linear probing (parallel arrays)|$$n$$|$$n$$|$$< 1.50$$|$$< 2.50$$|equals() hashCode()|between $$32n$$ and $$128n$$|

```
Hash tables.
・Simpler to code.
・No effective alternative for unordered keys.
・Faster for simple keys (a few arithmetic ops versus log N compares).
・Better system support in Java for strings (e.g., cached hash code).
Balanced search trees.
・Stronger performance guarantee.
・Support for ordered ST operations. (Navigation)
・Easier to implement compareTo() correctly than equals() and hashCode().
```

### Design a Hash Function

- Modular Hashing: The hash function is simply h(k) = k mod m for some m (usually, the number of buckets). The value k is an integer hash code generated from the key. If m is a power of two (i.e., m=2^p), then h(k) is just the p lowest-order bits of k. Can just use AND operation. Like Java HashMap is using the power of 2 strategy for table size.

```java
// (newCap = oldCap << 1) < MAXIMUM_CAPACITY
int index = (capacity - 1) & root.hash;
```

- Multiplicative Hashing: A faster but often misused alternative is multiplicative hashing, in which the hash index is computed as ⌊m * frac(ka)⌋. Here k is again an integer hash code, a is a real number and frac is the function that returns the fractional part of a real number.

- Cyclic Redundancy Checks (CRCs): For a longer stream of serialized key data, a cyclic redundancy check (CRC) makes a good, reasonably fast hash function. Fast software CRC algorithms rely on precomputed tables of data. As a rule of thumb, CRCs are about 3-4 times slower than multiplicative hashing.

- Cryptographic Hash Functions: Which try to make it computationally infeasible to invert them: if you know h(x), there is no way to compute x that is asymptotically faster than just trying all possible values and see which one hashes to the right result. Usually these functions also try to make it hard to find different values of x that cause collisions; they are collision-resistant. Like MD5 and SHA-1, MD5 is about twice as slow as CRC.

- Precomputing Hash Codes: High-quality hash functions can be expensive. If the same values are being hashed repeatedly, one trick is to **precompute** their hash codes and **store** them with the value. If the hash code is long and the hash function is high-quality (e.g., 64+ bits of a properly constructed MD5 digest), two keys with the same hash code are almost certainly the same value.


### Hash Function for String

_The hash code for a String can be computed as: s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]_

```java
public int hashCode() {
    int h = hash;
    if (h == 0 && value.length > 0) {
        char val[] = value;

        for (int i = 0; i < value.length; i++) {
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
```

### Implement own hashCode()

```java
public class Transaction {
  private String who;
  private Date when;
  private double amount;

  public int hashCode() {
    int hash = 1;
    hash = 31 * hash + who.hashCode();
    hash = 31 * hash + when.hashCode();
    hash = 31 * hash + ((Double) double).hashCode();
    return hash;
  }

}
```

### Implement a Hash Table

_Design HashMap, use Singly Linked List_

```java
public class HashTable<K, V> {
	// Some VMs reserve some header words in an array
	private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
	// The hash table data
	private transient Entry<?, ?>[] table;
	// The total number of entries in the hash table
	private transient int count;
	// The table is rehashed when it's size exceeds this threshold (capacity * loadFactor)
	private int threshold;
	// The load factor for the hash table
	private float loadFactor;

	public HashTable() {
		int capacity = 10;
		table = new Entry<?, ?>[1];
		loadFactor = 0.75f;
		threshold = (int) Math.min(capacity * loadFactor, MAX_ARRAY_SIZE + 1);
	}

	public int size() {
		return count;
	}

	public boolean containsValue(Object value) {
		Entry<?, ?> tab[] = table;
		for (int i = tab.length; i-- > 0;) {
			for (Entry<?, ?> e = tab[i]; e != null; e = e.next) {
				if (e.value.equals(value)) {
					return true;
				}
			}
		}
		return false;
	}

	// Compare both hash and key due to different key could have same hash code
	public boolean containsKey(Object key) {
		Entry<?, ?> tab[] = table;
		int hash = key.hashCode();
		// masks off the sign bit first and then compute the remainder
		int index = (hash & 0x7FFFFFFF) % tab.length;
		for (Entry<?, ?> e = tab[index]; e != null; e = e.next) {
			if ((e.hash == hash) && e.key.equals(key)) {
				return true;
			}
		}
		return false;
	}

	@SuppressWarnings("unchecked")
	public V get(Object key) {
		Entry<?, ?> tab[] = table;
		int hash = key.hashCode();
		int index = (hash & 0x7FFFFFFF) % tab.length;
		for (Entry<?, ?> e = tab[index]; e != null; e = e.next) {
			if ((e.hash == hash) && e.key.equals(key)) {
				return (V) e.value;
			}
		}
		return null;
	}

	public V put(K key, V value) {
		if (value == null)
			throw new NullPointerException();

		Entry<?, ?> tab[] = table;
		int hash = key.hashCode();
		int index = (hash & 0x7FFFFFFF) % tab.length;
		@SuppressWarnings("unchecked")
		Entry<K, V> entry = (Entry<K, V>) tab[index];
		for (; entry != null; entry = entry.next) {
			if ((entry.hash == hash) && entry.key.equals(key)) {
				V old = entry.value;
				entry.value = value;
				return old;
			}
		}

		addEntry(hash, key, value, index);
		return null;
	}

	private void addEntry(int hash, K key, V value, int index) {
		Entry<?, ?> tab[] = table;
		if (count >= threshold) {
			rehash();
			tab = table;
			hash = key.hashCode();
			index = (hash & 0x7FFFFFFF) % tab.length;
		}
		@SuppressWarnings("unchecked")
		Entry<K, V> e = (Entry<K, V>) tab[index];
		tab[index] = new Entry<>(hash, key, value, e);
		count++;
	}

	@SuppressWarnings("unchecked")
	private void rehash() {
		int oldCapacity = table.length;
		Entry<?, ?>[] oldMap = table;

		// overflow-conscious code
		int newCapacity = (oldCapacity << 1) + 1;
		if (newCapacity - MAX_ARRAY_SIZE > 0) {
			if (oldCapacity == MAX_ARRAY_SIZE)
				// Keep running with max buckets
				return;
			newCapacity = MAX_ARRAY_SIZE;
		}
		Entry<?, ?>[] newMap = new Entry<?, ?>[newCapacity];

		threshold = (int) Math.min(newCapacity * loadFactor, MAX_ARRAY_SIZE);
		table = newMap;

		for (int i = oldCapacity; i-- > 0;) {
			for (Entry<K, V> old = (Entry<K, V>) oldMap[i]; old != null;) {
				Entry<K, V> e = old;
				old = old.next;

				int index = (e.hash & 0x7FFFFFFF) % newCapacity;
				e.next = (Entry<K, V>) newMap[index];
				newMap[index] = e;
			}
		}
	}

	private static class Entry<K, V> implements Map.Entry<K, V> {
		final int hash;
		final K key;
		V value;
		Entry<K, V> next;

		protected Entry(int hash, K key, V value, Entry<K, V> next) {
			this.hash = hash;
			this.key = key;
			this.value = value;
			this.next = next;
		}

		public K getKey() {
			return key;
		}

		public V getValue() {
			return value;
		}

		public V setValue(V value) {
			if (value == null)
				throw new NullPointerException();
			V oldValue = this.value;
			this.value = value;
			return oldValue;
		}

		public boolean equals(Object o) {
			if (!(o instanceof Map.Entry))
				return false;
			Map.Entry<?, ?> e = (Map.Entry<?, ?>) o;
			return (key == null ? e.getKey() == null : key.equals(e.getKey()))
					&& (value == null ? e.getValue() == null : value.equals(e.getValue()));
		}

		public int hashCode() {
			return hash ^ Objects.hashCode(value);
		}

		public String toString() {
			return key.toString() + "=" + value.toString();
		}

		@SuppressWarnings("unchecked")
		protected Object clone() {
			return new Entry<>(hash, key, value, (next == null ? null : (Entry<K, V>) next.clone()));
		}
	}
}
```

## HashTable Boot Camp

### Group Anagrams

Given an array of strings, group anagrams together.  
For example, given: ["eat", "tea", "tan", "ate", "nat", "bat"],  
Return:  
<pre>
[
  ["ate", "eat","tea"],
  ["nat","tan"],
  ["bat"]
]
</pre>
Note: All inputs will be in lower-case.

_The first thought is to sort the string, and use it as the key to group strings in a map. Sorting all keys has time complexity O(nm(log(m))). Another solution is to use **prime** numbers, which can reduce to an O(nm) algorithm._

```java
public List<List<String>> groupAnagrams(String[] strs) {
  int[] prime = { 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103 };

  List<List<String>> result = new ArrayList<>();
  if (strs == null || strs.length == 0)
    return result;

  Map<Integer, List<String>> map = new HashMap<>();
  for (String s : strs) {
    int key = 1;
    for (char c : s.toCharArray()) {
      key *= prime[c - 'a'];
    }
    if (!map.containsKey(key))
      map.put(key, new ArrayList<>());
    map.get(key).add(s);
  }
  result.addAll(map.values());
  return result;
}

// Count the characters!
public List<List<String>> groupAnagrams2(String[] strs) {
    if (strs.length == 0 || strs.length == 0)
        return new ArrayList<>();
    Map<String, List<String>> ans = new HashMap<>();
    int[] count = new int[26];
    for (String s : strs) {
        Arrays.fill(count, 0);
        for (char c : s.toCharArray())
            count[c - 'a']++;
        StringBuilder sb = new StringBuilder("");
        for (int i = 0; i < 26; i++) {
            sb.append('#');
            sb.append(count[i]);
        }
        String key = sb.toString();
        // String key = Arrays.toString(count);
        if (!ans.containsKey(key))
            ans.put(key, new ArrayList<>());
        ans.get(key).add(s);
    }
    return new ArrayList<>(ans.values());
}
```

### Longest Distinct Substring

Given a string, find the length of the longest substring without repeating characters.

Examples: Given "abcabcbb", the answer is "abc", which the length is 3.

```java
public int longestDistinctSubset(String s) {
    if (s.length() == 0)
        return 0;
    Map<Character, Integer> map = new HashMap<Character, Integer>();
    int max = 0, l = 0;
    for (int r = 0; r < s.length(); r++) {
        char c = s.charAt(r);
        if (map.containsKey(c)) {
            // need to track max, otherwise "abba" will fail
            l = Math.max(l, map.get(c) + 1);
        }
        map.put(c, r);
        max = Math.max(max, r - l + 1);
    }
    return max;
}
```

### Longest K Distinct Substring

Given a string, find the length of the longest substring T that contains at most k distinct characters.

For example, Given s = “eceba” and k = 2, T is "ece" which its length is 3.

_Use a sliding window and hash map to count the distinct characters and their last occurrence._

```java
public int lengthOfLongestSubstringKDistinct(String str, int distinct) {
  if (str == null || str.length() == 0 || distinct <= 0)
    return 0;
  Map<Character, Integer> counter = new HashMap<>();
  int maxLen = 0, lo = 0;
  for (int hi = 0; hi < str.length(); hi++) {
    counter.compute(str.charAt(hi), (k, v) -> v == null ? 1 : v + 1);
    while (counter.size() > distinct) {
      counter.compute(str.charAt(lo), (k, v) -> v - 1);
      counter.remove(str.charAt(lo), 0);
      lo++;
    }
    maxLen = Math.max(maxLen, hi - lo + 1);
  }
  return maxLen;
}
```

Similar question as Fruit Into Baskets

```java
public int totalFruit(int[] tree) {
  Map<Integer, Integer> counter = new HashMap<>();
  int ans = 0, l = 0;
  for (int r = 0; r < tree.length; r++) {
    counter.compute(tree[r], (k, v) -> v == null ? 1 : v + 1);
    while (counter.size() > 2) {
      counter.compute(tree[l], (k, v) -> v - 1);
      if (counter.get(tree[l]) == 0)
        counter.remove(tree[l]);
      l++;
    }
    ans = Math.max(ans, r - l + 1);
  }
  return ans;
}
```

### Longest K Repeating Substring

Find the length of the longest substring T of a given string (consists of lowercase letters only) such that every character in T appears no less than k times.

For example, given s = "ababbc", k = 2. The result is 5 because the longest substring is "ababb", as 'a' is repeated 2 times and 'b' is repeated 3 times.

```java
public int longestSubstring(String s, int k) {
  return helper(s.toCharArray(), 0, s.length(), k);
}

public int helper(char[] s, int left, int right, int k) {
  if (right - left < k)
    return 0;
  int[] count = new int[26];
  for (int i = left; i < right; i++)
    count[s[i] - 'a']++;
  for (int i = left; i < right; i++) {
    // find the range (i, j) which has repeated characters < k
    // then exclude them with next level search!
    if (count[s[i] - 'a'] < k) {
      int j = i + 1;
      while (j < right && count[s[j] - 'a'] < k)
        j++;
      return Math.max(helper(s, left, i, k), helper(s, j, right, k));
    }
  }
  return right - left;
}
```

### Longest Consecutive Range

Given an unsorted array of integers, find the length of the longest consecutive elements sequence.

For example,  
Given [100, 4, 200, 1, 3, 2],  
The longest consecutive elements sequence is [1, 2, 3, 4]. Return its length: 4.  

Your algorithm should run in O(n) complexity.

_Store the numbers in a hash set to allow O(1) lookups. We only attempt to build sequences for the numbers that are not already part of a longer sequence. This can be accomplished by two ways:  
- Expand the current number in each direction by doing lookups in the hash set. **Remove** matched numbers.  
- Only work on the number that its immediate precedence is NOT in the hash set, as this number would necessarily be part of a longer sequence._

```java
public int longestConsecutive(int[] nums) {
  Set<Integer> set = new HashSet<>();
  for (int num : nums) {
    set.add(num);
  }

  int maxLength = 0;

  for (int num : set) {
    if (!set.contains(num - 1)) {
      int currentNum = num;
      int currentLength = 1;

      while (set.contains(currentNum + 1)) {
        currentNum += 1;
        currentLength += 1;
      }

      maxLength = Math.max(maxLength, currentLength);
    }
  }

  return maxLength;
}
```

### Minimum Window Subset

Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).

<pre>
For example,
S = "ADOBECODEBANC"
T = "ABC"
Minimum window is "BANC".
</pre>

Note:
If there is no such window in S that covers all characters in T, return the empty string "".

If there are multiple such windows, you are guaranteed that there will always be only one unique minimum window in S.

_For most substring problem, we are given a string and need to find a substring of it which satisfy some restrictions. A general way is to use a hash map assisted with two pointers. You can also use an extra queue to track the matched characters' last occurrences._

```java
public String minWindowSubset(String s, String t) {
  if (s == null || s.length() == 0 || t == null || t.length() == 0 || s.length() < t.length())
    return "";

  int counter = t.length(), start = -1, end = s.length();
  Deque<Integer> queue = new LinkedList<>();
  Map<Character, Integer> map = new HashMap<>();

  // count target's characters
  for (char c : t.toCharArray()) {
    map.put(c, map.getOrDefault(c, 0) + 1);
  }

  for (int i = 0; i < s.length(); i++) {
    char c = s.charAt(i);
    if (!map.containsKey(c))
      continue;

    // track position and count down
    int n = map.get(c);
    queue.add(i);
    map.put(c, n - 1);
    if (n > 0)
      counter--;

    // keep all counts <= 0, means T is all covered
    // remove the old/duplicate char index if any
    char head = s.charAt(queue.peek());
    while (map.get(head) < 0) {
      queue.poll();
      map.put(head, map.get(head) + 1);
      head = s.charAt(queue.peek());
    }

    if (counter == 0) {
      int newLen = queue.peekLast() - queue.peek() + 1;
      if (newLen < end - start) {
        start = queue.peek();
        end = queue.peekLast() + 1;
      }
    }

  }

  if (counter == 0)
    return s.substring(start, end);
  else
    return "";
}
```

_Concise java implementation_

```java
public String minWindow(String s, String t) {
  int[] map = new int[128];
  for (char c : t.toCharArray())
    map[c]++;
  int counter = t.length(), begin = 0, end = 0, distance = Integer.MAX_VALUE, head = 0;
  while (end < s.length()) {
    if (map[s.charAt(end++)]-- > 0)
      counter--;
    while (counter == 0) { // valid
      if (end - begin < distance)
        distance = end - (head = begin);
      if (map[s.charAt(begin++)]++ == 0)
        counter++; // make it invalid
    }
  }
  return distance == Integer.MAX_VALUE ? "" : s.substring(head, head + distance);
}
```

### Minimum Window Substring

Given strings S and T, find the minimum (contiguous) substring W of S, so that T is a subsequence of W.

If there is no such window in S that covers all characters in T, return the empty string "". If there are multiple such minimum-length windows, return the one with the left-most starting index.

<pre>
Example 1:
Input:
S = "abcdebdde", T = "bde"
Output: "bcde"
Explanation:
"bcde" is the answer because it occurs before "bdde" which has the same length.
"deb" is not a smaller window because the elements of T in the window must occur in order.
</pre>

_We can either use simple iterative searching to narrow down or take advantage of dynamic programming._

```java
// Iterative searching to narrow down range
public String minWindowSubsequence(String S, String T) {
  String output = "";
  int minLen = 20001;
  for (int i = 0; i <= S.length() - T.length(); i++) {
    while (i < S.length() && S.charAt(i) != T.charAt(0)) {
      i++;
    }
    int l = find(S.substring(i, Math.min(i + minLen, S.length())), T);
    if (l != -1 && l < minLen) {
      minLen = l;
      output = S.substring(i, i + l);
    }
  }
  return output;
}

private int find(String S, String T) {
  for (int i = 0, j = 0; i < S.length() && j < T.length();) {
    if (S.charAt(i) == T.charAt(j)) {
      i++;
      j++;
      if (j == T.length()) {
        return i;
      }
    } else {
      i++;
    }
  }
  return -1;
}
```

_Regarding dynamic programming, for substring S[0, i] and T[0, j], dp[i] is starting index k of the shortest postfix of S[0, i], such that T[0, j] is a subsequence of S[k, i]. Here T[0] = S[k], T[j] = S[i]. Otherwise, dp[i] = -1._

```java
// Dynamic programming to track indices
public String minWindowSubsequence2(String S, String T) {
  int m = S.length(), n = T.length();
  int[] dp = new int[m];
  Arrays.fill(dp, -1);
  for (int i = 0; i < m; i++) {
    if (S.charAt(i) == T.charAt(0))
      dp[i] = i;
  }
  for (int j = 1; j < n; j++) {
    int k = -1;
    int[] tmp = new int[m];
    Arrays.fill(tmp, -1);
    for (int i = 0; i < m; i++) {
      if (k != -1 && S.charAt(i) == T.charAt(j))
        tmp[i] = k;
      if (dp[i] != -1)
        k = dp[i];
    }
    dp = tmp; // swap it!
  }
  int start = -1, length = Integer.MAX_VALUE;
  // check the last row
  for (int i = 0; i < m; i++) {
    if (dp[i] != -1 && i - dp[i] + 1 < length) {
      start = dp[i];
      length = i - dp[i] + 1;
    }
  }
  return start == -1 ? "" : S.substring(start, start + length);
}
```

## Cache

### Design LRU Cache

Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: get and put.

get(key) - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.  
put(key, value) - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

Could you do both operations in O(1) time complexity?

Example:
<pre>
LRUCache cache = new LRUCache(2);
cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // returns 1
cache.put(3, 3);    // evicts key 2
cache.get(2);       // returns -1 (not found)
cache.put(4, 4);    // evicts key 1
cache.get(1);       // returns -1 (not found)
cache.get(3);       // returns 3
cache.get(4);       // returns 4
</pre>

_Maintain a double linked list of nodes, and a hash table to quick looks up nodes. Each time an node is looked up and is found in the hash table, it is moved to the head, when the length of the map exceeds n, when add a new node, remove the tail first._

```java
public class LRUCache {
	private Map<Integer, Node> cacheMap;
	private Node head, tail;
	private int capacity;

	public LRUCache(int capacity) {
		if (capacity <= 0)
			throw new IllegalArgumentException();
		this.capacity = capacity;
		cacheMap = new HashMap<>();
		head = new Node(0, 0);
		tail = new Node(0, 0);
		head.next = tail;
		tail.prev = head;
	}

	public int get(int key) {
		if (cacheMap.containsKey(key)) {
			Node node = cacheMap.get(key);
			deleteNode(node);
			addToHead(node);
			return node.value;
		}
		return -1;
	}

	public void put(int key, int value) {
		if (cacheMap.containsKey(key)) {
			Node node = cacheMap.get(key);
			node.value = value;
			deleteNode(node);
			addToHead(node);
		} else {
			Node node = new Node(key, value);
			cacheMap.put(key, node);
			if (cacheMap.size() > capacity) {
				cacheMap.remove(tail.prev.key);
				deleteNode(tail.prev);
			}
			addToHead(node);
		}
	}

	private void deleteNode(Node node) {
		node.prev.next = node.next;
		node.next.prev = node.prev;
	}

	private void addToHead(Node node) {
		node.next = head.next;
		node.next.prev = node;
		node.prev = head;
		head.next = node;
	}

	class Node {
		int key, value;
		Node prev, next;

		public Node(int key, int value) {
			this.key = key;
			this.value = value;
		}
	}
}
```
_The Java language provides the class LinkedHashMap, which can be used for this solution easily._

```java
public class LRUCache<K, V> {
  private Map<K, V> map;
  private int maxCapacity; // cache capacity

  public LRUCache(int maxCapacity) {
    // Keys are sorted on the basis of access order e.g Invoking the put, putIfAbsent, get,
    // getOrDefault, compute, computeIfAbsent, computeIfPresent, or merge methods results in an access
    // to the corresponding entry.
    map = new LinkedHashMap<K, V>(maxCapacity, 0.75f, true) {
      private static final long serialVersionUID = 1L;
      // triggered by put and putAll
      protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > maxCapacity;
      }
    };
  }

  public V get(K key) {
    return map.getOrDefault(key, null);
  }

  public void set(K key, V value) {
    map.put(key, value);
  }
}
```

### Design LFU Cache

Design and implement a data structure for Least Frequently Used (LFU) cache. It should support the following operations: get and put.

get(key) - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.  
put(key, value) - Set or insert the value if the key is not already present. When the cache reaches its capacity, it should invalidate the least frequently used item before inserting a new item. For the purpose of this problem, when there is a tie (i.e., two or more keys that have the same frequency), the least recently used key would be evicted.

Could you do both operations in O(1) time complexity?

Example:
<pre>
LFUCache cache = new LFUCache(2);
cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // returns 1
cache.put(3, 3);    // evicts key 2
cache.get(2);       // returns -1 (not found)
cache.get(3);       // returns 3.
cache.put(4, 4);    // evicts key 1.
cache.get(1);       // returns -1 (not found)
cache.get(3);       // returns 3
cache.get(4);       // returns 4
</pre>

_Use one hash map to store <key, value> pair, another one to store <key, node>, also use double linked list to keep the frequent of each key. In each node, keys with the same count are saved into linked hash set to keep in order._

_The two hash maps can actually be combined to one by storing <key, entry<value, node>> pair._

```java
public class LFUCache {
	private int capacity = 0;
	private Node head = null;
  // key - value pair
	private HashMap<Integer, Integer> valueHash = new HashMap<Integer, Integer>();
  // key - node pair, here node has frequency of each key
	private HashMap<Integer, Node> nodeHash = new HashMap<Integer, Node>();

	public LFUCache(int capacity) {
		this.capacity = capacity;
	}

	public int get(int key) {
		if (valueHash.containsKey(key)) {
			increaseCount(key);
			return valueHash.get(key);
		}
		return -1;
	}

	public void set(int key, int value) {
		if (capacity == 0)
			return;
		if (valueHash.containsKey(key)) {
			valueHash.put(key, value);
		} else {
			if (valueHash.size() >= capacity)
				removeLeastUsed();
			valueHash.put(key, value);
			addToHead(key);
		}
		increaseCount(key);
	}

	private void addToHead(int key) {
		if (head == null) {
			head = new Node(0);
			head.keys.add(key);
		} else if (head.count > 0) {
			Node node = new Node(0);
			node.keys.add(key);
			node.next = head;
			head.prev = node;
			head = node;
		} else {
			head.keys.add(key);
		}
		nodeHash.put(key, head);
	}

	private void increaseCount(int key) {
		Node node = nodeHash.get(key);
		node.keys.remove(key);

		if (node.next == null) {
			node.next = new Node(node.count + 1);
			node.next.prev = node;
			node.next.keys.add(key);
		} else if (node.next.count == node.count + 1) {
			node.next.keys.add(key);
		} else {
			Node tmp = new Node(node.count + 1);
			tmp.keys.add(key);
			tmp.prev = node;
			tmp.next = node.next;
			node.next.prev = tmp;
			node.next = tmp;
		}

		nodeHash.put(key, node.next);
		if (node.keys.size() == 0)
			removeNode(node);
	}

	private void removeLeastUsed() {
		if (head == null)
			return;
		int leastUsed = head.keys.iterator().next();
		head.keys.remove(leastUsed);
		if (head.keys.size() == 0)
			removeNode(head);
		nodeHash.remove(leastUsed);
		valueHash.remove(leastUsed);
	}

	private void removeNode(Node node) {
		if (node.prev == null) {
			head = node.next;
		} else {
			node.prev.next = node.next;
		}
		if (node.next != null) {
			node.next.prev = node.prev;
		}
	}

	private class Node {
		private int count = 0;
		private Set<Integer> keys = new LinkedHashSet<Integer>();
		private Node prev = null, next = null;

		private Node(int count) {
			this.count = count;
		}
	}
}
```

# Reference Resources
- [Source Code on GitHub](https://github.com/codebycase/algorithms-java/blob/master/src/main/java/a07_hash_cache_memory)
