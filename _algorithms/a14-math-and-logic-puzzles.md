---
title: Algorithms 14 - Math and Logic Puzzles
key: a14-math-and-logic-puzzles
tags: Math Puzzle Probability
---

## Prime Numbers

A prime number (or a prime) is a natural number greater than 1 that has no positive divisors other than 1 and itself.

Every positive integer can be decomposed into a product of primes that is unique up to ordering. For example:
84 = 2^2 * 3^1 * 5^0 * 7^1 * 11^0 * 13^0 * 17^0 * ...

<!--more-->

### Divisibility

The prime number law stated above means that, in order for a number x to divide a number y (x % y == 0), all primes in x's prime factorization must be in y's prime factorization.

In fact, the greatest common divisor of x and y (gcd(x, y)) and least common multiple of x and y (lcm(x, y)) will form this equation: `gcd(x, y) * lcm(x, y) = x * y`.

### Checking for Primality

```java
/**
 * The sqrt(n) is sufficient because where a * b = n, if a > sqrt(n), then b < sqrt(n), and we already checked b.
 */
public static boolean checkPrimality(int n) {
  if (n < 2)
    return false;
  int sqrt = (int) Math.sqrt(n);
  for (int i = 2; i <= sqrt; i++) {
    if (n % i == 0)
      return false;
  }
  return true;
}
```
### Collect a List of Primes

Also call Count Primes

The Sieve of Eratosthenes is a highly efficient way to generate a list of primes. It works by recognizing that all non-prime numbers are divisible by a prime number.

_There are number of optimizations that can be applied. One simple one is to only use odd numbers in the array, which would reduce the space usage by half._

```java
public static List<Integer> generatePrimes(int max) {
  List<Integer> primes = new ArrayList<>();
  boolean[] flags = new boolean[max + 1];

  // Set all flags to true other than 0 and 1
  Arrays.fill(flags, true);
  flags[0] = false;
  flags[1] = false;

  int prime = 2;
  while (prime <= Math.sqrt(max)) {
    // Cross off remaining multiples of prime
    // Starts with (prime * prime) because if we have a k * prime, where k < prime, this value
    // would have already been crossed off in a prior iteraton
    for (int i = prime * prime; i < flags.length; i += prime) {
      flags[i] = false;
    }
    // Find next value which is true
    prime = prime + 1;
    while (prime < flags.length && !flags[prime]) {
      prime++;
    }
  }
  // Collect all prime numbers
  for (int i = 0; i < flags.length; i++) {
    if (flags[i])
      primes.add(i);
  }

  return primes;
}
```

```java
public int countPrimes(int n) {
    boolean[] notPrime = new boolean[n];
    int count = 0;
    for (int i = 2; i < n; i++) {
        if (notPrime[i] == false) {
            count++;
            for (int j = 2; i * j < n; j++) {
                notPrime[i * j] = true;
            }
        }
    }
    return count;
}
```

```java
/* Count number of pairs of prime numbers p, q such that p * q <= n */
public static int countPrimePairs(int n) {
    int count = 0;
    List<Integer> primes = getPrimeNumbers(n);
    int i = 0, j = primes.size() - 1;
    while (i < j) {
        if (primes.get(i) * primes.get(j) > n) {
            j--;
        } else {
            count += j - i;
            i++;
            j--; // optional
        }
    }
    return count;
}

public static List<Integer> getPrimeNumbers(int n) {
    List<Integer> primes = new ArrayList<>();
    boolean[] notPrime = new boolean[n];
    for (int i = 2; i < n; i++) {
        if (notPrime[i] == false) {
            primes.add(i);
            for (int j = 2; i * j < n; j++) {
                notPrime[i * j] = true;
            }
        }
    }
    return primes;
}
```

## Probability

### Probability of A and B

Image we were picking a number between 1 and 10 (inclusive). What's the probability of picking a number that is both even and between 1 and 5? the odds of picking a number between 1 and 5 is 50%, and the odds of a number between 1 and 5 being even is 40%. So, the odds of doing both are:

```
P(x is even and x <= 5) = P(x is even given x <= 5) P(x <= 5)
                        = (2/5) * (1/2)
                        = 1/5
```

Observe that since `P(A and B) = P(B given A) P(A) = P(A given B) P(B)`, you can express the probability of A given B in terms of the reverse: `P(A given B) = P(B given A) P(A) / P(B)`.

### Probability of A or B

Image we were picking a number between 1 and 10 (inclusive). What's the probability of picking an even number or a number between 1 and 5?

```
P(x is even or x <= 5) = P(x is even) + P(x <= 5) - P(x is even and x <= 5)
                       = 1/2 + 1/2 - 1/5
                       = 4/5
```

Formula: `P(A or B) = P(A) + P(B) - P(A and B)`

### Pick all odd numbers

There are unique numbers 1 through 7, what's the probability to pick up the first all odd numbers without replacement?

Solution:

- We don't care the order
- All odd numbers are: 1, 3, 5, 7, and only one event for this
- All events to pick up 4 numbers are: (7*6*5*4)/(4*3*2*1) = 35
- So the answer is 1/35

### Probability of Transportation

Justin lives in Saint Paul and goes to school in Minneapolis. In the morning, he has 3 transportation options (bus, cab, or train) to school, and in the evening he has the same 3 choices for his trip home.

Question1: If Justin randomly chooses his ride in the morning and in the evening, what is the probability that he'll use both the bus and the train?

P(both bus) = 1/3 * 1/ 3 = 1/9  
P(both bus or bus train) = 1/9 + 1/9 = 2/9

Question2: If Justin randomly chooses his ride in the morning and in the evening, what is the probability that he'll use at lease once bus as a transportation.

total of both trip = 3 x 3 = 9  
\# of combination with bus = 3 + 3 - 1 = 5  
P(at least once bus) = 5 / 9

### Probability of Flip Coins

Flip a fair coin 5 times, what's the possibility of getting exact 3 heads?

P(each outcome) = (1/2*1/2*1/2*1/2*1/2) = 1/32

\# of poss. of 3 heads = (5 * 4 * 3) / (3 * 2 * 1) = 10

P(3 heads in 5 times) = 10 / 32 = 5 / 16

Summary: P(k heads in n flips) = (n!/((n-k)!k!)) / 2^n

_We don't care about the order of k heads!_

### Probability of All 1s Cards

A card game using 36 unique cards: four suits (diamonds, hearts, clubs, and spades) with cards number from 1 to 9 in each suit. A hand is a collection of 9 cards, which can be sorted however the player chooses. What is the probability of getting all four of the 1s?

P(all 4 1s in my hand of 9) = (# of ways in which event can happen) / (total # of hands)

total # of hands = (36x35x34x33x32x31x30x29x28) / (9x8x7x6x5x4x3x2x1) = 36!/((36-9)!x9!)

\# of hands with 4 1s = (1 1 1 1 32x31x30x29x28) / (5x4x3x2x1) = 32!/((32-5)!x5!)

The result is **2/935**

### Independence & Mutual Exclusivity

If A and B are independent events (that is, an event does not influence the another event), then `P(A and B) = P(A) * P(B)`.

If A and B are mutually exclusive (that is, if one happens, then the other cannot happen), then `P(A or B) = P(A) + P(B)`. This is because `P(A and B) = 0`.

### Complementary Events

When two mutually exclusive events cover all possible outcomes, they are complementary. The sum of all probabilities is thus 100%.

Tower Defense Game: Your castle is defended by five towers. Each tower has a 20% probability of disabling an invader before he reaches the gate. What are the chances of stopping him?

_Never sum the probabilities of independent events, that's a common mistake. Use complementary events twice._

- The 20% change of hitting is complementary to the 80% change of missing. The probability that all towers miss is: 0.8^5 ~= 0.33.

- The event "all towers miss" is complementary to "at least one tower hits". The probability of stopping the enemy is: 1 - 0.33 = 0.67

## Develop Rules and Patterns

In many cases, you will find it useful to write down "rules" or "patterns" that you discover while solving the problem.

Question: You have two ropes, and each takes exactly one hour to burn. How would you use them to time exactly 15 minutes? Node that the ropes are of uneven densities, so half the rope length-wise does not necessarily take half an hour to burn.

Approach is as follows:  
1. Light rope 1 at both ends and rope 2 at one end.  
2. When the two flames on Rope 1 meet, 30 minutes will have passed. Rope 2 has 30 minutes left of burn time.  
3. At that point, light Rope 2 at the other end.  
4. In exactly fifteen minutes, Rope 2 will be completely burnt.

## Worst Case Shifting

If an early decision results in a skewing of the worst case, we can sometimes change the decision to balance out the worst case.

Question: You have nine balls, Either are of the same weight, and one is heavier. You are given a balance which tells you only whether the left side or the right side is heavier. Find the heavy ball in just two uses of the scale.

A first approach is to divide the balls in sets of four, with the ninth ball sitting off to the side. But will result in a worst case of three weighings - one too many!

If we divide the balls into three items each, we will know after just one weighing which set has the heavy one. We can even formalize this into a rule: given N balls, where N is divisible by 3, one use of the scale will point us to a set of x/3 balls with the heavy ball.

## Useful Math

### Sum of Integers 1 through N

`1+2+3+...+n = n(n+1)/2`

Consider the following nested loops:

_There are total n(n-1)/2 total iterations of the inner for loop. Therefore, the code takes O(n^2) time._

```java
for (int i = 0; i < n; i++) {
  for (int j = i + 1; j < n; j++) {
    System.out.println(i + j);
  }
}
```

### Sum of Powers of 2

`2^0 + 2^1 + 2^2 + ... + 2^n = 2^(n+1) - 1`

_Think of it on a binary way, the sum of a sequence of powers of two is roughly equal to the next value in the sequence._

### Base of Logs

$$\log_{10} p = {\log_2 p \over \log_2 10}$$

_Logs of different bases are only off by a constant factor. For this reason, we largely ignore what the base of a log within a big O expression. It doesn't matter since we drop constants anyway._

### Permutations

How many ways are there of rearranging a string of n unique characters?

`n! = n * (n - 1) * (n - 2) * (n - 3) * ... * 1`

What if you were forming a k-length string (with all unique characters) from n total unique characters?

`n! / (n - k)! = n * (n - 1) * (n - 2) * (n - 3) * ... * (n - k + 1)`

_The idea is you have n options for what to put in the first characters, then n - 1 options for the second slot (one option is taken), then n - 2 options for what to put in the third slot, and so on._

### Combinations

Suppose you have a set of n distinct characters. How many ways are there of selecting k characters into a new set (where order doesn't matter)? That is, how many k-sized subsets are there out of n distinct elements?

_From the above Permutation section, we'd have n! / (n - k)! k-length substrings. Since each k-sized subset can be rearranged k! unique ways into a string, each subset will be duplicated k! times in this list of substrings._

$${n \choose k} = {1 \over k!} * {n! \over (n - k)!} = {n! \over k!(n - k)!}$$

### Proof by Induction (归纳证明法)

Induction is a way of proving something to be true. It is closely related to recursion.

Let's use this to prove that there $$2^n$$ subsets of an n-element set.

- Definitions: let $$S = \{a_1, a_2, a_3, ..., a_n\}$$ be the n-element set.
- Base case: Prove there are $$2^0$$ subsets of {}. This is true, since the only subset of {} is {}.
- Assume that there are $$2^n$$ subsets of $$\{a_1, a_2, a_3, ..., a_n\}$$.
- Prove that there are $$2^{n+1}$$ subsets of $$\{a_1, a_2, a_3, ..., a_{n+1}\}$$

  Consider the subsets of $$\{a_1, a_2, a_3, ..., a_{n+1}\}$$. Exactly half will contain $$a_{n+1}$$ and half will not.  
  The subsets that do not contain $$a_{n+1}$$ are just the subsets of $$\{a_1, a_2, a_3, ..., a_n\}$$. We assume there are $$2^n$$ of those.  
  Since we have the same number of subsets with x as without x, there are $$2^n$$ subsets with $$a_{n+1}$$.  
  Therefore, we have $$2^n + 2^n$$ subsets, which is $$2^{n+1}$$.

## Logic Puzzles

### Find the Fastest 3 Horses

25 horses, 5 race tracks. How many races you have to run to  select top 3 horses.

_The idea is once you found No.1, use the logic to get rid of horses as many as possible._

- First race all 25 in groups for 5 to figure out top 3 in each group, call them as below:  

  ```
  A1 B1 C1 D1 E1
  A2 B2 C2 D2 E2
  A3 B3 C3 D3 E3
  ```

- Now race A1 B1 C1 D1 E1 together and get the ranking as A1 > B1 > C1 > D1 > E1.

  So A1 is No.1.   
  Since we just need top 3, we can get rid of D and E groups.  
  Also A1 > B1 > C1, so we can get rid of C2 and C3.  
  Furthermore, A1 > B1 > B2, so let's get rid of B3.

- The final race is among A2 A3 B1 B2 C1 to figure out No.2 and No.3 positions.

### 100 Doors in a Row

You have 100 doors in a row that are all initially closed. you make 100 passes by the doors starting with the first door every time. The first time through you visit every door and toggle the door (if the door is closed, you open it, if its open, you close it). the second time you only visit every 2nd door (door #2, #4, #6). the third time, every 3rd door (door #3, #6, #9), etc, until you only visit the 100th door.

So what state are the doors in after the last pass? which are open which are closed?

Solution:

A door is toggled in ith walk if i divides door number. For example the door number 45 is toggled in (1, 45), (3, 15), (5, 9) walk, the all 3 pairs will make the door switched back to initial state Closed.

But there are door numbers which would become open, for example 16, the pair (4, 4) means only one walk, Similarly all other perfect squares like 4, 9, ...

So the opened doors are: 1, 4, 9, 16, 25, 36, 49, 64, 81, 100.

BTW, we can use binary search to figure out if a number is a perfect square.

### How many bricks in the wall?

A contractor estimated that one of his two bricklayers would take 9 hours to build a certain wall and the other 10 hours. However, he knew from experience that when they worked together, 10 fewer bricks got laid per hour. Since he was in a hurry, he put both men on the job and found it took exactly 5 hours to build the wall. How many bricks did it contain?

Solution:

- Let b = # of bricks in the wall
- b/9 = # of bricks laid per hr by the 1st bricklayer
- b/10 = # of bricks laid per hr by the 2nd bricklayer
- b/9 + b/10 - 10 = # of bricks laid per hr when they work together
- So the equation is (b/9 + b/10 - 10) * 5 = b
- The answer is *900* bricks!

### Optimal Salary in Canada

Citizens of Canada pay as much income tax (percentage-wise) as they make dollars per week. What is the optimal salary in Canada?

Solution:

- The income tax percentage is salary/100
- The function is: income = salary - (salary * salary/100)
- Now we need to figure out what salary can make the max income
  1. Assume salary = 25, then income = 18.75
  2. Assume salary = 35, then income = 22.75
  3. Assume salary = 50, then income = 25
  4. Assume salary = 65, then income = 22.75
  5. Assume salary = 75, then income = 18.75
- After a few calculation, you can see the optimal salary should be $50/week

### Replicate Dice with Coins

How do you replicate a dice with flipping coins?

Solution:

What you can do, is to employ a method called rejection sampling:

- Flip the coin 3 times and interpret each flip as a bit (0 or 1).
- Concatenate the 3 bits, giving a binary number in [0, 7].
- If the number is in [1, 6], take it as a die roll.
- Otherwise, i.e. if the result is 0 or 7, repeat the flips.

Since 6/8 of the possible outcomes lead to termination in each set, the probability of needing more than l sets of flips to get a die roll is 1 - 6/8 = 1/4 which is efficient in practice.

### 2 Eggs and 100-Story Building

You are holding two eggs in a 100-story building. If an egg is thrown out of the window, it will not break if the floor number is less than X, and it will always break if the floor number is equal to or greater than X. What strategy would you use to determine X with the minimum number of drops in a worst case scenario?

Solution:

If **only one egg is available**, We have to drop the egg from the first-floor window; if it survives, drop it from the second floor window until it breaks. In the worst cases, this method may require 100 droppings.

If we use Binary Search Method to find the floor and we start from 50'th floor, then we end up doing 50 comparisons in worst case. The worst case happens when the required floor is 49'th floor.

So we are going to use below equation to optimize the solution.

- Assume we should make our first attempt on x'th floor.
- If it breaks, we try remaining (x-1) floors from bottom one by one until the 2nd egg breaks.
- If it doesn't break, we jump (x - 1) floors (Because we have already made one attempt and there are less (100 - x) floors to try now, we don't want to go beyond x attempts). So next floor we try is x + (x - 1).
- Similarly, if still not break, next need to jump to floor x + (x - 1) + (x -2).
- So x + (x - 1) + (x - 2) + (x - 3) + ... + 1 = 100 => x(x+1)/2 = 100 => x = 13.651

_Therefore, we start trying from 14'th floor, the optimal number of trials is 14 in worst case. Actually, we can tell it'd be the 13'th floor if the egg doesn't break on 12th floor, so it'd be 13 trials in worst case._

### Determine Maximum Floors

As above, given c eggs and a maximum of d allowable drops, what is the maximum number of floors that you can test in the worst-case?

Solution:

Let F(c, d) be maximum number of floors we can test with c identical eggs and at most d drops. F(1, d) = d.

If we are given c eggs and d drops we can start at floor F(c-1,d-1)+1 and drop an egg. If the egg breaks, then we can use the remaining c-1 eggs and d-1 drops to determine the floor exactly, since it must be in the range [1, F(c-1,d-1)], If the egg did not break, we proceed to floor F(c, d-1)+F(c-1,d-1)+1.

Therefore, F satisfied the recurrence: F(c, d) = F(c, d - 1) + F(c - 1, d - 1) + 1;

```java
public class DetermineMaximumFloors {
  public static int getMaxFloors(int eggs, int drops) {
		int[][] dp = new int[eggs + 1][drops + 1];
		for (int i = 0; i < eggs + 1; i++) {
			Arrays.fill(dp[i], -1);
		}
		return getMaxFloors(eggs, drops, dp);
	}

	private static int getMaxFloors(int eggs, int drops, int[][] dp) {
		if (eggs == 0 || drops == 0)
			return 0;
		else if (eggs == 1)
			return drops;
		if (dp[eggs][drops] == -1) {
			dp[eggs][drops] = getMaxFloors(eggs, drops - 1, dp) + getMaxFloors(eggs - 1, drops - 1, dp) + 1;
		}
		return dp[eggs][drops];
	}
}
```

### 10 Floors with 4 Elevators

In a building of 10 floors with 4 elevators, there are 800 or so employees. As with most office buildings we all arrive around the same time and leave around the same time. How to minimize waiting time for elevators.

_The elevator optimization could be on waiting time, cost and routing. All will need to learn the passenger traffic flow/pattern in the building. Like the statistical forecasts including information of entering and exiting passengers per floor and direction at fifteen minute intervals. The three traffic components, i.e. incoming, outgoing and inter-floor components, in a building are forecast._

Solution:

The main idea is the idle elevators should move to a position, automatically, which will best serve the usage relationships/patterns through the day.

- **Morning/post lunch: One-to-many relationship.** All elevators return to the 1st floor automatically when idle. Even most of elevators can go no-stop down to floor and bypass passengers.

- **Mid morning/mid afternoon: Many to many relationship.** Idle elevators return to floors 1, 4, 7 and 10. This pattern puts an elevator within a floor of all floors at all times, while leaves an elevator ready for ground floor arriving guests, and prioritizes an elevator for top floor executives. The 1, 4, 7 and 10 relationship is maintained as elevators are used, so empty elevators move to keep the balance as in-use elevators deliver riders to their chosen floor.

- **Late afternoon: Many-to-one relationship.** Idle elevators return to floors 5, 7, 9 and 10. Elevators will rarely be idle during peak times, but it should be reinforced that most elevator use will be taking people down rather than up. Therefore, moving the idle elevators up to higher floors makes them better positioned to carry people down to the first floor. It can also be assumed that the lowest floors use the stairs at the end of the day already because their position nearest the first floor will always result in an already full elevator being delivered to them when pressing the "call" button.

# Reference Resources
- [Source Code on GitHub](https://github.com/codebycase/algorithms-java/tree/master/src/main/java/a17_math_logic_puzzle)
