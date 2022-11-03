---
layout: post
title: "FARIO 2005 Monkey Tour Solution - an adventure!"
date: 2022-11-02
categories: ["graph theory", "bfs", "math", "kruskal"]
mathjax: true
---
Unofficial editorial for [this](https://orac2.info/problem/fario05monkey/) problem.

### Algorithm outline

If you can quickly calculate the swings required (let’s call it “Monkey distance”) to go between two arbitrary points in the grid, you can use this as an “edge” weight, draw $$E = O(N^2)$$ edges between the N nests and then use a variation of Kruskal’s algorithm to solve in $$O(E log E)$$ 

The algorithm is as follows:

- Sort edges by increasing order of weight
- Repeatedly add edges in increasing order of weight until there is a connected component of size $$\le N/2$$ (use [Union-Find](https://en.wikipedia.org/wiki/Disjoint-set_data_structure) data structure)
- Your answer is the weight of the last edge that you added

### Monkey distance

The 'Monkey distance' subproblem has many symmetrical cases. In fact, we only need to consider the Monkey distance from the origin $$(0,0)$$ to all integer coordinates $$(x,y)$$ where $$0 \le x,y \le 200$$. Calculating the Monkey distance from $$(0,0)$$ to $$(x,y)$$ corresponds to all cases where the two points are separated by $$x$$ horizontal units and $$y$$ vertical units. This approach is correct because any two points must be separated by a maximum of 200 horizontal/vertical units.

```python
# Monkey distance between two points i, j
def weight(i,j):
	x = abs(x_coordinate[i] - x_coordinate[j])
	y = abs(y_coordinate[i] - y_coordinate[j])
	return from_origin[x][y]
```

So, with this observation, how to find Monkey distance?

Any swing can be represented as the diagonal of an $$a \times b$$ rectangle ($$a$$ and $$b$$ both positive) which has a corner at some integer coordinate. By the distance constraint (the diagonal length must be $$\le L$$) and the formula for Euclidean distance, $$(a,b)$$ must satisfy $$a^2 + b^2 \le L^2$$ . From this inequality, we must also have $$a,b \le L$$ (Lemma 1). 

To ensure our monkey does not swing in line with another tree, we must also have $$gcd(a,b) = 1$$ (Lemma 2 - Proof described later).

By Lemma 1 there are $$O(L^2)$$ possible $$(a,b)$$ pairs. You can trivially precompute them.

Now imagine, in yet another graph, each integer coordinate is a node and valid swings from that coordinate are edges. We can BFS from (0,0) and output the minimum number of edges required to reach every coordinate. Obviously we can only do this with a finite number of coordinates and we must account for the fact that our monkey can swing to negative coordinates (going outside the $$x \times y$$ bounding box); from experimenting with the online judge, constraining x and y coordinates to $$[-220, 220]$$ suffices to score 100 points.

### Lemma 2 "Proof"

Proposition: $$gcd(a,b) = 1$$ for all valid $$(a,b)$$ pairs.

WLOG assume that our swing is from $$(0,0)$$ to $$(a,b)$$ . If this is not true just rotate, reflect and translate the swing until it is - the proof's validity is not affected. 

**Case 1:** $$a,b > 0$$

For this case, suppose $$gcd(a,b) > 1$$. Our diagonal swing is a line segment. This segment can be represented by the linear function $$f(x) = \frac{b}{a}x$$ with $$0 \le x \le a$$. At $$x = \frac{a}{gcd(a,b)}$$, which is clearly not an endpoint as $$1 < gcd(a,b) \le a$$, we have $$f(x) = \frac{b}{a} \times \frac{a}{gcd(a,b)} = \frac{b}{gcd(a,b)}$$. By definition of greatest common **divisor** we know that $$ \frac{b}{gcd(a,b)} $$ is an integer. 

Then we would have the integer point $$(\frac{a}{gcd(a,b)}, \frac{b}{gcd(a,b)})$$ that lies in line with the swing. So $$gcd(a,b) > 1$$ implies that the swing passes through an integer coordinate that is not an endpoint (another tree). Then, by the law of contraposition, $$gcd(a,b) = 1$$ implies that the swing does not pass through another tree.

**Case 2:** $$a = 0 \: \text{or} \: b = 0$$

WLOG assume $$a = 0$$. If $$b > 1$$, the swing will at least pass through $$(0,1)$$. So we must have $$b = 1$$. Similar logic can be applied when $$b = 0$$.

So the only valid $$(a,b)$$ pairs in this case are $$(0,1)$$ and $$(1,0)$$ which satisfies $$gcd(0,1) = gcd(1,0) = 1$$.

Further proof and implementation is left as an exercise to the reader.