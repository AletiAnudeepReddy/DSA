Segment Tree — Complete Explanation

A Segment Tree is a data structure used when we have an array and need to perform many range queries and updates efficiently.

The main idea is:

Instead of repeatedly processing an entire range, we divide the array into segments and store information about each segment.

1. Why do we need a Segment Tree?

Suppose:

arr = [2, 1, 5, 3, 4, 7, 6, 8]

Suppose you repeatedly ask:

What is the sum from index 2 to 6?
What is the sum from index 1 to 5?
What is the sum from index 3 to 7?

A simple approach is:

for (int i = l; i <= r; i++) {
    sum += arr[i];
}

For every query, this takes:

O(n)

If there are q queries:

O(n × q)

That can become very slow.

2. Prefix Sum solves static range sum

If the array never changes, prefix sums are better.

For:

arr = [2, 1, 5, 3, 4]

Create:

prefix = [0, 2, 3, 8, 11, 15]

Then:

sum(l, r) = prefix[r + 1] - prefix[l]

Very fast:

Query = O(1)

But there's a problem.

What if:

arr[2] = 10

Now we need to update the prefix sums after index 2.

That costs:

O(n)

So:

Method	Query	Update
Brute force	O(n)	O(1)
Prefix Sum	O(1)	O(n)
Segment Tree	O(log n)	O(log n)

This is where Segment Tree becomes useful.

3. Core idea

Take:

arr = [2, 1, 5, 3, 4, 7, 6, 8]

Instead of storing individual elements only, we build a tree.

                         [0,7]
                       /       \
                    [0,3]      [4,7]
                   /    \      /    \
                [0,1] [2,3] [4,5] [6,7]
                / \    / \    / \    / \
               2   1  5   3  4   7  6   8

Each node represents a range.

For a sum Segment Tree, each node stores the sum of its range.

So:

[0,1] = 2 + 1 = 3
[2,3] = 5 + 3 = 8
[4,5] = 4 + 7 = 11
[6,7] = 6 + 8 = 14

Then:

[0,3] = 3 + 8 = 11
[4,7] = 11 + 14 = 25

Root:

[0,7] = 11 + 25 = 36
4. Think of it as dividing the array

This is the most important intuition.

Suppose we have:

[0 ............... 7]

Split:

[0 .... 3] [4 .... 7]

Then:

[0..3] → [0..1] [2..3]

[4..7] → [4..5] [6..7]

Continue until one element remains.

So Segment Tree is basically:

Divide the array into smaller ranges and store information about each range.

5. How is the tree stored?

We normally don't create actual Node objects.

Instead, we use an array.

For an array of size n, commonly:

int[] tree = new int[4 * n];

Why 4*n?

Because a Segment Tree can require up to roughly 4n positions in this recursive array representation.

6. Building the Segment Tree

Suppose:

arr = [2, 1, 5, 3]

We start with:

build(0, 0, 3)

Meaning:

node = 0
left = 0
right = 3

We calculate:

mid = (left + right) / 2

So:

mid = 1

Split:

[0,3]
 /   \
[0,1] [2,3]

Then continue recursively.

Base case

When:

left == right

we reached one element.

So:

tree[node] = arr[left];
Otherwise

Build both children:

build(leftChild, left, mid);
build(rightChild, mid + 1, right);

Then combine:

tree[node] = tree[leftChild] + tree[rightChild];
7. Basic Java implementation
class SegmentTree {

    int[] tree;
    int n;

    SegmentTree(int[] arr) {
        n = arr.length;
        tree = new int[4 * n];

        build(0, 0, n - 1, arr);
    }

    void build(int node, int left, int right, int[] arr) {

        // Leaf node
        if (left == right) {
            tree[node] = arr[left];
            return;
        }

        int mid = left + (right - left) / 2;

        // Build left subtree
        build(2 * node + 1, left, mid, arr);

        // Build right subtree
        build(2 * node + 2, mid + 1, right, arr);

        // Combine
        tree[node] =
            tree[2 * node + 1] +
            tree[2 * node + 2];
    }
}

Notice:

2 * node + 1

is the left child.

And:

2 * node + 2

is the right child.

This is the same idea as storing a binary tree in an array.

8. Range Query

Now suppose we want:

sum(1, 3)

For:

arr = [2, 1, 5, 3]

Answer:

1 + 5 + 3 = 9

The important part is that we don't visit every element.

We ask each segment:

Does this segment completely belong to my query?

There are three cases.

Case 1: Completely outside

Suppose query is:

[1,3]

and current segment is:

[0,0]

No overlap.

Return:

0

For sum.

Case 2: Completely inside

Suppose query is:

[1,3]

and current segment is:

[2,3]

This entire segment is needed.

So simply return:

tree[node]

We don't need to go deeper.

This is where Segment Tree gets its efficiency.

Case 3: Partial overlap

Suppose query is:

[1,3]

and current segment is:

[0,3]

Only part of it is required.

So we go to both children.

9. Query code
int query(int node, int left, int right, int ql, int qr) {

    // Completely outside
    if (qr < left || right < ql) {
        return 0;
    }

    // Completely inside
    if (ql <= left && right <= qr) {
        return tree[node];
    }

    int mid = left + (right - left) / 2;

    int leftSum =
        query(2 * node + 1, left, mid, ql, qr);

    int rightSum =
        query(2 * node + 2, mid + 1, right, ql, qr);

    return leftSum + rightSum;
}
10. Let's dry run

Array:

[2, 1, 5, 3]

Query:

sum(1,3)

Tree:

              [0,3] = 11
             /       \
        [0,1] = 3   [2,3] = 8
        /    \       /    \
       2      1     5      3

Query [1,3].

Start:

[0,3]

Partial overlap.

Go left:

[0,1]

Partial overlap.

Split:

[0,0] → outside → 0
[1,1] → inside → 1

Go right:

[2,3]

Completely inside.

Return:

8

Final:

0 + 1 + 8 = 9

We never individually process 5 and 3.

We simply used:

tree[2,3] = 8

That's the main power of Segment Tree.

11. Update

Now suppose:

arr[2] = 10

Originally:

arr = [2,1,5,3]

becomes:

arr = [2,1,10,3]

We need to update the nodes containing index 2.

Path:

[0,3]
   ↓
[2,3]
   ↓
[2]

Update the leaf:

[2] = 10

Then recalculate its parents.

[2,3] = 10 + 3 = 13

Then:

[0,3] = 3 + 13 = 16

Only one path changed.

That's why update is:

O(log n)
12. Update code
void update(int node, int left, int right, int index, int value) {

    // Reached the element
    if (left == right) {
        tree[node] = value;
        return;
    }

    int mid = left + (right - left) / 2;

    if (index <= mid) {
        update(
            2 * node + 1,
            left,
            mid,
            index,
            value
        );
    } else {
        update(
            2 * node + 2,
            mid + 1,
            right,
            index,
            value
        );
    }

    // Recalculate current node
    tree[node] =
        tree[2 * node + 1] +
        tree[2 * node + 2];
}
13. Complete Segment Tree
class SegmentTree {

    int[] tree;
    int n;

    SegmentTree(int[] arr) {
        n = arr.length;
        tree = new int[4 * n];

        build(0, 0, n - 1, arr);
    }

    // Build
    void build(int node, int left, int right, int[] arr) {

        if (left == right) {
            tree[node] = arr[left];
            return;
        }

        int mid = left + (right - left) / 2;

        build(2 * node + 1, left, mid, arr);
        build(2 * node + 2, mid + 1, right, arr);

        tree[node] =
            tree[2 * node + 1] +
            tree[2 * node + 2];
    }

    // Range Query
    int query(int node, int left, int right,
              int ql, int qr) {

        // No overlap
        if (qr < left || right < ql) {
            return 0;
        }

        // Complete overlap
        if (ql <= left && right <= qr) {
            return tree[node];
        }

        int mid = left + (right - left) / 2;

        int leftSum =
            query(2 * node + 1, left, mid, ql, qr);

        int rightSum =
            query(2 * node + 2, mid + 1, right, ql, qr);

        return leftSum + rightSum;
    }

    // Point Update
    void update(int node, int left, int right,
                int index, int value) {

        if (left == right) {
            tree[node] = value;
            return;
        }

        int mid = left + (right - left) / 2;

        if (index <= mid) {
            update(
                2 * node + 1,
                left,
                mid,
                index,
                value
            );
        } else {
            update(
                2 * node + 2,
                mid + 1,
                right,
                index,
                value
            );
        }

        tree[node] =
            tree[2 * node + 1] +
            tree[2 * node + 2];
    }
}

Usage:

int[] arr = {2, 1, 5, 3, 4};

SegmentTree st = new SegmentTree(arr);

int sum = st.query(0, 0, arr.length - 1, 1, 3);

st.update(0, 0, arr.length - 1, 2, 10);
14. Why is it O(log n)?

The tree looks like:

                  n
                /   \
              n/2   n/2
             / \     / \
           n/4 ... 

Every time we go down one level, the range approximately gets divided by 2.

So the height is:

log₂(n)

Therefore:

Build

Every element is processed once:

O(n)
Point Update

We travel from root to leaf:

O(log n)
Range Query

We traverse a limited number of tree nodes:

O(log n)

More precisely, a standard segment-tree range query is O(log n) for the usual fixed-range aggregate setting.

Space
O(n)

Usually we allocate:

new int[4 * n]
15. The most important concept: Overlap

If you understand these three cases, you understand the heart of Segment Tree.

For current segment:

[left, right]

and query:

[ql, qr]
No overlap
qr < left || right < ql

Return identity.

For sum:

0
Complete overlap
ql <= left && right <= qr

Return:

tree[node]
Partial overlap

Otherwise:

go left
go right
combine results

Visualize it as:

                Current Segment
                     |
          +----------+----------+
          |          |          |
       Outside    Inside     Partial
          |          |          |
        return 0   return     recurse
                   tree[node]
16. Segment Tree isn't only for sum

This is very important.

The tree doesn't inherently mean sum.

It can store many different things.

Range Sum
left + right

Identity:

0
Range Minimum
Math.min(left, right)

Identity:

Integer.MAX_VALUE
Range Maximum
Math.max(left, right)

Identity:

Integer.MIN_VALUE
GCD
gcd(left, right)

Identity:

0

So the general idea is:

Segment Tree
     |
     +---- What information do I store?
     |
     +---- How do I combine two children?
     |
     +---- What is the identity value?
17. Example: Range Minimum Query

Suppose:

arr = [5, 2, 7, 1, 3]

Instead of:

tree[node] = leftSum + rightSum

we do:

tree[node] = Math.min(
    tree[leftChild],
    tree[rightChild]
);

Then:

query(1, 4)

returns the minimum value in that range.