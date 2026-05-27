# Top View of Binary Tree

**Difficulty:** Medium  
**Topics:** Binary Tree, BFS, Queue, TreeMap

---

# Problem Statement

Given the root of a binary tree, return the **Top View** of the binary tree.

The **Top View** consists of the nodes visible when the tree is viewed from the top.

If multiple nodes have the same **Horizontal Distance (HD)**, only the **first (topmost)** node is included.

---

# Intuition

Assign every node a **Horizontal Distance (HD)**.

```
Root       -> HD = 0
Left Child -> HD - 1
Right Child-> HD + 1
```

Now perform **Level Order Traversal (BFS)**.

Since BFS visits nodes level by level, the **first node** encountered for every horizontal distance is always the topmost node.

Store that node in a `TreeMap`.

If another node comes with the same HD, simply ignore it.

---

# Horizontal Distance Example

```
           1
         /   \
        2     3
       / \   / \
      4   5 6   7
```

Assign HDs

```
             1(0)

       2(-1)      3(+1)

   4(-2) 5(0) 6(0) 7(+2)
```

Notice

```
HD = 0

Nodes

1
5
6
```

Only **1** appears in the Top View because it is visited first.

---

# Why TreeMap?

The answer should be printed from

```
Leftmost Column

↓

Rightmost Column
```

TreeMap automatically keeps Horizontal Distances sorted.

Example

```
HD

-2
-1
 0
 1
 2
```

Output becomes

```
4 2 1 3 7
```

without any extra sorting.

---

# Why BFS?

Suppose

```
        1
       /
      2
       \
        4
```

Both

```
1

and

4
```

have HD = 0.

Using BFS

Visit

```
1

↓

2

↓

4
```

The first node for HD = 0 is

```
1
```

which is exactly what we need.

---

# Example

```
          1
        /   \
       2     3
      / \   / \
     4   5 6   7
```

Queue

```
(1,0)
```

Visit

```
HD 0

Store 1
```

Push

```
(2,-1)

(3,+1)
```

Visit

```
HD -1

Store 2
```

Visit

```
HD +1

Store 3
```

Visit

```
4

HD -2

Store 4
```

Visit

```
5

HD 0

Already occupied

Ignore
```

Visit

```
6

HD 0

Ignore
```

Visit

```
7

HD +2

Store
```

Final Map

```
HD

-2 -> 4

-1 -> 2

 0 -> 1

+1 -> 3

+2 -> 7
```

Answer

```
[4,2,1,3,7]
```

---

# Algorithm

1. Create a queue storing `(Node, Horizontal Distance)`.
2. Insert the root with HD = 0.
3. While the queue is not empty:
   - Remove the front node.
   - If its HD is not present in the map, store it.
   - Push left child with `HD - 1`.
   - Push right child with `HD + 1`.
4. Traverse the TreeMap and collect the values.

---

## Complexity

- **Time Complexity:** `O(n log n)`

  - BFS visits every node once.
  - TreeMap operations take `O(log n)`.

- **Space Complexity:** `O(n)`

---

## Java Solution

```java
class Pair {
    Node node;
    int hd;

    Pair(Node node, int hd) {
        this.node = node;
        this.hd = hd;
    }
}

class Solution {

    static ArrayList<Integer> topView(Node root) {

        ArrayList<Integer> ans = new ArrayList<>();

        if (root == null) {
            return ans;
        }

        Map<Integer, Integer> map = new TreeMap<>();
        Queue<Pair> queue = new LinkedList<>();

        queue.offer(new Pair(root, 0));

        while (!queue.isEmpty()) {

            Pair current = queue.poll();

            Node node = current.node;
            int hd = current.hd;

            if (!map.containsKey(hd)) {
                map.put(hd, node.data);
            }

            if (node.left != null) {
                queue.offer(new Pair(node.left, hd - 1));
            }

            if (node.right != null) {
                queue.offer(new Pair(node.right, hd + 1));
            }
        }

        for (int value : map.values()) {
            ans.add(value);
        }

        return ans;
    }
}
```

---

# Visualization

```
              1
            /   \
           2     3
          / \   / \
         4   5 6   7
```

Horizontal Distances

```
4  -> -2

2  -> -1

1  ->  0

3  -> +1

7  -> +2
```

Top View

```
4 2 1 3 7
```

---

# Key Takeaways

- Assign every node a **Horizontal Distance (HD)**.
- Perform **BFS (Level Order Traversal)**.
- Store only the **first node** encountered for each HD.
- Use a **TreeMap** so columns remain automatically sorted.
- BFS guarantees the first node at each HD is the topmost visible node.

### Memory Trick

```
Root

↓

HD = 0

↓

Left -> HD - 1

Right -> HD + 1

↓

BFS

↓

First node for every HD

↓

TreeMap

↓

Top View
```