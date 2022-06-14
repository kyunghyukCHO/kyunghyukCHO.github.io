---
layout: single
title:  " [자료구조] Union Find & an Application "
---

Application (Minimum Spanning Tree)
===
* Given a Graph, find Subset of Edges so that a Connected Graph results with Minimum Sum of Edge Costs
* here, Tree means Connected Actyclic Graph

kruskal Algorithm
---
* keep adding Edges
    * From smaller weights
    * As long as no Cycle results
    * until N-1 Edges are added 

Why is Kruskal Correct?
---
* adding smallest edge "that does not make a cycle"
* if it makes a cycle, then you cannot add the edge
* if you insist on adding the edge, then you have to remove an edge with smallest Cost -> Does not make sense 
* Not a complete Proof but that's the idea

Implementation Considerations
---
* you have to sort edges by weight?
* how to represent ad edge (for sorting)?
    * you can just use the triple (a,b,w), where a,b are Nodes and w is the weight of the edge
* when considering an edge(a,b,w), you have to know if a and b belong to a "connected" group
* you have to be able to answer the question efficiently
* Tool of All above this line : DFS -> 덩어리 내 연결 x 덩어리 끼리 연결 o

DFS is slow 
---
* DFS takes O(n) time
* the whole kruskal algorithm will take O(mn) time
* can we do faster?
* can we answer this question faster?
    * for an edge(a,b,w), does a and b belong to a "connected" group

----------

Union/Find or Disjoint Set
---
* N items
* initially N groups with 1 item each
* tow operations
    * union(a,b) : a 그룹과 b 그룹을 한 그룹으로 union
    * Find(a) : a 가 속한 그룹의 ID 를 return

The Structure
---
* each item is a node
* initially there are n trees with on node each
* after some union operations
    * each tree is one group
    * the root of each tree is the id of the group
    * no other restrictions
* a node stores only the pointer to the parent
* 즉 child pointer 가 존재하지 않습니다. -> we can try to keep the Trees in "good" form
* union 하면서 한쪽의 노드들을 다른 그룹의 루트에 모두 붙이면 좋지만, child를 찾을 수 있는 방법이 없습니다.  

which should be the Root?
---
* by # of nodes
* by Height
* both give you O(logN)

path Compression
---
find 한 노드에서 지나치는 모든 노드들을 root에 붙이는 방법입니다

valiant's proofs
---
* first proof
    * one operation takes O(logN) amortized Time
    * Amortized Time means average time in the worst case
* second proof
    * one operation takes O(lon*N) amortized Time
    * log*N means N이 1이 될때까지 log를 붙일 수 있는 횟수 -> 굉장히 작은 수
* Third Proof
    * one operation takes O(alpha(N)) amortized Time












