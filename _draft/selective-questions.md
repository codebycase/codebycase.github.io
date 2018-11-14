## 2018-10-01

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

public int searchInRotatedSortedArray(int[] nums, int target) {
	int start = 0;
	int end = nums.length - 1;
	while (start <= end) {
		int mid = (start + end) / 2;
		if (nums[mid] == target)
			return mid;

		if (nums[start] <= nums[mid]) { // left side sorted
			if (target < nums[mid] && target >= nums[start]) // target in left side
				end = mid - 1;
			else
				start = mid + 1;
		} else { // right side sorted
			if (target > nums[mid] && target <= nums[end]) // target in right side
				start = mid + 1;
			else
				end = mid - 1;
		}
	}
	return -1;
}

public class SlidingWindowMaximum {
	public int[] maxSlidingWindow(int[] nums, int k) {
		if (nums.length == 0 || k <= 0)
			return new int[0];
		int[] result = new int[nums.length - k + 1];
		Deque<Integer> queue = new LinkedList<>();
		for (int i = 0; i < nums.length; i++) {
			// discard if the element is out of the k size window
			while (!queue.isEmpty() && queue.peek() < i - k + 1) {
				queue.poll();
			}
			// discard elements smaller than nums[i] from the tail
			while (!queue.isEmpty() && nums[queue.peekLast()] < nums[i]) {
				queue.pollLast();
			}
			queue.offer(i);
			if (i >= k - 1) {
				result[i - k + 1] = nums[queue.peek()];
			}
		}
		return result;
	}
}

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


The industry regular expression uses NFA and Graph search to implement. Nondeterministic finite-state automata (NFA) can "guess" the right one when faced with more than one way to try to match the pattern.

- Build the NFA corresponding to the given RE.
  - Maintain a stack.
  - Add e-transition edges for closure/or.
  - Takes time and space proportional to m in the worst case.

Dynamic programming: we proceed with the same recursion as above, except because calls will only ever be made to match(text[i:], pattern[j:]), we use dp(i, j) to handle those calls instead, saving us expensive string-building operations and allowing us to cache the intermediate results. Time and space complexity are both O(TP).

// Top Down DP with Recursion
public static boolean regExpMatchTopDown(String text, String pattern) {
  Boolean[][] memo = new Boolean[text.length() + 1][pattern.length() + 1];
  return regExpMathRecursion(0, 0, text, pattern, memo);
}

private static boolean regExpMathRecursion(int i, int j, String text, String pattern, Boolean[][] memo) {
  if (memo[i][j] != null)
    return memo[i][j];

  // base case, reached the end
  if (j == pattern.length())
    return i == text.length();

  boolean firstMatch = i < text.length() && (pattern.charAt(j) == text.charAt(i) || pattern.charAt(j) == '.');
  if (j + 1 < pattern.length() && pattern.charAt(j + 1) == '*') {
    // skip 2 chars in pattern (just ignore x*) since * can much zero preceding elements.
    // skip one char in text since * can match multiple times of this preceding elements!
    memo[i][j] = regExpMathRecursion(i, j + 2, text, pattern, memo)
        || (firstMatch && regExpMathRecursion(i + 1, j, text, pattern, memo));
  } else {
    // skip a char for both pattern and text!
    memo[i][j] = firstMatch && regExpMathRecursion(i + 1, j + 1, text, pattern, memo);
  }

  return memo[i][j];
}

public class LongestPalindromicSubstring {
	private int start = 0;
	private int maxLen = 0;

	/**
	 * We observe that a palindrome mirrors around its center. Therefore, a palindrome can be
	 * expanded from its center, and there are only 2n - 1 such centers.
	 */
	public String longestPalindrome(String s) {
		if (s == null || s.length() < 2)
			return s;
		for (int i = 0; i < s.length() - 1; i++) {
			extendPalindrome(s, i, i); // between one, e.g. 'abcba'
			extendPalindrome(s, i, i + 1); // between two, e.g. 'abccba'
		}
		return s.substring(start, start + maxLen);
	}

	private void extendPalindrome(String s, int lo, int hi) {
		while (lo >= 0 && hi < s.length() && s.charAt(lo) == s.charAt(hi)) {
			lo--;
			hi++;
		}
		if (maxLen < hi - lo - 1) {
			start = lo + 1;
			maxLen = hi - lo - 1;
		}
	}
}


// say this pair of words: {"dcbab","cd"}, we need check both "dcbabcd" and "cddcbab"
public List<List<Integer>> palindromePairs2(String[] words) {
    List<List<Integer>> pairs = new LinkedList<>();
    if (words == null || words.length == 0)
        return pairs;
    Map<String, Integer> map = new HashMap<>();
    // add reversed words to map!
    for (int i = 0; i < words.length; i++)
        map.put(new StringBuilder(words[i]).reverse().toString(), i);
    for (int i = 0; i < words.length; i++) {
        String word = words[i];
        int length = word.length();
        // place the word on left side
        int end = 0;
        while (end < length) {
            Integer j = map.get(word.substring(0, end));
            if (j != null && i != j && isPalindrome(word.substring(end, length)))
                pairs.add(Arrays.asList(i, j));
            end++;
        }
        // place the word on right side
        int begin = length - 1;
        while (begin >= 0) {
            Integer j = map.get(word.substring(begin, length));
            if (j != null && i != j && isPalindrome(word.substring(0, begin)))
                pairs.add(Arrays.asList(i, j));
            begin--;
        }
        /*
        int l = 0, r = 0;
        while (l <= r) {
            Integer j = map.get(word.substring(l, r));
            // check both left and right side
            if (j != null && i != j && isPalindrome(word.substring(l == 0 ? r : 0, l == 0 ? word.length() : l)))
                pairs.add(l == 0 ? Arrays.asList(i, j) : Arrays.asList(j, i));
            if (r < word.length())
                r++;
            else
                l++;
        }
        */
    }
    return pairs;
}

private boolean isPalindrome(String word) {
  int i = 0, j = word.length() - 1;
  while (i < j) {
    if (word.charAt(i++) != word.charAt(j--))
      return false;
  }
  return true;
}

// min cut for palindrome
public int minCut(String s) {
  int n = s.length();
  int[] dp = new int[n]; // min cut for s[0:i) to be partitioned
  boolean[][] isPal = new boolean[n][n]; // true means s[i:j) is a valid palindrome

  for (int i = 0; i < n; i++) {
    int min = i;
    for (int j = 0; j <= i; j++) {
      // [j, i] is palindrome if [j + 1, j - 1] is palindrome and s[j] == s[i]
      if (s.charAt(j) == s.charAt(i) && (j + 1 > i - 1 || isPal[j + 1][i - 1])) {
        isPal[j][i] = true;
        min = j == 0 ? 0 : Math.min(min, dp[j - 1] + 1);
      }
    }
    dp[i] = min;
  }
  return dp[n - 1];
}

public class PalindromePermutation {
    public static List<String> generatePalindromes(String s) {
        List<String> result = new ArrayList<>();
        if (s == null || s.length() == 0)
            return result;
        int[] count = new int[128];
        for (int i = 0; i < s.length(); i++)
            count[s.charAt(i)]++;
        int odd = -1;
        for (int i = 0; i < count.length; i++) {
            if (count[i] % 2 != 0) {
                if (odd != -1) // found more than one odd!
                    return result;
                odd = i; // cache this odd char
            }
            count[i] = count[i] / 2; // half it!
        }
        StringBuilder temp = new StringBuilder();
        // just need to generate half length
        generatePalindromesDfs(result, temp, count, s.length() / 2, odd);
        return result;
    }

    private static void generatePalindromesDfs(List<String> result, StringBuilder builder, int[] count, int halfLen,
            int oddChar) {
        if (builder.length() == halfLen) {
            // replicate another half!
            if (oddChar != -1)
                builder.append((char) oddChar);
            for (int i = builder.length() - (oddChar == -1 ? 1 : 2); i >= 0; i--) {
                builder.append(builder.charAt(i));
            }
            result.add(builder.toString());
            return;
        }
        int prevLen = builder.length();
        for (int i = 0; i < count.length; i++) {
            if (count[i] > 0) {
                builder.append((char) (i));
                count[i]--;
                generatePalindromesDfs(result, builder, count, halfLen, oddChar);
                count[i]++;
                builder.setLength(prevLen);
            }
        }
    }

    public static void main(String[] args) {
        assert generatePalindromes("aababac").toString().equals("[aabcbaa, abacaba, baacaab]");
    }
}

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

public class TicTacToe {
    private int[] rows;
    private int[] cols;
    private int diagonal;
    private int antiDiagonal;

    public TicTacToe(int n) {
        rows = new int[n];
        cols = new int[n];
    }

    public int move(int row, int col, int player) {
        int toAdd = player == 1 ? 1 : -1;

        rows[row] += toAdd;
        cols[col] += toAdd;
        if (row == col)
            diagonal += toAdd;
        if (col == cols.length - row - 1)
            antiDiagonal += toAdd;

        int size = rows.length;
        if (Math.abs(rows[row]) == size || Math.abs(cols[col]) == size || Math.abs(diagonal) == size
                || Math.abs(antiDiagonal) == size) {
            return player;
        }

        return 0;
    }
}
```

## 2018-11-01

```java
// The input can be Map<String, Collection<String>> dislikes
public boolean possibleBipartition(int N, int[][] dislikes) {
		// construct graph
		Map<Integer, Set<Integer>> graph = new HashMap<>();
		for (int i = 1; i <= N; ++i)
				graph.put(i, new HashSet<>());
		for (int[] edge : dislikes) {
				graph.get(edge[0]).add(edge[1]);
				graph.get(edge[1]).add(edge[0]);
		}
		// coloring bipartites
		Map<Integer, Integer> coloring = new HashMap<>();
		for (int start : graph.keySet()) {
				if (!coloring.containsKey(start)) {
						// use BFS
						Queue<Integer> queue = new ArrayDeque<>();
						queue.offer(start);
						coloring.put(start, 0);
						while (!queue.isEmpty()) {
								Integer node = queue.poll();
								for (int nei : graph.get(node)) {
										if (!coloring.containsKey(nei)) {
												queue.offer(nei);
												coloring.put(nei, coloring.get(node) ^ 1);
										} else if (coloring.get(nei).equals(coloring.get(node))) {
												return false;
										}
								}
						}
						// use DFS
						/*
						if (!possibleBipartitionDfs(start, 0, graph, coloring))
								return false;
						*/
				}
		}
		return true;
}

public boolean possibleBipartitionDfs(Integer node, Integer color, Map<Integer, Set<Integer>> graph, Map<Integer, Integer> coloring) {
		if (coloring.containsKey(node))
				return coloring.get(node).equals(color);

		coloring.put(node, color);

		for (int nei : graph.get(node)) {
				if (!possibleBipartitionDfs(nei, color ^ 1, graph, coloring))
						return false;
		}

		return true;
}

// Use bucket sort, O(n)
public List<Integer> topKFrequent(int[] nums, int k) {
  List<Integer> result = new ArrayList<>();
  if (nums.length == 0)
    return result;

  // Avoid using hash map
  int min = Integer.MAX_VALUE, max = Integer.MIN_VALUE;
  for (int i = 0; i < nums.length; i++) {
    if (nums[i] < min)
      min = nums[i];
    if (nums[i] > max)
      max = nums[i];
  }
  int[] data = new int[max - min + 1];
  for (int i = 0; i < nums.length; i++) {
    data[nums[i] - min]++;
  }

  // Index is frequency
  @SuppressWarnings("unchecked")
  List<Integer>[] bucket = new ArrayList[nums.length + 1];
  for (int i = 0; i < data.length; i++) {
    if (data[i] > 0) {
      if (bucket[data[i]] == null) {
        bucket[data[i]] = new ArrayList<Integer>();
      }
      List<Integer> list = bucket[data[i]];
      list.add(i + min);
      bucket[data[i]] = list;
    }
  }
  for (int i = nums.length; i >= 0 && result.size() < k; i--) {
    if (bucket[i] != null)
      result.addAll(bucket[i]);
  }
  return result;
}
```

### Work Break

```java
// Dynamic programming
public boolean wordBreak3(String s, List<String> wordDict) {
  Set<String> wordDictSet = new HashSet<>(wordDict);
  boolean[] found = new boolean[s.length() + 1];
  found[0] = true;
  for (int i = 1; i <= s.length(); i++) {
    for (int j = 0; j < i; j++) {
      if (found[j] && wordDictSet.contains(s.substring(j, i))) {
        found[i] = true;
        break;
      }
    }
  }
  return found[s.length()];
}
```

### Word Break II

```java
public class WordBreak {
    public static List<String> wordBreak(String input, List<String> wordDict) {
        List<String> result = new ArrayList<>();
        if (input.length() == 0 || wordDict.isEmpty())
            return result;
        int minLen = Integer.MAX_VALUE, maxLen = Integer.MIN_VALUE;
        Set<String> wordSet = new HashSet<>();
        for (String word : wordDict) {
            wordSet.add(word);
            minLen = Math.min(minLen, word.length());
            maxLen = Math.max(maxLen, word.length());
        }
        StringBuilder sentence = new StringBuilder();
        boolean[] failed = new boolean[input.length()]; // failed memo
        wordBreak(input, wordSet, minLen, maxLen, 0, failed, sentence, result);
        return result;
    }

    private static boolean wordBreak(String input, Set<String> wordSet, int minLen, int maxLen, int start,
            boolean[] failed, StringBuilder sentence, List<String> result) {
        if (start == input.length()) {
            sentence.setLength(sentence.length() - 1);
            result.add(sentence.toString());
            return true;
        }
        // break ealier
        if (failed[start])
            return false;
        boolean succeed = false;
        for (int i = start + minLen - 1; i < Math.min(input.length(), start + maxLen); i++) {
            String sub = input.substring(start, i + 1);
            if (wordSet.contains(sub)) {
                int sLen = sentence.length();
                sentence.append(sub).append(' ');
                if (wordBreak(input, wordSet, minLen, maxLen, i + 1, failed, sentence, result))
                    succeed = true;
                sentence.setLength(sLen); // back track
            }
        }
        failed[start] = !succeed;
        return succeed;
    }

    public static void main(String[] args) {
        String s = "pineapplepenapple";
        List<String> wordDict = Arrays.asList("apple", "pen", "applepen", "pine", "pineapple");
        assert wordBreak(s, wordDict).toString()
                .equals("[pine apple pen apple, pine applepen apple, pineapple pen apple]");
    }
}
```

### Fixed-size Sliding Window

```java
public int kEmptySlots(int[] flowers, int k) {
  int[] days = new int[flowers.length];
  for (int i = 0; i < flowers.length; i++)
    days[flowers[i] - 1] = i + 1;
  int left = 0, right = k + 1, minDay = Integer.MAX_VALUE;
  for (int i = 0; right < days.length; i++) {
    if (days[i] < days[left] || days[i] <= days[right]) {
      if (i == right)
        minDay = Math.min(minDay, Math.max(days[left], days[right])); // we get a valid subarray
      left = i;
      right = left + k + 1;
    }
  }
  return (minDay == Integer.MAX_VALUE) ? -1 : minDay;
}
```

### Burst Balloons

```java
// Divide and conquer with memorization
public int maxCoins(int[] iNums) {
  int[] nums = new int[iNums.length + 2];
  int n = 1;
  for (int x : iNums) {
    if (x > 0) // remove zero balloons
      nums[n++] = x;
  }
  // add 2 imaged balloons!
  nums[0] = nums[n++] = 1;

  int[][] memo = new int[n][n];
  return burstBalloons(memo, nums, 0, n - 1);
}

public int burstBalloons(int[][] memo, int[] nums, int left, int right) {
  if (memo[left][right] > 0)
    return memo[left][right];
  int max = 0;
  for (int i = left + 1; i < right; i++) {
    // treat i as the last balloon
    int coins = nums[left] * nums[i] * nums[right];
    max = Math.max(max, burstBalloons(memo, nums, left, i) + coins + burstBalloons(memo, nums, i, right));
  }
  return memo[left][right] = max;
}
```
