Shortest Path in Undirected Graph with Unit Weights
🧩 Problem

You are given an undirected graph with n vertices numbered from 0 to n - 1.

Each edge has a unit weight of 1.

Given:

edges — the edges of the graph
n — number of vertices
m — number of edges
src — source vertex

Return the shortest distance from src to every other vertex.

If a vertex cannot be reached from src, return -1 for that vertex.

💡 Key Idea

Because every edge has the same weight 1, we can use BFS (Breadth-First Search).

Think of BFS as exploring the graph level by level:

Distance 0 → source
Distance 1 → nodes directly connected to source
Distance 2 → nodes two edges away
Distance 3 → nodes three edges away
...

Therefore, the first time we reach a node using BFS, we have found its shortest distance.

Why BFS?

For example:

        1
       /
0 ---- 2 ---- 3
       \
        4

Starting from 0:

Distance 0:
0


Distance 1:
1, 2


Distance 2:
3, 4

So:

dist[0] = 0
dist[1] = 1
dist[2] = 1
dist[3] = 2
dist[4] = 2
🔍 Approach
Step 1: Build the adjacency list

The graph is undirected.

If we have:

0 -- 1

we add:

0 → 1
1 → 0

So:

adj.get(edges[i][0]).add(edges[i][1]);
adj.get(edges[i][1]).add(edges[i][0]);
Step 2: Initialize distances

Initially, we don't know the distance to any node.

So set every distance to a very large value:

dist[i] = 1e9;

Then:

dist[src] = 0;

because the distance from the source to itself is 0.

Step 3: Put source into the queue
Queue<Integer> q = new LinkedList<>();
q.add(src);

BFS starts from the source.

Step 4: Perform BFS

Remove a node from the queue:

int node = q.remove();

For every neighbor:

for(int it : adj.get(node))

the distance through the current node is:

dist[node] + 1

If this is shorter than the previously known distance:

if(dist[node] + 1 < dist[it])

update it:

dist[it] = dist[node] + 1;
q.add(it);
Step 5: Convert unreachable nodes to -1

After BFS, any node still having:

dist[i] == 1e9

was never reachable.

So change it to:

dist[i] = -1;
🧠 Why Does This Work?

Every edge has weight 1.

Suppose we are currently processing a node at distance 3.

Any unvisited neighbor will have distance:

3 + 1 = 4

BFS processes nodes in increasing order of distance:

0 → 1 → 2 → 3 → ...

Therefore, when we find a shorter distance for a node, that distance represents the shortest number of edges required to reach it.

This is exactly why BFS is preferred for shortest path in an unweighted/unit-weight graph.

📝 Dry Run

Consider:

n = 6
edges = [[0,1], [0,2], [1,3], [2,3], [3,4]]
src = 0

Graph:

      1
     / \
    0   3 ---- 4
     \ /
      2


5 is disconnected
Initial
dist = [0, INF, INF, INF, INF, INF]


queue = [0]
Process 0

Neighbors:

1, 2

Update:

dist[1] = 1
dist[2] = 1

Queue:

[1, 2]
Process 1

Neighbor 3:

dist[3] = dist[1] + 1
        = 1 + 1
        = 2

Queue:

[2, 3]
Process 2

Neighbor 3:

dist[2] + 1 = 2

But:

dist[3] = 2

So there is no improvement.

Process 3

Neighbor 4:

dist[4] = 3
Process 4

No new shorter paths.

Node 5

Node 5 was never reached:

dist[5] = INF

Therefore:

dist[5] = -1
Final answer
[0, 1, 1, 2, 3, -1]
💻 Java Code
class Solution {


    public int[] shortestPath(int[][] edges, int n, int m, int src) {


        // Build adjacency list
        ArrayList<ArrayList<Integer>> adj = new ArrayList<>();


        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
        }


        // Undirected graph
        for (int i = 0; i < m; i++) {
            adj.get(edges[i][0]).add(edges[i][1]);
            adj.get(edges[i][1]).add(edges[i][0]);
        }


        // Distance array
        int[] dist = new int[n];


        for (int i = 0; i < n; i++) {
            dist[i] = (int) 1e9;
        }


        // Distance from source to itself
        dist[src] = 0;


        // BFS queue
        Queue<Integer> q = new LinkedList<>();
        q.add(src);


        while (!q.isEmpty()) {


            int node = q.remove();


            for (int it : adj.get(node)) {


                // Every edge has weight 1
                if (dist[node] + 1 < dist[it]) {


                    dist[it] = dist[node] + 1;
                    q.add(it);
                }
            }
        }


        // Convert unreachable nodes to -1
        for (int i = 0; i < n; i++) {


            if (dist[i] == (int) 1e9) {
                dist[i] = -1;
            }
        }


        return dist;
    }
}
🔄 Important Difference: BFS vs Dijkstra

This is an important pattern to remember.

Graph	Algorithm
Unweighted / every edge has weight 1	BFS
Weighted graph with non-negative weights	Dijkstra
DAG with weights	Topological Sort + Relaxation
Negative weights	Bellman-Ford

For this problem:

Every edge = 1
       ↓
Unweighted graph
       ↓
BFS
       ↓
Shortest distance
⏱️ Complexity

Let:

V = number of vertices
E = number of edges
Time Complexity

Building the adjacency list:

O(E)

BFS:

O(V + E)

Final traversal:

O(V)

Therefore:

O(V + E)
Space Complexity

Adjacency list:

O(V + E)

Distance array:

O(V)

Queue:

O(V)

Overall:

O(V + E)
🎯 Pattern to Remember

Whenever you see:

Shortest path in an undirected/unweighted graph

Immediately think:

BFS

And the basic template is:

Build adjacency list
        ↓
dist[source] = 0
        ↓
Put source in Queue
        ↓
BFS
        ↓
dist[neighbor] = dist[node] + 1
        ↓
Unreachable → -1
One-line intuition

BFS explores the graph level by level, and because every edge costs exactly 1, the level at which we reach a node is its shortest distance from the source.