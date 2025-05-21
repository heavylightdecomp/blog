---
layout: post
title: "AIIO 2019 Scootchhop Solution"
date: 2023-12-13
categories: ["grid graph", "dp", "simd", "srq"]
mathjax: true
---
*Note: I forgot to finish the rest of this post for ages, lol. I'll update this post with a detailed breakdown of the full solution Soon™*

It's been a while since I posted here! Anyways, here's an unofficial editorial for [this](https://orac2.info/problem/aiio19hop/) problem.

### Subtasks 1-5

Key observation here is that even though the grid could be filled with negative numbers, we almost never touch them. In fact, we only need to touch negatives if they are on our starting/ending squares. So in a modified grid, we can set all negatives to 0, find the best path through this grid (touching no negative values), and then add the negative values into our final answer.

The optimal path in our modified grid (which is representative of the optimal path in the original grid) always goes 1 square right or down (Lemma 1). So this is a simple path DP problem: `dp[i][j] = grid_value[i][j] + max(dp[i+1][j], dp[i][j+1])`, where `dp[i][j]` represents the best way to get to $$(R, C)$$. 

### Lemma 1 "Proof"

Suppose you had an optimal path in the original grid $$(x_1, y_1) \rightarrow (x_2, y_2) \rightarrow ... \rightarrow (x_N, y_N)$$. 

Now, in the modified grid, from any square $$(x_i, y_i)$$ you can reach the next square $$(x_{i+1}, y_{i+1})$$ by repeatedly going 1 square right/down. For example, you can go $$x_{i+1} - x_i$$ squares right and $$y_{i+1} - y_i$$ squares down. If you touch a zero (which would have been zero/negative originally), then the cost of your path doesn't change, maintaining its optimality. An edge case is that you must factor in negatives at the start and at the end as described before.

### Subtask 6

In this subtask you are guaranteed all queries pass through a vertical midpoint, call it column `m`. Now, when it goes through this midpoint, it has to pass a certain row, let's call it `k`. That is, for any path, there exists a "crossing point" $$(k, m) \rightarrow (k, m+1)$$. Then, we can construct an aforementioned DP to calculate best path from all squares in left half to $$(k, m)$$, and another DP for best path from $$(k, m+1)$$ to all squares in right half. This is `2 * O(RC) = O(RC)`. So for each query, the best path which crosses middle column at row `k` is `left_dp[k][x_start][y_start] + right_dp[k][x_end][y_end]`, and to answer the query, take the maximum of this over all values of k. There is `O(RC)` DP grid structure for each of the `R` possible values of `k`, making our final time complexity $$O(R^{2}C)$$.


### Full Solution
Use a divide and conquer method similar to the ["Static Range Queries" trick](https://usaco.guide/plat/DC-SRQ?lang=cpp)
