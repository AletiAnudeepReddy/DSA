Shortest Path in a DAG
Problem

Given a Directed Acyclic Graph (DAG) with:

N vertices numbered from 0 to N - 1
M directed edges
Each edge has a weight

Find the shortest distance from vertex 0 to every other vertex.

For example, an edge:

u -> v with weight wt

means we can travel from u to v with cost wt.

The key property is that the graph is a DAG, so it contains no cycles.

1. Main Idea

For a general weighted graph, we commonly think about algorithms such as Dijkstra or Bellman-Ford.

But for a DAG, there is a much simpler and efficient approach:

Topological Sort
       ↓
Process vertices in topological order
       ↓
Relax all outgoing edges
       ↓
Shortest distances

The two important concepts are:

Topological Sort
Edge Relaxation
2. Why Topological Sort Helps

Suppose we have:

0 → 1 → 2 → 3

The topological order is:

0, 1, 2, 3

Notice something important:

Before we process a node, all nodes that can come before it have already been processed.

Therefore, when we process a node, we already know its shortest distance.

For example:

dist[0] = 0


process 0
    ↓
calculate dist[1]


process 1
    ↓
calculate dist[2]


process 2
    ↓
calculate dist[3]

This is why we don't need repeated relaxation like Bellman-Ford.

3. Topological Sort

For the given code, topological sorting is done using DFS.

private void topoSort(
        int node,
        ArrayList<ArrayList<Pair>> adj,
        int[] vis,
        Stack<Integer> st) {


    vis[node] = 1;


    for (int i = 0; i < adj.get(node).size(); i++) {


        int v = adj.get(node).get(i).first;


        if (vis[v] == 0) {
            topoSort(v, adj, vis, st);
        }
    }


    st.add(node);
}

The important line is:

st.add(node);

It happens after visiting all neighbors.

This is called postorder insertion.

4. Why Add Node After Its Neighbors?

Consider:

0 → 1 → 2

DFS starts from 0.

DFS(0)
    ↓
DFS(1)
    ↓
DFS(2)

Node 2 has no outgoing edges, so:

stack.push(2)

Return to 1:

stack.push(1)

Return to 0:

stack.push(0)

Stack conceptually contains:

[2, 1, 0]

When we pop:

0 → 1 → 2

which is exactly the topological order.

5. Graph Representation

Each edge is stored as:

Pair(destination, weight)

For example:

0 → 1 with weight 5

is stored as:

adj.get(0).add(new Pair(1, 5));

So:

adj[0]
   ↓
[(1,5), ...]

where:

first  = destination
second = weight
6. Distance Array

We maintain:

int[] dist = new int[N];

Initially, every distance is infinity:

dist = [∞, ∞, ∞, ∞, ...]

because we don't know how to reach those nodes yet.

Since Java doesn't have a direct integer infinity, we use:

(int) 1e9

Then:

dist[0] = 0;

because the distance from the source to itself is 0.

Example:

dist = [0, ∞, ∞, ∞]
7. What is Relaxation?

Suppose we have:

u ----wt----> v

and currently:

dist[u] = 5

Then going through u to v costs:

dist[u] + wt

If this is smaller than our current dist[v], update it:

if (dist[u] + wt < dist[v]) {
    dist[v] = dist[u] + wt;
}

This operation is called relaxation.

8. Complete Algorithm
Step 1

Create the adjacency list.

u → (v, weight)
Step 2

Perform topological sorting using DFS.

Step 3

Initialize:

dist[i] = infinity
Step 4

Set:

dist[0] = 0
Step 5

Pop nodes from the topological stack.

For every outgoing edge:

node → v

perform:

dist[v] = min(
    dist[v],
    dist[node] + weight
)
Step 6

Return dist.

9. Dry Run

Consider:

N = 6


Edges:


0 → 1 (2)
0 → 4 (1)
1 → 2 (3)
4 → 2 (2)
2 → 3 (6)
4 → 5 (4)

Graph:

          2
      ┌──────→ 1 ───3──→ 2 ───6──→ 3
      │                    ↑
      │                    │2
      │                    │
      0                    4
      │                    ↑
      │1                  /
      └──────→ 4 ────────
                │
                └──4──→ 5

One possible topological ordering:

0 → 1 → 4 → 2 → 5 → 3

Initially:

dist = [0, INF, INF, INF, INF, INF]
Process 0

Edges:

0 → 1 (2)
0 → 4 (1)

Therefore:

dist[1] = 0 + 2 = 2
dist[4] = 0 + 1 = 1

Now:

dist = [0, 2, INF, INF, 1, INF]
Process 1

Edge:

1 → 2 (3)

Therefore:

dist[2] = 2 + 3 = 5

Now:

dist = [0, 2, 5, INF, 1, INF]
Process 4

Edges:

4 → 2 (2)
4 → 5 (4)

For 2:

dist[4] + 2
= 1 + 2
= 3

Current:

dist[2] = 5

So update:

dist[2] = 3

For 5:

dist[5] = 1 + 4
        = 5

Now:

dist = [0, 2, 3, INF, 1, 5]
Process 2

Edge:

2 → 3 (6)

Therefore:

dist[3] = 3 + 6
        = 9

Final:

dist = [0, 2, 3, 9, 1, 5]

So the shortest distance from 0 is:

0 → 1 = 2
0 → 4 = 1
0 → 4 → 2 = 3
0 → 4 → 2 → 3 = 9
0 → 4 → 5 = 5
10. Java Code
class Solution {
        // Build adjacency list
        ArrayList<ArrayList<Pair>> adj =
            new ArrayList<>();


        for (int i = 0; i < N; i++) {
            adj.add(new ArrayList<>());
        }


        for (int i = 0; i < M; i++) {


            int u = edges[i][0];
            int v = edges[i][1];
            int wt = edges[i][2];


            adj.get(u).add(new Pair(v, wt));
        }


        // Topological sort
        int[] vis = new int[N];
        Stack<Integer> st = new Stack<>();


        for (int i = 0; i < N; i++) {


            if (vis[i] == 0) {
                topoSort(i, adj, vis, st);
            }
        }


        // Initialize distances
        int[] dist = new int[N];


        for (int i = 0; i < N; i++) {
            dist[i] = (int) 1e9;
        }


        // Source
        dist[0] = 0;


        // Process in topological order
        while (!st.isEmpty()) {


            int node = st.pop();


            // Only process reachable nodes
            if (dist[node] == (int) 1e9) {
                continue;
            }


            for (int i = 0; i < adj.get(node).size(); i++) {


                int v = adj.get(node).get(i).first;
                int wt = adj.get(node).get(i).second;


                // Relaxation
                if (dist[node] + wt < dist[v]) {
                    dist[v] = dist[node] + wt;
                }
            }
        }


        return dist;
    }
}
11. The Most Important Part of the Code

This is the heart of the algorithm:

while (!st.isEmpty()) {


    int node = st.pop();


    for (Pair edge : adj.get(node)) {


        int v = edge.first;
        int wt = edge.second;


        if (dist[node] + wt < dist[v]) {
            dist[v] = dist[node] + wt;
        }
    }
}

Think of it as:

Take nodes in topological order
              ↓
       Look at every edge
              ↓
       Try to improve distance
              ↓
            relax
12. Why Does This Work?

The key property is:

In a topological ordering of a DAG, every node appears before all the nodes reachable from it.

Therefore, when we process node, all possible ways of reaching node have already been considered.

So:

dist[node]

is already its shortest possible distance.

Then we can safely use it to update its neighbors.

This is why one pass over the topological order is enough.

13. Why Not Dijkstra?

Dijkstra is commonly used for shortest paths in weighted graphs.

But for a DAG:

Topological Sort + Relaxation

is simpler and faster.

DAG
Time: O(V + E)
Dijkstra with priority queue
Time: O((V + E) log V)

for the usual adjacency-list implementation.

The special property of a DAG lets us avoid the priority queue.

14. Important Note About Negative Weights

One nice property of the DAG shortest-path algorithm is that edge weights can be negative.

For example:

0 → 1 (-5)
1 → 2 (3)

It still works because the graph has no cycles.

Dijkstra, on the other hand, does not work correctly with negative edge weights.

15. Complexity

Let:

V = number of vertices
E = number of edges
Topological Sort

Every vertex and edge is visited once:

O(V + E)
Shortest Path Relaxation

Every vertex and edge is processed once:

O(V + E)

Therefore:

Time Complexity
O(V + E)
Space Complexity

Adjacency list:

O(V + E)

Visited array:

O(V)

Distance array:

O(V)

Stack:

O(V)

Overall:

Space: O(V + E)
16. Pattern to Remember

This is an important graph pattern:

DAG
 ↓
Topological Sort
 ↓
Initialize dist[source] = 0
 ↓
Process nodes in topo order
 ↓
Relax every outgoing edge
 ↓
Shortest Path

The core relaxation formula is:

dist[v] = Math.min(
    dist[v],
    dist[u] + weight
);

And the reason it is O(V + E) is:

Each vertex and each edge is processed only once.