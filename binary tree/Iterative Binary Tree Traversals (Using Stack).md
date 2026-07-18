# Iterative Binary Tree Traversals (Using Stack)

**Problems:**
- **144. Binary Tree Preorder Traversal**
- **94. Binary Tree Inorder Traversal**
- **145. Binary Tree Postorder Traversal**

**Difficulty:** Easy  
**Topics:** Binary Tree, Stack, DFS

---

# Why Iterative Traversal?

Recursive traversals use the **system call stack**.

The iterative approach simulates recursion using an explicit **Stack**, avoiding recursive function calls.

---

# 1. Preorder Traversal (Root → Left → Right)

## Idea

Preorder visits:

```
Root
↓
Left
↓
Right
```

Since a stack is **LIFO (Last In, First Out)**:

- Process the root immediately.
- Push the **right child first**.
- Push the **left child next**.

This ensures the left child is processed before the right child.

---

## Example

```
        1
       / \
      2   3
     / \
    4   5
```

### Stack Operations

```
Push 1

Stack

[1]
```

Pop

```
1

Output

1

Push 3
Push 2

Stack

[3,2]
```

Pop

```
2

Output

1 2

Push 5
Push 4

Stack

[3,5,4]
```

Continue until the stack becomes empty.

Final Output

```
1 2 4 5 3
```

---

## Algorithm

1. Push the root into the stack.
2. Pop the top node.
3. Visit it.
4. Push its right child.
5. Push its left child.
6. Repeat until the stack becomes empty.

---

## Java Solution

```java
class Solution {

    public List<Integer> preorderTraversal(TreeNode root) {

        List<Integer> res = new ArrayList<>();

        if (root == null) return res;

        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);

        while (!stack.isEmpty()) {

            TreeNode node = stack.pop();
            res.add(node.val);

            if (node.right != null)
                stack.push(node.right);

            if (node.left != null)
                stack.push(node.left);
        }

        return res;
    }
}
```

---

# 2. Inorder Traversal (Left → Root → Right)

## Idea

The goal is to visit the **leftmost node first**.

1. Keep pushing left children onto the stack.
2. Once there is no left child:
   - Pop the node.
   - Visit it.
   - Move to its right child.
3. Repeat.

---

## Example

```
        1
       / \
      2   3
     / \
    4   5
```

Push all left nodes

```
1

↓

2

↓

4
```

Stack

```
[1,2,4]
```

Pop

```
4

Output

4
```

Now go to its right child (none).

Pop again

```
2

Output

4 2
```

Move to right child

```
5
```

Continue similarly.

Final Output

```
4 2 5 1 3
```

---

## Algorithm

1. Keep moving left while pushing nodes.
2. If no left child:
   - Pop the top node.
   - Visit it.
   - Move to its right child.
3. Repeat until both stack and current node become empty.

---

## Java Solution

```java
class Solution {

    public List<Integer> inorderTraversal(TreeNode root) {

        List<Integer> res = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();

        TreeNode node = root;

        while (true) {

            if (node != null) {
                stack.push(node);
                node = node.left;
            } else {

                if (stack.isEmpty())
                    break;

                node = stack.pop();
                res.add(node.val);
                node = node.right;
            }
        }

        return res;
    }
}
```

---

# 3. Postorder Traversal (Left → Right → Root) — Using Two Stacks

## Idea

Postorder is:

```
Left
↓

Right
↓

Root
```

Instead of generating it directly, observe:

If we visit

```
Root → Right → Left
```

and reverse the order, we get

```
Left → Right → Root
```

This is achieved using two stacks.

---

## Example

```
        1
       / \
      2   3
```

### Stack 1

```
Push 1

Pop 1

Push into Stack2

Push left
Push right
```

Stack1

```
[2,3]
```

Continue.

Stack2 finally contains

```
1 3 2
```

Pop Stack2

```
2 3 1
```

which is the required postorder traversal.

---

## Algorithm

1. Push the root into Stack 1.
2. Pop from Stack 1 and push into Stack 2.
3. Push the left child into Stack 1.
4. Push the right child into Stack 1.
5. Repeat until Stack 1 is empty.
6. Pop everything from Stack 2.

---

## Java Solution

```java
class Solution {

    public List<Integer> postorderTraversal(TreeNode root) {

        List<Integer> res = new ArrayList<>();

        if (root == null) return res;

        Stack<TreeNode> st1 = new Stack<>();
        Stack<TreeNode> st2 = new Stack<>();

        st1.push(root);

        while (!st1.isEmpty()) {

            TreeNode node = st1.pop();
            st2.push(node);

            if (node.left != null)
                st1.push(node.left);

            if (node.right != null)
                st1.push(node.right);
        }

        while (!st2.isEmpty()) {
            res.add(st2.pop().val);
        }

        return res;
    }
}
```

---

# Complexity

| Traversal | Time | Space |
|-----------|------|-------|
| Preorder (1 Stack) | `O(n)` | `O(h)` |
| Inorder (1 Stack) | `O(n)` | `O(h)` |
| Postorder (2 Stacks) | `O(n)` | `O(n)` |

Where:

- `n` = Number of nodes
- `h` = Height of the tree

---

# Key Takeaways

### Preorder (NLR)

- Visit the node immediately.
- **Push right first, then left** so that the left subtree is processed first.

### Inorder (LNR)

- Keep pushing all left nodes.
- Visit after reaching the leftmost node.
- Move to the right subtree after visiting.

### Postorder (LRN)

- Difficult to perform directly using one stack.
- Traverse in **Root → Right → Left** order and reverse it using a second stack.
- Two-stack solution is simple and easy to remember.

### Memory Trick

```
Preorder

Visit
Push Right
Push Left
```

```
Inorder

Push Left
Visit
Go Right
```

```
Postorder

Stack1 : Root Right Left

↓

Reverse using Stack2

↓

Left Right Root
```