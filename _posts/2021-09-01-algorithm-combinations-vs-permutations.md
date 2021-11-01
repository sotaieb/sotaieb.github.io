---
title: "Algorithm-Combinations VS Permutations"
categories:
  - Algorithm
tags:
  - Algorithm
---

A Permutation is an ordered Combination.

In other words, when the order doesn't matter, it is a Combination. Combinations are for groups.
In the other hand, when the order does matter it is a Permutation.

For example, given an array of three different digits:
n = [1, 2, 3, 4]

How many possible ways that a specific number of four different digits (r=4) can be arranged using the above array digits (n=4) ?
P(n) = n!
P(3) = 3! = 3 x 2 x 1 = 6

1234
1243
1324
1342
1432
1423
2134
2143
2314
2341
2431
2413
3214
3241
3124
3142
3412
3421
4231
4213
4321
4312
4132
4123

How many possible ways that a specific number of two different digits (r=2) can be arranged using the above array digits (n=4) ?

The number of permutations of n objects taken r at a time is determined by the following formula:
P(n,r) = n!/(n-r)!
P(4,2) = 4!/2! = (4 x 3 x 2 x 1) / (2 x 1) x 12
0!=1

12
13
14
21
23
24
31
32
34
41
42
43

## Permutations with Repetition

There are n possibilities at every choice (r times).
n possiblities at first choice, n possibilities at second choice...
Such as "333"

P(n, r) = n exp r

## Permutations without Repetition

In this case, we reduce the number of available choices each time.
In other words, our first choice has n possiblities, our next choice has n-1 possibilities, we can't choose the first element again.
Without repetition our choices get reduced each time.

P = n!

But when we want to select just r positions

n! / (n−r)!

Example: How many ways can first and second place be awarded to 10 people?
P(n,r) = 10!/(10-2)! = 90

## Combination with repition

Order does not matter. (such as (5,5,5,10,10))

C(n,r) = (r + n − 1)! / r! x (n − 1)!

## Combination without repition

Combinations reduce permutations because order does not matter. (such as lottery numbers (1,43,10,18,5))

C(n,r) = n! / (r! x (n−r)!)

## Links

<https://www.mathsisfun.com/combinatorics/combinations-permutations.html>
