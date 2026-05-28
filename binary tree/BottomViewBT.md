# Bottom View of Binary Tree

**Difficulty:** Medium  
**Topics:** Binary Tree, BFS, Queue, TreeMap

---

# Problem Statement

Given the root of a binary tree, return the **Bottom View** of the binary tree.

The **Bottom View** consists of the nodes visible when the tree is viewed from the bottom.

If multiple nodes have the same **Horizontal Distance (HD)**, the **lowest (last visited in level order)** node is included.

---

# Intuition

Assign every node a **Horizontal Distance (HD)**.

```
Root       -> HD = 0
Left Child -> HD - 1
Right Child-> HD + 1
```

Perform **Level Order Traversal (BFS)**.

Unlike **Top View**, we **overwrite** the value for every Horizontal Distance.

Since BFS visits nodes level by level, nodes appearing at lower levels are processed later and overwrite previous values.

Thus, after traversal, the map stores the bottommost node for every HD.

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

For Bottom View,

```
1

↓

5

↓

6
```

The last processed node is

```
6
```

So HD = 0 stores

```
6
```

---

# Why TreeMap?

We need the answer from

```
Leftmost Column

↓

Rightmost Column
```

TreeMap automatically keeps the Horizontal Distances sorted.

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
4 2 6 3 7
```

---

# Why BFS?

BFS processes nodes level by level.

Whenever another node appears at the same Horizontal Distance, we simply replace the previous value.

The last stored node will always be the bottommost visible node.

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

Store
```

Visit

```
5

HD 0

Overwrite 1

Store 5
```

Visit

```
6

HD 0

Overwrite 5

Store 6
```

Visit

```
7

HD +2

Store
```

Final Map

```
-2 -> 4

-1 -> 2

 0 -> 6

+1 -> 3

+2 -> 7
```

Answer

```
[4,2,6,3,7]
```

---

# Algorithm

1. Create a queue storing `(Node, Horizontal Distance)`.
2. Insert the root with HD = 0.
3. While the queue is not empty:
   - Remove the front node.
   - Store its value in the map (**overwrite existing value**).
   - Push left child with `HD - 1`.
   - Push right child with `HD + 1`.
4. Traverse the TreeMap and collect the values.

---

## Complexity

- **Time Complexity:** `O(n log n)`

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

    public ArrayList<Integer> bottomView(Node root) {

        ArrayList<Integer> ans = new ArrayList<>();

        if (root == null) {
            return ans;
        }

        TreeMap<Integer, Integer> map = new TreeMap<>();
        Queue<Pair> queue = new LinkedList<>();

        queue.offer(new Pair(root, 0));

        while (!queue.isEmpty()) {

            Pair current = queue.poll();

            Node node = current.node;
            int hd = current.hd;

            // Always overwrite
            map.put(hd, node.data);

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

6  ->  0

3  -> +1

7  -> +2
```

Bottom View

```
4 2 6 3 7
```

---

# Top View vs Bottom View

| Top View | Bottom View |
|----------|-------------|
| Store the **first** node at every HD | Store the **last** node at every HD |
| Never overwrite | Always overwrite |
| First visible node | Lowest visible node |
| `if(!map.containsKey(hd))` | `map.put(hd, value)` |

---

# Key Takeaways

- Assign every node a **Horizontal Distance (HD)**.
- Perform **BFS** while tracking HD.
- Use a **TreeMap** to maintain columns in sorted order.
- For **Top View**, store only the first node.
- For **Bottom View**, overwrite every node at the same HD.
- After traversal, the map contains the bottommost visible node for every column.

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

Overwrite every HD

↓

TreeMap

↓

Bottom View
```