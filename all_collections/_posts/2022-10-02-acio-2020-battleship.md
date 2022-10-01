---
layout: post
title: "ACIO 2020 Battleship Solution"
date: 2022-10-02
categories: ["acio", "solution", "competitive programming", "dp", "sortings"]
mathjax: true
---
A solution to [this](https://orac2.info/problem/acio20contest0battleship/) problem. As usual, please consider reading through and attempting the problem for a while :) 

Without loss of generality let's assume battleship A is either to the left or above of battleship B. The case where A is to the right or below B is covered by the case where locations of A and B are swapped. 

### Subtask 1 (single row map)
Because there is only 1 row, for this subtask we can consider the problem in a 1D array. Let `dp[i]` be the maximum battleship restricted to the subarray `[1...i]`. `dp[i]` is easily calculated -> `max(dp[i-1], value of battleship placement ending at i)`. But how is it useful? Well, if battleship B starts at `j` then battleship A is restricted to subarray `1...j-1`. So the best placement of A (when B starts at `j`) is `dp[j-1]`. Our answer with respect to the fixed placement of B is `dp[j-1] + cost of battleship starting at j`. Our overall answer is the maximum value of this over all possible `j` (basically we iterate through the `O(N)` possible starting points of B).

### Subtask 2 (N = M = K)
In this subtask, a ship takes up an entire row/column. Thus ships must both be rows or both be columns. Hence our answer is `max(maximum 2 columns, maximum 2 rows)`. You can find the maximum 2 rows trivially: Sort the rows by their row sum and return the sum of the maximum 2 entries.

You can apply similar logic for columns, or you can rotate the grid 90 degrees and reapply the above algorithm. Because rows in this rotated grid will correspond to columns in the original grid :O

This subtask can also be solved with dynamics programming (a simple exercise). 
### Final solution
> battleship A is either to the left or above of battleship B.

If you think about the solution this way, it is not very complicated. **We'll iterate through all O(NM) possible placements of battleship B**. Then, we can consider the two cases separately: A is to the left of B and A is above B. In the first case, the optimal A should be the maximum battleship within the subrectangle "above" B. In the second case, it should be the maximum battleship within the subrectangle "to the left" of B.

In both cases, A is the maximum battleship within a subrectangle that starts at the topleft corner. Let `dp[i][j]` be the maximum battleship contained within the subrectangle starting at the topleft corner `(1,1)` and ending at `(i,j)`. We see that a suitable dynamics programming recurrence for is `dp[i][j] = max(dp[i-1][j], dp[i][j-1], cost of placing battleship horizontally/vertically ending at (i,j))`. A good way to think about this is `max(best battleship within subrectangle (i,j) without touching (i,j), best battleship touching (i,j))`. 

Equipped with this knowledge, with respect to battleship B ending at `(i,j)`:

When A is above B, A is optimally `dp[i-1][M]`. When A is to the left of B, A is optimally `dp[N][j-1]`.

Proof and implementation is left as an exercise to the reader (there are a few edge cases to consider!).