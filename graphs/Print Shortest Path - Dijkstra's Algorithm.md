# Print Shortest Path - Dijkstra's Algorithm

## 1. Problem

Given a **weighted undirected graph** with:

* `n` vertices
* `m` edges
* `edges[i] = [u, v, w]`, meaning there is an edge between `u` and `v` with weight `w`

We need to find the **shortest path from vertex `1` to vertex `n`**.

Instead of returning only the shortest distance, we need to **print/return the actual path**.

If there is no path from `1` to `n`, return:

```text
[-1]
```

### Example

Suppose:

```text
n = 5
```

and the graph contains:

```text
1 --2-- 2
|       |
5       3
|       |
3 --1-- 4 --2-- 5
```

We want the shortest path from:

```text
1 → 5
```

There may be multiple possible paths, but we need the one with the minimum total weight.

The important difference from normal Dijkstra is:

> We need to remember **which node we came from** so that we can reconstruct the actual shortest path.

---

# 2. Main Idea

Normal Dijkstra gives us:

```text
dist[i] = shortest distance from source to i
```

For example:

```text
dist[1] = 0
dist[2] = 2
dist[3] = 5
dist[4] = 5
dist[5] = 7
```

But this doesn't tell us the actual path.

For example:

```text
dist[5] = 7
```

doesn't tell us whether the path was:

```text
1 → 2 → 4 → 5
```

or:

```text
1 → 3 → 4 → 5
```

So we maintain another array:

```java
parent[]
```

---

# 3. What is `parent[]`?

The `parent` array stores:

> **The previous node used to reach this node through the current shortest path.**

Suppose we find:

```text
1 → 2 → 4 → 5
```

Then:

```text
parent[2] = 1
parent[4] = 2
parent[5] = 4
```

So starting from `5`, we can go backwards:

```text
5
↓
parent[5] = 4
↓
parent[4] = 2
↓
parent[2] = 1
```

We get:

```text
5 → 4 → 2 → 1
```

Then reverse it:

```text
1 → 2 → 4 → 5
```

That is our shortest path.

---

# 4. Approach

We use **Dijkstra's Algorithm + Parent Array**.

### Step 1: Build the adjacency list

Because the graph is undirected, for every edge:

```text
u --w-- v
```

we add:

```text
u → v
v → u
```

---

### Step 2: Create `dist[]`

Initially, every distance is infinity:

```text
dist[i] = 1e9
```

The source is vertex `1`:

```text
dist[1] = 0
```

---

### Step 3: Create `parent[]`

Initially:

```java
parent[i] = i;
```

Why?

It gives every node a valid initial parent and makes reconstruction easier.

For example:

```text
parent[1] = 1
parent[2] = 2
parent[3] = 3
...
```

When we find a shorter path to a node, we update its parent.

---

### Step 4: Use a Min-Heap

We store:

```text
(distance, node)
```

in the PriorityQueue.

The node with the smallest distance is processed first.

Initially:

```text
(0, 1)
```

because the source is `1`.

---

# 5. Relaxation

For every neighbor of the current node, calculate:

```text
newDistance = currentDistance + edgeWeight
```

Then check:

```java
if (currentDistance + edgeWeight < dist[adjNode])
```

If this is true, we found a shorter path.

So update:

```java
dist[adjNode] = currentDistance + edgeWeight;
```

But because we also need the actual path, we must update:

```java
parent[adjNode] = node;
```

This is the **most important difference** from normal Dijkstra.

---

# 6. Why Do We Update `parent[]`?

Suppose we are currently processing node `2` and find:

```text
2 → 4
```

with a better distance.

Then:

```java
parent[4] = 2;
```

This means:

```text
To reach 4 using the shortest path,
we came from 2.
```

Later, if the shortest path to `5` comes through `4`:

```java
parent[5] = 4;
```

We eventually get:

```text
5 → 4 → 2 → 1
```

Reverse it:

```text
1 → 2 → 4 → 5
```

---

# 7. Java Code

```java
import java.util.*;

class Solution {

    static class Pair {
        int first;
        int second;

        Pair(int first, int second) {
            this.first = first;
            this.second = second;
        }
    }

    public static List<Integer> shortestPath(
            int n,
            int m,
            int[][] edges) {

        // Create adjacency list
        ArrayList<ArrayList<Pair>> adj = new ArrayList<>();

        for (int i = 0; i <= n; i++) {
            adj.add(new ArrayList<>());
        }

        // Undirected graph
        for (int i = 0; i < m; i++) {

            int u = edges[i][0];
            int v = edges[i][1];
            int weight = edges[i][2];

            adj.get(u).add(new Pair(v, weight));
            adj.get(v).add(new Pair(u, weight));
        }

        // Min-heap: (distance, node)
        PriorityQueue<Pair> pq =
            new PriorityQueue<>(
                (x, y) -> Integer.compare(x.first, y.first)
            );

        // Shortest distance
        int[] dist = new int[n + 1];

        // Parent array for path reconstruction
        int[] parent = new int[n + 1];

        for (int i = 1; i <= n; i++) {
            dist[i] = (int) 1e9;
            parent[i] = i;
        }

        // Source = 1
        dist[1] = 0;

        pq.add(new Pair(0, 1));

        // Dijkstra
        while (!pq.isEmpty()) {

            Pair current = pq.poll();

            int dis = current.first;
            int node = current.second;

            for (Pair edge : adj.get(node)) {

                int adjNode = edge.first;
                int edgeWeight = edge.second;

                // Relaxation
                if (dis + edgeWeight < dist[adjNode]) {

                    dist[adjNode] = dis + edgeWeight;

                    // Remember where we came from
                    parent[adjNode] = node;

                    pq.add(
                        new Pair(dist[adjNode], adjNode)
                    );
                }
            }
        }

        // No path from 1 to n
        List<Integer> path = new ArrayList<>();

        if (dist[n] == (int) 1e9) {
            path.add(-1);
            return path;
        }

        // Reconstruct path from n back to 1
        int node = n;

        while (parent[node] != node) {

            path.add(node);

            node = parent[node];
        }

        // Add source
        path.add(1);

        // Currently path is n -> ... -> 1
        Collections.reverse(path);

        return path;
    }
}
```

---

# 8. Understanding the Code Step by Step

## Building the Graph

For an edge:

```text
u = 1
v = 2
weight = 4
```

we add:

```java
adj.get(1).add(new Pair(2, 4));
adj.get(2).add(new Pair(1, 4));
```

Because the graph is **undirected**.

---

## Distance Array

We initialize:

```java
dist[i] = 1e9;
```

This represents infinity.

Then:

```java
dist[1] = 0;
```

because the distance from the source to itself is zero.

---

## Parent Array

Initially:

```java
parent[i] = i;
```

For example:

```text
parent[1] = 1
parent[2] = 2
parent[3] = 3
parent[4] = 4
parent[5] = 5
```

As we discover shorter paths, we change these values.

---

# 9. Dry Run

Consider this graph:

```text
        2
   1 -------- 2
   |          |
  4|          |1
   |          |
   3 -------- 4
        2     |
              | 3
              |
              5
```

Edges:

```text
1 → 2 = 2
1 → 3 = 4
2 → 4 = 1
3 → 4 = 2
4 → 5 = 3
```

Source:

```text
1
```

Destination:

```text
5
```

---

## Initial State

Distance:

```text
dist = [∞, 0, ∞, ∞, ∞, ∞]
```

Parent:

```text
parent = [_, 1, 2, 3, 4, 5]
```

PriorityQueue:

```text
(0, 1)
```

---

## Step 1: Process Node 1

Current:

```text
node = 1
distance = 0
```

Neighbors:

```text
1 → 2 = 2
1 → 3 = 4
```

### Neighbor 2

Calculate:

```text
0 + 2 = 2
```

Since:

```text
2 < ∞
```

update:

```text
dist[2] = 2
parent[2] = 1
```

Queue:

```text
(2, 2)
```

### Neighbor 3

Calculate:

```text
0 + 4 = 4
```

Update:

```text
dist[3] = 4
parent[3] = 1
```

Queue:

```text
(2, 2)
(4, 3)
```

---

## Step 2: Process Node 2

Smallest distance:

```text
(2, 2)
```

So:

```text
node = 2
distance = 2
```

Neighbor:

```text
2 → 4 = 1
```

Calculate:

```text
2 + 1 = 3
```

Since:

```text
3 < ∞
```

update:

```text
dist[4] = 3
parent[4] = 2
```

Queue:

```text
(3, 4)
(4, 3)
```

---

## Step 3: Process Node 4

Current:

```text
distance = 3
node = 4
```

Edge:

```text
4 → 5 = 3
```

Calculate:

```text
3 + 3 = 6
```

Update:

```text
dist[5] = 6
parent[5] = 4
```

Now:

```text
parent[5] = 4
parent[4] = 2
parent[2] = 1
```

---

# 10. Reconstructing the Path

Now we know:

```text
parent[5] = 4
parent[4] = 2
parent[2] = 1
parent[1] = 1
```

Start from destination:

```text
node = 5
```

### First iteration

```text
path.add(5)
node = parent[5]
     = 4
```

Path:

```text
[5]
```

### Second iteration

```text
path.add(4)
node = parent[4]
     = 2
```

Path:

```text
[5, 4]
```

### Third iteration

```text
path.add(2)
node = parent[2]
     = 1
```

Path:

```text
[5, 4, 2]
```

Now:

```text
parent[1] == 1
```

Stop.

Add `1`:

```text
[5, 4, 2, 1]
```

But this is backwards.

So:

```java
Collections.reverse(path);
```

Final:

```text
[1, 2, 4, 5]
```

Therefore the shortest path is:

```text
1 → 2 → 4 → 5
```

with distance:

```text
2 + 1 + 3 = 6
```

---

# 11. Why `parent[node] != node`?

This condition:

```java
while (parent[node] != node)
```

is used to stop when we reach the source.

Initially:

```text
parent[1] = 1
```

Therefore, when:

```text
node = 1
```

we have:

```text
parent[1] == 1
```

and the loop stops.

This is why initializing:

```java
parent[i] = i;
```

is useful.

---

# 12. Why Do We Reverse the Path?

Dijkstra calculates the shortest path starting from:

```text
1 → ... → n
```

But when reconstructing, we start from the destination:

```text
n
```

and follow the parents backwards:

```text
n → parent[n] → parent[parent[n]] → ... → 1
```

So we initially get:

```text
n → ... → 1
```

Therefore we use:

```java
Collections.reverse(path);
```

to obtain:

```text
1 → ... → n
```

---

# 13. Important Difference From Normal Dijkstra

### Normal Dijkstra

We only maintain:

```text
dist[]
```

because we only care about the shortest distances.

### Print Shortest Path

We maintain:

```text
dist[]
parent[]
```

because:

```text
dist[]   → tells us the shortest distance
parent[] → tells us the actual path
```

So remember:

```text
Dijkstra
   ↓
Find shortest distance

Dijkstra + parent[]
   ↓
Find shortest distance
   +
Reconstruct shortest path
```

---

# 14. Time Complexity

Let:

```text
n = number of vertices
m = number of edges
```

### Building adjacency list

Every edge is added twice because the graph is undirected:

```text
O(m)
```

### Dijkstra

Each edge can be relaxed and PriorityQueue operations take:

```text
O(log n)
```

Therefore:

```text
O((n + m) log n)
```

For a connected graph, this is commonly written as:

```text
O(m log n)
```

### Path Reconstruction

We follow the parent pointers from `n` to `1`.

In the worst case:

```text
O(n)
```

This does not change the overall complexity.

### Final Time Complexity

```text
O((n + m) log n)
```

---

# 15. Space Complexity

We use:

```text
Adjacency List → O(n + m)
dist[]          → O(n)
parent[]        → O(n)
PriorityQueue   → O(m) worst case
path            → O(n)
```

Therefore:

```text
Space Complexity = O(n + m)
```

---

# 16. Key Takeaways

### 1. Dijkstra finds the shortest distance

```text
dist[node]
```

stores the minimum distance from source `1` to that node.

---

### 2. `parent[]` is used to reconstruct the path

Whenever we find a better path:

```java
parent[adjNode] = node;
```

This means:

> "The shortest path to `adjNode` currently comes through `node`."

---

### 3. Reconstruct from destination

Start:

```java
node = n;
```

Then:

```text
n
↓
parent[n]
↓
parent[parent[n]]
↓
...
↓
1
```

---

### 4. Reverse the result

Because reconstruction happens backwards:

```text
n → ... → 1
```

we use:

```java
Collections.reverse(path);
```

to get:

```text
1 → ... → n
```

---

### 5. If destination is unreachable

Check:

```java
if (dist[n] == 1e9)
```

and return:

```text
[-1]
```

---

### 6. The most important pattern to remember

```text
PriorityQueue
      ↓
Dijkstra
      ↓
Relax edge
      ↓
Update dist[]
      ↓
Update parent[]
      ↓
Start from destination
      ↓
Follow parent[]
      ↓
Reverse path
      ↓
Shortest Path
```

### Final Formula

```text
if (dis + edgeWeight < dist[adjNode]) {

    dist[adjNode] = dis + edgeWeight;

    parent[adjNode] = node;

    pq.add(new Pair(dist[adjNode], adjNode));
}
```

**`dist[]` tells you how short the path is.
`parent[]` tells you which path to take.**
