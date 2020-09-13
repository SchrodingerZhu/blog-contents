## Two-Phase Method with Simplex Tableau

TL;DR, two-phase method involves two LP and can be solved by construct two simplex tableau.

However, there two things to consider:

- How to acquire the initial tableau in Phase I.
- How to make the transition from Phase I to Phase II.

### Initial Tableau

Unfortunately, the initial form of the auxiliary problem is not in the canonical form. This means we must calculate the reduced costs on our own. Luckily, this calculation is not very heavy:

- for the basic part, the reduced costs are zero.

- for the nonbasic part, we have:
$$
  \overline c_j = c_j - c_B^\top A_B^{-1}A_j = -\mathbf 1^\top A_j
$$
  then the reduced cost of each column the just the negation of the sum of the coefficients (which are the elements in the corresponding column of the lower-left part).

With this initial tableau, we have continue our simplex method until we finish the problem in Phase II.

### Transition between Two Phases

When Phase I is solved and the conditions allow us to continue with Phase II, we can do the following things to have a smooth transition between Two Phases:

- Drop all the columns associated with the auxiliary variables.

- Change the top row to the reduced costs corresponding to theoriginal problem by using the reduced cost formula and compute the current (negative) objective value:
$$
  \overline c_j = c_j - c_B^\top A_B^{-1}A_j
$$
This time the reduced cost is calculated in the hard way but as $A_B$ be in a identity form, the calculation is not that painful.

After the transition, we can just use simplex method to finish the LP.

## Complexity Analysis

The time complexity of simplex method turns to be related to the selecting rule. However,  none of the investigated rules will result in a polynomial-time algorithm. The lower bound of the problem is still open.

For all existing rules, the simplex method all runs into an exponential trap with the Klee–Minty cube:

$$
\begin{aligned}
x_1 &\le 5 \\
4x_1 + x_2 &\le 25 \\
8x_1 + 4x_2 + x_3 &\le 125 \\
... \\
2^Dx_1 + 2^{D - 1}x_2 + ... + 4x_{D - 1} + x_D &\le 5^D \\
\mathbf x &\ge 0
\end{aligned}
$$

This describes a hypercube with $2^D$ vertices. It is proofed that if our objective function is

$$
\sum_{i = 1}^D 2^{D - i}x_i
$$

then a simplex method starting from the origin need to run for $2^D$ steps before reaching the final optimal vertex $\begin{bmatrix} 0 \\ 0 \\...\\ 5^D \end{bmatrix}$.

This sounds discouraging but one can actually proof that the average complexity of the simplex method is upper bounded by a polynomial. Other positive results include:

- from Barie category theory point of view, most" matrices can be solved by the simplex algorithm in a polynomial number of steps
- using the smoothed analysis,  the running time of the simplex method on input with noise is  polynomial in the number of variables and the magnitude of the  perturbations.

So, what is the complexity of general LP problems? Soviet Union mathematician Khachiyan published the first polynomial-time algorithm **ellipsoid method** in 1979, which shows that LP is within the $P$ category. However, in practice, this method is suffers from numerical issues and it runs extremely slow. 

There is also another method, the interior point method which performs well both theoretically and practically. To learn about it, we need to first grab some basic knowledge of the **Duality Theory**.

## Duality Theory

Starting from the standard form of LP problems ($A \in \mathbb R^{m\times n}$):

$$
\min_x c^Tx, Ax = b, x \ge 0
$$

This problem can be translated into:

$$
\begin{aligned}
\min_{x\ge 0} \left[c^T x + \max_{y\in\mathbb R^m} y^\top(b - Ax) \right]
\end{aligned}
$$

Notice that if the original problem is feasible this new form guarantee that $Ax = b$:

Otherwise, let $\delta = b - Ax$ such that there exists $\delta_i \ne 0$. We can always choose:

$$
y = k \begin{bmatrix}0 \\ 0 \\ ... \\ b_i \\ ... \end{bmatrix}
$$

such that:

$$
y^\top (b - Ax) = k\delta^2_i
$$

which means:

$$
\max y^\top(b - Ax) \to \infty
$$

It turns out that the minimization part and the maximization part can be exchanged:

$$
\begin{aligned}
&\min_{x\ge 0} \max_{y\in\mathbb R^m} \left[c^\top x +  y^\top(b - Ax) \right]\\
\Rightarrow &  \max_{y\in\mathbb R^m} \min_{x\ge 0}  \left[c^\top x +  y^\top(b - Ax) \right]\\ \\
\Rightarrow &  \max_y \left[b^\top y + \min_{x\ge 0} x^\top (c - A^\top y) \right] (\star)
\end{aligned}
$$

The proof of the equivalence will be provided  later in strong duality theory. There are also some direct results on these kinds of problems (which can be proved using our later method):

##### Concrete von Neumann Minimax Theorem

Let $A$ be a linear mapping between between Euclidean spaces $E$ and $F$. Let $C \subset E$ and $D \subset F$ be nonempty compact and convex sets. Then:

$$
\max_{y\in D} \min_{x \in C} \langle Ax, y\rangle = \min_{x \in C} \max_{y \in D} \langle Ax, y \rangle 
$$

##### Von Neumann-Fan Minimax Theorem

Let $X$ and $Y$ be Banach spaces. Let $C \subset X$ be nonempty and convex, and let $D \subset Y$ be nonempty, weakly compact and convex. Let $g : X \times Y \to \mathbb R$ be convex with respect to $x \in C$ and concave and upper-semicontinuous with respect $y \in D$, and weakly continuous in $y$ when restricted to $D$. Then

$$
\max_{ y \in D} \inf_{x \in C} g(x, y) = \inf_{x \in C} \max_{y \in D} g(x, y)
$$

For now, let us just get back to the formula marked by $(\star)$, it should not escape our observation that this formula is actually equivalent to the following form:

$$
\max_y b^\top y, A^\top y \le c ~~~~(\alpha)
$$

Why can we drop the minimum part?

- If $A^\top y \le c$, then we have $c - A^\top y \ge 0$, hence the value of minimum part is $0$ with $x = \mathbf 0$.
- Otherwise, by a similar trick we used above, one can show that we can easily construct a $x$ such that the minimum value can be scaled up to $-\infty$

We now get the dual problem $(\alpha)$. By similar approaches, one can show that we have the following corresponding relations between primal and dual LP problems in general:

$$
\begin{array}{c|c}
\text{primal} & \text{dual} \\
\hline 
\min c^\top x & \max b^\top y \\
a_i^\top x \ge b_i, \forall i \in M_1 & y_i \ge 0, \forall i \in M_1 \\
a_i^\top x \le b_i, \forall i \in M_2 & y_i \le 0, \forall i \in M_2 \\
a_i^\top x = b_i, \forall i \in M_3 & y_i \textbf{ free}, \forall i \in M_3 \\
x_j \ge 0, \forall j \in N_1 & A_j^\top y \le c_j, \forall j \in N_1 \\
x_j \le 0, \forall j \in N_2 & A_j^\top y \ge c_j, \forall j \in N_1 \\
x_j \textbf{ free}, \forall j \in N_3 & A_j^\top y = c_j, \forall j \in N_1 \\
\end{array}
$$

- $a_i^\top$ is the $i$-th row of $A$, $A_j$is the $j$-th column of $A$.
- Each primal constraint corresponds to a dual variable
- Each primal variable corresponds to a dual constraint
- Equality constraints always correspond to free variables

With this table, one can calculate the dual problem rapidly. Notice that if the primal problem is a maximization problem, then we need to use the table in a reversed way and translate each variable from right to left.

#### Example: SVM

Let us have a look of how can we transform a SVM problem into its dual problem. Recall the primal problem is:

$$
\begin{aligned}
\min_{w, b, t} &~~ \sum_{i = 1}^m t_i \\ &~~ t_i + y_i(x_i^Tw + b) \ge 1 ~~~~(\beta)
\\ &~~ t_i \ge 0
\end{aligned}
$$

We can reformulate $(\beta)$ into a compact form:

$$
\begin{bmatrix} \text{diag}(y)X^\top & y & \mathbf 1 \end{bmatrix} 
\begin{bmatrix}
w \\ b \\ t
\end{bmatrix}
\ge \mathbf 1
$$

So in the dual problem, the constraints matrix is:

$$
\begin{bmatrix} 
	\text{diag}(y)X^\top 
	\\ y^\top 
	\\ \mathbf 1 
\end{bmatrix} u =
\begin{bmatrix} 
	\text{diag}(y)X^\top u
	\\ y^\top u
	\\ u
\end{bmatrix}
\begin{matrix} 
	 \Rightarrow = 0 ~ (w \text{ free}) \\
	 \Rightarrow = 0 ~ (b \text{ free}) \\
	 \Rightarrow \le \mathbf 1 ~ (t \ge 0) \\
\end{matrix}
$$

So the dual problem is:

$$
\begin{aligned}
\max_u &~~ \mathbf 1^\top u \\
       &~~ X\text{diag}(y)u = 0 \\
       &~~ y^\top u = 0 \\
       &~~ u \ge 0, u \le \mathbf 1
\end{aligned}
$$

## Invariance of Transformations

The reason why we care about duality theory is that the dual problem and the primal problem are closely related. In fact, the following two theorems describe the equivalence between the primal and the dual:

#### Theorem: Duality under Transformations

If we transform a linear program to an equivalent one (e.g., by replacing free variables, adding slack variables, etc.), then the dual of the two problems will be equivalent.

#### Theorem: Dual of the Dual

If we transform the primal to its dual and transform the resulting dual to its dual, then we will obtain a problem equivalent to the primal problem, i.e., the dual of dual is the primal.

## Duality Theorems

Now we start to proof what we have promised up to now (the equivalence of the problems). First, let us take  a look at the following statement:

#### Weak Duality Theorem

If $x$ is feasible for the primal problem and $y$ is feasible for the dual, then we have:

$$
b^\top y \le c^\top x
$$

If this holds, we directly have the following result:

- Primal problem is lower bounded by the dual problem
- Dual problem is upper bounded by the primal problem
- The optimal value of the primal is larger than that of the dual.
- Unboundedness on one side implies in-feasibility on the other side.

**Weak Duality Theorem** can be proved in a few lines:

Let $x, y$ be feasible points of the primal and dual problem, then

$$
b^\top y  = (Ax)^\top y = x^\top(A^\top y) \le c^\top x
$$

Q.E.D.

Now we have some inspiring relations among the feasible points of the primal and dual problems. How about the optimal value?

From **Weak Duality Theorem**, If $x$ is feasible for the primal problem and $y$ is feasible for the dual such that 

$$
c^\top x = b^\top y
$$

then we can conclude that $x, y$ must give the optimal value of both problems. So the **Optimality conditions for LP** is:

- $x$ is primal feasible
- $y$ is dual feasible
- $c^\top x = b^\top y$ 

Then, it is naturally to ask: starting from a pair of optimal solution of the primal and the dual, do we always have the same optimal value? This leads us to the following theorem

#### Strong Duality Theorem

If a linear program has an optimal solution, so does its dual and the optimal values of the primal and dual problems are equal.

Before the proof, we first look at a special properties of the simplex method. The dual optimal solution actually comes as a by-product when we use the simplex algorithm. The term:

$$
c^\top_BA^{-1}_B
$$

will be the dual optimal solution.

This gives us the idea that simplex method is actually solving the primal and dual problem as the same time.

Now let us get back to the proof.

Without loss of generality, we set our LP problem to the standard form.

Assume that we have solved the primal problem with optimal solution $x'$ with basis $B$, such that

$$
x_B' = A_B^{-1}b, x_N' = x_{B^C}' = 0
$$

As for the reduced cost, we have:

$$
c^\top - c_B^\top A_B^{-1}A \ge 0
$$

We can define $y'^\top = c_B^\top A_B^{-1} \in \mathbb R^{1 \times m}$. Then,

$$
A^\top y' = A^\top(A^{-1})^\top c_B \le c
$$

which means $y'$ is a feasible for the dual problem.

The objective value of the dual:

$$
b^\top y' = b^\top(A^{-1})^\top c_B = x'^\top c_B = c_B^\top x'
$$

By weak duality,

$$
c_B^\top x' \ge b^\top y, \forall y 
$$

Then, $b^\top y'$ must be the optimal value and it is the same as the optimal value of the primal problem.

##### Possible Solution State

Combine what we have discussed until now, we can list all possible cases of the solution state between the primal and the dual LP problems:

$$
\begin{array}{c|ccc}
P / D & \text{finite optimal} & \text{unbounded} & \text{infeasible} \\
\hline
\text{finite optimal} & \checkmark & \times & \times  \\
\text{unbounded}& \times  & \times & \checkmark   \\
\text{infeasible}& \times & \checkmark & \checkmark  
\end{array}
$$

## Complementarity Conditions

Up to now, we should have a clear understanding of the relations between the primal problem and the dual problem. However, there is another important thing left to do: If we have already got an optimal solution of one side, can we figure out a corresponding solution in the other side? This can be done with the power of **Complementarity Conditions**.

Let us start with the standard form of LP problems.
$$
\begin{array}{c|c}
\text{Primal} & \text{Dual} \\
\hline
\min c^\top x & \max b^\top y \\
Ax = b, x \ge 0 & A^\top y \le c
\end{array}
$$
In this case, the **Complementarity Conditions** gives us:

Let $x$ and $y$ be feasible points of the primal and dual problem,respectively. Then, $x$ and $y$ are optimal solutions if and only if:
$$
\begin{aligned}
x_i \gt 0 &\Longrightarrow A^\top_i y = c_i \\
A_i^\top y \lt c_i &\Longrightarrow x_i = 0
\end{aligned}
$$
Or in other words,
$$
x_i (c_i - A_i^\top y) = 0, \forall i
$$
The proof is not complicated:

Let $x$, $y$ be feasible optimal solutions of the primal and the dual problems. By **Strong Duality**, 
$$
0 = c^\top x - b^\top y = c^\top x - (A^\top y)^\top x = c^\top x - y^\top Ax
$$
Expanding the last part, we directly get:
$$
0 = c^\top x - y^\top Ax = \sum_{i = 1}^nx_i(c_i - A_i^\top y)
$$
Since $x \ge 0$ and $A^\top y \le c$, 
$$
x_i(c_i - A_i^\top y) \ge 0, \forall i
$$
This yields that:
$$
x_i(c_i - A_i^\top y) = 0 , \forall i
$$
If we change the dual problem by adding slack variables:
$$
\max b^\top y , A_i^\top y + s = c, s \ge 0
$$
then we have
$$
x_is_i = 0, \forall i
$$
this is obvious as $s_i = c_i - A_i^\top y, \forall i$

In general, we have the following theorem stating the relation between the variables of the optimal solutions:

#### Theorem: Complementarity Conditions

Let $x$ and $y$ be feasible points of the primal and dual problem. Then $x$ and $y$ are optimal if and only if:
$$
\begin{cases}
y_i(a_i^\top x - b_i) = 0, & \forall i \\
x_j(A_j^\top y - c_j) = 0, & \forall j
\end{cases}
$$
This also gives us another proof of $y = (A_B^{-1})^\top c_B$ under the standard form with $x_i \gt 0, \forall i \in B$, because in this case, we always have
$$
A_i^\top y = c_i, \forall i \in B
$$

## Dual and Simplex Tableau

If we start our tableau with a canonical form:
$$
\left[\begin{array}{cc|c}
c^\top & 0_m & 0 \\ \hline
A & \mathbf 1_m & b
\end{array}\right]
$$
Then when we reach an optimal solution, the corresponding tableau is
$$
\left[\begin{array}{cc|c}
c^\top - c^\top_BA^{-1}_BA & \mathbf{-c_B^\top A^{-1}_B} & -c_B^\top A^{-1}_Bb \\ \hline
A_B^{-1}A &  A_B^{-1} & A_B^{-1}b
\end{array}\right]
$$
It does not escape our eye that the right half of the top left cell is just the negation of our dual solution.

If our tableau is not initiated in the standard form, we shall calculate the dual solution on our own by
$$
y = (A^{-1}_B)^\top c_B 
$$

## References

- Lecture Notes 7-8 for MAT3007 (Optimization) by Dr. Andre Milzarek.  Institute for Data and Decision Analytics, The Chinese University of  Hong Kong, Shenzhen.
- Introduction to Linear Optimization, by D. Bertsimas and J. Tsitsiklis