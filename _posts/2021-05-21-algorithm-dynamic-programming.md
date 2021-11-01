---
title: "Algorithm-Dynamic Programming"
categories:
  - Algorithm
tags:
  - Algorithm
  - Dynamic Programming
---

In a nutshell, it’s a method of solving a complex problem by breaking it down into
a collection of smaller subproblems, solving them just once, and storing their solution.

The problem should be able to be divided into smaller overlapping sub-problem.
An optimum solution can be achieved by using an optimum solution of smaller sub-problems.
Dynamic algorithms use memorization.
Dynamic programming problems can be solved using 2 approaches:

Bottom Up Dynamic programming: In this approach, we first analyze the problem and see the order
in which the sub-problems are solved.
We start by solving the trivial sub problem and build up towards the given problem.

Top Down Dynamic Programming: In this approach, we start solving the given problem by breaking it down.
If you see that a given sub problem has been solved already, then just return the stored solution.
