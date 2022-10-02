---
layout: post
title: "Mo's algorithm can be rollbacked!"
date: 2022-10-02
categories: ["sqrt decomposition", "tutorial", "competitive programming"]
mathjax: true
---
### Motivation problem
You are given a graph of V nodes and N edges. Answer Q queries of the form: how many connected components are there in the subgraph formed by edges $$L_i...R_i$$?

### Algorithm description 
In general Mo's algorithm can answer range queries where it is easy to add an element to an existing range and revert the changes made by the last few added elements (rollbacking). It does not need to perform a standard deletion of an element from a range. For many problems such as the motivation problem above, rollbacking is much easier than standard deletion from the data structure because you just need to store a list of what has been changed and then "revert" those changes. For simplicity, assume both operations are $$O(1)$$.

Divide our N elements into $$\sqrt{N}$$ "buckets" of size $$\sqrt{N}$$. Queries will "lie in the same bucket" if their left endpoints lie in the same bucket. Then, in each bucket, sort queries by their right endpoint. The ith query in that bucket will now be referred to as $$L_i, R_i$$. Initially your data structure represents $$[B,B]$$ where B is the first element "to the right" of our bucket. Push the right pointer until it represents $$[B, R_1]$$ . From there, push the left pointer so that it represents $$[L_1, R_1]$$. Answer the query. Then, because you have just pushed the elements $$[L_1, B-1]$$ you can easily rollback their changes. From there, push your right pointer so that the DS represents $$[B, R_2]$$. Repeat the aforementioned process for all queries in the bucket.

There is one problem though: what happens when a query's right endpoint falls before B? Clearly the query size must be $$\le \sqrt{N}$$ for this to happen, so we can ignore these "light queries" for now and answer them trivially in $$O(\sqrt{N})$$ after running the main algorithm. 

Here is a visualization of the algorithm:
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/LglNp-h7ijM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Example of a "light query":

![Light query example](https://i.imgur.com/FqTMQrA.png)