
---
layout: post
title: "Claris Loves Painting"
date: 2022-07-21
tags: segtree merge pst
---


### problem
[HDU 5709](https://vjudge.net/problem/HDU-5709)

You have a tree with $n$ nodes an each value has its own weight $a_i$. You have to handle $q$ queries of, for all nodes that in node $u$'s subtree and have distance at most $k$ from it, how many distinct weights are there?

Queries are forced online.

$(1 \leq n, q \leq 10^5), (1 \leq ai \leq n)$


### thoughts

you know its doomed when claris is mentioned. 

### solution

The solution involves a not of implicit/persisent segtrees and requires a rather important prerequisite, [generic segtree merging](https://codeforces.com/blog/entry/49446).

```cpp
void merge(int& k1, int k2){
	if(min(k1, k2) == 0){
		k1 ^= k2;
		return ;
	}
	merge(lc[k1], lc[k2]);
	merge(rc[k1], rc[k2]);
	sum[k1] += sum[k2];
}
```

This is code that merges two implicit segment trees. The goal is to merge the info of the segtrees rooted at `k1` and `k2` into the segtree rooted at `k1`. The first if statement finds if either of the two nodes needing to merge is null. If at lesat one is null, then you set `k1` to be the non null value. Doing `k1 + k2, max(k1, k2), k1 or k2` all also work, I just like xor the best. Then if both nodes and nonempty, you merge the left childs of both nodes into the left child of `k1` and the same for the right child. Finally `sum[k1] += sum[k2]` is the easiest way to pull without worrying about the edge case of leaf nodes.

The intuition about this code is that you have some nodes that both trees share, we'll call these the common nodes. Then for the nodes that are not common nodes, you already have a working segtree structure from one of the trees for that so you can just keep it.

As for the complexity analysis, this takes $O(n \log n)$ memory at most, since no new nodes are created and there are $O(n \log n)$ nodes in the segtrees initially before any merging. As for time, it gets a little trickier. So imagine two trees $A$ and $B$ that are getting merged. How much time is spent is equilivant to the number of common nodes between $A$ and $B$, (lets call this $C$). Since we return if either node is empty at some spot, so the merge function only recurses if both $A$ and $B$ have that node as non empty. It is easy to see this is the same thing as the $\lvert C \rvert$ . Now what is the size of the new tree? Saying its size $\lvert A \rvert + \lvert B \rvert$ is overestimating by a bit... in fact it is overestimating only by the $\lvert C \rvert$! Since the common node are counted twice in the estimate of $\lvert A \rvert +  \lvert B \rvert$ the actual size should be $\lvert A \rvert + \lvert B \rvert - \lvert C \rvert$. So essentially, we used $O(\lvert C \rvert)$ time to remove $\lvert C \rvert$ nodes. With this in mind, the total time complexty/number of nodes removed cannote exceed the number of nodes present initally. And this is equal to $n \log n$.

Now that we have the prereq out of the way, how do we go about solving this problem? So with this merging, we have two choices on what to build the segtree on. 
1. If we keep implicit segtrees over how frequent a color is, modify our merge to count the number of distinct colors but not handle the distance constraint. (:O this is a [cses problem](https://cses.fi/problemset/task/1139) and I have [code written too](https://cses.fi/paste/e7e61beab682a66a414740/))
2. If we build a segtree over the frequencies of the node depths, we can get the number of total colors within some distance of a node but not handle the distinct constraint.

Why not use both?

So ideally we want to keep a persistent segtree per node, and on each node $u$'s pst we want to store a frequency table. In particular $st_i$ should be the number of colors who's minimal depth is $i$ in $u$'s subtree. So on each node, you can merge the pst's of their children and add the current node's value as well. But this new pst overcounts. So we keep a second set of implicit segtrees storing the "inverse" if the pst so to speak. So if a color $a$ is minimal distance $d$ to the current node $u$, $u$'s pst should have `st[u]++`, and the second segtree should store `st2[a] = d`. Note that since we only keep distinct values at the same time, the second segtree should only have distinct entrees. So as you merge the second set of segtrees of all its children, there might exist duplicate entrees. So to counteract this, you can update the pst to subtract out the contribution of the largest distance, and keep the smallest distance as the new one. So together, you can maintain the correct pst for each node by using the second segtree.

The total complexity is $O(n \log n + q \log n)$.
trust

code is [here](https://pastebin.com/vn95A1Dj)

### other things

i spent some time debugging the multitest since i didnt clear right... also what type of stupid judge counts output to stderr as part of wa?

