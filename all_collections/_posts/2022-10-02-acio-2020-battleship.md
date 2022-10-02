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

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1005;
int dp[maxn], ar[maxn], pref[maxn];
#define INF 2000000005
int cost(int i, int j) { //battleship placed at [i...j]
	return pref[j] - pref[i-1];
}
signed main() {
	freopen("shipin.txt", "r", stdin);
	freopen("shipout.txt", "w", stdout);
	ios_base::sync_with_stdio(0);
	cin.tie(0);
	int N, M, K; cin >> N >> M >> K;
	for(int i = 1; i <= M; i++) cin >> ar[i], pref[i] = ar[i] + pref[i-1];
	fill(dp, dp+M+1, -INF);
	for(int i = 1; i <= M; i++) if(i-K+1 >= 1) dp[i] = max(dp[i-1], cost(i-K+1, i));
	int res = 0;
	for(int j = 1; j+K-1 <= M; j++) res = max(res, dp[j-1] + cost(j, j+K-1));
	cout << res << '\n';
}
```
### Subtask 2 (N = M = K)
In this subtask, a ship takes up an entire row/column. Thus ships must both be rows or both be columns. Hence our answer is `max(maximum 2 columns, maximum 2 rows)`. You can find the maximum 2 rows trivially: Sort the rows by their row sum and return the sum of the maximum 2 entries.

You can apply similar logic for columns, or you can rotate the grid 90 degrees and reapply the above algorithm. Because rows in this rotated grid will correspond to columns in the original grid :O

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1005;
int ar[maxn][maxn];
signed main() {
	freopen("shipin.txt", "r", stdin);
	freopen("shipout.txt", "w", stdout);
	ios_base::sync_with_stdio(0);
	cin.tie(0);
	int N, M, K; cin >> N >> M >> K;
	for(int i = 1; i <= N; i++) {
		for(int j = 1; j <= M; j++) {
			cin >> ar[i][j];
		}
	}
	priority_queue<int> rows, cols;
	for(int i = 1; i <= N; i++) {
		int rsum = 0;
		for(int j = 1; j <= M; j++) {
			rsum += ar[i][j];
		}
		rows.push(rsum);
	}
	for(int j = 1; j <= M; j++) {
		int csum = 0;
		for(int i = 1; i <= N; i++) {
			csum += ar[i][j];
		}
		cols.push(csum);
	}
	int opt_r = 0, opt_c = 0;
	opt_r += rows.top(), rows.pop();
	opt_r += rows.top();
	
	opt_c += cols.top(), cols.pop();
	opt_c += cols.top();
	cout << max(opt_r, opt_c) << '\n';
}
```
This subtask can also be solved with dynamics programming (a simple exercise). 
### Final solution
> battleship A is either to the left or above of battleship B.

If you think about the solution this way, it is not very complicated. **We'll iterate through all O(NM) possible placements of battleship B**. Then, we can consider the two cases separately: A is to the left of B and A is above B. In the first case, the optimal A should be the maximum battleship within the subrectangle "above" B. In the second case, it should be the maximum battleship within the subrectangle "to the left" of B.

In both cases, A is the maximum battleship within a subrectangle that starts at the topleft corner. Let `dp[i][j]` be the maximum battleship contained within the subrectangle starting at the topleft corner `(1,1)` and ending at `(i,j)`. We see that a suitable dynamics programming recurrence for `dp` is `dp[i][j] = max(dp[i-1][j], dp[i][j-1], cost of placing battleship horizontally/vertically ending at (i,j))`. A good way to think about this is `max(best battleship within subrectangle (i,j) without touching (i,j), best battleship touching (i,j))`.

Equipped with this knowledge, with respect to battleship B ending at `(i,j)`:

When A is above B, A is optimally `dp[first empty row above B][M]`. When A is to the left of B, A is optimally `dp[N][first empty column to left of B]`.

![Visualization of the above](https://i.imgur.com/HBph9z7.png)

Proof and implementation is left as an exercise to the reader (there are a few edge cases to consider!).