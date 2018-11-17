---
layout: article
title: Algorithms - List & Linked Lists
key: a03-list-linked-lists
cover: /assets/images/algorithms/algorithm-overall.jpg
categories: Algorithms
tags: List
sharing: true
aside:
  toc: true
show_edit_on_github: true
pageview: true
---

# List

An ordered collection (also known as a sequence). The user of this interface has precise control over where in the list each element is inserted. The user can access elements by their integer index (position in the list), and search for elements in the list.

### Insert Delete GetRandom O(1)

Design a data structure that supports all following operations in average O(1) time.

insert(val): Inserts an item val to the set if not already present.
remove(val): Removes an item val from the set if present.
getRandom: Returns a random element from current set of elements. Each element must have the same probability of being returned.

```java
public class RandomizedSet {
	List<Integer> nums;
	Map<Integer, Integer> locs;
	java.util.Random rand = new java.util.Random();

	/** Initialize your data structure here. */
	public RandomizedSet() {
		nums = new ArrayList<Integer>();
		locs = new HashMap<Integer, Integer>();
	}

	/**
	 * Inserts a value to the set. Returns true if the set did not already contain the specified
	 * element.
	 */
	public boolean insert(int val) {
		boolean contain = locs.containsKey(val);
		if (contain)
			return false;
		locs.put(val, nums.size());
		nums.add(val);
		return true;
	}

	/** Removes a value from the set. Returns true if the set contained the specified element. */
	public boolean remove(int val) {
		boolean contain = locs.containsKey(val);
		if (!contain)
			return false;
		int loc = locs.get(val);
		if (loc < nums.size() - 1) { // not the last one than swap the last one with this val
			int lastone = nums.get(nums.size() - 1);
			nums.set(loc, lastone);
			locs.put(lastone, loc);
		}
		locs.remove(val);
		nums.remove(nums.size() - 1);
		return true;
	}

	/** Get a random element from the set. */
	public int getRandom() {
		return nums.get(rand.nextInt(nums.size()));
	}

}
```

# Linked List

- Insert and delete are local operations and have O(1) time complexity. Search requires traversing the entire list at worst case, the time complexity is O(n).
- Consider using **a dummy head** to avoid having to check empty lists. This simplifies code, and makes bugs less likely.
- Algorithms operating on singly linked lists often benefit from using two iterator, one ahead of the other, or one advancing quicker than the other.
- Arrays.asList() returns a fixed-size list (**adapter**) backed by the specified array (Arrays.ArrayList<E>). You can change/sort existing entries, but cannot add or delete entries.

<!--more-->

## Singly Linked Lists

### Delete a Node

Write a function to delete a node (except the tail) in a singly linked list, given only access to that node.

```java
public void deleteNode(ListNode node) {
	 if (node.next == null)
			 return;
		node.val = node.next.val;
		node.next = node.next.next;
}
```

### Rotate a List

Suppose an array list comprises [a, b, c, d, e]. To move the element at index 1 (b) forward two positions: [a, c, d, b, e].

_Use Collections.rotate() with subList(). Or find the index, loop and assign new values._

```java
List<String> list = Arrays.asList("a", "b", "c", "d", "e");
Collections.rotate(list.subList(1, 4), -1);
```

### Right Shift a List

Write a program that takes as input a singly linked list and a nonnegative integer k, and return s the list cyclically shifted to the right by k.

_Use the fact that linked lists can be cut and sublists reassembled very efficiently._

```java
public ListNode rightShiftList(ListNode list, int k) {
  if (list == null)
    return list;

  // computate the length and the tail
  int len = 1; // starts with 1
  ListNode tail = list;
  while (tail.next != null) {
    len++;
    tail = tail.next;
  }

  k %= len; // if k > len, k is actually k mod len
  if (k == 0)
    return list;

  tail.next = list; // make a cycle
  int stepsToNewHead = len - k;
  ListNode newTail = tail;
  while (stepsToNewHead-- > 0) {
    newTail = newTail.next;
  }
  ListNode newHead = newTail.next;
  newTail.next = null;

  return newHead;
}
```

### Check Palindromic

Write a program that tests whether a singly linked list is palindromic.

_We can reverse the second half of the original list and then compare with the first half._

```java
public boolean checkPalindromic(ListNode list) {
  // find the second half of l
  ListNode slow = list, fast = list;
  while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
  }

  ListNode firstHalf = list;
  ListNode secondHalf = reverseList(slow);
  while (secondHalf != null && firstHalf != null) {
    if (secondHalf.val != firstHalf.val)
      return false;
    secondHalf = secondHalf.next;
    firstHalf = firstHalf.next;
  }
  return true;
}

public ListNode reverseList(ListNode head) {
  ListNode prev = null, curr = head;
  while (curr != null) {
    ListNode temp = curr.next;
    curr.next = prev;
    prev = curr;
    curr = temp;
  }
  return prev;
}

private ListNode reverseList2(ListNode list) {
  ListNode dummy = new ListNode(0, list);
  ListNode before = dummy;
  ListNode middle = before.next;
  while (middle.next != null) {
    ListNode after = middle.next;
    middle.next = after.next;
    after.next = before.next;
    before.next = after;
  }
  return dummy.next;
}
```

### Remove Nth Last Node

Also called "Remove Nth Node From End of List"

Given a singly linked list and an integer k, write a program to remove the kth last element.

_We use two iterators to traverse the list. The first one is advanced by k steps, and then both advance in tandem. When the first one reached the end, the second one should be on the target. Time complexity: O(L)._

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
	ListNode dummyHead = new ListNode(0);
	dummyHead.next = head;

	ListNode first = dummyHead.next; // start with next!
	while (n-- > 0) {
		first = first.next;
	}

	ListNode second = dummyHead; // start with dummy head!
	while (first != null) {
		first = first.next;
		second = second.next;
	}

	// second points to the (k+1)-th last node
	second.next = second.next.next;
	return dummyHead.next;
}
```

### Remove Duplicates

The problem is concerned with removing duplicates from a sorted list of integers.

```java
public ListNode removeDuplicates(ListNode l) {
  ListNode cur = l;

  while (cur != null) {
    ListNode next = cur.next;
    while (next != null && next.val == cur.val) {
      next = next.next;
    }
    cur.next = next;
    cur = next;
  }

  return l;
}
```

### Reverse a Sublist

Reverse a linked list from position m to n. Do it in-place and in one-pass.

For example: Given 1->2->3->4->5->NULL, m = 2 and n = 4, return 1->4->3->2->5->NULL.

```java
public ListNode reverseSubList(ListNode head, int m, int n) {
		if (head == null)
			return null;

		ListNode dummy = new ListNode(0);
		dummy.next = head;

		ListNode before = dummy;
		for (int i = 1; i < m; i++) {
			before = before.next;
		}

		ListNode middle = before.next;
		for (int i = 0; i < n - m; i++) {
			ListNode after = middle.next;
			middle.next = after.next;
			after.next = before.next;
			before.next = after;
		}

		return dummy.next;
	}
```

### Merge K Sorted Lists

Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

```java
/**
 * The loop runs n * k times. In every iteration of loop, we call heapify which takes O(Log(k))
 * time. Therefore, the time complexity is O(nkLog(k)).
 */
public ListNode mergeKSortedLists(ListNode[] lists) {
  if (lists == null || lists.length == 0)
    return null;

  Queue<ListNode> queue = new PriorityQueue<>(lists.length, (a, b) -> (a.val - b.val));
  for (ListNode node : lists) {
    if (node != null)
      queue.offer(node);
  }

  ListNode dummy = new ListNode(0);
  ListNode tail = dummy;

  while (!queue.isEmpty()) {
    ListNode node = queue.poll();

    // remove duplicates if required
    while (node.next != null && node.val == node.next.val) {
      node = node.next;
    }

    // check duplicates if required
    if (tail.val != node.val) {
      tail.next = node;
      tail = node;
    }

    if (node.next != null)
      queue.offer(node.next);
  }

  return dummy.next;
}
```

```java
// simply merge two sorted list
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
		if (l1 == null)
				return l2;
		if (l2 == null)
				return l1;
		if (l1.val < l2.val) {
				l1.next = mergeTwoLists(l1.next, l2);
				return l1;
		} else {
				l2.next = mergeTwoLists(l2.next, l1);
				return l2;
		}
}
```

### Odd Even Linked List

Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.

You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.

Example 1:

```
Input: 1->2->3->4->5->NULL
Output: 1->3->5->2->4->NULL
```

```java
public ListNode oddEvenList(ListNode head) {
	if (head == null)
		return null;
	ListNode odd = head, even = head.next, evenHead = even;
	while (even != null && even.next != null) {
		odd.next = even.next;
		odd = odd.next;
		even.next = odd.next;
		even = even.next;
	}
	odd.next = evenHead;
	return head;
}
```

### Reorder Linked List

Given a singly linked list L: L0→L1→…→Ln-1→Ln,
reorder it to: L0→Ln→L1→Ln-1→L2→Ln-2→…

You may not modify the values in the list's nodes, only nodes itself may be changed.

Example: Given 1->2->3->4->5, reorder it to 1->5->2->4->3.

_Use two pointer slow and fast to find the middle._

```java

```

### Linked List Cycle

Given a linked list, return the node where the cycle begins. If there is no cycle, return null.

Note: Do not modify the linked list.

_We can use a slow iterator by one and a fast iterator by two to traverse the list._

_x + y = k (slow); x + y + N = 2k (fast); => x = N - y = z_

![UTF Character Encodings](/assets/images/algorithms/linked-list-cycle.png)

```java
public ListNode detectCycle(ListNode head) {      
     ListNode slow = head, fast = head;

     while (fast != null && fast.next != null) {
         slow = slow.next;
         fast = fast.next.next;
         if (slow == fast) { // there is a cycle
             // point slow back to start
             slow = head;
             // both pointers advance at the same time
             while (slow != fast) {
                 slow = slow.next;
                 fast = fast.next;
             }
             return slow;
         }
     }

     return null;
 }
```

### Intersection of Two LinkedList

Write a program to find the node at which the intersection of two singly linked lists begins.

For example, the following two linked lists:

```
A:          a1 → a2
                   ↘
                     c1 → c2 → c3
                   ↗            
B:     b1 → b2 → b3
begin to intersect at node c1.
```

_Maintain two pointers, When pA reaches the end of a list, then redirect it to the head of B (yes, B, that's right.); similarly when pB reaches the end of a list, redirect it the head of A. Finally when they met, they should travelled the same distance and met at the intersection node._

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    if (headA == null || headB == null)
        return null;  
    ListNode a = headA, b = headB;
    while (a != b) {
        a = a == null ? headB : a.next;
        b = b == null ? headA : b.next;
    }
    return a;
}
```


### List Pivoting

Implement a function which takes as input a singly linked list and integer k and perform a pivot of the list respect to k.

_We reorganize the original list nodes into three new lists in a single traversal (less, equal, greater). Then we combine the three lists in a final step._

```java
public ListNode listPivoting(ListNode l, int x) {
  ListNode lessHead = new ListNode(0);
  ListNode equalHead = new ListNode(0);
  ListNode greaterHead = new ListNode(0);
  ListNode lessIter = lessHead;
  ListNode equalIter = equalHead;
  ListNode greaterIter = greaterHead;
  ListNode iter = l;
  while (iter != null) {
    if (iter.val < x) {
      lessIter.next = iter;
      lessIter = iter;
    } else if (iter.val == x) {
      equalIter.next = iter;
      equalIter = iter;
    } else {
      greaterIter.next = iter;
      greaterIter = iter;
    }
    iter = iter.next;
  }
  // combine three lists
  greaterIter.next = null;
  equalIter.next = greaterHead.next;
  lessIter.next = equalHead.next;
  return lessHead.next;
}
```

### Add Two Numbers

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Example:

Input: (2 -> 4 -> 3) + (5 -> 6 -> 4); Output: 7 -> 0 -> 8; Explanation: 342 + 465 = 807.

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
  ListNode head = new ListNode(0);
  ListNode prev = head;
  int carry = 0;
  while (l1 != null || l2 != null || carry != 0) {
    ListNode cur = new ListNode(0);
    int sum = ((l2 == null) ? 0 : l2.val) + ((l1 == null) ? 0 : l1.val) + carry;
    cur.val = sum % 10;
    carry = sum / 10;
    prev.next = cur;
    prev = cur;

    l1 = (l1 == null) ? l1 : l1.next;
    l2 = (l2 == null) ? l2 : l2.next;
  }
  return head.next;
}
```

### Add Two Numbers II

You are given two non-empty linked lists representing two non-negative integers. The most significant digit comes first and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Example:

Input: (7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4) Output: 7 -> 8 -> 0 -> 7

_Reverse the 2 lists or use 2 stacks._

```java
public ListNode addTwoNumbersII(ListNode l1, ListNode l2) {
  Stack<Integer> stack1 = new Stack<>();
  Stack<Integer> stack2 = new Stack<>();

  while (l1 != null) {
    stack1.push(l1.val);
    l1 = l1.next;
  }

  while (l2 != null) {
    stack2.push(l2.val);
    l2 = l2.next;
  }

  int sum = 0;
  ListNode node = new ListNode(0);
  while (!stack1.isEmpty() || !stack2.isEmpty()) {
    if (!stack1.isEmpty())
      sum += stack1.pop();
    if (!stack2.isEmpty())
      sum += stack2.pop();
    node.val = sum % 10;
    ListNode head = new ListNode(sum /= 10);
    head.next = node;
    node = head;
  }

  return node.val == 0 ? node.next : node;
}
```

### Overlapping Lists

Write a program that takes two singly linked lists, and determines if there exists a node that is common to both lists. The 2 lists may each or both have a cycle.

_Study different cases to check the overlapping._

```java
public ListNode overlappingLists(ListNode l1, ListNode l2) {
  // store the start of cycle if any
  ListNode root1 = detectCycle(l1);
  ListNode root2 = detectCycle(l2);

  if (root1 == null && root2 == null) {
    return overlappingNoCycleLists(l1, l2);
  } else if ((root1 == null && root2 != null) || (root1 != null && root2 == null)) {
    return null; // one list has cycle, one has no cycle
  }

  // now both lists have cycles!
  ListNode temp = root2;
  do {
    temp = temp.next;
  } while (temp != root1 && temp != root2);

  // l1 and l2 do not end in the same cycle
  if (temp != root1) {
    return null; // cycles are disjoint
  }

  // l1 and l2 end in the same cycles, locate the overlapping node if they first overlap
  // before cycle starts
  int stemLen1 = distance(l1, root1);
  int stemLen2 = distance(l2, root2);
  int count = Math.abs(stemLen1 - stemLen2);
  if (stemLen1 > stemLen2) {
    l1 = advance(l1, count);
  } else {
    l2 = advance(l2, count);
  }

  while (l1 != l2 && l1 != root1 && l2 != root2) {
    l1 = l1.next;
    l2 = l2.next;
  }

  // if l1 == l2, means the overlap first occurs before the cycle starts; otherwise, the first
  // overlapping node is not unique, we can return any node on the cycle.
  return l1 == l2 ? l1 : root1;
}

private ListNode overlappingNoCycleLists(ListNode l1, ListNode l2) {
  int l1Len = length(l1), l2Len = length(l2);

  // advance the longer list to get equal length lists
  if (l1Len > l2Len) {
    l1 = advance(l1, l1Len - l2Len);
  } else {
    l2 = advance(l2, l2Len - l1Len);
  }

  while (l1 != null && l2 != null && l1 != l2) {
    l1 = l1.next;
    l2 = l2.next;
  }

  // null implies there is no overlap between l1 and l2
  return l1;
}

private int distance(ListNode start, ListNode end) {
  int distance = 0;
  while (start != end) {
    start = start.next;
    distance++;
  }
  return distance;
}

private ListNode advance(ListNode l1, int k) {
  while (k-- > 0) {
    l1 = l1.next;
  }
  return l1;
}

private int length(ListNode l) {
  int len = 0;
  while (l != null) {
    l = l.next;
    len++;
  }
  return len;
}
```

### Zip Linked List

Let L be a singly linked list. Assume its nodes are numbered starting at 0, Define the zip of L to be the list consisting of the interleaving of the nodes numbered 0, 1, 2,..with the nodes numbered n - 1, n - 2, n - 3,... Implement the zip function.

Solution:

Pay a one-time cost of O(n) to reverse the second half of the original list. Now all we need to do is interleave this with the first half of the original list.

```java
public ListNode zipLinkedList(ListNode list) {
  if (list == null || list.next == null)
    return list;

  // find the second half of list
  ListNode slow = list, fast = list;
  while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
  }

  ListNode firstHalfHead = list;
  ListNode secondHalfHead = slow.next;
  slow.next = null; // split the list

  // reverse the second half
  secondHalfHead = reverseList(secondHalfHead);

  // interleave the 2 lists
  ListNode firstHalfIter = firstHalfHead;
  ListNode secondHalfIter = secondHalfHead;
  while (secondHalfIter != null) {
    ListNode temp = secondHalfIter.next;
    secondHalfIter.next = firstHalfIter.next;
    firstHalfIter.next = secondHalfIter;
    firstHalfIter = firstHalfIter.next.next;
    secondHalfIter = temp;
  }

  return list;
}
```

### Copy a Posting List

Also called "Copy List with Random Pointer".

A posting list is a single linked list with an additional "jump" field at each node. The jump field points to any other node. Implement a function which takes a postings list and returns a copy of it.

Solution:

The key to improve space complexity is to use the next field for each node in the original list to record the mapping from the original node to its copy. Means we first expand and double the original list with new nodes and finally revert the original list.

Another way is to use a map to cache all new nodes, and assemble them accordingly.

```java
public ListNode copyPostingList(ListNode list) {
  if (list == null)
    return list;

  // make a copy without assigning the jump field
  // insert this new node alongside
  ListNode iter = list;
  while (iter != null) {
    ListNode newNode = new ListNode(iter.val, iter.next, null);
    iter.next = newNode; // insert new node
    iter = newNode.next; // move to next original node
  }

  // assign the jump field in the copied list
  iter = list;
  while (iter != null) {
    if (iter.jump != null) {
      // iter.jump.next is the copied node!
      iter.next.jump = iter.jump.next;
    }
    iter = iter.next.next;
  }

  // revert original list and assign the next field of copied list
  iter = list;
  ListNode newListHead = iter.next;
  while (iter.next != null) {
    ListNode temp = iter.next;
    iter.next = temp.next; // skip new node
    iter = temp;
  }

  return newListHead;
}
```

```java
public RandomListNode copyRandomList(RandomListNode head) {
    if (head == null)
        return null;
    Map<RandomListNode, RandomListNode> map = new HashMap<>();
    // copy all nodes
    RandomListNode node = head;
    while (node != null) {
        map.put(node, new RandomListNode(node.label));
        node = node.next;
    }
    // assign next and random pointers
    node = head;
    while (node != null) {
        map.get(node).next = map.get(node.next);
        map.get(node).random = map.get(node.random);
        node = node.next;
    }
    return map.get(head);
}
```

### Combinations of Phone Number

Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

Example: Input: "23" Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].

```java
public List<String> letterCombinations(String digits) {
  LinkedList<String> result = new LinkedList<String>();
  if (digits == null || digits.trim().length() == 0)
    return result;

  String[] keyMap = new String[] { "0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz" };

  result.add("");
  for (int i = 0; i < digits.length(); i++) {
    int x = digits.charAt(i) - '0';
    while (result.peek().length() == i) {
      String t = result.poll();
      for (char s : keyMap[x].toCharArray()) {
        result.offer(t + s);
      }
    }
  }

  return result;
}
```

# Reference Resources
- [Source Code on GitHub](https://github.com/codebycase/algorithms-java/tree/master/src/main/java/a03_linked_lists)
