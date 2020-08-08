## Interpreting the Dual Problem

It turns out that dual problem actually have their own meanings. The interpretations of dual problems may give us a better understanding of the duality theory.

### Production Planning

Production Planning problem is one of the most common examples of LP. It takes to following form:
$$
\max c^\top x, x \ge 0, Ax \le b
$$
Here, $x$ is the vector of the amount of different products to produce. $Ax \le b$ gives us the resource limit and $c\top x$ is the profit.

The dual problem should be easy to calculate:
$$
\min b^\top y, A^\top y \ge c,  y \ge 0
$$
How to understand the dual problem? Imagine that a trader wants to buy resources from the factory. More precisely, he wants to buy $b_i$ units of $i$-th resource with price $y_i$. So, the problem is to minimize the total cost. Then, what is $A^\top y \ge c$ talking about?

Consider we have $A_j^\top y \lt c_j$, then the factory can manufacture $j$-th product on its own to gain more profit so that the price is not fair enough to make a deal. Hence, in order to establish an agreement, we must have
$$
A^\top y \ge c
$$
Therefore, the dual variables can be interpreted as fair prices for each resource.

### Multi-Firm Alliances

Now let us look at an enhanced problem derived from **Production Planning**. We are still producing products, however, we now need to handle multiple firms:

- All firms produce a same set of products with identical consumption matrix $A$ and profit vector $c$.
- Firm $i$ has access to resources upper-bounded by $b_i$
- Each firm wants to maximize its own profit $V_i$ by:

$$
\max c^\top x, x \ge 0, Ax \le b_i
$$

With a powerful computer, it is not hard to solve the maximization problem for every firm. The real problem occurs when multiple firms want to join together to pool their resources and gain a higher profit.

For a given subset $S$ of firms who form an alliance together, the maximization problem now becomes:
$$
\max c^\top x, x \ge 0, Ax \le \sum_{i \in S} b_i
$$
The total optimal profit ($c^\top x$) for the alliance is denoted by $V_S$. If all firms are joining together, we call the alliance as the **grand alliance** and its optimal profit is denoted by $V_\ast$.

However, the union is not formed for free. We must have a fair profit allocation for each member of the alliance. One of the important criteria is that every sub-alliance must gain more profit in the grand alliance. Otherwise, the union is unstable.

Let $z_i$ be the profit allocated for $i$-th firm, then the constants of the allocation is described by:
$$
\begin{aligned}
\sum_{i = 1}^n z_i &= V_\ast \\
\sum_{i \in S} z_i &\ge V_S, \forall S \in \textbf{Pow}(1, ..., m)
\end{aligned}
$$
It such allocation $z = (z_1, ..., z_m)$ exists, we call it the **core** of the grand alliance. Apparently, it is not easy to find a feasible solution become cardinal of the power set is as large as $2^m$, which is of an exponential complexity. 

However, the dual of the grand alliance production problem is much easier to solve:
$$
\min_y \left(\sum_{i = 1}^m b_i\right)^\top y, A^\top y \ge c, y \ge 0
$$
What's more, we claim that the **core** of the grand alliance is
$$
z = \left[b_i^\top y_\ast\right]_m
$$
where $y_\ast$ is the optimal solution of the dual problem.

**Proof**

By strong duality theory, we have
$$
\left(\sum_{i = 1}^m b_i\right)^\top y = \sum_{i = 1}^m z_i = V_\ast
$$
Hence, we only need to show $\sum_{i \in S} z_i \ge V_S, \forall S \in \textbf{Pow}(1, ..., m)$.

Consider the production problem for every single subset alliance.
$$
\begin{aligned}
&\max c^\top x, x \ge 0, Ax \le \sum_{i \in S} b_i \\ 
&\min \left(\sum_{i \in S} b_i\right)^\top y, A^\top y \ge c, y \ge 0
\end{aligned}
$$
The real magic is that $y_\ast$ is also feasible for the dual problem for any sub-alliance. Hence, according to weak duality theory, we have:
$$
\sum_{i = 1}^m z_i = \left(\sum_{i = 1}^m b_i\right)^\top y \ge V_S
$$
which means $z = \left[b^\top_iy_\ast\right]_m$ is exactly the core of the grand alliance.

### Alternative Systems

Consider a set of linear inequalities:
$$
A^\top y \le c
$$
To verify existence, we only need to find a solution (we call it a **certificate**). 

Can we also grand a certificate for non-existence? The answer is positive. In fact, if we can find a solution for the following problem:
$$
Ax = 0, x \ge 0, c^\top x < 0
$$
then there must be no solution for the original system.

**Proof**

We first construct a feasibility problem:
$$
\max_y 0, A^\top y \le c
$$
The dual of the problem is
$$
\min c^\top x, Ax = 0, x \ge 0
$$
Assume there exists $x$ with $Ax = 0, x \ge 0, c^\top x < 0$, then by scaling $x$ we make $c^\top x$ arbitrarily negative. This unboundedness means the primal problem is infeasible.

### Maxflow Problem

![maxflow](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cb/MFP1.jpg/360px-MFP1.jpg)

To focus on Linear programming, we will not give a detailed definition of maxflow problem here, but you can [check it on wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cb/MFP1.jpg/360px-MFP1.jpg). One can transform the problem by adding a unlimited edge from $t$ to $s$ such that the problem becomes maximizing the flow on that edge. This form is equivalent to the original problem and can be described using the following formulas:
$$
\begin{aligned}
\max_{x, \Delta}&~~~ \Delta \\
s.t. &\sum_{j: (j, i) \in E} x_{ji} - \sum_{j:(i, j)\in E} x_{ij} = 0, &\forall i \ne s, t
\\
&\sum_{j: (j, s) \in E} x_{js} - \sum_{j:(s, j)\in E} x_{sj} + \Delta = 0
\\
&\sum_{j: (j, t) \in E} x_{jt} - \sum_{j:(t, j)\in E} x_{tj} - \Delta = 0
\\
&x_{ij} \le w_{ij} & \forall (i, j) \in E
\\
&x_{ij} \ge 0 & \forall (i, j) \in E
\end{aligned}
$$
The dual problem takes a while to derive so we simply list it here:
$$
\begin{aligned}
\min &~~ \sum_{(i, j) \in E} c_{ij}z_{ij} \\
s.t. &~~ z_{ij} \ge y_i - y_j, &\forall (i, j) \in E \\
     &~~ y_s - y_t = 1 \\
     &~~ z_{ij} \ge 0
\end{aligned}
$$
We are more interested in the meaning of the dual problem. If $y_i \gt y_j$, then $z_{ij} = y_i - y_j \gt 0$, otherwise, we have $z_{ij} = 0$. 

Let us simply assume $y_i =0, 1, \forall i$, then $y_i$ is actually working as a label for the nodes and $\sum_{(i, j) \in E} c_{ij}z_{ij}$ is the flow the two clusters of nodes.

The structure of the two clusters and the edges between them is called a **cut** of the network. Now we comes to another famous problem, the **min-cut** problem. Moreover, we get the following theorem for free:

#### Max-flow Min-Cut Theorem

The maximum flow of the network is equal to the smallest cut size of any subset $S$ of vertices.

If the value of a flow is equal to the value of some cut, then both are optimal.

### Game Theory (Optional)

Consider two players $A$ and $B$ are playing game each with options $m$ options and $n$ options. A matrix $K \in \mathbb R^{m \times n}$ describes the gaining of player $A$. More precisely, when $A$ provides option $i$ and $B$ provides option $j$, then $A$ will gain $K_{ij}$ points while $B$ will lose $K_{ij}$ points.

Let $x, y$ be the probability distribution vector of $A$ and $B$, then the probability of option $(i, j)$ will be $x_iy_j$.

It is not hard to see, the expectation of $A$s gaining is
$$
x^\top Ky
$$
Now we want to maximize $A$'s gaining no matter how $B$ distributes his/her probability, this problem can be described by:
$$
\max_{x, t} t, \begin{cases}
K^\top x \ge t\mathbf 1 \\
\mathbf 1^\top x = 1, \\
x \ge 0
\end{cases}
$$
Actually, the region described by:
$$
\begin{cases}
\mathbf 1^\top x = 1, \\
x \ge 0
\end{cases}
$$
is called standard simplex or probability simplex. We denote it by $\mathcal P$. 

Surprisingly, the dual problem is
$$
\min_{y, t} s, \begin{cases}
K y \le s\mathbf 1 \\
\mathbf 1^\top y = 1, \\
y \ge 0
\end{cases}
$$
which can be interpreted as player $B$ wanting to minimize his/her loss.

Here is part of the derivation of the dual:
$$
\begin{aligned}
&\max_{x\ge 0, t} \min_{y \ge 0, s} \left[t  +  y^\top (K^\top x - t\mathbf1) + s(\mathbf 1^\top x - 1\right)] \\
\Rightarrow& \min_{y \ge 0, s} \max_{x\ge 0, t}  \left[t  +  y^\top (K^\top x - t\mathbf1) + s(\mathbf 1^\top x - 1\right)] \\ 
\end{aligned}
$$
At this step, if we re-insert the constraints:
$$
x \in \mathcal P, y \in \mathcal P
$$
we can directly get the form:
$$
\max_{x \in P}\min_{y \in P}y^\top Kx
$$
Since duality holds here,
$$
\max_{x \in P}\min_{y \in P}y^\top Kx = \min_{y \in P} \max_{x \in P} y^\top Kx
$$
This indicates that as long as this value is $0$, the game is fair for both sides.

## Sensitivity Analysis

Another topic of LP is study the change of the output when the input changes.

The first kind of sensitive analysis is the **local sensitivity** where the change of input is relatively small.

### Local Sensitivity

Again, we consider the standard form of the LP problem:
$$
\min c^\top x, Ax = b,  x \ge 0
$$
We denote the associated optimal value by $V$. When fixing $A, c$, $V$ is a function of $b$, denoted by $V(b)$.

The following theorem gives us a powerful tool the analyze **Local Sensitivity**:

#### Theorem: Differentiability of the Optimal Value Function

If the dual has a unique optimal solution $y^\ast$, then $\nabla V(b) = y ^\ast$. 

This also indicates that if the dual optimal solution is not unique (or is unbounded or infeasible), then the gradient is not well-defined.

A straightforward explanation of the gradient value comes from the dual problem. In the dual form, the optimal value is expressed as $b^\top y$, therefore it is not hard to see the rate of change with respect to $b$. If we change $b$ by a small amount $\Delta b$, such that the optimal solution does not change, then the change of $V$ must be $\Delta b^\top y^\ast$

Similarly, we also have a theorem for $V(c)$ ( if $A$ and $b$ are fixed, $V$ can be viewed as a function of $c$):

#### Theorem: Differentiability of $V(c)$

If the primal prob. has a unique optimal solution $x^\ast$, then $\nabla V(c) =x^\ast$

If we change $c$ by a small amount $\Delta c$, such that the optimal solution does not change, then the change of $V$ must be $\Delta c^\top x^\ast$

The above result is also true for non-standard form LPs.

#### Inactive Constraints

Suppose we have the following LP:
$$
\max c^\top x, Ax \le b, x \ge 0
$$
If we have an optimal solution $x^\ast$ with $a_ix^\ast  \lt b$ for some $i$. By complementarity conditions, we have $y_i = 0$.

According to the above results, we can then conclude that if we change $b$ slightly, the optimal value will not be affected.

#### Shadow Prices

In the case of $\nabla V(b) = y ^\ast$ where $y^*$ is optimal, we call $y^\ast$ the shadow prices of $b$, because it indicates the potential increment of profit if there is one unit more of that resource.

It can also be viewed as the unit value or the unit fair price for the resource.

### Global Sensitivity

Up to now, we have only discussed about the potential change corresponding to the change of $c$ or $b$. However, as we have stated before, this potential only works when the change of input is relatively small. How small is small enough? That's the question we want to talk about in Global Sensitivity. 

Generally speaking, the change is small enough as long as it does not affect the optimal basis. However, to answer the question more precisely, we need to refer to the simplex tableau.
$$
\left[\begin{array}{c|c}
c^\top - c_B^\top A_B^{-1}A & -c_B^\top A_B^{-1}b \\
\hline
A_B^{-1}A & A_B^{-1}b
\end{array}\right]
$$


Notice that at the optimal point, we must have
$$
\begin{aligned}
c^\top - c_B^\top A_B^{-1}A &\ge 0 \\
A_B^{-1}b &\ge 0
\end{aligned}
$$
Now we talk about how much we can change $b$ and $c$ without break local sensitivity.

#### Changing $b$

Since $c^\top - c_B^\top A_B^{-1}A$ has nothing to do with $b$, we only need to check the solution part.

When changing $b$ to $b + \Delta b$, the basic part of the solution changes to
$$
\tilde{x}_B = A^{-1}_B(b + \Delta b) = x^\ast + A^{-1}_B\Delta b
$$
As long as we have $\tilde{x}_B \ge 0$, the local sensitivity will hold. What's more, the new optimal solution will be
$$
V(\tilde b) = V^\ast + c_B^\top A_B^{-1}\Delta b = V^\ast + (y ^\ast)^\top\Delta b
$$
How about the change to a single variable ($\Delta b_i$) ? Let $e_i$ be the vector with a single $1$ at position $i$, the above condition shows that
$$
x^\ast + \Delta b_i A_B^{-1}e_i \ge 0
$$
By solving this inequality, we can find the range of the allowed change on the $i$-th variable.

#### Changing $c$

Changing $c$, nevertheless, is a little bit complicated as it involves a more complex part of our tableau.

Again, we suppose $c$ is changed to
$$
\tilde{c} = c + \Delta c
$$
This time we need to be care of the constraint:
$$
c^\top - c_B^\top A_B^{-1}A \ge 0
$$
The basic part is always zero by definition, so what we truly need to check is:
$$
\tilde{c}^\top_N - \tilde{c}_B^\top A_B^{-1}A_N \ge 0
$$
We narrow down our focus onto the changes on a single variable (say $j$-th variable).

##### Case 1: $j \in B$

In this case, we have:
$$
\begin{aligned}
&\tilde{c}^\top_N - \tilde{c}_B^\top A_B^{-1}A_N \\
=~&  c^\top_N - (c_B^\top + \Delta c_je_j^\top) A_B^{-1}A_N
\\ \ge ~& 0
\end{aligned}
$$
Let $r$ be the vector of original reduce costs, then the above constraints can be written as
$$
r_N^\top -  \Delta c_je_j^\top A_B^{-1}A_N \ge 0
$$

##### Case 2: $j \in N$

With a similar approach, we get:
$$
\begin{aligned}
&\tilde{c}^\top_N - \tilde{c}_B^\top A_B^{-1}A_N \\
=~&  c^\top_N + \Delta b_je^\top - c_B^\top  A_B^{-1}A_N
\\ \ge ~& 0
\end{aligned}
$$
And again, this is equivalent to
$$
r_N + \Delta b_je_j \ge 0
$$

#### Out of the Range

- If changing $c$ results in negative components, we can then continue the simplex method.
- If changing $b$ causes in-feasibility:
  - One can restart the simplex method
  - Try to continue the dual problem

#### Changing $A$

- All changes happen on non-basic part: One can recalculate the reduced costs and everything is similar to changing $c$.
- Changes involve basic part: no simple way to deal with it in general

#### Adding a Variable

- BFS is still basic and feasible.
- Only need to check the newly imported reduced costs and decide whether we need to continue the simplex method

#### Add a Constraint

- If original BFS is still feasible then nothing need to be done.
- Otherwise, it is similar to changing $b$.

## References

- Lecture Notes 9-10 for MAT3007 (Optimization) by Dr. Andre Milzarek. Institute for Data and Decision Analytics, The Chinese University of Hong Kong, Shenzhen.
- Introduction to Linear Optimization, by D. Bertsimas and J. Tsitsiklis

