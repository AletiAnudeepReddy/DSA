# 3658. GCD of Odd and Even Sums

## Problem Statement

Given an integer `n`, compute:

- **sumOdd**: Sum of the first `n` positive odd numbers.
- **sumEven**: Sum of the first `n` positive even numbers.

Return the **Greatest Common Divisor (GCD)** of these two sums.

---

## Approach

### Step 1: Calculate the Sum of Odd Numbers
The first `n` odd numbers are:

`1, 3, 5, 7, ...`

Iterate from `1` to `n` and add `(2 * i - 1)` to `odd_sum`.

### Step 2: Calculate the Sum of Even Numbers
The first `n` even numbers are:

`2, 4, 6, 8, ...`

Iterate from `1` to `n` and add `(2 * i)` to `even_sum`.

### Step 3: Find the GCD
Use the **Euclidean Algorithm** to compute the GCD of `odd_sum` and `even_sum`.

The Euclidean Algorithm works as follows:

- If `a == 0`, return `b`.
- Otherwise, recursively compute `gcd(b % a, a)`.

---

## Java Solution

```java
class Solution {
    public int gcd(int a, int b) {
        if (a == 0) {
            return b;
        }
        return gcd(b % a, a);
    }

    public int gcdOfOddEvenSums(int n) {
        int even_sum = 0;
        int odd_sum = 0;

        for (int i = 1; i <= n; i++) {
            even_sum += i * 2;
            odd_sum += i * 2 - 1;
        }

        return gcd(odd_sum, even_sum);
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(n + log(max(sumOdd, sumEven)))`
  - `O(n)` to calculate the sums.
  - `O(log N)` for the Euclidean GCD algorithm.

- **Space Complexity:** `O(log N)`
  - Due to the recursive calls in the GCD function.

---

## Key Takeaways

- The first `n` odd and even numbers are generated using simple arithmetic expressions.
- The Euclidean Algorithm efficiently computes the GCD.
- This solution is simple, readable, and works efficiently within the given constraints.