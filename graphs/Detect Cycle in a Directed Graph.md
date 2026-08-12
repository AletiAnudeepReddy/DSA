Detect Cycle in a Directed Graph — Kahn's Algorithm
Problem

Given a directed graph with V vertices and an adjacency list adj, determine whether the graph contains a cycle.

Example
V = 3
adj = [[1], [2], [0]]

Graph:

0 → 1 → 2
    ↑   ↓
    └───┘

There is a cycle:

0 → 1 → 2 → 0

Output:

true
Approach: Topological Sort using Kahn's Algorithm

Kahn's Algorithm is normally used to find a topological ordering of a directed graph.

Important property

A directed graph has a topological ordering if and only if it contains no cycle.

So:

If we can process all V vertices → No cycle
If some vertices cannot be processed → Cycle exists
Key Idea: Indegree

indegree[i] = number of edges coming into vertex i.

For example:

0 → 1 → 2
↑       ↓
└───────┘

Every vertex has:

indegree[0] = 1
indegree[1] = 1
indegree[2] = 1

There is no vertex with indegree 0.

Therefore, Kahn's algorithm cannot start → cycle exists.

Algorithm
Step 1: Calculate indegree

For every edge:

u → v

increase:

indegree[v]++;
Step 2: Add all indegree-0 vertices to queue
for(int i = 0; i < V; i++) {
    if(indegree[i] == 0) {
        q.add(i);
    }
}

These vertices can safely appear first in a topological ordering.

Step 3: Process the queue

Remove a node:

int node = q.remove();
cnt++;

Then remove its outgoing edges conceptually:

node → neighbor

Therefore decrease:

indegree[neighbor]--;

If its indegree becomes 0, add it to the queue.

Step 4: Count processed vertices

At the end:

if(cnt == V)
    return false;
else
    return true;

Why?

If:

cnt == V

every vertex was processed → topological ordering exists → no cycle.

If:

cnt < V

some vertices are stuck with indegree > 0 → they belong to a cycle or depend on a cycle → cycle exists.

Java Code
class Solution {

    public boolean isCyclic(int V, ArrayList<ArrayList<Integer>> adj) {

        int[] indegree = new int[V];

        // Calculate indegree of every vertex
        for(int i = 0; i < V; i++) {
            for(int it : adj.get(i)) {
                indegree[it]++;
            }
        }

        Queue<Integer> q = new LinkedList<>();

        // Add vertices with indegree 0
        for(int i = 0; i < V; i++) {
            if(indegree[i] == 0) {
                q.add(i);
            }
        }

        int cnt = 0;

        // Kahn's Algorithm
        while(!q.isEmpty()) {

            int node = q.peek();
            q.remove();

            cnt++;

            // Remove node's outgoing edges
            for(int it : adj.get(node)) {

                indegree[it]--;

                if(indegree[it] == 0) {
                    q.add(it);
                }
            }
        }

        // If all vertices were processed, no cycle
        return cnt != V;
    }
}
Dry Run

Consider:

V = 3
adj = [[1], [2], [0]]

Graph:

0 → 1
↑   ↓
└── 2
Calculate indegree
0 → 1
1 → 2
2 → 0

Therefore:

indegree[0] = 1
indegree[1] = 1
indegree[2] = 1

Queue:

[]

There is no indegree-0 vertex.

So the while loop never executes.

cnt = 0
V = 3

Therefore:

cnt != V

returns:

true

Hence, cycle exists.

Why does a cycle cause this?

Consider:

0 → 1 → 2 → 0

Each node is waiting for another node to be removed.

0 needs 2
1 needs 0
2 needs 1

So none of them ever reaches:

indegree = 0

They remain stuck.

That's exactly how Kahn's algorithm detects the cycle.

Example Without Cycle
0 → 1 → 2

Indegrees:

0 = 0
1 = 1
2 = 1

Queue initially:

[0]

Process 0:

0 → 1

Decrease:

indegree[1] = 0

Queue:

[1]

Process 1:

1 → 2

Now:

indegree[2] = 0

Queue:

[2]

Process 2.

Finally:

cnt = 3
V = 3

Therefore:

cnt == V

No cycle.

The Most Important Intuition

Think of indegree 0 as "nothing is blocking this node."

Kahn's algorithm repeatedly does:

Find an unblocked node
        ↓
Process it
        ↓
Remove its outgoing edges
        ↓
Some other nodes may become unblocked
        ↓
Repeat

If eventually everyone gets unblocked:

cnt == V

→ no cycle.

If some nodes remain permanently blocked:

cnt < V

→ cycle exists.

Complexity

Let:

V = number of vertices
E = number of edges
Time

Calculating indegree:

O(E)

Processing every vertex and edge:

O(V + E)

Overall:

O(V + E)
Space
indegree[] → O(V)
queue      → O(V)

The adjacency list itself takes:

O(V + E)

So auxiliary space is:

O(V)
Kahn's Algorithm vs DFS Cycle Detection

There are two common ways to detect a cycle in a directed graph:

Method	Main Idea
DFS	Detect a back edge using recursion/path tracking
Kahn's BFS	Check whether topological sorting is possible
DFS

Uses:

visited[]
pathVisited[]

A node encountered again in the current recursion path means cycle.

Kahn

Uses:

indegree[]
queue
count

If:

count < V

then cycle exists.

Remember This

For directed graph cycle detection using Kahn's Algorithm:

Calculate indegree
       ↓
Put indegree-0 nodes in queue
       ↓
BFS
       ↓
Decrease indegree of neighbors
       ↓
Add newly-zero nodes
       ↓
Count processed nodes
       ↓
cnt == V ? No Cycle : Cycle
One-line formula
return cnt != V;

Because a cycle means a complete topological ordering is impossible.