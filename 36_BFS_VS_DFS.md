# BFS vs DFS: Explore Nearby or Go Deeper?

Imagine searching for a file in a folder full of subfolders.

You could check every folder at the current level before going deeper. Or you could follow one folder path all the way down, then return to explore the others.

That is the core difference between **Breadth-First Search (BFS)** and **Depth-First Search (DFS)**.

Both traverse trees and graphs. What changes is the order they explore—and the problems they solve best.

## Breadth-First Search: Explore Level by Level

BFS visits the starting node, then its immediate neighbors, then their neighbors.

It typically uses a **queue**: the first node added is the first node processed.

Consider this tree:

- A has children B and C.
- B has children D and E.
- C has children F and G.

Starting at A and visiting children from left to right, BFS produces:

**A → B → C → D → E → F → G**

It finishes one level before moving to the next.

### When is BFS useful?

BFS is useful when you need the **shortest path by number of edges in an unweighted graph**.

For example, if each connection between two people counts as one step, BFS can find the fewest connections between you and another person.

This works because BFS explores all nodes one step away before exploring nodes two steps away.

If edges have different costs, ordinary BFS does not guarantee the cheapest path.

## Depth-First Search: Follow One Branch

DFS follows a branch as far as possible, then backtracks to explore another branch.

It typically uses a **stack**, either explicitly or through recursive function calls.

Using the same tree and visiting children from left to right, DFS produces:

**A → B → D → E → C → F → G**

It explores B’s entire branch before moving to C.

### When is DFS useful?

DFS is useful for problems that involve exploring possibilities or relationships deeply, such as:

- Detecting cycles.
- Finding connected components.
- Topological sorting in a directed acyclic graph.
- Backtracking through puzzles and possible solutions.

DFS can find a path between nodes, but the first path it finds may not be the shortest.

## BFS vs DFS at a Glance

| Feature | BFS | DFS |
|---|---|---|
| Exploration order | Level by level | Branch by branch |
| Main structure | Queue | Stack or recursion |
| Shortest path in an unweighted graph | Guaranteed | Not guaranteed |
| Typical use | Fewest-step paths | Deep exploration and backtracking |

For a full traversal using an adjacency list, both take **O(V + E)** time, where V is the number of vertices and E is the number of edges.

Memory depends on the graph’s shape. BFS can hold a large frontier in a wide graph. Recursive DFS can build a large call stack in a deep graph. Both can require **O(V)** auxiliary space.

## One Important Detail: Track Visited Nodes

Graphs can contain cycles.

Without tracking visited nodes, your traversal may keep returning to the same nodes indefinitely.

For BFS, mark a node as visited when you enqueue it. For DFS, mark it when you first enter it.

## Key Takeaway

**Use BFS when you need the fewest steps in an unweighted graph. Use DFS when you need to explore branches, dependencies, or possible solutions deeply.**

The right choice comes from the problem you need to solve.