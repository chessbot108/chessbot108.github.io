---
layout: post
title: "ONTAK 2010 Peaks"
date: 2022-07-20
tags: segtree merge mst kruskal
---

### problem

[luogu 41797](https://www.luogu.com.cn/problem/P4197#submit)

You are given a graph of $n$ nodes and $m$ edges. Each node has a value $h$ Each edge connects two nodes $u$ and $v$ with edge weight $w$. There may be multi-edges and the graph is not guaranteed to be connected. You now have $q$ queries in the form of $v, x, k$ in which you have to output the $k$'th **largest** value of $h$ on a node that can be reached only using edges weight $x$ if you start at node $v$.

$(n \leq 10^5), (m, q \leq 5 \cdot 10^5), (h, x, w \leq 10^9)$

### two logs solution

The solution is to do the queries offline. On each node, you store an indexed set (ordered statics tree) containing the original value of $h$ initially. Then you want to sort the queries and edges in order of increasing weight (making sure to handle the edges before the queries). When you process an edge, you merge them into a connected component (if they aren't merged already) and use small-to-large to put the two indexed sets into one. Then for a query, you query the $k$'th largest on the indexed set of the current node's component. Using the proof of small-to-large, you can show that each element moves $\log n$ time, and it take $\log n$ time to perform one move operation. So the final complexity is $O(n \log^2 n + q \log n + (q + m) \log (q + m))$.

Now try to solve this online...
