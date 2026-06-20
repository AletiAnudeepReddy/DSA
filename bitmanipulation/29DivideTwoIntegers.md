# 29. Divide Two Integers

**LeetCode:** 29  
**Difficulty:** Medium

## Problem

Given two integers `dividend` and `divisor`, divide them **without using**

- Multiplication (`*`)
- Division (`/`)
- Modulus (`%`)

The result should be truncated toward zero.

If the answer overflows the 32-bit signed integer range, return:

- `Integer.MAX_VALUE` if overflow is positive.
- `Integer.MIN_VALUE` if overflow is negative.

---

# Approach 1 - Repeated Subtraction (Brute Force)

### Idea

Keep subtracting the divisor from the dividend until the remaining value becomes smaller than the divisor.

Example:

```
22 / 3

22 - 3 = 19
19 - 3 = 16
16 - 3 = 13
13 - 3 = 10
10 - 3 = 7
7 - 3 = 4
4 - 3 = 1

Stopped because 1 < 3

Answer = 7
```

### Code

```java
class Solution {
    public int divide(int dividend, int divisor) {
        int sum = 0;
        int cnt = 0;

        while (sum + divisor <= dividend) {
            sum += divisor;
            cnt++;
        }

        return cnt;
    }
}
```

### Problems with this approach

### 1. Time Limit Exceeded

Suppose

```
dividend = 2,000,000,000
divisor = 1
```

We perform nearly **2 billion iterations**.

Time Complexity:

```
O(dividend/divisor)
```

which is far too slow.

---

### 2. Doesn't handle negative numbers

Example

```
-10 / 3
10 / -3
-10 / -3
```

The loop condition no longer works correctly.

---

# Approach 2 - Binary Exponential Subtraction (Bit Manipulation)

## Observation

Instead of subtracting the divisor **one time**, subtract the **largest possible multiple** of the divisor.

Instead of

```
22 - 3 - 3 - 3 - ...
```

subtract

```
12
then
6
then
3
```

Much fewer operations.

---

## Key Idea

Instead of finding

```
3
6
9
12
15
18
21
```

(which requires multiplication)

we use powers of two.

```
3 × 2^0 = 3
3 × 2^1 = 6
3 × 2^2 = 12
3 × 2^3 = 24 (too large)
```

Notice

```
3 × 2^k
```

can be computed using

```
3 << k
```

because

```
x << k = x × 2^k
```

---

## Example Walkthrough

### dividend = 22

### divisor = 3

Initially

```
Dividend = 22
Quotient = 0
```

---

### Step 1

Find the largest power of two.

```
3 << 0 = 3
3 << 1 = 6
3 << 2 = 12
3 << 3 = 24 (too large)
```

Largest valid multiple

```
12
```

Subtract it

```
22 - 12 = 10
```

Add

```
2^2 = 4
```

Current quotient

```
q = 4
```

---

### Step 2

Remaining dividend

```
10
```

Again,

```
3
6
12 (too large)
```

Largest is

```
6
```

Subtract

```
10 - 6 = 4
```

Add

```
2^1 = 2
```

Current quotient

```
q = 6
```

---

### Step 3

Remaining

```
4
```

Largest multiple

```
3
```

Subtract

```
4 - 3 = 1
```

Add

```
2^0 = 1
```

Current quotient

```
q = 7
```

---

### Step 4

Remaining

```
1
```

Since

```
1 < 3
```

Stop.

Answer

```
7
```

---

## Why add `2^cnt`?

Suppose

```
12 = 3 × 4
```

Since

```
4 = 2^2
```

we removed

```
4 divisors
```

So we add

```
4
```

to the quotient.

Instead of writing

```
q += 4
```

we write

```java
q += 1 << cnt;
```

because

```
1 << cnt = 2^cnt
```

---

## Handling Negative Numbers

Division rules:

```
+ / + = +
+ / - = -
- / + = -
- / - = +
```

We compute

```
|dividend|
|divisor|
```

using absolute values.

Finally apply the sign.

```java
boolean sign = (dividend < 0) == (divisor < 0);
```

If both have the same sign

```
positive result
```

otherwise

```
negative result
```

---

## Why use long?

Consider

```
Math.abs(Integer.MIN_VALUE)
```

```
-2147483648
```

Its positive value

```
2147483648
```

cannot fit inside an `int`.

So

```java
long n = Math.abs((long) dividend);
long d = Math.abs((long) divisor);
```

---

## Overflow Case

Only one case overflows.

```
Integer.MIN_VALUE / -1
```

```
2147483648
```

which exceeds

```
Integer.MAX_VALUE
```

So return

```java
Integer.MAX_VALUE
```

---

# Algorithm

```
Convert dividend and divisor to positive long values.

Store whether answer should be positive or negative.

While dividend >= divisor

    Find the largest shift such that

        divisor << (shift + 1)

    is still <= dividend.

    Add

        1 << shift

    to quotient.

    Subtract

        divisor << shift

    from dividend.

Apply sign.

Return answer.
```

---

# Complexity

Finding the largest shift takes at most **31 iterations**.

The outer loop also runs at most **31 times** because each subtraction removes at least half of the remaining dividend.

Therefore,

**Time Complexity**

```
O((log N)^2)
```

Since the number of bits in a 32-bit integer is fixed (31 shifts), this is effectively **O(1)**.

**Space Complexity**

```
O(1)
```

---

# Java Code

```java
class Solution {
    public int divide(int dividend, int divisor) {

        // Handle overflow
        if (dividend == Integer.MIN_VALUE && divisor == -1) {
            return Integer.MAX_VALUE;
        }

        // Determine the sign of the result
        boolean sign = (dividend < 0) == (divisor < 0);

        // Convert to positive long values
        long n = Math.abs((long) dividend);
        long d = Math.abs((long) divisor);

        long q = 0;

        while (n >= d) {

            int cnt = 0;

            // Find the largest power of two multiple
            while (n >= (d << (cnt + 1))) {
                cnt++;
            }

            // Add the corresponding power of two
            q += 1L << cnt;

            // Remove that multiple from dividend
            n -= d << cnt;
        }

        // Apply sign
        return sign ? (int) q : (int) -q;
    }
}
```

---

# Key Takeaways

- Repeated subtraction is too slow (**O(N)**).
- Use **left shift (`<<`)** to efficiently compute multiples of the divisor.
- Always subtract the **largest possible power-of-two multiple**.
- Convert to `long` before taking `Math.abs()` to safely handle `Integer.MIN_VALUE`.
- Handle the special overflow case: `Integer.MIN_VALUE / -1`.
- Think of the quotient as the sum of powers of two, just like building a number from its binary representation.