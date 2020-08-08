Recall that the standard of an LP is like the following:
$$
\begin{aligned}
			 \min &~~~ c^Tx
\\
\text{subject to} &~~~ Ax = b 
\\
                  &~~~ x \ge 0
\end{aligned}
$$
where $x \in \mathbb R^n, b \in \mathbb R^m, A \in \mathbb R^{m\times n}$

In the following discussions, we always assume that $A$ has full rank $m$ by default. 

## Basic Solutions for LP

We call $x$ a basic solution of an LP is and only if:

- $Ax = b$
- $\exists B, s.t., \{A_i, i \in B\}$ forms a basis of $\mathbb R^m$ where $x_i = 0, \forall i \notin B$

To find a basic solution, the most trivial way is to re-write $A$ and solve the equation directly:
$$
[A_B ~ A_N] \begin{bmatrix} x_B \\  \mathbf 0 \end{bmatrix} = b
$$
Hence, a trivial form of the basic solution should be:
$$
x_B = A_B^{-1}b
$$


### Notions

We call $B= \{B(1),...,B(m)\}$the **basic indices** for this basics olution, $A_{B(1)},...,A_{B(m)}$the **basic columns**, $A_B$ the basis matrix and $x_{B(1)},...,x_{B(m)}$ the basic variables. 

We call the remaining indices the **non-basic indices**, the remaining columns of A the **non-basic columns** and the remaining variables the **non-basic variables**.

### Remark

The number of basic solutions for an LP with size $m \times n$ is upper bounded by
$$
C(n, m) = \frac{n!}{m!(n -m)!}
$$
which is a finite number.

### Basic Feasible Solutions for LP

A basic solution that also satisfies $x \ge 0$ is a **basic feasible solution** (**BFS**).

For linear programming, BFS has some very nice properties.

#### Theorem (Extreme Point and BFS)

For the standard LP polyhedron $P:=\{x\in \mathbb R^n:Ax=b,x\ge0\}$,the following statements are equivalent:

1. $x$ is an extreme point of $P$.
2. $x$ is a basic feasible solution.

This can be proved using contradictions.

#### Fundamental  LP  Theorem

Consider a linear problem in standard form and assume that $A$ has full row rank $m$.

1. If the feasible set is nonempty, there is a basic feasible solution.
2. If there is an optimal solution, there is an optimal solution that is also a basic feasible solution.

#### Corollary: Characteristics

If an LP with $m$ constraints (in the standard form) has an optimal solution, then there must be an optimal solution with no more than $m$ positive entries.

A direct result of the above properties is that we only need to look at all the basic feasible solutions to find the optimal solution.

However, iterating all BFS still has an exponential time complexity and we must figure out a smart way to find the optimal BFS efficiently.

A good way to do so is the **Simplex Method**.

## Simplex Method

Simplex method, as its name suggests, adapts a geometric approach to find the optimal solution among the BFS. 

Two basic solutions are **neighboring (or adjacent)** if they differ by exactly one basic (or non-basic) index.

Simplex method starts with a given BFS and then examine its neighboring BFS. If there is a better choice, then it will change current solution to the new one and begin the next iteration. The whole procedure ends if no neighboring BFS are providing a better objective value.

In the geometric point of value, we start from an extreme point of the polyhedron and check all the adjacent extreme points. If a better one presents, then our action is just to move our position along the boundary hyperplane to the new extreme point.

### Find a Neighboring BFS

Now, we show an efficient way to find a better neighboring BFS.

Let us suppose we have a better direction to move, then in the moving procedure, we are alternating the chosen basis within our BFS. Suppose the current BFS is $x$, we want to express the alternating procedure in the following form:
$$
x + \theta d, \theta \ge 0
$$
Here, vector $d$ describes the direction we want move. Suppose $j \in N$ is the incoming basis, then we have:

-  $d_j = 1$
- $d_k = 0, \forall k \in N, k \ne j$

What about the entries in $d_B$? Since we want $x + \theta d$ to be a BFS, we have:
$$
A(x + \theta d) = b = Ax
$$
which yields that
$$
Ad = 0
$$
Again, we apply re-ordering tricks here:
$$
\begin{bmatrix}
A_B & A_N
\end{bmatrix}\begin{bmatrix}
d_B \\  d_N
\end{bmatrix} = 0
$$
Hence, 
$$
A_B d_B + A_Nd_N = A_Bd_B + A_jd_j = 0
$$
So, if $j$ is given, then the $d_B$ part is already decided, which is:
$$
d_B = -A_B^{-1}A_j
$$
As for the whole vector $d$:
$$
d = \begin{bmatrix}
-A_B^{-1}A_j \\  0 \\  ... \\ 1 \\  ... \\  0
\end{bmatrix}
$$
where the $1$ appears at the $j$-th entry.

Once the direction is determined, we need to check how much can we move along the direction, which is characterized by $\theta$.

The first capture for us is that if $d \ge 0$, then $x_N + \theta d_N$ is always feasible for any $\theta \ge 0$. which means that we can move along the direction no matter how much we want and it will always result in a better solution. In this case, the problem must be **unbounded**.

Otherwise, for the rows in $N$, the feasibility is always kept, since:
$$
x_N = \mathbf 0 \Rightarrow x_N + \theta d_N \ge 0, \forall \theta \ge 0
$$
However, we must take a careful look at the $x_B + \theta d_B$ part. In general, we just want to find a $\theta$ that:
$$
\theta = \max \{\alpha : x_B + \alpha d_B \ge 0\}
$$
Hence, we can check all the possible value entry by entry. Since $x$ is a BFS, we have $x_i \ge 0, \forall i$. So the $\theta$ is chosen as:
$$
\theta = \min_{i \in B, d_i \lt 0} \{ -\frac{x_i}{d_i}\} 
$$
There are some annoying cases that $x_i$ may be $0$ under some special situations. In such cases, we say $x$ is a degenerate BFS, because that zero entry in $x_B$ disables any possible improvement no matter which direction we are moving toward. Nevertheless, even when we encounters degeneracy, we can still do the zero-magnitude movement and changing the basis to a new set. If we are lucky, then perhaps in the new site, we will either find no better direction or get no negative $d_i$ coincides with a negative entry in $x_B$, which saves us from the degenerate situation. Later we will see, by following some rules, we can always have the good luck. Geometrically, a degenerate solution occurs when more than $m$ hyperplanes intersect at the same point.

Moreover, the following property makes sure that our destination is still basic and feasible, which enables us to start the iteration over and over again to approach the optimal value:

##### Theorem: Properties of $y$

Let $x$ be a BFS with basic indices $B$ and let $y=x+\theta d$ be generated by one simplex iteration. Then,$y$ is a basic feasible solution associated with the basic indices $B' = \{B(1),...,B(l-1),j,B(l+1),...,B(m)\}$.

The feasibility is obvious. We only need to show that $y$ is basic.

We only consider $\theta > 0$, otherwise $y = x$ is of course a BFS. 

Assuming that $A_{B'}$ is dependent, then we can find a set of $\alpha_i$ s.t.
$$
\sum_{i = 1}^m \alpha_iA_{B'(i)} = 0
$$
Hence, 
$$
\sum_{i = 1}^m \alpha_i A_B^{-1}A_{B'(i)} = 0
$$
Notice that $A_B$ differs $A_{B'}$ only from one row $B(l)$.

Hence we have $A_{B}^{-1}A_{B'(i)} = e_i, \forall i \ne l$, while $A_{B}^{-1}A_{B'(l)} = -d_B$

Notice that apart from $-d_B$ column, other $e_i$ are already independent and they all have $0$ entry on the $l$-th row.

Since we are only considering the case of $\theta > 0$, $x_l-\theta d_l = 0$ ($l$ is the outgoing basis) which means,
$$
A^{-1}_BA_{B'}
$$
is independent.

Hence, $A_B'$ cannot be dependent.

Up to now, we have solved the problem of how to move; yet as a happy traveler among the hyperplanes, we have to decide where to move and how to do with the cost (objective function value). 

We want to make sure that whenever we move, the cost will decrease (non-strictly for degenerate cases).

 The cost function for the new BFS is:
$$
c^T(x + \theta d) = c^Tx + \theta c^Td
$$
Given that $\theta \ge 0$, those directions $d$s worth a trial are the $d$s making $c^Td \le 0$. We again separate the basis and non-basis entries in $c^T$, 
$$
c^Td = [c^T_B ~~c^T_N] \begin{bmatrix}
d_B \\  d_N
\end{bmatrix} = [c^T_B ~~c^T_N]\begin{bmatrix}
-A_B^{-1}A_j \\  d_N
\end{bmatrix} = -c^T_BA_B^{-1}A_j + \sum_{i\in N}d_ic_i = -c^T_BA_B^{-1}A_j + c_j
$$
The problem is now solved and we only need to calculate the above formula for each $j \in N$ and pick out those who acquires a negative value. Then, on each move, the cost is reduced by $\theta(-c^T_BA_B^{-1}A_j + c_j)$.

When there is no negative reduced cost, we can just stop our travel. The following theorem claims that the stop site provides an optimal solution:

##### Theorem: Stopping Criterion

Consider a basic feasible solution $x$ associated with the basis $B(1),...,B(m)$ and let  $\overline c$ be the corresponding vector of reduced costs. If  $\overline c \ge 0$, then $x$ must be optimal.

In fact, for any other solution $y$:

Define $d = y -x$.

We have $Ad = Ay - Ax = b - b = 0$

By re-writing:
$$
0 = \begin{bmatrix}A_B & A_N\end{bmatrix}
\begin{bmatrix}d_B \\  d_N\end{bmatrix} = A_Bd_B + A_Nd_N
$$
Hence,
$$
d_B = -A_B^{-1}A_Nd_N = -A_B^{-1}\sum_N A_id_i
$$


Then the cost，
$$
c^Td = \begin{bmatrix}c^T_B & c^T_N\end{bmatrix}
\begin{bmatrix}d_B \\ d_N\end{bmatrix} 
= c^T_Bd_B + \sum_Nc_id_i
=\sum_N(c_i - c_B^TA_B^{-1}A_i)d_i 
 = \sum_{N} \overline {c_i}d_i
$$


Since $\overline c \gt 0$, and $\exists i \in N, d_i \gt 0$, we can see that
$$
c^Td \gt 0
$$

Now, there is only one remaining problem, how to acquire necessary luck during our journey. In fact, when traveling among extreme points, we need to make choices from time to time. There are two things we need to decide about:

- which basis is incoming?
- which basis is outgoing?

These choices occur because sometimes we make get multiple negative reduced cost or same $\theta$ value with different choices of basis.

For the incoming basis, the following are some common strategies to deal with the problem:

1. Smallest Index Rule: Choose the smallest index $j$ with $\overline <0$
2. Most Negative Rule: Choose the smallest  $\overline c_j$.
3. Most Decrement Rule: Choose $j$ with the smallest $\theta \overline c_j$.

In practice, we have the the following theorem to kill our hesitation and bring us the luck we need at the same time:

##### Theorem: Bland’s Rule

If we use the smallest index rule for choosing both the entering basis and the exiting basis, then no cycle will occur in the simplex algorithm.

## Two-Phase Method

The above discuss is under the the assumption that we are given a BFS at the beginning. In real-world, we must find a BFS before apply the simplex method. However, finding a BFS by iterating all possible combination is not doable in practice.

Consider a special form of LP where the constraints part is:
$$
Ax \le b, A \in \mathbb R^{m \times n}, b \ge 0
$$
Under this situation, we need to first import $m$ slack variables and transform it into an equation:
$$
[A ~~I_m] \begin{bmatrix}x \\ S_m\end{bmatrix} = b
$$
Since we add $I_m$ to the original matrix, we get $m$ independent columns for free. 

Then a trivial BFS is
$$
x = 0, S_m = b
$$
Inspired by this example, we use Two-Phase Method to solve a general LP.

First, we change the standard form into a new problem:
$$
\min 1^Ty, [A ~~ I]\begin{bmatrix}x \\ y\end{bmatrix} = b, x \ge 0, y \ge 0
$$
This LP problem has a trivial BFS $x = 0, y = b$ and can be solved directly using simplex method.

1. If the optimal value is not 0, then we can claim that the original problem is infeasible.
2. If the optimal value is 0 with solution $(x', 0)$,then $x'$ must be a BFS of the auxiliary problem. But then it must be a BFS for the original problem as well. We can start from this point to initialize the simplex method. If $x$ is degenerate( has less than $m$ positive entries) and still contains basic indices in the auxiliary variables, then one can pick any other columns (such that they form an independent matrix) in the non-basic part in $x^*$ to make it a BFS for the original problem.

There is another method that can be used to solve LPs without a starting BFS. Consider the following auxiliary problem:
$$
\min c^Tx + M1^Ty, Ax + y = b ,x, y \ge 0
$$

We can also use simplex method to solve it with a trivial initial BFS. If $M$ is large enough, we can image that $y$ will approach to $0$. However, the problem kind of depends on a wise choice of the constant value, which makes it less friendlier in practice.

## Simplex Tableau

Consider the following tableau with a initial BFS:
$$
\left[\begin{array}{c|c}
c^T - c_B^TA^{-1}_BA & -c_B^TA^{-1}_Bb \\ 
\hline 
A^{-1}_BA & A^{-1}_Bb
\end{array}
\right]
$$

- On upper left corner is the reduced cost vector 
- On the upper right corner is the negation of the current objective function value
- On the lower right corner is the current BFS
- Split the basis and non-basis part, the upper right must contains a zero subvector and the lower-left must contains an identity.

This form of an LP is called the canonical form: The constraint matrix for the basic variables (not necessarily the first $m$ columns) is an identity matrix. The objective function part for the basic variables is zero.

Following Bland's rules, if there are negative entries in the upper left part, then we can choose the column with the smallest index as the incoming basis.

How to decide the outgoing column? we use the **Minimal Ratio Test**:
$$
\theta = \min \{\frac{\overline{b_i}}{\overline{A_{ij}}} : \overline {A_{ij}} \ge 0\}
$$
where $\overline{b_i}$ is the $i$-th entry of the lower-right part; $\overline{A_{ij}}$ is the entry on the $i$-th row $j$-th column of the lower left part.

The selected row is the **pivot row** and the selected column is the **pivot column**.  

Their intersection is the **pivot element**.

Once can solve the LP by performing the following two steps repeatedly until $\overline c \ge 0$:

- Divide each element in the pivot row by the pivot element.
- Add proper multiples (could be negative) of the pivot row(after the first step) to each other rows, including the top row of objective coefficients, such that all other elements in thepivot column become zeros (including the top row).

Notice that both operations include the right-hand-side column of $\overline b$. At any step, if there is a all negative column in the left part of the whole matrix, then the problem is unbounded.

## References

- Lecture Notes 4-6 for MAT3007 (Optimization) by Dr. Andre Milzarek. Institute for Data and Decision Analytics, The Chinese University of Hong Kong, Shenzhen.
- Introduction to Linear Optimization, by D. Bertsimas and J. Tsitsiklis