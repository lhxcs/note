# Graph Algorithmes



## Definitions

### Terminology

- $G(V,E)$
- Undirected graph
- Directed graph(digraph)
- Complete graph: a graph that has the maximum number of edges.
  - Undirected: $E=C_n^2$
  - Directed: $E=P_N^2$
- $v_i,v_j$ are adjacent, $(v_i,v_j)$ is incident on $v_i$ and $v_j$.
- Subgraph $G^{'}\subset G$: $V(G^{'})\subset V(G)$ && $E(G^{'})\subset E(G)$
- path
- Simple path: $v_1,v_2,……,v_n$ are distinct.
- Cycle.
- connected graph
-  (Connected) Component of an undirected G: the maximal connected subgraph.
- A tree: a graph that is connected and acyclic.
- DAG: a directed acyclic graph
- Strongly connected directed graph
- Strongly connected component
- Degree: number of edges incident to $v$. $E=\sum degree/2$

### Representation of Graphs

#### Adjacency Matrix

`adj_mat[i][j]`=1 if $(v_i,v_j)\in E(G)$.

If $G$ is undirected, then adj_mat is symmetric. Thus we can save space by storing only half of the matrix.

#### Adjacency Lists

使用$n$个链表来表示图，链表节点表示顶点。每个链表存储了该顶点所有的邻接顶点。



## Topological Sort

- **AOV Network**: digraph $G$ in which $V(G)$ represents activities and $E(G)$ represents precedence relations.

- $i$ is predecessor of $j$：there is a path from $i$ to $j$
- $i$ is an immediate predecessor of $j$: $(i,j)\in E(G)$

- **Partial order**: a precedence relation which is both transitive and irreflexive.

Feasible AOV network must be a dag (directed acyclic graph).

- A topological order is a linear ordering of the vertices of a graph such that, for any two vertices $i,j$, if $i$ is a predecessor of $j$ in the network the n $i$ precedes $j$ in the linear ordeing.

The topological orders may not be unique for a  network.  

```c
void Topsort(Graph G){
    Queue Q;
    int Counter=0;
    Vertex V,W;
    Q = CreateQueue(NumVertex);  MakeEmpty(Q);
    for( each vertex V )
        if(indegree[V]==0) Enqueue(V,Q);
    while(!IsEmpty(Q)){
        V = Dequeue(Q);
        TopNum[V] = ++Counter;
        for (each W adjacent to V)
            if(--Indegree[W]==0) Enqueue(W,Q);
    }
    if(Counter!=NumVertex)
        Error("Graph has a cycle");
    DisposeQueue(Q);/*free memory*/
}
```



## Shortest Path Algorithms

Given a digraph $G=(V,E)$ and a cost function $c(e)$ for $e\in E(G)$. The length of a path $P$ from source to destination is $\sum c(e_i)$, also called weighted path length.

### Single-Source Shortest-Path Problem

- If there is no negative-cost cycle, the shortest path from $s$ to $s$ is defined to be zero.

#### Unweighted Shortest Paths

**idea**: Breath-first search

```c
void Unweighted(Table T){
    Queue Q;
    Vertex V,W;
    Q=
}
```



#### Dijkstra's Algorithm

for weighted shortest paths

```c
void Dijkstra(Table T){
    
}
```

