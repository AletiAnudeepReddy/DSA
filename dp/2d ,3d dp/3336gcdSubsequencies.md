Find the Number of Subsequences With Equal GCD (Leetcode 3336)
Approach

The problem asks us to count the number of pairs of disjoint non-empty subsequences such that both subsequences have the same GCD.

Key Observation

For every element in the array, we have exactly three choices:

Ignore the element.
Put it into the first subsequence (seq1).
Put it into the second subsequence (seq2).

Since every element has three choices, a brute-force solution explores 3ⁿ possibilities.

Instead of storing the actual subsequences, we only need to keep track of:

Current index (idx)
GCD of the first subsequence (gcd1)
GCD of the second subsequence (gcd2)

This works because of the property:

gcd(gcd(a, b), c) = gcd(a, b, c)

Whenever we add a new element x to a subsequence, the new GCD becomes

newGCD = gcd(currentGCD, x)

Initially both subsequences are empty, so we represent their GCD as 0.

gcd(0, x) = x

which automatically makes the first inserted element the GCD of that subsequence.

The recursive state is:

solve(idx, gcd1, gcd2)

where

idx → current index
gcd1 → GCD of the first subsequence
gcd2 → GCD of the second subsequence

At every index, we perform three recursive calls:

Skip the current element.
Add it to the first subsequence.
Add it to the second subsequence.

When all elements are processed (idx == n), we count the pair only if

gcd1 == gcd2 && gcd1 != 0

The condition gcd1 != 0 ensures that both subsequences are non-empty because 0 represents an empty subsequence.

Solution 1: Pure Recursion
Algorithm
Try all three choices for every element.
Update the GCD whenever an element is added.
At the end, count the pair if both GCDs are equal and non-zero.
Time Complexity
O(3^n)
Space Complexity
O(n)
class Solution {

    static final int MOD = 1_000_000_007;

    private int solve(int idx, int gcd1, int gcd2, int[] nums) {

        if (idx == nums.length) {
            return (gcd1 == gcd2 && gcd1 != 0) ? 1 : 0;
        }

        long ans = 0;

        // Skip current element
        ans += solve(idx + 1, gcd1, gcd2, nums);

        // Put into first subsequence
        ans += solve(idx + 1, gcd(gcd1, nums[idx]), gcd2, nums);

        // Put into second subsequence
        ans += solve(idx + 1, gcd1, gcd(gcd2, nums[idx]), nums);

        return (int)(ans % MOD);
    }

    public int subsequencePairCount(int[] nums) {
        return solve(0, 0, 0, nums);
    }

    private int gcd(int a, int b) {
        if (a == 0) return b;
        return gcd(b % a, a);
    }
}
Solution 2: Memoization (Top-Down DP)
Idea

Many recursive states are repeated.

The state

(idx, gcd1, gcd2)

may be reached through multiple paths.

Instead of solving it again, store its answer in a DP table.

DP State
dp[idx][gcd1][gcd2]

stores the number of valid pairs from that state onward.

Time Complexity
O(n × 201 × 201)
Space Complexity
O(n × 201 × 201)
import java.util.Arrays;

class Solution {

    static final int MOD = 1_000_000_007;

    int[][][] dp;

    private int solve(int idx, int gcd1, int gcd2, int[] nums) {

        if (idx == nums.length) {
            return (gcd1 == gcd2 && gcd1 != 0) ? 1 : 0;
        }

        if (dp[idx][gcd1][gcd2] != -1)
            return dp[idx][gcd1][gcd2];

        long ans = 0;

        ans += solve(idx + 1, gcd1, gcd2, nums);

        ans += solve(idx + 1, gcd(gcd1, nums[idx]), gcd2, nums);

        ans += solve(idx + 1, gcd1, gcd(gcd2, nums[idx]), nums);

        dp[idx][gcd1][gcd2] = (int)(ans % MOD);

        return dp[idx][gcd1][gcd2];
    }

    public int subsequencePairCount(int[] nums) {

        dp = new int[nums.length][201][201];

        for (int i = 0; i < nums.length; i++) {
            for (int j = 0; j <= 200; j++) {
                Arrays.fill(dp[i][j], -1);
            }
        }

        return solve(0, 0, 0, nums);
    }

    private int gcd(int a, int b) {
        if (a == 0) return b;
        return gcd(b % a, a);
    }
}
Solution 3: Bottom-Up DP (Tabulation)
Idea

In the recursive solution,

solve(idx, gcd1, gcd2)

depends only on

solve(idx + 1, ...)

Therefore, we fill the DP table from the last index towards the beginning.

DP State
dp[idx][gcd1][gcd2]

represents the number of valid ways considering elements from idx to n - 1.

Transition
dp[idx][gcd1][gcd2] =
    dp[idx + 1][gcd1][gcd2]
  + dp[idx + 1][gcd(gcd1, nums[idx])][gcd2]
  + dp[idx + 1][gcd1][gcd(gcd2, nums[idx])]
Base Case

When all elements have been processed,

dp[n][g][g] = 1

for every g > 0.

Time Complexity
O(n × 201 × 201)
Space Complexity
O(n × 201 × 201)
class Solution {

    static final int MOD = 1_000_000_007;

    public int subsequencePairCount(int[] nums) {

        int n = nums.length;

        int[][][] dp = new int[n + 1][201][201];

        for (int g = 1; g <= 200; g++) {
            dp[n][g][g] = 1;
        }

        for (int idx = n - 1; idx >= 0; idx--) {

            for (int gcd1 = 0; gcd1 <= 200; gcd1++) {

                for (int gcd2 = 0; gcd2 <= 200; gcd2++) {

                    long ans = 0;

                    // Skip current element
                    ans += dp[idx + 1][gcd1][gcd2];

                    // Put into first subsequence
                    ans += dp[idx + 1][gcd(gcd1, nums[idx])][gcd2];

                    // Put into second subsequence
                    ans += dp[idx + 1][gcd1][gcd(gcd2, nums[idx])];

                    dp[idx][gcd1][gcd2] = (int)(ans % MOD);
                }
            }
        }

        return dp[0][0][0];
    }

    private int gcd(int a, int b) {
        if (a == 0) return b;
        return gcd(b % a, a);
    }
}