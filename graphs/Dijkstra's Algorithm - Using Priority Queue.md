# Dijkstra's Algorithm - Using Priority Queue

## 1. Problem

Given a **weighted graph** and a **source vertex `S`**, find the **shortest distance from `S` to every other vertex**.

### Important Conditions

Dijkstra's Algorithm works when:

* The graph can be **directed or undirected**.
* Edge weights must be **non-negative** (`weight >= 0`).
* We need the shortest path from **one source vertex** to all vertices.

### Example

Consider this graph:

```text
        1
   0 --------> 1
   |           |
  4|           |2
   ↓           ↓
   2 --------> 3
        1
```

Edges:

```text
0 → 1 = 1
0 → 2 = 4
1 → 3 = 2
2 → 3 = 1
```

Source:

```text
S = 0
```

Shortest distances are:

```text
0 → 0 = 0
0 → 1 = 1
0 → 3 = 3
0 → 2 = 4
```

So:

```text
[0, 1, 4, 3]
```

---

# 2. Intuition / Approach

The main idea of Dijkstra's Algorithm is:

> Always process the vertex that currently has the **smallest known distance** from the source.

To efficiently find the vertex with the smallest distance, we use a:

```text
PriorityQueue
```

configured as a **min-heap**.

---

## Step 1: Create a distance array

We maintain:

```java
int[] dist
```

where:

```text
dist[i] = shortest distance currently known from source S to vertex i
```

Initially, we don't know the distance to any vertex.

So:

```text
dist = [∞, ∞, ∞, ∞]
```

Then the distance from the source to itself is:

```text
dist[S] = 0
```

For source `0`:

```text
dist = [0, ∞, ∞, ∞]
```

---

## Step 2: Put the source into the PriorityQueue

We store:

```text
(distance, vertex)
```

So initially:

```text
PriorityQueue
----------------
(0, 0)
```

This means:

```text
distance = 0
vertex = 0
```

---

## Step 3: Remove the minimum-distance vertex

The PriorityQueue always gives us the vertex with the smallest distance.

Suppose we remove:

```text
(0, 0)
```

Now we process all neighbors of vertex `0`.

---

# 3. Relaxation

This is the most important operation in Dijkstra's Algorithm.

Suppose we have:

```text
current vertex = u
neighbor = v
edge weight = w
```

We already know:

```text
dist[u]
```

Therefore, the distance to reach `v` through `u` would be:

```text
dist[u] + w
```

We compare it with the current known distance:

```text
dist[v]
```

If:

```text
dist[u] + w < dist[v]
```

then we found a shorter path.

So we update:

```java
dist[v] = dist[u] + w;
```

and put the new pair into the PriorityQueue.

This process is called **relaxation**.

---

# 4. Why PriorityQueue?

Without a PriorityQueue, we would have to repeatedly search for the unprocessed vertex having the smallest distance.

That can be expensive.

A PriorityQueue gives us the minimum-distance vertex efficiently:

```text
insert → O(log V)
remove minimum → O(log V)
```

Therefore, Dijkstra becomes efficient.

---

# 5. Java Code

```java
import java.util.*;

class Solution {

    // Pair stores:
    // distance = shortest distance
    // node = vertex
    static class Pair {
        int distance;
        int node;

        Pair(int distance, int node) {
            this.distance = distance;
            this.node = node;
        }
    }

    // Finds the shortest distance from source S
    // to every vertex.
    static int[] dijkstra(
            int V,
            ArrayList<ArrayList<ArrayList<Integer>>> adj,
            int S) {

        // Min-heap based on distance
        PriorityQueue<Pair> pq =
            new PriorityQueue<>((x, y) ->
                Integer.compare(x.distance, y.distance)
            );

        // Distance array
        int[] dist = new int[V];

        // Initially all distances are infinity
        Arrays.fill(dist, (int) 1e9);

        // Distance from source to itself is 0
        dist[S] = 0;

        // Add source to priority queue
        pq.add(new Pair(0, S));

        while (!pq.isEmpty()) {

            // Get vertex with minimum distance
            Pair current = pq.poll();

            int ds = current.distance;
            int node = current.node;

            // Process all adjacent vertices
            for (int i = 0; i < adj.get(node).size(); i++) {

                int edgeWeight =
                    adj.get(node).get(i).get(1);

                int adjNode =
                    adj.get(node).get(i).get(0);

                // Relaxation
                if (ds + edgeWeight < dist[adjNode]) {

                    dist[adjNode] = ds + edgeWeight;

                    // Add updated distance to min-heap
                    pq.add(
                        new Pair(dist[adjNode], adjNode)
                    );
                }
            }
        }

        return dist;
    }
}
```

---

# 6. Understanding the Adjacency List

The code assumes the adjacency list has this structure:

```text
adj[node] → list of [neighbor, weight]
```

For example:

```text
0 → [ [1,1], [2,4] ]

1 → [ [0,1], [3,2] ]

2 → [ [0,4], [3,1] ]

3 → [ [1,2], [2,1] ]
```

For:

```java
adj.get(node).get(i).get(0)
```

we get the neighboring vertex.

For:

```java
adj.get(node).get(i).get(1)
```

we get the edge weight.

---

# 7. Dry Run

Consider:

```text
0 → 1 = 1
0 → 2 = 4
1 → 3 = 2
2 → 3 = 1
```

Source:

```text
S = 0
```

Initial distance array:

```text
dist = [0, ∞, ∞, ∞]
```

PriorityQueue:

```text
[(0, 0)]
```

---

## Step 1: Process vertex 0

Remove:

```text
(0, 0)
```

Neighbors:

```text
0 → 1 = 1
0 → 2 = 4
```

For vertex `1`:

```text
0 + 1 < ∞
```

Update:

```text
dist[1] = 1
```

Add:

```text
(1, 1)
```

For vertex `2`:

```text
0 + 4 < ∞
```

Update:

```text
dist[2] = 4
```

Add:

```text
(4, 2)
```

Now:

```text
dist = [0, 1, 4, ∞]
```

PriorityQueue:

```text
[(1,1), (4,2)]
```

---

## Step 2: Process vertex 1

The smallest distance is:

```text
(1, 1)
```

Remove it.

Vertex `1` has an edge:

```text
1 → 3 = 2
```

Calculate:

```text
dist[1] + 2
= 1 + 2
= 3
```

Currently:

```text
dist[3] = ∞
```

Therefore:

```text
3 < ∞
```

Update:

```text
dist[3] = 3
```

Add:

```text
(3, 3)
```

Now:

```text
dist = [0, 1, 4, 3]
```

PriorityQueue:

```text
[(3,3), (4,2)]
```

---

## Step 3: Process vertex 3

Remove:

```text
(3,3)
```

Its neighbors don't produce any shorter distance.

So:

```text
dist = [0, 1, 4, 3]
```

---

## Step 4: Process vertex 2

Remove:

```text
(4,2)
```

Edge:

```text
2 → 3 = 1
```

Calculate:

```text
dist[2] + 1
= 4 + 1
= 5
```

But:

```text
dist[3] = 3
```

Since:

```text
5 < 3
```

is false, we don't update.

Final answer:

```text
[0, 1, 4, 3]
```

---

# 8. Why Does Dijkstra Work?

Suppose the PriorityQueue gives us a vertex with the smallest current distance.

Because all edge weights are **non-negative**, going through another vertex cannot suddenly produce a shorter path after we have already found the smallest possible distance.

For example:

```text
Source → A = 3
```

If `A` is currently the minimum-distance vertex, any path that goes:

```text
Source → B → A
```

must have:

```text
distance(Source → B) + distance(B → A)
```

Since edge weights are non-negative, this cannot make an already finalized shortest distance smaller.

That is why Dijkstra works.

---

# 9. Important: Dijkstra Does NOT Work With Negative Edges

Consider:

```text
A → B = 2
A → C = 5
C → B = -10
```

Initially:

```text
A → B = 2
```

But later:

```text
A → C → B
= 5 + (-10)
= -5
```

So the previously assumed shortest distance to `B` becomes incorrect.

Therefore:

> **Dijkstra's Algorithm should not be used when negative edge weights are present.**

For graphs with negative edges, algorithms such as **Bellman-Ford** are appropriate.

---

# 10. Time Complexity

Let:

```text
V = number of vertices
E = number of edges
```

Each edge can cause a relaxation, and PriorityQueue operations take:

```text
O(log V)
```

Therefore:

```text
Time Complexity = O((V + E) log V)
```

For a connected graph, this is commonly written as:

```text
O(E log V)
```

---

# 11. Space Complexity

We use:

```text
dist[]       → O(V)
PriorityQueue → O(E) in the worst case
Adjacency List → O(V + E)
```

Therefore:

```text
Space Complexity = O(V + E)
```

If the adjacency list is considered part of the input, the **extra algorithmic space** is mainly:

```text
O(V + E)
```

because the priority queue can contain multiple entries for the same vertex.

---

# 12. Key Takeaways

1. **Dijkstra finds the shortest paths from one source to all vertices.**

2. It works only when:

```text
edge weight >= 0
```

3. Use a **PriorityQueue / Min-Heap** to always process the smallest current distance.

4. The most important formula is the **relaxation condition**:

```text
dist[current] + edgeWeight < dist[neighbor]
```

5. If the condition is true:

```java
dist[neighbor] = dist[current] + edgeWeight;
```

6. Store this updated distance in the PriorityQueue:

```java
pq.add(new Pair(dist[neighbor], neighbor));
```

7. Initial distances are:

```text
dist[source] = 0
dist[all other vertices] = ∞
```

8. Time complexity:

```text
O((V + E) log V)
```

9. Space complexity:

```text
O(V + E)
```

10. **Dijkstra does not work correctly with negative edge weights.**

### The core idea to remember

```text
Take the closest vertex
        ↓
Explore its neighbors
        ↓
Try to improve their distances
        ↓
If improved → put them in PriorityQueue
        ↓
Repeat
```

That is the entire idea behind **Dijkstra + PriorityQueue**.
