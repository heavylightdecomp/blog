---
layout: post
title: "FARIO 2020 Papa and Baby Frog Solution"
date: 2022-09-17
categories: ["aio", "solution", "competitive programming", "cpp"]
---
If Papa is at the tallest stone, he cannot jump to another stone. Otherwise, he can jump to exactly one stone. Similar logic applies to Baby. If Papa can jump from stone u to v, draw a directed edge from `u` to `v`. Do the same for Baby. Sorting stones by height and using C++ set.lower_bound/set.upper_bound, we can find these edges in `O(n log n)`.

Clearly these two graphs are both trees (well, actually, there can be multiple trees in each graph, but you will see this does not affect the solution). In the Papa tree, for a fixed home node, candidate school nodes for Papa are the k ancestors above. But there is another constraint: in the Baby tree, the selected home node must be part of the `k` ancestors of a candidate school node. Rephrase the 2nd constraint as "in the Baby tree, a candidate school node must be in the selected home node's subtree and have a distance to home node <= `k`". Suppose we know the candidates that are in the home node's subtree. Then, if there is a possible school node, it can be the node that minimizes distance to root.

Proof sketch:
w.r.t Baby tree,
Distance between fixed home node `u` and some candidate school `v` is `distToRoot(v)-distToRoot(u)` (recall u is an ancestor of v). We must have `distToRoot(v)-distToRoot(u) <= k`. Suppose we have a candidate school `a` such that `distToRoot(a)` is minimal, and a valid candidate school `b`. Clearly `distToRoot(b) >= distToRoot(a)` and `distToRoot(b)-distToRoot(u) <= k`. Then, we have by algebra `distToRoot(a)-distToRoot(u) <= k`, thus if there exists a valid `b` then `a` must also be valid.

Do a DFS on Papa tree, when each node is visited, it will be fixed as the home node. Then, each node's k ancestors can be maintained in O(n) amortized time using `deque` approach:
```cpp
#define dtr distToRoot
deque<node> ans = {}
void dfs(node x) {
	vector<node> torevive;
	while(ans.size() > k) {
		node gone = ans.front();
		ans.pop_front();
		torevive.push_back(gone);
	}
	// ans now stores k ancestors of fixed home node x
	ans.push_back(x); //prepare for next dfs
	for(node child : children[x]) {
		dfs(child);
	}
	assert(ans.back() == x);
	for(node y : torevive) ans.push_front(y);  
}
```
where DFS is called on all the root nodes on Papa tree.

In the Baby tree apply [Euler Tour technique](https://usaco.guide/gold/tree-euler). Then, in Baby Tree, for all nodes u, `st[x]` of each node x in its subtree will be in the range `st[u], en[u]]`. 

For each fixed home node `u`, w.r.t Baby tree, we can now use segment tree to query for minimum `dtr` in `[st[u], en[u]`. If some node y in Papa tree is not part of the k ancestors, index `st[y]` in segment tree to infinity. This can be achived by initializing every element in segment tree to $$\infty$$. And then, in the DFS, when we add `y` to deque, we set index `st[y]` to `dtr(y)` in segment tree. When we remove `y` from deque, we set index `st[y]` to infinity. 

Call this minimum dtr `d`, if `d-dtr(u) <= k` then there exists a valid school node for `u`. Checking this amounts to a segment tree query in the DFS.

Proof and implementation is left as an excercise to the reader.