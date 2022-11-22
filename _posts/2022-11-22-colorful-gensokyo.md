---
layout: "post"
title: "colorful gensokyo"
date: 2022-11-22
tags: math matrix interpolation polynomial
---

### Problem

You are given a graph with $n$ nodes and $m$ edges. There are no self loops and multiedges. Each edge has a color of $1$, $2$, or $3$. For each $i$ and $j$ count the number of ways to make a spanning tree with $i$ edges of color $1$, $j$ edges of color $2$, and $k$ edges of color $3$. Since these numbers may be very large, please find it mod $10^9 + 7$


$2 \leq n \leq 50, 0 \leq m \leq \frac{n(n-1)}{2}$


### thoughts

kirchoffs tree theorem is a thing


### solution


When it comes to counting spanning trees, [kirchoffs spanning tree theorem](https://en.wikipedia.org/wiki/Kirchhoff%27s_theorem) is helpful. The weighted version of kirchoffs spanning tree states means that is possible to find the sum of the product of all edge weights across all spanning trees. This sum can be computed by taking the determinant of a special matrix defined the edge weights of the graph. Let us define this operation of taking a graph and counting the determinant of the special matrix as evaluting a graph.

Since we are dealing with counting discrete objects, we can think to use generating functions. We can assign each edge with a weight of $x$, $y$ or $z$ depending on if it has color $1$, $2$, or $3$ respectively. Evaluating this graph will give us a polynomial of $x$, $y$, and $z$ where the coefficient on $x^i y^j z^k$ will have the answer for the number of spanning trees with $x$ color 1, $y$ color 2, and $z$ color 3.

Since the number of edges of type $3$ is forced, we only have to count the number of spanning trees with $i$ color $1$ edges and $j$ color $2$ edges. Also since the degree of $x$ cannot exceed $n-1$ in the final polynomial, we can replace $y$ with $x^{(n-1)}$. This gives us an polynomial of degree at most $n^2 - 1$ in which the coefficient of $x^{p}$ counts the number of spanning trees with $i = p \mod (n-1)$ and $j = \frac{p}{(n - 1)}$. Let us call this polynomial $f(x)$.

Since computing the determinant with polynomials may be hard, we can think to use polynomial interpolation. Polynomial interpolation is a technique in which figuring out $d + 1$ points a polynomial of degree $d$ goes through allows us to construct the original polynomial. We can treat $x = a$ for some value of $a$ as a graph of weighted edges with color $1$ as $a$, color $2$ as $a^{(n-1)}$ and color $3$ as $1$. After evaluating the graph, we can recover what $f(a)$ is.

After evaluating $n^2$ points ($(n^2 - 1) + 1$), we can use interpolation to recover what $f(x)$ is. For interpolation

$$
f(x) = \sum_{i = 0}^{n^2} (f(a_i) \cdot\prod_{j = 0, j \neq i}^{n^2} \frac{(x-a_j)}{(a_i - a_j)})

$$

Intuitively, for some $a_i$, the above formula always guarantees that $f(x)$ passes through $f(a_i)$ when $x = a_i$. This is because if $i \neq a_i$ the product will be $0$, and otherwise it will be $1$. [This math.stackexchange comment](https://math.stackexchange.com/a/1688336) helps undestand interpolation better. It is also best to use $a = {0, 1, 2, \dots, n^2}$

As a side note, it is very possible to TLE while trying to construct the right hand sum polynomial. I store the big product of $\prod_{j = 0}^{n^2} (x - j)$ and used synthetic division to divide out $(x - a_i)$ when considering $a_i$.

The final complexity is $O(n^6)$ since you are doing an $n^3$ determinant for $n^2$ points to interpolate.

Final code is [here](https://pastebin.com/DXjDtWC2)