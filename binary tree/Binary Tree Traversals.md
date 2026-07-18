# Binary Tree Traversals (DFS)

**Problems:**
- **144. Binary Tree Preorder Traversal**
- **94. Binary Tree Inorder Traversal**
- **145. Binary Tree Postorder Traversal**

**Difficulty:** Easy  
**Topics:** Binary Tree, DFS, Recursion

---

# Traversal Orders

## 1. Preorder (Root → Left → Right)

Visit the current node first, then recursively traverse the left and right subtrees.

```
Root
 ↓
Left
 ↓
Right
```

### Example

```
        1
         \
          2
         /
        3
```

Traversal

```
1 → 2 → 3
```

---

## Java Solution

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {

        List<Integer> res = new ArrayList<>();

        if (root == null) {
            return res;
        }

        res.add(root.val);
        res.addAll(preorderTraversal(root.left));
        res.addAll(preorderTraversal(root.right));

        return res;
    }
}
```

---

# 2. Inorder (Left → Root → Right)

Visit the left subtree first, then the current node, and finally the right subtree.

```
Left
 ↓
Root
 ↓
Right
```

### Example

```
        1
         \
          2
         /
        3
```

Traversal

```
1 → 3 → 2
```

---

## Java Solution

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {

        List<Integer> res = new ArrayList<>();

        if (root == null) {
            return res;
        }

        res.addAll(inorderTraversal(root.left));
        res.add(root.val);
        res.addAll(inorderTraversal(root.right));

        return res;
    }
}
```

---

# 3. Postorder (Left → Right → Root)

Visit both subtrees before visiting the current node.

```
Left
 ↓
Right
 ↓
Root
```

### Example

```
        1
         \
          2
         /
        3
```

Traversal

```
3 → 2 → 1
```

---

## Java Solution

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {

        List<Integer> res = new ArrayList<>();

        if (root == null) {
            return res;
        }

        res.addAll(postorderTraversal(root.left));
        res.addAll(postorderTraversal(root.right));
        res.add(root.val);

        return res;
    }
}
```

---

# Dry Run

Consider the tree:

```
        1
       / \
      2   3
     / \   \
    4   5   6
```

| Traversal | Order |
|-----------|-------|
| **Preorder** | 1 → 2 → 4 → 5 → 3 → 6 |
| **Inorder** | 4 → 2 → 5 → 1 → 3 → 6 |
| **Postorder** | 4 → 5 → 2 → 6 → 3 → 1 |

---

# Complexity

| Traversal | Time | Space |
|-----------|------|-------|
| Preorder | `O(n)` | `O(h)` |
| Inorder | `O(n)` | `O(h)` |
| Postorder | `O(n)` | `O(h)` |

Where:

- `n` = Number of nodes
- `h` = Height of the tree (`O(log n)` for balanced trees, `O(n)` for skewed trees)

---

# Key Takeaways

- **Preorder:** **Root → Left → Right** (NLR)
  - Used for copying/serializing a tree.

- **Inorder:** **Left → Root → Right** (LNR)
  - For a BST, this gives the nodes in **sorted order**.

- **Postorder:** **Left → Right → Root** (LRN)
  - Useful for deleting/freeing a tree or evaluating expression trees.

### Easy Mnemonic

```
Preorder  : Root → Left → Right  (NLR)

Inorder   : Left → Root → Right  (LNR)

Postorder : Left → Right → Root  (LRN)
```

> **The only difference between these traversals is the position of the Root.** Remember where the Root is visited, and the traversal becomes easy to derive.