---
layout: article
title: Algorithms - Fundamentals
key: page-a01-fundamentals
cover: /assets/images/algorithms/algorithm-overall.jpg
categories: Algorithms
tags: Primitive Bit Charset
---

## Complexity

name						|complexity		|description									|example
----------------|-------------|-----------------------------|--------------
constant				|$$1$$				|statement										|add two numbers
logarithmic			|$$\log n$$		|divide in half								|binary search
linear					|$$n$$				|loop													|find the max
linearithmic		|$$n\log n$$	|divide and conquer						|merge sort
quadratic				|$$n^2$$			|double nested loop						|check all pairs
cubic						|$$n^3$$			|triple nested loop						|check all triples
exponential			|$$2^n$$			|exhaustive search						|check all subsets
factorial				|$$n!$$				|permutation and combination	|all subsets

### Figure Running Time

```java
// linear (N + N/2 + N/4 + ...)
public int codeOne(int n) {
	int sum = 0;
	for (int k = n; k > 0; k /= 2) {
		for (int i = 0; i < k; i++)
			sum++;
	}
	return sum;
}

// linear (1 + 2 + 4 + 8 + ...)
public int codeTwo(int n) {
	int sum = 0;
	for (int i = 1; i < n; i *= 2) {
		for (int j = 0; j < i; j++)
			sum++;
	}
	return sum;
}

// linearithmic (the outer loop loops lgN times)
public int codeThree(int n) {
	int sum = 0;
	for (int i = 1; i < n; i *= 2) {
		for (int j = 0; j < n; j++)
			sum++;
	}
	return sum;
}
```

### Linear Programming

Specialized Algorithms vs. Linear Programming

![Linear Programming](/assets/images/algorithms/linear-programming.png)

## Primitive Types

Primitive types are the most basic data types within the Java language. There are 8: **boolean, byte, char, short, int, long, float and double**. The width of these types is the number of bits of storage each takes in memory.

- **byte**: An 8-bit signed two's complement integer, from -128 to 127 (inclusive).
- **short**: A 16-bit signed two's complement integer. You can use a byte or a short to save memory in large arrays when it matters.
- **char**: A single 16-bit Unicode character, from '\u0000' to '\uffff' (or 65,535 inclusive).
- **int**: By default, is a 32-bit signed two's complement integer, from -2^31 to 2^31-1, about 4 billion numbers.
- **float**: A single-precision 32-bit floating point. Use a float to save memory in large arrays of floating point numbers.
- **double**: A double-precision 64-bit floating point. Prefer the box-type static method for comparing values, e.g., Double.compare(x, 1.23) == 0 rather than x == 1.23. As it's more resilient to floating values like infinity, negative infinity, NaN.
- **boolean**: A boolean value is one bit: 1 for true, 0 for false. However, the actual size of a boolean variable in memory is not precisely defined, could be 8 bit since computers typically access memory one byte at a time.

## Objects Memory

- To determine the memory usage of an object, we add the amount of memory used by each instance variable to the overhead associated with each object, typically 16. The overhead includes a references to the object's class, garbage collection information, and synchronization information.
- More over, the memory usage is typically padded to be a multiple of 8 bytes (on a 64-bit machine). For example, an Integer object uses 24 bytes (16 bytes of overhead, 4 bytes for its int value, and 4 bytes of padding); an int variable uses 8 bytes.
- A Counter object uses 32 bytes: 16 bytes of **overhead**, 8 bytes for its String **reference**, 4 bytes for its int **value**, and 4 bytes of **padding** (This can waste some memory but it speeds up memory access and garbage collection).

```java
public class Counter {
	private String name;
	private int count;
}
```

- An array of primitive-type values typically requires 24 bytes of header information (16 bytes of object overhead, 4 bytes for the length, and 4 bytes of padding) plus the emory needed to store the values. For example, an array of n int values uses **24+4n** bytes (rounded up to be a multiple of 8).

```java
int[] a = new int[n];
```

- The standard String representation (Java 7) has two instance variables: a reference to a character array value[] and an int value hash code. Therefore, a String of length n typically use 32 bytes for the String object (16 bytes overhead + 8 bytes for array reference + 4 bytes for int instance variable + 4 bytes padding), plus 24 + 2n bytes for the character array. The total is **56+2n** bytes.

![String Memory](https://algs4.cs.princeton.edu/14analysis/images/String-memory.png)

- Moreover, memory consumption is a complicated dynamic process when function calls are involved. For example, when you program calls a method, the system allocates the memory needed for the method (for its local variables) from a special area of memory called the **stack**, so each recursive call implies a memory usage. When you create an object with new, the system allocates the memory known as the **heap**.


## Two's Complement

Computers typically store signed integers in two's complement representation which has the advantage for CPU just needs to one kind of addition computing. A positive number is represented as itself, and negative numbers are represented by the two's complement of their absolute value.

Say a 4 bit integer 0010 represent the positive number 2, while 1010 represents the negative number -6 because the first bit 1 means negative, the complement of rest is $$2^3 - 2 = 6$$.

In other words, the binary representation of -K (negative K) as a N-bit number is $$concat(1, 2^{N-1} - K)$$.

To get the negation of a number in two's complement. you can also simply invert all the bits through the number, and add one.

```
1. ~0010 -> 1101 // invert bits
2. 1101 -> 1110 // plus 1, result is -2
```

In Java 8+, we can use unsigned Integer as below:

```java
int vInt = Integer.parseUnsignedInt("4294967295");
System.out.println(vInt); // -1
String sInt = Integer.toUnsignedString(vInt);
System.out.println(sInt); // 4294967295
```

## Number Literals

The largest positive int (2^31 - 1) can be written as:

- decimal literal: 2147483647
- hexadecimal literal: 0x7fff_ffff
- octal literal: 0177_7777_7777 (0b01_111_111_111_111_111_111_111_111_111_111)
- binary literal: 0b0111_1111_1111_1111_1111_1111_1111_1111

### Floating Point

A float contains a sign bit s (interpreted as plus or minus), 8 bits for the exponent e, and 23 bits for the mantissa M. M is normalized to be between 0.5 and 1. This normalization is always possible by adjusting the binary exponent accordingly. The decimal number is represented according to the following formula.

`$$(-1)^s × m × 2^{(e - 127)}$$`

This exactly represents the number:

$$2^{e-127} (1 + m / 2^{23}) = 2^{-4}(1 + 3019899/8388608)$$ = 11408507/134217728 = 0.085000000894069671630859375.

```
0.085:
bits:    31   30-23           22-0
binary:   0 01111011 01011100001010001111011
decimal:  0    123           3019899
```

Not every decimal number can be represented as a binary fraction. For example 1/10 = 1/16 + 1/32 + 1/256 + 1/512 + 1/4096 + 1/8192 + ... In this case, the number 0.1 is approximated by the closest 23 bit binary fraction 0.000110011001100110011. So the floating point arithmetic is not exact since some real numbers require an infinite number of digits to be represented, e.g., the mathematical constants e and π and 1/3, even 1/10!

```java
double x1 = 0.3;
double x2 = 0.1 + 0.1 + 0.1;
System.out.println(x1 == x2); // false

double z1 = 0.5;
double z2 = 0.1 + 0.1 + 0.1 + 0.1 + 0.1;
System.out.println(z1 == z2); // true
```

### Lexicographical Numbers

Given an integer n, return 1 - n in lexicographical order.

For example, given 13, return: [1,10,11,12,13,2,3,4,5,6,7,8,9].

Please optimize your algorithm to use less time and space. The input size may be as large as 5,000,000.

Solution:

The idea is pretty simple. If we look at the order we can find out we just keep adding digit from 0 to 9 to every digit and make it a tree.
Then we visit every node in pre-order.

```
       1        2        3    ...
      /\        /\       /\
   10 ...19  20...29  30...39   ....
```

```java
public List<Integer> lexicalOrder(int n) {
	List<Integer> res = new ArrayList<>();
	for (int i = 1; i < 10; i++) {
		lexicalOrderDfs(i, n, res);
	}
	return res;
}

public void lexicalOrderDfs(int cur, int n, List<Integer> res) {
	if (cur > n)
		return;
	else {
		res.add(cur);
		for (int i = 0; i < 10; ++i) {
			lexicalOrderDfs(10 * cur + i, n, res);
		}
	}
}
```

### Excel Column Number

Given a column title as appear in an Excel sheet, return its corresponding column number.

For example:

```
    A -> 1
    B -> 2
    C -> 3
    ...
    Z -> 26
    AA -> 27
    AB -> 28
    ...
```

```java
public int titleToNumber(String s) {
	int ans = 0;
	for (int i = 0; i < s.length(); i++) {
		ans += ((s.charAt(i) - 'A') + 1) * Math.pow(26, s.length() - i - 1);
	}
	return ans;
}
```

## Character Encoding

All characters are stored in the computer as one or more **bytes**, or essentially bits. A character encoding provides a key to unlock these bytes. It is a set of mappings between the bytes in the computer and the characters in the character set. Without the key, the data looks like garbage.

The ASCII table has 128 characters, with values from 0 through 127. Thus, 7 bits are sufficient to represent a character in ASCII; however, most computers typically reserve 1 byte, (8 bits), for an ASCII character. One byte allows a numeric range from 0 through 255 which leaves room for growth in the size of the character set, or for a sign bit.

Unicode is standard for representing characters as integers. Unlike ASCII, which uses 7 bits for each character, Unicode uses 16 bits (65,536 characters), can support many languages. To implement Unicode, we need character encoding to reflect the way the coded character set is mapped to bytes for manipulation in a computers.

There are three different Unicode character encodings: UTF-8, UTF-16 and UTF-32. UTF-8 is the most widely used by web sites, uses one byte for the first 128 code points, and **up to 4 bytes** for other characters. The first 128 Unicode code points are the ASCII characters; so an ASCII text is a UTF-8 text.

- UTF-8 uses 1 byte to represent characters in the ASCII set, two bytes for characters in several more alphabetic blocks, and three bytes for the rest of the BMP (Basic Multilingual Plane). Supplementary characters use 4 bytes.
- UTF-16 uses 2 bytes for any character in the BMP, and 4 bytes for supplementary characters.
- UTF-32 uses 4 bytes for all characters.

![UTF Character Encodings](/assets/images/algorithms/unicode-encodings.png)

```
This is how the UTF-8 encoding would work:

For 1-byte character, the first bit is a 0, followed by its unicode code. For n-bytes character, the first n-bits are all one's, the n+1 bit is 0, followed by n-1 bytes with most significant 2 bits being 10.


	Char. number range  |        UTF-8 octet sequence
		 (hexadecimal)    |              (binary)
	--------------------+---------------------------------------------
	0000 0000-0000 007F | 0xxxxxxx
	0000 0080-0000 07FF | 110xxxxx 10xxxxxx
	0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx
	0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
```

### Design A UTF-8 Validation

Given an array of integers representing the data, return whether it is a valid UTF-8 encoding.

Note: The input is an array of integers. Only the least significant 8 bits of each integer is used to store the data. This means each integer represents only 1 byte of data.

```java
public class UTF8Validation {
	public static boolean validateUtf8(int[] data) {
		if (data == null || data.length == 0)
			return false;
		boolean isValid = true;
		for (int i = 0; i < data.length; i++) {
			if (data[i] > 255)
				return false; // overflow, 1 after 8th digit, 100000000
			int numberOfBytes = 0;
			if ((data[i] & 128) == 0) { // 0xxxxxxx, 1 byte, 128(10000000)
				numberOfBytes = 1;
			} else if ((data[i] & 224) == 192) { // 110xxxxx, 2 bytes, 224(11100000), 192(11000000)
			//} else if ((data[i] & 0b11100000) == 0b11000000) { // or use bit format
				numberOfBytes = 2;
			} else if ((data[i] & 240) == 224) { // 1110xxxx, 3 bytes, 240(11110000), 224(11100000)
				numberOfBytes = 3;
			} else if ((data[i] & 248) == 240) { // 11110xxx, 4 bytes, 248(11111000), 240(11110000)
				numberOfBytes = 4;
			} else {
				return false;
			}
			for (int j = 1; j < numberOfBytes; j++) { // check that the next n bytes start with 10xxxxxx
				if (i + j >= data.length)
					return false;
				if ((data[i + j] & 192) != 128)
					return false; // 192(11000000), 128(10000000)
			}
			i = i + numberOfBytes - 1;
		}
		return isValid;
	}

	public static void main(String[] args) {
		// The octet sequence: 11000101 10000010 00000001.
		assert validateUtf8(new int[] { 197, 130, 1 }) == true;
		// The octet sequence: 11101011 10001100 00000100
		assert validateUtf8(new int[] { 235, 140, 4 }) == false;
	}
}
```

### Set Charset/Encoding to UTF-8

- In Eclipse Preferences (General -> Workspace), The text file encoding is by default UTF-8 now. It was CP-1251 or others, better change to UTF-8.

- In a web application project, also recommend to use UTF-8 encoding or for html, jsp, xml, css, request etc.

```
@CHARSET "UTF-8"; /* CSS Encoding */

<?xml version="1.0" encoding="UTF-8"?> <!-- XML Encoding -->

<script type="text/javascript" charset="utf-8"> <!-- JavaScript Encoding -->

<meta http-equiv="content-type" content="text/html;charset=UTF-8" /> <!-- HTML Encoding -->

<%@ page language="java" pageEncoding="UTF-8" contentType="text/html;charset=UTF-8"%> <!-- Tag Encoding -->

<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> <!-- Maven Build Encoding -->

requestEntity.setContentType("application/xml; charset=UTF-8"); // HTTP Request Entity Encoding

writer = new PrintWriter(new OutputStreamWriter(output, "UTF-8"), true); // Specify Writer's Encoding
```

### Read and Convert to UTF-8

- Let's say, the database is using ISO-8859-1 to store data, but some columns could have special characters, we need to convert to UTF-8 in favor of UI or other processing.

```java
	public static String decodedToUtf8(String original, String charset) throws Exception {
		return new String(original.getBytes(charset), "UTF-8"); // say database is using ISO-8859-1
	}
```

- When parsing or constructing the url/link, you often need to decode or encode the url query string (parameter values).

```java
	public static String decodeURLText(String urlText) {
		try {
			return URLDecoder.decode(urlText, "UTF-8");
		} catch (UnsupportedEncodingException e) {
			throw new RuntimeException(e);
		}
	}

	public static String encodeURLText(String urlText) {
		try {
			return URLEncoder.encode(urlText, "UTF-8").replace("+", "%20");
		} catch (UnsupportedEncodingException e) {
			throw new RuntimeException(e);
		}
	}
```

- In some cases, the code points can be used directly to handle a few special characters due to the file encoding can't be set to UTF-8:

```java
System.out.println("\u00AE \u00A7 \u00A9 \u00A4"); // they are characters: ® § © ¤

if (paramValue.contains("\u00A9")) {
	paramValue = paramValue.replace("\u00A9", "&copy");
	hasEntity = true;
}
```

# Bit Manipulation

Bit manipulation is used in a variety of problems. Sometimes, the question explicitly calls for bit manipulation, Other times, it's simply a useful technique to optimize your code. You should be comfortable doing bit manipulation by hand, as well as with code.

### Bit Manipulation Samples

```
0110 + 0110 = 1100
0100 * 0011 = 4 * 0011 = 0011 << 2 = 1100
1101 ^ (~1101) = 1101 ^ 0010 = 1111 // a ^ (~a) = 1
```

```
Operator    Name         Example     Result  Description
a & b       and          3 & 5       1       1 if both bits are 1.
a | b       or           3 | 5       7       1 if either bit is 1.
a ^ b       xor          3 ^ 5       6       1 if both bits are different.
~a          not          ~3          -4      Inverts the bits.
n << p      left shift   3 << 2      12      Shifts the bits of n left p positions. Zero bits are shifted into the low-order positions.
n >> p      right shift  5 >> 2      1       Shifts the bits of n right p positions. If n is a 2's complement signed number, the sign bit is shifted into the high-order positions.
n >>> p     right shift  -4 >>> 28   15      Shifts the bits of n right p positions. Zeros are shifted into the high-order positions.
```

### Accelerate Bit Manipulation

The expression x & (x - 1) clear the lowest set bit in x.

```
16 & (16 - 1) = 0, 11 & (11 - 1) = 10, 20 & (20 - 1) = 16
```

```java
public static int countBits(long x) {
	int count = 0;
	while (x != 0) {
		x &= (x - 1);
		count++;
	}
	return count;
}

/**
 * Given a non negative integer number num. For every numbers i in the range 0 ≤ i ≤ num calculate
 * the number of 1's in their binary representation and return them as an array.
 */
public static int[] countBits2(int x) {
	int[] ans = new int[x + 1];
	// solution 1: last set bit
	for (int i = 1; i <= x; i++) {
		ans[i] = ans[i & (i - 1)] + 1;
	}
	// solution 2: least significant bit
	/*
	for (int i = 0; i <= x; i++) {
		ans[i] = ans[i >> 1] + (i & 1); // P(x) = P(x/2) + (x mod 2)
	}
	*/
	return ans;
}
```

The expression x & ~(x - 1) or x & -x extracts the lowest set bit of x.

```
16 & ~(16 - 1) = 16, 11 & ~(11 - 1) = 1, 20 & ~(20 - 1) = 4
16 & -16 = 16, 11 & -11 = 1, 20 & -20 = 4
```

Consider we have an array [0...n-1], design algorithm to find the sum of first i elements,
and update the value of specified element both in O(log(n)).

The idea is based on the fact that all positive integers can be represented as sum of powers of 2. For example 19 can be represented as 16 + 2 + 1. Every node of BI Tree stores sum of n elements where n is a power of 2.

Design a binary indexed tree. also called Fenwick Tree.

![BinaryIndexedTree](/assets/images/algorithms/binary-indexed-tree.png)

```java
public class BinaryIndexedTree {
	final static int MAX = 1000;
	static int[] tree = new int[MAX];

	void constructTree(int arr[]) {
		for (int i = 1; i < tree.length; i++)
			tree[i] = 0;
		for (int i = 0; i < arr.length; i++)
			update(i, arr[i]); // O(nlog(n))
	}

	int getSum(int index) {
		int sum = 0;
		index = index + 1; // index is 1 more than arr[]
		while (index > 0) {
			sum += tree[index];
			index -= index & -index;
		}
		return sum;
	}

	// Add val to tree[i] and all of its ancestores
	public void update(int index, int val) {
		index = index + 1;
		while (index <= tree.length) {
			tree[index] += val;
			index += index & -index;
		}
	}
}
```

### Arithmetic vs. Logical Right Shift

```
-75 (10110101) >> 1 = -38 (11011010) // arithmetic shift, fill in the new bits with the sign bit.
-75 (10110101) >>> 1 = 90 (01011010) // logical shift, fill in 0 in the most significant bit.
```

### Getting and Setting Bit Functions

```java
public boolean getBit(int num, int i) {
	return (num & (1 << i)) != 0;
}

public int setBit(int num, int i) {
	return num | (1 << i);
}

public int clearBit(int num, int i) {
	return num & ~(1 << i);
	// clear all bits from the most significant bit through i (inclusive)
	// return num & ((1 << i) - 1);
	// clear all bits from i through 0 (inclusive)
	// return num & ((-1 << i + 1)); // NOTE: a sequence of 1 is -1
}

public int updateBit(int num, int i, boolean bitIs1) {
	int value = bitIs1 ? 1 : 0;
	int mask = ~(1 << i); // to clear bit
	return (num & mask) | (value << i);
}

public long swapBits(long x, int i, int j) {
	// extract the i-th and j-th bits, and see if they differ.
	if (((x >>> i) & 1) != ((x >>> j) & 1)) {
		long mask = (1L << i) | (1L << j); // combine
		x ^= mask; // flip their values with XOR
	}
	return x;
}

```

### Packing/Unpacking int to bytes

```java
static void putInt(byte[] b, int off, int val) {
		b[off + 3] = (byte) (val       );
		b[off + 2] = (byte) (val >>>  8);
		b[off + 1] = (byte) (val >>> 16);
		b[off    ] = (byte) (val >>> 24);
}

static int getInt(byte[] b, int off) {
		return ((b[off + 3] & 0xFF)      ) +
					 ((b[off + 2] & 0xFF) <<  8) +
					 ((b[off + 1] & 0xFF) << 16) +
					 ((b[off    ]       ) << 24);
}
```

# Interview Questions

### Word's Parity

The parity of a binary word is 1 if the number of 1s in the word is odd; otherwise, it is 0. e.g., the parity of 1011 is 1, and the parity of 10001000 is 0.
How would you compute the parity of a very large number of 64-bit words?

```java
// Test the value of each bit while tracking the number of 1s seen so far.
// Complexity: O(n)
public short parity1(long x) {
	short result = 0;
	while (x != 0) {
		result ^= (x & 1); // store the number mod 2 since we only care if even or odd
		x >>>= 1;
	}
	return result;
}

// x & (x - 1) equals x with its lowest set bit erased.
// Complexity: O(k) k is the number of 1s.
public short parity2(long x) {
	short result = 0;
	while (x != 0) {
		result ^= 1;
		x &= (x - 1); // drops the lowest set bit of x
	}
	return result;
}

// The parity of (11010111) is the same as parity of (1101) XORed with (0111) which is (1010).
// Complexity: O(log(n))
public short parity3(long x) {
	for (int i = 32; i >= 1; i /= 2) {
		x ^= x >>> i;
	}
	return (short) (x & 0x1); // extract the last bit
}

// Especially for a long stream of words, like to compute CRC, we can cache precomputed parity.
// Complexity: O(n/L)
public short parity4(long x) {
	final int MASK_SIZE = 16;
	final int BIT_MASK = 0xFFFF;
	// feed in the precomputed parity
	final short[] precomputedParity = new short[2^16];
	return (short) (precomputedParity[(int) ((x >>> (3 * MASK_SIZE)) & BIT_MASK)]
			^ precomputedParity[(int) ((x >>> (2 * MASK_SIZE)) & BIT_MASK)]
			^ precomputedParity[(int) ((x >>> (MASK_SIZE)) & BIT_MASK)]
			^ precomputedParity[(int) (x & BIT_MASK)]);
}
```


### Reverse Bits

```java
	/**
	 * Reverse bits of a given 32 bits unsigned integer.
	 *
	 * For example, given input 43261596 (represented in binary as
	 * 00000010100101000001111010011100), return 964176192 (represented in binary as
	 * 00111001011110000010100101000000).
	 *
	 */
	public int reverseBits(int n) {
		int result = 0;
		for (int i = 0; i < 32; i++) {
			result += n & 1;
			n >>>= 1; // must do unsigned shift
			if (i < 31) // for last digit, don't shift!
				result <<= 1;
		}
		return result;
	}

	/**
	 * How to optimize if this function is called multiple times? We can divide an int into 4 bytes,
	 * and reverse each byte then combine into an int. For each byte, we can use cache to improve
	 * performance.
	 */
	private final Map<Byte, Integer> cache = new HashMap<Byte, Integer>();

	public int reverseBits2(int n) {
		int result = 0;
		for (int i = 0; i < 4; i++) {
			result += reverseByte((byte) ((n >>> 8 * i) & 0xFF)); // reverse per byte
			if (i < 3)
				result <<= 8;
		}
		return result;
	}

	private int reverseByte(byte b) {
		Integer value = cache.get(b); // first look up from cache
		if (value != null)
			return value;
		value = 0;
		// reverse by bit
		for (int i = 0; i < 8; i++) {
			value += ((b >>> i) & 1);
			if (i < 7)
				value <<= 1;
		}
		cache.put(b, value);
		return value;
	}
```

### Reverse Digits

Write a program which takes an integer and returns the integer corresponding to the digits of the input written in reverse order. For example, the reverse of 42 is 24, and the reverse of -314 is -413.

```java
public static long reverseDigits(int x) {
	long result = 0;
	int remain = Math.abs(x);
	while (remain > 0) {
		result = result * 10 + remain % 10;
		remain /= 10;
	}
	return x < 0 ? -result : result;
}
```

### Compute $$x*y$$

Write a program that multipliers two non-negative integers with bitwise operators and shift-and-add.

_To multiply x and y we initialize the result to 0 and iterate through the bits of x, adding (2^k)y to the result if the kth bit of x is 1, simulating the add operation._

```java
public static long multiply(long x, long y) {
	long sum = 0;

	while (x != 0) {
		if ((x & 1) != 0) {
			sum = add(sum, y);
		}
		x >>>= 1;
		y <<= 1;
	}

	return sum;
}

// simple and easier code
private static long add(long a, long b) {
	long c = 0; // carrier
	while (b != 0) {
		c = a & b;
		a = a ^ b;
		b = c << 1;
	}
	return a;
}

private static long add2(long a, long b) {
	long sum = 0, carryin = 0, k = 1, tempA = a, tempB = b;
	while (tempA != 0 || tempB != 0) {
		long ak = a & k, bk = b & k;
		long carryout = (ak & bk) | (ak & carryin) | (bk & carryin);
		sum |= (ak ^ bk ^ carryin);
		carryin = carryout << 1;
		k <<= 1;
		tempA >>>= 1;
		tempB >>>= 1;
	}
	return sum | carryin;
}

public int add(int a, int b) {
		int c = 0;
		while (b != 0) {
				c = a & b;
				a = a ^ b;
				b = c << 1;
		}
		return a;
}
```

### Compute $$x/y$$

Given two positive integers, compute their quotient, using only the addition, subtraction, and shifting operators.

_A brute-force approach is to iteratively subtract y from x until what remains is less than y. The number of such subtractions is exactly the quotient._

_A better approach is to try and get more work done in each iteration. Such as, we can compute the largest k such that (2^k)y <= x, subtract (2^k)y from x, and add 2^k to the quotient._

_If it takes n bits to represent x/y, there are O(n) iterations._

```java
public static int divide(int x, int y) {
	if (y == 0)
		throw new IllegalArgumentException();
	int result = 0;
	int power = 32;
	long yPower = y << power;
	while (x >= y) {
		while (yPower > x) {
			yPower >>>= 1;
			power--;
		}
		result += 1 << power;
		x -= yPower;
	}
	return result;
}
```

### Compute $$x^y$$

Write a program that takes a double x and an integer y and returns $$x^y$$. You can ignore overflow and underflow.

_Generalizing, if the least significant bit of y is 0, the result is $$(x^{y/2})^2$$; Otherwise, it is $$x*(x^{y/2})^2$$. This gives us a recursive algorithm._

_To avoid overflow, e.g. y = Integer.MIN_VALUE, we can convert to long type first!_

```java
// bottom-up recursive
public static double power(double x, int y) {
	double result = 1.0;

	long n = y; // avoid overflow, e.g., n = Integer.MIN_VALUE
	if (n < 0) {
		x = 1.0 / x;
		n = -n;
	}

	while (n != 0) {
		if ((n & 1) == 1) // will run twice for odd number, and only once for even number!
			result *= x;
		x *= x;
		n >>>= 1;
	}

	return result;
}

// top-down recursive
public static double powerII(double x, int y) {
	if (y == 0)
		return 1;
	if (y == 1)
		return x;

	if (y < 0) {
		y = -y;
		x = 1.0 / x;
	}

	int s = y >> 1; // divide by 2
	double half = powerII(x, s);

	if ((y & 1) == 0)
		return half * half;
	else
		return half * half * x;

}
```

### Add Binary Strings

```java
/**
 * Given two binary strings, return their sum (also a binary string).
 *
 * For example, a = "11" b = "1" Return "100".
 *
 * @author lchen
 *
 */
public class AddBinaryStrings {
	public String addBinary(String a, String b) {
		StringBuilder builder = new StringBuilder();
		int i = a.length() - 1;
		int j = b.length() - 1;
		int carry = 0;
		while (i >= 0 || j >= 0) {
			int sum = carry;
			if (i >= 0) {
				sum += a.charAt(i) - '0';
				i--;
			}
			if (j >= 0) {
				sum += b.charAt(j) - '0';
				j--;
			}
			builder.append(sum % 2);
			carry = sum / 2;
		}
		if (carry != 0)
			builder.append(carry);
		return builder.reverse().toString();
	}
}
```

### Find Closest Integer

```java
/**
 * Find the closest integer with the same weight (the same number of bits set to 1)
 *
 * @param x
 * @return
 */
public static long closestIntSameBitCount(long x) {
	final int NUM_UNSIGNED_BITS = 63;
	// x is assumed to be non-negative so we know the leading bit is 0. We
	// restrict to our attention to 63 LSBs.
	for (int i = 0; i < NUM_UNSIGNED_BITS - 1; ++i) {
		if ((((x >>> i) & 1) != ((x >>> (i + 1)) & 1))) {
			x ^= (1L << i) | (1L << (i + 1)); // swaps bit-i and bit-(i + 1).
			return x;
		}
	}
	// throw error if all bits of x are 0 or 1.
	throw new IllegalArgumentException("All bits are 0 or 1");
}
```

### Combine Multiple IDs

In the case, to design a MySQL sharding approach. You might use a 64 bit ID which contains 16 bit shard ID, 10 bits type ID, and 36 bit local ID.

```
ID = (shard ID << 46) | (type ID << 36) | (local ID<<0)
Given a ID 241294492511762325
Shard ID = (241294492511762325 >> 46) & 0xFFFF = 3429
Type ID  = (241294492511762325 >> 36) & 0x3FF = 1
Local ID = (241294492511762325 >>  0) & 0xFFFFFFFFF = 7075733
```

```java
/**
 * 16 + 10 + 36 = 62 bits in total!
 *
 * @param shardId
 *            contains 16 bits
 * @param typeId
 *            contains 10 bits
 * @param localId
 *            contains 36 bits
 * @return 64 bits ID
 */
public long encodeId(long shardId, long typeId, long localId) {
	return shardId << (10 + 36) | typeId << 36 | localId;
}

/**
 * @param id
 * @return shardId, typeId, localId
 */
public long[] decodeId(long id) {
	long[] result = new long[3];
	result[0] = (id >> 46) & 0xFFFF; // 1111,1111,1111,1111
	result[1] = (id >> 36) & 0x3FF; // 11,1111,1111
	result[2] = id & 0xFFFFFFFF;
	return result;
}
```

### Integers without Consecutive Ones

Given a positive integer n, find the number of non-negative integers less than or equal to n, whose binary representations do NOT contain consecutive ones.

```
Example 1:
Input: 5
Output: 5
Explanation:
Here are the non-negative integers <= 5 with their corresponding binary representations:
0 : 0
1 : 1
2 : 10
3 : 11
4 : 100
5 : 101
Among them, only integer 3 disobeys the rule (two consecutive ones) and the other 5 satisfy the rule.
```

_If we know the value of f[n−1] and f[n−2], in order to generate the required binary numbers with
n bits, we can append a 0 to all the binary numbers contained in f[n−1] without creating an invalid number. These numbers give a factor of f[n−1] to be included in f[n]. But, we can't append a 1 to all these numbers, since it could lead to the presence of two consecutive ones in the newly generated numbers. Thus, for the currently generated numbers to end with a 1, we need to fix a 01 at the end of all the numbers contained in f[n−2]. This gives a factor of f[n−2] to be included in
f[n]. Thus, in total, we get f[n]=f[n−1]+f[n−2]._

_Now we can say that we start scanning the given number num from its MSB. For every 1 encountered at the i​th bit position(counting from 0 from LSB), we add a factor of f[i] to the resultant count. For every 0 encountered, we don't add any factor. We also keep a track of the last bit checked. If we happen to find two consecutive 1's at any time, we add the factors for the positions of both the 1's and stop the traversal immediately. If we don't find any two consecutive 1's, we proceed till reaching the LSB and add an extra 1 to account for the given number num as well, since the procedure discussed above considers numbers upto num without including itself._

_The complexity is O(log2​​(max_int)=32)_

```java
public int findIntegers(int num) {
	int[] f = new int[32];
	f[0] = 1; // first bit: 1
	f[1] = 2; // second bit: 10 01
	for (int i = 2; i < f.length; i++)
		f[i] = f[i - 1] + f[i - 2];
	int i = 30, sum = 0, prev_bit = 0;
	while (i >= 0) {
		if ((num & (1 << i)) != 0) {
			sum += f[i];
			if (prev_bit == 1) {
				sum--; // without including itself since it's invalid
				break;
			}
			prev_bit = 1;
		} else
			prev_bit = 0;
		i--;
	}
	return sum + 1; // include itself
}
```


### A Palindromic Integer

```java
/**
 * Write a program that takes an integer and determines if that integer's representation as a
 * decimal string is a palindrome.
 *
 * For example, return true for the inputs 0, 1, 7, 11, 121, 333, and 2147447412, and false for
 * the inputs -1, 12, 100.
 *
 * @param x
 * @return
 */
public static boolean isPalindromeNumber(int x) {
	if (x <= 0)
		return x == 0;

	final int numDigits = (int) (Math.floor(Math.log10(x))) + 1;
	int msdMask = (int) Math.pow(10, numDigits - 1);
	for (int i = 0; i < (numDigits / 2); i++) {
		if (x / msdMask != x % 10)
			return false;
		x %= msdMask; // remove the most significant digit of x.
		x /= 10; // remove the least significant digit of x.
		msdMask /= 100;
	}

	return true;
}
```

### Uniform Random Numbers

How would you implement a random number generator that generates a random integer i between a and b, inclusive, given a random number generator that produces zero or one with equal probability? All values in [a, b] should be equally likely.

[Replicate Dice With Coins](/algorithms/2018/01/08/a17-math-and-logic-puzzles.html#replicate-dice-with-coins)

```java
public static int uniformRandom(int lowerBound, int upperBound) {
	int result, numberOfOutcomes = upperBound - lowerBound + 1;

	do {
		result = 0;
		for (int i = 0; (1 << i) < numberOfOutcomes; i++) {
			result = (result << 1) | random.nextInt(2);
		}
	} while (result >= numberOfOutcomes);

	return result + lowerBound;
}
```

### Rectangle Intersection

This problem is concerned with rectangles whose sides are parallel to the X-axis and Y axis. Write a program which tests if two rectangles have a non empty intersection, return the rectangle formed by their intersection.

![UTF Character Encodings](/assets/images/algorithms/rectangle-intersection.png)

```java
public class RectangleIntersection {
	static class Rectangle {
		int x, y, width, height;

		public Rectangle(int x, int y, int width, int height) {
			this.x = x;
			this.y = y;
			this.width = width;
			this.height = height;
		}
	}

	public static Rectangle intersectRectangle(Rectangle R1, Rectangle R2) {
		if (!isIntersect(R1, R2))
			return new Rectangle(0, 0, -1, -1); // no intersection

		int x = Math.max(R1.x, R2.x);
		int y = Math.max(R1.y, R2.y);
		int width = Math.min(R1.x + R1.width, R2.x + R2.width) - x;
		int height = Math.min(R1.y + R1.height, R2.y + R2.height) - y;
		return new Rectangle(x, y, width, height);
	}

	private static boolean isIntersect(Rectangle R1, Rectangle R2) {
		return R1.x <= R2.x + R2.width && R1.x + R1.width >= R2.x
			&& R1.y <= R2.y + R2.height && R1.y + R1.height >= R2.y;
	}

	public static void main(String[] args) {
		Rectangle R1, R2;
		R1 = new Rectangle(0, 0, 2, 2);
		R2 = new Rectangle(1, 1, 3, 3);
		Rectangle result = intersectRectangle(R1, R2);
		assert (result.x == 1 && result.y == 1 && result.width == 1 && result.height == 1);
		R1 = new Rectangle(0, 0, 1, 1);
		R2 = new Rectangle(1, 1, 3, 3);
		result = intersectRectangle(R1, R2);
		assert (result.x == 1 && result.y == 1 && result.width == 0 && result.height == 0);
		R1 = new Rectangle(0, 0, 1, 1);
		R2 = new Rectangle(2, 2, 3, 3);
		result = intersectRectangle(R1, R2);
		assert (result.x == 0 && result.y == 0 && result.width == -1 && result.height == -1);
	}
}
```

### Design A Bit Vector

Bit Vector/Set is a compact way to store a list of boolean values.
JDK has a built in BitSet class which implements a vector of bits that grows as needed.
Here is a simple but good example to demonstrate the bit vector with common bit tasks: Sizing, Shifting, Getting and
Setting.

An array of int can be used to deal with array of bits. Assuming size of int to be 4 bytes, when we talk about an int, we are dealing with 32 bits. Say we have int A[10], means we are working on 10 * 4 * 8 = 321 bits.

```java
public class DesignBitVector {
	private static final int INT_SIZE = 32; // 4 bytes = 4 * 8 bits
	private int length;
	private int[] vector;

	public DesignBitVector(int length) {
		this.length = length;
		if (length % INT_SIZE == 0)
			vector = new int[length / INT_SIZE];
		else
			vector = new int[length / INT_SIZE + 1];
	}

	public int length() {
		return length;
	}

	public boolean get(int i) {
		if (i < 0 || i >= length)
			throw new ArrayIndexOutOfBoundsException(i);
		return (vector[i / INT_SIZE] & (1 << (i % INT_SIZE))) != 0;
	}

	public void set(int i, boolean flag) {
		if (i < 0 || i >= length)
			throw new ArrayIndexOutOfBoundsException(i);
		if (flag)
			vector[i / INT_SIZE] |= 1 << (i % INT_SIZE); // mask like: 1000
		else
			vector[i / INT_SIZE] &= ~(1 << (i % INT_SIZE)); // mask like: 0111
	}

	public void print() {
		for (int v : vector) {
			for (int i = 0; i < INT_SIZE; i++) {
				System.out.print((v >> i & 1) - 0);
			}
		}
		System.out.println();
	}

	public static void main(String[] args) {
		DesignBitVector bitVector = new DesignBitVector(10);
		bitVector.print();
		bitVector.set(1, true);
		bitVector.set(3, true);
		bitVector.set(5, true);
		bitVector.print();
		assert bitVector.get(3);
		assert !bitVector.get(4);
		assert bitVector.get(5);
		bitVector.set(1, false);
		bitVector.set(3, true);
		bitVector.set(5, false);
		bitVector.print();
		assert !bitVector.get(5);
	}
}
```  

### Check Power Of Two

Write a program to check if an integer is a power of two.   
How many ways can you provide?

```java
public class PowerOfTwo {
	// Divides by 2 until the quotient is odd or less than 1
	public static boolean isPowerOfTwo1(int x) {
		while ((x % 2 == 0) && x > 1) {
			x /= 2;
		}
		return x == 1;
	}

	// Compute each power of two incrementally until x is less than or equal to the value
	public static boolean isPowerOfTwo2(int x) {
		int powerOfTwo = 1;
		while (powerOfTwo < x && powerOfTwo < Integer.MAX_VALUE / 2) {
			powerOfTwo *= 2;
		}
		return x == powerOfTwo;
	}

	// Binary search of precomputed powers of two stored in an array
	// To improve multiple calls, declare powersOfTwo as global variable
	public static boolean isPowerOfTwo3(int x) {
		int[] powersOfTwo = { 1, 2, 4, 8, 16, 32, 64, 128, 256, 512, 1024, 2048, 4096, 8192, 16384, 32768, 65536,
				131072, 262144, 524288, 1048576, 2097152, 4194304, 8388608, 16777216, 33554432, 67108864, 134217728,
				268435456, 536870912, 1073741824 };
		if (x == 0)
			return false;
		if (x == 1)
			return true;
		int left = 0, right = powersOfTwo.length - 1;
		while (left <= right) {
			int middle = left + (right - left) / 2;
			if (x == powersOfTwo[middle])
				return true;
			else if (x < powersOfTwo[middle])
				right = middle - 1;
			else
				left = middle + 1;
		}
		return false;
	}

	// Counting 1 bits until more than 1 bit found
	public static boolean isPowerOfTwo4(int x) {
		int numOfOneBits = 0;
		while (x > 0 && numOfOneBits <= 1) {
			if ((x & 1) == 1)
				numOfOneBits++;
			x >>= 1;
		}
		return numOfOneBits == 1;
	}

	// Remove all right 0 bits, equivalent of the divide by two
	public static boolean isPowerOfTwo5(int x) {
		while ((x & 1) == 0 && x > 1) {
			x >>= 1;
		}
		return x == 1;
	}

	// Shortcut: Decrement and Compare
	public static boolean isPowerOfTwo6(int x) {
		return (x != 0) && (x & (x - 1)) == 0;
	}

	// Shortcut: Complement and Compare
	public static boolean isPowerOfTwo7(int x) {
		return (x != 0) && (x & (~x + 1)) == x;
	}

	public static void main(String[] args) {
		assert isPowerOfTwo1(524288) == true;
		assert isPowerOfTwo1(16392) == false;
		assert isPowerOfTwo2(524288) == true;
		assert isPowerOfTwo2(16392) == false;
		assert isPowerOfTwo3(524288) == true;
		assert isPowerOfTwo3(16392) == false;
		assert isPowerOfTwo4(524288) == true;
		assert isPowerOfTwo4(16392) == false;
		assert isPowerOfTwo5(524288) == true;
		assert isPowerOfTwo5(16392) == false;
		assert isPowerOfTwo6(524288) == true;
		assert isPowerOfTwo6(16392) == false;
		assert isPowerOfTwo7(524288) == true;
		assert isPowerOfTwo7(16392) == false;
	}
}
```

### Max XOR of Two Nums

Maximum XOR of Two Numbers in an Array

Given a non-empty array of numbers, a0, a1, a2, … , an-1, where 0 ≤ ai < 231.

Find the maximum result of ai XOR aj, where 0 ≤ i, j < n.

Could you do this in O(n) runtime?

Example: Input: [3, 10, 5, 25, 2, 8] Output: 28

Explanation: The maximum result is 5 ^ 25 = 28.

Solution: Actually it's faster to just use brute force and keep tracking the max xor, which is log(n^2). Let build a Trie tree to solve this question in log(n).

```java
public class XorTwoNums {
	class TreeNode {
		int val;
		TreeNode zero, one;
		boolean isEnd;
	}

	class TrieTree {
		TreeNode root;

		public TrieTree() {
			this.root = new TreeNode();
		}

		public void insert(int num) {
			TreeNode current = root;
			int j = 1 << 30;
			while (j > 0) {
				int b = (j & num) == 0 ? 0 : 1;
				if (b == 0 && current.zero == null)
					current.zero = new TreeNode();
				else if (b == 1 && current.one == null)
					current.one = new TreeNode();
				current = b == 0 ? current.zero : current.one;
				j >>= 1;
			}
			current.val = num;
			current.isEnd = true;
		}

	}

	public int findMaximumXOR(int[] nums) {
		if (nums.length < 2)
			return 0;
		TrieTree tree = new TrieTree();
		for (int num : nums) {
			tree.insert(num);
		}
		TreeNode now = tree.root;
		while (now.one == null || now.zero == null)
			now = now.one == null ? now.zero : now.one;
		return findMaximumXOR(now.zero, now.one);
	}

	public int findMaximumXOR(TreeNode zero, TreeNode one) {
		if (zero.isEnd && one.isEnd)
			return one.val ^ zero.val;
		if (zero.zero == null)
			return findMaximumXOR(zero.one, one.zero == null ? one.one : one.zero);
		else if (zero.one == null)
			return findMaximumXOR(zero.zero, one.one == null ? one.zero : one.one);
		else if (one.zero == null)
			return findMaximumXOR(zero.zero, one.one);
		else if (one.one == null)
			return findMaximumXOR(zero.one, one.zero);
		else // split to 2 branches
			return Math.max(findMaximumXOR(zero.zero, one.one), findMaximumXOR(zero.one, one.zero));
	}

	public static void main(String[] args) {
		XorTwoNums solution = new XorTwoNums();
		assert solution.findMaximumXOR(new int[] { 8, 10, 2 }) == 10;
	}
}
```

### Island Perimeter

You are given a map in form of a two-dimensional integer grid where 1 represents land and 0 represents water. Grid cells are connected horizontally/vertically (not diagonally). The grid is completely surrounded by water, and there is exactly one island (i.e., one or more connected land cells). The island doesn't have "lakes" (water inside that isn't connected to the water around the island). One cell is a square with side length 1. The grid is rectangular, width and height don't exceed 100. Determine the perimeter of the island.

Example:

```
[[0,1,0,0],
 [1,1,1,0],
 [0,1,0,0],
 [1,1,0,0]]

Answer: 16
```

```java
public int islandPerimeter(int[][] grid) {
	if (grid.length == 0 || grid[0].length == 0)
		return 0;
	int perimeter = 0;
	for (int i = 0; i < grid.length; i++) {
		for (int j = 0; j < grid[i].length; j++) {
			if (grid[i][j] == 1) {
				perimeter += 4;
				if (i >= 1 && grid[i - 1][j] == 1)
					perimeter -= 2; // minus the inner joined up neighbors
				if (j >= 1 && grid[i][j - 1] == 1)
					perimeter -= 2; // minus the inner joined left neighbors
			}
		}
	}
	return perimeter;
}
```

### Max Area of Island

Given a non-empty 2D array grid of 0's and 1's, an island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.

Find the maximum area of an island in the given 2D array. (If there is no island, the maximum area is 0.)

```
Example 1:
[[0,0,1,0,0,0,0,1,0,0,0,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 [0,1,0,0,1,1,0,0,1,0,1,0,0],
 [0,1,0,0,1,1,0,0,1,1,1,0,0],
 [0,0,0,0,0,0,0,0,0,0,1,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]
Given the above grid, return 6. Note the answer is not 11, because the island must be connected 4-directionally.
```

Time Complexity: O(R∗C), where R is the number of rows in the given grid, and C is the number of columns. We visit every square once.
Space complexity: O(R∗C), the space used by seen to keep track of visited squares, and the space used by the call stack during our recursion.

```java
public int maxAreaOfIsland(int[][] grid) {
		boolean[][] seen = new boolean[grid.length][grid[0].length];
		int ans = 0;
		for (int r = 0; r < grid.length; r++) {
				for (int c = 0; c < grid[0].length; c++) {
						ans = Math.max(ans, extendArea(r, c, grid, seen));
				}
		}
		return ans;
}

public int extendArea(int r, int c, int[][] grid, boolean[][] seen) {
		if (r < 0 || r >= grid.length || c < 0 || c >= grid[0].length || seen[r][c] || grid[r][c] == 0) // NOTE: grid[r][c] == 0
				return 0;
		seen[r][c] = true;
		return (1 + extendArea(r + 1, c, grid, seen) + extendArea(r - 1, c, grid, seen)
						+ extendArea(r, c - 1, grid, seen) + extendArea(r, c + 1, grid, seen));
}
```

### Number of Islands

Given a non-empty 2D array grid of 0's and 1's, an island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.

Count the number of islands and count the number of **distinct** islands. An island is considered to be the same as another if and only if one island can be translated (and not rotated or reflected) to equal the other.

Solution 1:

We can clear the joined lands recursively, this will modify the grid.

```java
int[][] dirs = { { 0, 1 }, { 1, 0 }, { -1, 0 }, { 0, -1 } };

public int numIslands(char[][] grid) {
	if (grid == null || grid.length == 0)
		return 0;
	int count = 0;
	for (int i = 0; i < grid.length; i++) {
		for (int j = 0; j < grid[i].length; j++) {
			if (grid[i][j] == '1') {
				count++;
				clearJoinedLands(grid, i, j);
			}
		}
	}
	return count;
}

private void clearJoinedLands(char[][] grid, int i, int j) {
	if (i < 0 || i >= grid.length || j < 0 || j >= grid[i].length || grid[i][j] == '0')
		return;
	grid[i][j] = '0';
	for (int[] dir : dirs) {
		clearJoinedLands(grid, i + dir[0], j + dir[1]);
	}
	return;
}
```

```java
int[][] dirs = { { 0, 1 }, { 1, 0 }, { -1, 0 }, { 0, -1 } };

public int numDistinctIslands(int[][] grid) {
	Set<String> islands = new HashSet<>();
	for (int i = 0; i < grid.length; i++) {
		for (int j = 0; j < grid[i].length; j++) {
			if (grid[i][j] != 0) {
				StringBuilder sb = new StringBuilder();
				dfs(grid, i, j, sb, "o"); // origin
				// grid[i][j] = 0;
				islands.add(sb.toString());

			}
		}
	}
	return islands.size();
}

private void dfs(int[][] grid, int i, int j, StringBuilder sb, String dir) {
	if (i < 0 || i >= grid.length || j < 0 || j >= grid[i].length || grid[i][j] == 0)
		return;
	sb.append(dir);
	grid[i][j] = 0;
	dfs(grid, i - 1, j, sb, "u");
	dfs(grid, i, j + 1, sb, "r");
	dfs(grid, i + 1, j, sb, "d");
	dfs(grid, i, j - 1, sb, "l");
	sb.append("b"); // bound
}
```

Solution 2:

Make use of a Union Find (compressed path) data structure of size m*n to store all the nodes in the graph and initially each node's parent value is set to -1 to represent an empty graph. Our goal is to update Union Find with each land (1) and union lands belong to the same island.

For each land (1) position (row, col), union it with its adjacent neighbors if they belongs to some islands, if none of its neighbors belong to any islands, then initialize the new position as a new island (set parent value to itself) within Union Find.

![Quick Union Overview](https://algs4.cs.princeton.edu/15uf/images/quick-union-overview.png)

_The Union Find is simple and efficient, but the Depth-first Search is even simper and more efficient. It is a fundamental recursive method that follows the graph's edges to find the vertices connected to the source._

```java
int[][] dirs = { { 0, 1 }, { 1, 0 }, { -1, 0 }, { 0, -1 } };

	public int numIslands2(char[][] grid) {
		if (grid == null || grid.length == 0)
			return 0;
		int count = 0;

		int m = grid.length;
		int n = grid[0].length;
		int[] roots = new int[m * n];
		Arrays.fill(roots, -1);

		for (int i = 0; i < grid.length; i++) {
			for (int j = 0; j < grid[i].length; j++) {
				if (grid[i][j] == '1') {
					int root = n * i + j;
					roots[root] = root;
					count++;

					for (int[] dir : dirs) {
						int x = i + dir[0];
						int y = j + dir[1];
						int neighbor = n * x + y;
						if (x < 0 || x >= m || y < 0 || y >= n || roots[neighbor] == -1)
							continue;
						int rootNeighbor = findRootIsland(roots, nb);
						if (root != rootNeighbor) {
							roots[root] = rootNeighbor; // union two islands
							root = rootNeighbor;
							count--;
						}
					}
				}
			}
		}

		return count;
	}

	private int findRootIsland(int[] roots, int id) {
		while (id != roots[id]) {
			roots[id] = roots[roots[id]]; // compress path
			id = roots[id];
		}
		return id;
	}
```

# Reference Resources
- [Source Code on GitHub](https://github.com/codebycase/algorithms-java/tree/master/src/main/java/a01-fundamentals)
- [Character encodings: Essential concepts](https://www.w3.org/International/articles/definitions-characters/)
