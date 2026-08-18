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
