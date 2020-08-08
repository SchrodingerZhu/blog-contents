From this note, we will start talk about non-linear programs. To get a first glance of NLPs, let us first check out how we can issue the **Interior Point Method**.

## Interior Point Method

Interior Point Method takes care of the primal and the dual problem at the same time. Recall that we have the following constraints for LPs:

- Primal Feasibility: 

$$
  Ax = b, x \ge 0
$$

- Dual Feasibility:

$$
  A^\top y \le c
$$

- Complementarity:
$$
  x_is_i = x_i(x_i-A^\top y) = 0
$$

In the simplex method, we search along basic feasible solutions; which means we always maintain the primal feasibility. Since we are checking among the combinations of basis, at each iteration, we can simply define:

$$
y = (A_B^{-1})^\top c_N
$$

such that the reduced costs are represented by:

$$
c^\top - y^\top A
$$

Therefore, for each non-basic variable, the corresponding $x_i$ is zero; while for basic variable, the reduced costs are zero; this naturally yields the Complementarity Conditions.

While Complementarity Conditions hold during the whole process, there are cases when the dual feasibility is violated. However, notice that when we finally reach a stop condition, we will have the reduced costs greater or equal than zero:

$$
c^\top - y^\top A \ge 0
$$

which means the dual feasibility is then achieved.

### Proposition: Optimality Properties of the Simplex Method

During each iteration, the simplex method maintains primal feasibility and the complementarity conditions. It seeks a solution that is dual feasible.

It then strikes on us that what if we maintain the other two conditions during the process? In fact, this will result in the dual-simplex method and the interior point method.

### Dual-Simplex Method

- It maintains dual feasibility.
- It maintains the complementarity conditions.
- However, primal feasibility does not need to be satisfied during the iterations. It seeks for primal feasible BFS.
- The tableau can be seen as a rotated variant of the primal one.

There are cases where using the dual simplex method can be more convenient:

- If a dual BFS is available (but we don’t have a primal BFS).
- We have mentioned a scenario in the discussion of the sensitivity analysis (when $b$ is changed by a large amount or a constraint is added)

### The Interior Point Method

The interior point method maintains both primal feasibility and dual feasibility during its iterations and seeks for a pair of solutions that satisfy the complementarity conditions.

Looking at all constraints, a optimal pair satisfies:

- $Ax = 0, x \ge 0$,
- $A ^\top y + s = c, s \ge 0$
- $x_i \cdot s_i = 0, \forall i$

There is a set of nonlinear equations here, which makes it to solve. As we have mentioned, IPM do not maintain complementarity conditions during the process, this means we can relax the problem to

Looking at all constraints, a optimal pair satisfies:

- $Ax = 0, x \ge 0$,
- $A ^\top y + s = c, s \ge 0$
- $x_i \cdot s_i \le \mu, \forall i$

We can $\mu \gt 0$ the complementarity gap.

So the IPM goes like the following: If we have found a solution for a certain $\mu$, then it might be possible to find a solution for a smaller $\mu$. Then we keep decreasing $\mu$ until we can find a solution of the LP. The essential step in the interior point method is to show that this is indeed doable – the approach is similar to Newton’s method.

Again, we need to resolve the issue of finding an initial basic solution in the interior point method. This can be resolved by solving an auxiliary problem (called the homogeneous self-dual problem).

Overall, the complexity of IPM is $O(n^{3.5})$

### Properties

#### Theorem: Quality of Solutions

The interior point method will always find the optimal solution with the maximum possible number of non-zeros.

- If we want a high-rank solution (with the maximum possible non-zeros), then choose the interior point method.
- If we want a low-rank solution (with small number of non-zeros), then choose the simplex method.
- The optimal solution output of the simplex method may depend on the initial solution (as well as on the pivoting rules)

## Non-Linear Programming

### Mathematical Preparations 

In [Optimization Learning Notes 1](https://zhuyi.fan/post/optimization-learning-notes-1.html), we have talked about the definition of optimizers (local/global minimizer/maximizer). So in non-linear programming, we are still going discuss about how we can find these optimizers for our problems.

In order to do so, we need to first introduce some math tools.

- **Gradient and First-Order Taylor Expansion:** Suppose $f(x) = f(x_1, x_2, ..., x_n)$ is continuous differentiable. Then we denote the gradient of $f$  by:

$$
  \nabla f(x) = 
  \left(\frac{\partial f}{\partial x_i}\right)_n
$$
  And the First-Order Taylor Expansion yields:
$$
  f(x + td) = f(x) + t \nabla f(x)^\top d + o(t), t \to 0
$$

- **Hessian and Second-Order Taylor Expansion:** If $f$ is twice continuously differentiable, then the Hessian of $f$ is given by:
- 
$$
  \nabla^2f(x) = \left(\frac{\partial^2 f}{\partial x_i\partial x_j}\right)_{n \times n}
$$

  And the Second-Order Taylor Expansion yields:

$$
  f(x + td) = f(x) + t\nabla f(x)^\top d + \frac{1}{2}t^2 d^\top \nabla^2f(x)d + o(t^2), t \to 0
$$

  By **Schwarz's theorem**, we know that for twice continuously differentiable functions has the following property:

$$
  \frac{\partial^2 f}{\partial x_i\partial x_j} = \frac{\partial^2 f}{\partial x_j\partial x_i}
$$

  So the **Hessian** matrix is symmetric.

### Optimality Conditions for Unconstrained Problems

W.L.O.G, we focus our discussion on minimizers.

To obtain a local minimizer, it is necessary to have:

$$
\nabla f(x) = 0
$$

Otherwise, by First-Order Taylor Expansion,

$$
f(x + td) = f(x) + t \nabla f(x)^\top d + o(t), t \to 0
$$

We can find $d$ such that $\nabla f(x)^\top d \lt 0$  and when $t$ is small enough, we can then make

$$
f(x + td) \lt f(x)
$$

#### First-Order Necessary Conditions (FONC)

If $x^\ast$ is a local minimizer of the unconstrained problem $\min{}_{x\in \mathbb R^n} f(x)$,then we must have $\nabla f(x) = 0$.

#### Example: Least Square Problem

In least square problem, we want to use a linear combination of $x_i$ to approximate a target value $y$ such that

$$
y \approx \sum_{i = 1}^n \beta _i x_i
$$

Given $m$ observations of $(x, y)$ (which are denoted by $(x_i, y_i)$), we now want to find a suitable $\beta$ such that it solves that following problem:

$$
\min{}_\beta \sum_{i = 1}^m \left(y_i - \sum_{j = 1}^n \beta_j x_{ij}\right)^2
$$

In the matrix form, we have:

$$
\begin{aligned}
&\min{}_\beta \|X\beta - y\|^2 \\
=&\beta^\top X^\top X \beta - 2 \beta^\top X^\top y + y^\top y
\end{aligned}
$$

So, the FONC for the problem is

$$
X^\top X\beta - X^\top y = 0
$$

--------

**FONC** is not sufficient, so we need to add more constraints for make sure a point is our local minimizer. Another important condition is **SONC**.

By Taylor Expansion, we already have:

$$
f(x + td) = f(x) + t\nabla f(x)^\top d + \frac{1}{2}t^2 d^\top \nabla^2f(x)d + o(t^2), t \to 0
$$

Suppose FONC holds, then,

$$
f(x + td) = f(x) + \frac{1}{2}t^2 d^\top \nabla^2f(x)d + o(t^2), t \to 0
$$

If $x$ is a local minimizer, then we have, 

$$
d^\top \nabla^2f(x)d \ge 0, \forall d
$$

In other words, $\nabla^2f(x)$ is positive semidefinite.

#### Second-Order Necessary Conditions (SONC)

If $x^\ast$ is a local minimizer of $f$, then it holds that:

1. $\nabla f(x^\ast) = 0$;
2. For all $d \in \mathbb R^n$, $d^\top \nabla^2f(x)d \ge 0$.

#### Semi-definiteness

We call a (symmetric) matrix $A$ positive (negative) semidefinite (PSD/NSD) if and only if for all $x$ we have

$$
x^\top A x \ge 0 (\le 0)
$$

- we only talk about PSD for symmetric matrices.
- For non-symmetric ones, we use $\frac{1}{2}(A^\top + A)$ to define PSD. (We always have $x^\top A x = \frac{1}{2}x^\top(A^\top + A)x$)
- A symmetric matrix is PSD, it all of its eigenvalues are nonnegative
- A symmetric matrix is PSD, it all of its leading principles are nonnegative.
- For any matrix $A$, $A^\top A$ is always symmetric and PSD.

#### Definition:  Stationary Points and Saddle Points

- A point $x$ satisfying $\nabla f(x) = 0$ is called critical point or stationary point.
- A stationary point is called saddle point if it is neither a local minimizer nor a local maximizer.

#### Corollary:  Saddle Points

Suppose that $x^\ast$ is a stationary point ($\nabla f(x^\ast) = 0$) and that the Hessian $\nabla^2 f(x^\ast)$ is indefinite, then $x^\ast$ is a saddle point.

-----

**SONC** is still not sufficient, but it can be modified into a sufficient condition

#### Second-Order Sufficient Conditions (SOSC)

Let $f$ be twice continuously differentiable. If $x^\ast$ satisfies: 

1. $\nabla f(x^\ast) = 0$;
2. For all $d \in \mathbb R^n - \{ 0\}$ , $d^\top \nabla^2f(x)d \gt 0$.

Then $x^\ast$ is a strict local minimizer.

#### Definiteness

We call a (symmetric) matrix $A$ positive (negative) definite (PD/ND) if and only if for all $x$ we have

$$
x^\top A x \gt 0 (\lt 0)
$$

PD implies PSD and correspondingly, PD also has similar properties as PSD, but in a strict version.

**Remark**: the above conditions can be transformed into maximization conditions if we change PSD to NSD and PD to ND.

#### Proofs

To proof **SOSC**, we first need to proof a lemma:

##### Lemma:  Bounds and Eigenvalues

Let $A \in \mathbb R^{n\times n}$ be a symmetric matrix. Then
$$
\lambda_{\min_{}}(A)\|x\|^2 \le x^\top A x \le \lambda_{\max_{}}(A)\|x\|^2, \forall x \in \mathbb R^n
$$
where $\lambda_{\min_{}}(A), \lambda_{\max_{}}(A)$ are the smallest and the largest one among all eigenvalues of $A$.

##### Proof of the Lemma

Remember the eigenvalue-decomposition (EVD) of the symmetric matrix $A$ is written as:
$$
A = Q\Lambda Q^\top = \sum_{i = 1}^n \lambda q_iq_i^\top
$$
where
$$
Q = [q_i]_n \in \mathbb R^{n \times n}
$$
are $n$ orthonormal eigenvectors $q_i$ of $A$, and
$$
\Lambda = \text{diag}\left(\lambda_1, ..., \lambda_n\right)
$$
is a diagonal matrix with these eigenvalues.

Then, we can proof the lemma. $\forall x \in \mathbb R^n$, we have
$$
\begin{aligned}
x^\top A x &= x^\top Q \Lambda Q^\top x \\
           &= \tilde x^\top \Lambda \tilde x ~~~(\tilde x = Q^\top x) \\
           &= \sum_{i = 1}^n \lambda_i \tilde x^2_i \\
\end{aligned}
$$
It is easy to see:
$$
\sum_{i = 1}^n \lambda_{\min_{}} \tilde x^2_i \le \sum_{i = 1}^n \lambda_i \tilde x^2_i \le \sum_{i = 1}^n \lambda_{\max_{}} \tilde x^2_i
$$
Since $Q$ is orthonormal,
$$
\|Q^\top x\| ^2 =  \|x\|^2
$$
Hence, we get
$$
\lambda_{\min_{}}(A)\|x\|^2 \le x^\top A x \le \lambda_{\max_{}}(A)\|x\|^2, \forall x \in \mathbb R^n
$$

##### Proof of SOSC

By Taylor Expansion,
$$
f(x + h) = f(x) + \nabla f(x) ^\top h + \frac{1}{2}h^\top \nabla^2f(x) h + o(\|h\|^2), h \to 0
$$
With $\mu = \lambda_{\min_{}}(\nabla^2 f(x^\ast))$ and $\nabla f(x^\ast) = 0$
$$
\begin{aligned}
f(x^\ast + d) &= f(x^\ast) + \nabla f(x^\ast)^\top d + \frac{1}{2}d^\top \nabla^2f(x^\ast)d + o(\|d\|)^2 \\
& \ge f(x^\ast) + \|d\|^2 \left[\frac{\mu}{2} + \frac{o(\|d\|^2)}{\|d\|^2}\right]  
\end{aligned}
$$
when $\|d\|^2$ is small enough.

By the definition by little $o$ notation,
$$
\lim_{d \to 0}\frac{o(\|d\|^2)}{\|d\|^2} = 0
$$
Therefore, for all $d$ close enough to $0$, we have
$$
\frac{o(\|d\|^2)}{\|d\|^2} \ge -\frac{\mu}{4}
$$
Moreover, since SOSC requires the Hessian to be PD, then $\mu \ge 0$, which yields
$$
f(x^\ast + d) \ge f(x^\ast) + \frac{\mu}{4} \|d\|^2 \gt f(x^\ast)
$$
for $d$ sufficiently small. This indicates that $x^\ast$ is truly a local minimum.

### Existence of Solutions

#### Weierstra$\beta$ Theorem

When we talked about Linear Programs, we never consider the cases where feasible region of the problem is bounded but not finite optimal. Actually, there is a theorem protecting us from being bothered by such situations:

Let $f: \mathbb R^n \to \mathbb R$ be a continuous function and let $\Omega \subset \mathbb R^n$ be a bounded, closed and nonempty set. Then $f$ attains a global maximum and global minimum on $\Omega$,

Here, we also clarify some definitions:

##### Closeness

The set $\Omega$ is closed if for every convergent sequence $\{x_k\}$ with $x^k \in \Omega$ for all $k$ and $\lim_{k \to \infty} x^k = x$, it holds that $x \in \Omega$.

Closeness is a topological property, that is if you have a continuous injective function mapping a closed set $\Omega$ to another set $\Psi$, then $\Psi$ must be also closed.

##### Boundedness

$\Omega$ is bounded if there is $B \gt 0$ such that $\|x\| \le b, \forall x \in \Omega$.

##### Compactness

A closed and bounded set is also called compact. 

#### Coercivity

For unconstrained problems, however, we cannot directly use Weierstra$\beta$ Theorem because the feasible region may not be bounded. Fortunately, there is another tool, Coercivity that can help us in unconstrained cases.

##### Definition

A continuous function $f: \mathbb R^n \to \mathbb R$ is said to be coercive if

$$
\lim_{\|x\| \to \infty} f(x) = +\infty
$$

- Geometrically, coercivity means that $f(x)$ increases as $x$ moves away from the origin in any possible direction.

- Mathematically, coercivity means
- 
$$
  \forall B \gt 0, \exists r \gt 0, s.t.\|x\| \gt r \Longrightarrow f(x) \gt B 
$$

It turns out that coercivity guarantees the existence of solutions:

##### Theorem:  Coercivity and Existence of Solutions

Let $f : \mathbb R^n \to \mathbb R$ be a continuous and coercive function. Then for all $\alpha \gt 0$, the level set

$$
L_{\le \alpha} := \{ x \in \mathbb R^n : f(x) \le \alpha\}
$$

is compact and $f$ has at least one global minimizer.

###### Proof

We already know $f$ is continuous, $L_{\le \alpha}$ is closed (**closeness preserves under continuous mapping**).

Assume that the level set is not bounded. Then,

$$
\exists (x_k)_\alpha, x_k \in L_{\le \alpha}, k \in \mathbb N, s.t. \|x_k\| \to \infty (k \to \infty)
$$

Since $f$ is coercive, we know

$$
\lim_{k \to \infty} f(x_k) = +\infty
$$

However, this is a contradiction to $x_k \in L_{\le \alpha}$. So the level set of a coercive function is compact.

### Optimality Conditions for Constrained Problems

#### Feasible Directions

The key difference between Unconstrained Problems and Constrained Problems is that the possible moving directions can be limited for the points of the latter one. This brings more difficulties for finding the solution.

Given $x \in \Omega$, we call vector $d$ a **feasible direction** at $x$ if there exists $\overline t \gt 0$ such that $x + td \in \Omega$ for all $0 \le t \le \overline t$.

#### FONC

Let $x^*$ be a local minimum of $\min{}_{x \in \Omega} f(x)$. Then for any feasible direction $d$ at $x^\ast$, we must have $\nabla f(x^\ast)^\top d \ge 0$.

#### Descent Directions

Let $f$ be continuous differentiable. Then $d$ is called a descent direction at $x$ if and only if $\nabla f(x)^\top d\lt0$.

If we denote the set of feasible directions at $x$ by $S_\Omega(x)$ and the set of descent directions at $x$ by $S_D(x)$, then the first order necessary condition can be written as

$$
S_\Omega(x^\ast) \cap S_D(x^\ast) = \emptyset
$$

#### General Optimality Conditions

From now on, let us consider the general form of the nonlinear programs:

$$
\begin{aligned}
\min{}_{x \in \mathbb R^n}~~& f(x) \\
s.t.~~&g_i(x) \le 0, \forall i = 1,\dots,m, \\
&h_i(x) = 0, \forall j = 1,\dots,p
\end{aligned}
$$

The feasible set is $\Omega = \{x \in \mathbb R^n : g(x) \le 0, h(x) = 0\}$.

#### Definition:  Active and Inactive Set

At a point $x \in \Omega$, the set $\mathcal A(x) := \{g_i(x) = 0\}$ denotes the set of active constraints. The set of inactive constraints is given by $\mathcal I(x) := \{i : g_i(x) < 0\}$.

#### Linear Constraints

Let us start our journey with the linearly constrained problems.

For the model:

$$
\min{}_x f(x), s.t. Ax \ge b ~~~~~~~~~~~~~~~~~~~~~(\star)
$$

The FONC can be expressed as

If $x^\ast$ is a local minimum of $(\star)$, then

$$
\begin{aligned}
&\exists y \ge 0, s.t. \\
&
\nabla f(x^\ast) - A^\top y = 0\\
&y_i(a_i^\top x^\ast - b_i) = 0, \forall i
\end{aligned}
$$

##### Proof

Now we show this actually guarantees that $\nabla f(x^\ast)^\top d \ge 0$.

This is equivalent to

$$
[\min{}_{d \in \mathbb R^n} \nabla f(x^\ast) ^\top d, s.t. d \in S_{\Omega}(x^\ast)] \ge 0
$$

Since inactive indices provide no limit to the moving direction, we get

$$
[\min{}_{d \in \mathbb R^n} \nabla f(x^\ast) ^\top d, s.t. a_i^\top d \ge 0 , \forall i \in \mathcal A(x^\ast)] \ge 0
$$

Define

$$
C = \begin{bmatrix}
c_i^\top \\ \vdots \\ c_m^\top 
\end{bmatrix}, c_i^\top = \begin{cases}a_i^\top, i \in \mathcal A(x^\ast) \\
0, i \in \mathcal I(x^\ast)
\end{cases}
$$

Rewrite the condition as

$$
[\min{}_{d \in \mathbb R^n} \nabla f(x^\ast) ^\top d, s.t. Cd \ge 0] \ge 0
$$
This is a linear program and the dual is
$$
\max{}_y 0, s.t. y \ge 0, C^\top y = \nabla f(x^\ast)
$$

The primal problem is bounded and feasible, so according to the strong duality, the dual is also feasible.

Consequently, we can find

$$
y \ge 0, \nabla f(x^\ast) = C^\top y = \sum_{i \in \mathcal A(x^\ast)}a_iy_i + \sum_{ i \in \mathcal I(x^\ast)} c_iy_i
$$

This is exactly:

$$
\begin{aligned}
&\exists y \ge 0, s.t. \\
&\nabla f(x^\ast) - A^\top y = 0\\
&y_i(a_i^\top x^\ast - b_i) = 0, \forall i
\end{aligned}
$$

The last constraints enforces $y_i = 0, \forall i \in \mathcal I(x^\ast)$

So if the optimization model changes to:

$$
\min{}_x f(x), s.t. Ax = b
$$

Then there are no inactive constraints anymore, so the FONC changes to

$$
\exists y \in \mathbb R^m, \nabla f(x^\ast) = A^\top y
$$

### General Inequality Constraints

For general cases, consider the following nonlinear program:

$$
\begin{aligned}
\min{}_{x \in \mathbb R^n} &~~ f(x) \\
s.t. &~~ g_i(x) \le 0, \forall i = 1,\dots, m
\end{aligned}
$$

**Assumption: $f$, $g_i$ are continuous differentiable.**

When $f(x)$ reaches a local minimum at $x^\ast$, we have the following lemma:

#### Lemma:  No Feasible Descent for Inequality Constraints

Let $x^\ast$ be a local minimum of the above problem. Then, there does not exist a vector $d \in \mathbb R^n$ s.t.

$$
\nabla f(x^\ast) d < 0 \wedge \nabla g_i(x^\ast)^\top d \lt 0, \forall i \in \mathcal A(x^\ast)
$$

**Proof**

Suppose we have found a $d$ such that

$$
\nabla f(x^\ast) d < 0 \wedge \nabla g_i(x^\ast)^\top d \lt 0, \forall i \in \mathcal A(x^\ast)
$$

Then, by Taylor Expansion, we can find $\epsilon_1, \epsilon_2$ s.t.

$$
f(x^\ast + td) < f(x^\ast) \wedge g_i(x^\ast + td) \lt g_i(x^\ast) = 0, \forall i \in \mathcal A(x^\ast)
$$

$\forall t \in (0, \epsilon_1)$ and mean while,

$$
\forall i \in \mathcal I(x^\ast), g_i(x^\ast) < 0, \forall t \in [0, \epsilon_2)
$$

So we can see that,

$$
x^\ast + td, \forall t\in [0, \min{}(\epsilon_1, \epsilon_2)) 
$$

is even optimal, which is a contradiction.

### Theorem:  Fritz-John Conditions

Further extend the above idea, we then come across **Fritz-John Conditions**:

Let $x^\ast$ be a local minimum of the above problem. Then there exists $\lambda_0, \lambda_1, ..., \lambda _m \ge 0$, which are not all zeros, such that

$$
\begin{aligned}
\lambda_0\nabla f(x^\ast) + \sum_{i = 1}^m \lambda_i\nabla g_i(x^\ast) &= 0 \\
\lambda_ig_i(x^\ast) &= 0, \forall i = 1, \dots, m.
\end{aligned}
$$

**Proof**

We prove this theorem by the last lemma, we cannot find such $d$ that:

$$
 \nabla f(x^\ast) d < 0 \wedge \nabla g_i(x^\ast)^\top d \lt 0, \forall i \in \mathcal A(x^\ast)
$$

Consider the problem

$$
\begin{aligned}
\min{}_{d, t} t ~~s.t. ~~& \nabla f(x^\ast)^\top d \le t \\
                       & \nabla g_i(x^\ast)^\top d \le t, \forall i \in \mathcal A(x^\ast)
\end{aligned}
$$

the optimal solution must be non-negative.

Again, we define

$$
C = \begin{bmatrix}c_1^\top \\ \vdots \\ c_m^\top \end{bmatrix}
$$

where

$$
c_i^\top = \begin{cases}
\nabla g_i(x^\ast)^\top, &\forall i \in \mathcal A(x^\ast) \\
0, &\forall i \in \mathcal I(x^\ast)
\end{cases}
$$

Rewrite the problem with $C$, we get

$$
\begin{aligned}
\min{}_{d, t} t ~~s.t. ~~& \nabla f(x^\ast)^\top d - t \le 0 \\
                       & Cd - t\mathbf 1 \le 0
\end{aligned}
$$

The dual problem is a feasibility problem:

$$
\begin{aligned}
\max{}_{\tilde\lambda_0, \tilde \lambda} 0 ~~s.t.~~& \tilde \lambda_i \le 0 \\
& \tilde \lambda_0 \nabla f(x^\ast) + C^\top \tilde \lambda = 0 \\
& -\tilde \lambda_0 - \mathbf 1^\top \tilde \lambda = 1
\end{aligned}
$$

Since strong duality holds, the problem is feasible. 

So we can find  $\lambda_0, \lambda_1, ..., \lambda _m \ge 0$, 

$$
\begin{aligned}
\lambda_0\nabla f(x^\ast) + \sum_{i = 1}^m \lambda_i\nabla g_i(x^\ast) &= 0 \\
\lambda_ig_i(x^\ast) &= 0, \forall i = 1, \dots, m.
\end{aligned}
$$

(rescale $-\tilde \lambda_i$ to obtain $\lambda_ig_i(x^\ast) = 0, \forall i$; and $\tilde \lambda_0 + \mathbf 1^\top \tilde \lambda \gt 1$ guarantees that not all $\lambda_0, \lambda_1, \dots, \lambda_m$ are zero.) 

However, **Fritz-John Conditions** are not very useful. $\lambda_0 = 0$ can happen and in that case, we need to check all possible combinations that makes the set of

$$
\nabla g_i(x^\ast), \forall i \in \mathcal A(x^\ast)
$$

linearly dependent.

And, without the constraints from $f$, many of the combinations may not be minimizer.

### KKT-Conditions for Inequality Constraints

KKT-Conditions use an assumption to overcome the major drawbacks of  **Fritz-John Conditions**. 

Let $x^\ast$ be a local minimum and suppose the set of

$$
\nabla g_i(x^\ast), \forall i \in \mathcal A(x^\ast)
$$

are linearly independent. Then, 

Then there exists $\lambda_1, ..., \lambda _m \ge 0$, which are not all zeros, such that

$$
\begin{aligned}
\nabla f(x^\ast) + \sum_{i = 1}^m \lambda_i\nabla g_i(x^\ast) &= 0 \\
\lambda_ig_i(x^\ast) &= 0, \forall i = 1, \dots, m.
\end{aligned}
$$

The proof is trivial.  We can find required by **Fritz-John Conditions**,

$$
\lambda_0, \lambda_1, ..., \lambda _m \ge 0
$$

Since 

$$
\nabla g_i(x^\ast), \forall i \in \mathcal A(x^\ast)
$$

are linearly independent, $\lambda_0 \ne 0$. Then we can divide all $\lambda_i$ by $\lambda_0$ and obtain the required lambdas for KKT Conditions.

### KKT Conditions:  The General Setting

Now, it is the high time for General Nonlinear Optimization Problem

$$
\begin{aligned}
\min_{x \in \mathbb R^n} &~~ f(x) \\
s.t. &~~ g_i(x) \le 0, \forall i = 1,\dots, m \\
     &~~ h_j(x) = 0, \forall j = 1, \dots, p
\end{aligned}
$$
To begin with, we associate each constraint with a Lagrangian multiplier:
$$
g_i(x) \le 0 \leadsto \lambda_i \\
h_i(x) \le 0 \leadsto \mu_i
$$
We define the Lagrangian of this problem by:
$$
L(x, \lambda, \mu) = f(x) + \sum_{i = 1}^m \lambda_ig_i(x) + \sum_{j = 1}^p\mu_jh_j(x)
$$
Until now you may still feel confused about what can we do with the Lagrangian. However, if you look at the form carefully, you may have grabbed a similar feeling as we are deriving a dual form. Indeed, here we require 
$$
\lambda_i \ge 0, \mu_j \text{ free}
$$
These conditions are exactly called **dual feasibility**.

And we also have the **complementarity conditions**:
$$
\begin{aligned}
\lambda_ig_i(x) = 0,& \forall i \\
\mu_jh_i(x) = 0,& \forall j ~~(\text{omitted})
\end{aligned}
$$
Our **main condition** comes from the Lagrangian (We move all the constraints to the objective and interpret them as reward/penalty functions, so the following condition does make sense):
$$
\nabla_x L = 0
$$
To sum up, the formal definition of **KKT conditions** is:

#### Theorem:  KKT Condition

If $x$ is a local minimizer and if a **regularity condition** holds, then there exist $\lambda$ and μ such that:

1. Main Condition
   $$
   \nabla f(x) + \sum_{i = 1}^m \lambda_i\nabla g_i(x) + \sum_{j = 1}^p\mu_j\nabla h_j(x) = 0
   $$

2. Dual Feasibility
   $$
   \lambda_i \ge 0, \forall i \in \{1, \dots, m\}
   $$

3. Complementarity
   $$
   \lambda_ig_i(x) = 0, \forall i\in \{1, \dots, m\}
   $$

4. Primal Feasibility (extended)
   $$
   g_i(x) \le 0, h_j(x) = 0, \forall i, j
   $$

#### Constraint Qualifications

Again, we require the collection of gradients
$$
\{\nabla g_i(x^\ast) : i \in \mathcal A(x^\ast)\} \cup\{\nabla h_j(x^\ast): j = 1, \dots, p\}
$$
to be linearly independent (fully-ranked).

- This condition is a **constraint qualification** (CQ) and is called **Linear Independence Constraint Qualification** (LICQ).
- A feasible point $x^\ast$ satisfying the LICQ is called **regular**.
- There are more CQs: ACQ, GCQ, MFCQ, PLICQ, Slater’s condition. See [wikipedia](https://en.wikipedia.org/wiki/Karush%E2%80%93Kuhn%E2%80%93Tucker_conditions#Regularity_conditions_(or_constraint_qualifications)).

#### Remarks

- A (feasible) point satisfying the KKT conditions is called a KKT point
- KKT points are candidates for local optimal solutions – just like stationary points.

### Second-Order Optimality Conditions for Constrained Problems

KKT conditions are necessary first-order optimality conditions. KKT-points are potential candidates for local minimizer. We still need to investigate Second-Order Optimality Conditions to check whether a point is truly a local minimizer or not.

We assume that $f$, $g_i$,and $h_j$ are twice continuous differentiable. Therefore, we get calculate the Hessian of our **Lagrangian**:
$$
\nabla_{xx}^2L(x, \lambda, \mu) =  \nabla^2 f(x) + \sum_{i = 1}^m \lambda_i\nabla^2 g_i(x) + \sum_{j = 1}^p\mu_j\nabla^2 h_j(x)
$$
We define the so-called **critical cone**:
$$
\begin{aligned}
\mathcal C(x) := \{
d \in \mathbb R^ n :& \nabla f(x)^\top d = 0, \nabla g_i^\top d \le 0, \forall i \in \mathcal A(x), \\
& \nabla h_j(x)^\top d = 0, \forall j\}
\end{aligned}
$$
Then, the second-order necessary conditions take the following form:

#### Theorem:  SONC for Constrained Problems

Let $x^\ast$ be a regular point and local min. Then, the KKT-conditions hold and there are **unique multiplier** $\lambda \in \mathbb R^m$ and $\mu \in \mathbb R^p$ such that
$$
\nabla f(x^\ast) + \nabla g(x^\ast) \cdot \lambda + \nabla h(x^\ast) \cdot \mu = 0 \\
g(x^\ast) \le 0, h(x^\ast) = 0, \lambda \ge 0, \lambda_ig_i(x^\ast) = 0 , \forall i
$$
and we have
$$
d^\top \nabla^2_{xx}L(x^\ast, \lambda, \mu) d \ge 0, \forall d \in \mathcal C(x^\ast)
$$

##### Remark

The uniqueness of $\lambda$ and $\mu$ in the SONC follows from the LICQ. (This can be helpful in calculations). There do exist variants of SONC withouf LICQ, but normally they are hard to use and form is unfriendly.

#### Theorem:  SOSC for Constrained Problems

Let $x^\ast$ be a KKT-point with multiplier $\lambda \in \mathbb R^m$ and $\mu \in \mathbb R^p$ such that
$$
\nabla f(x^\ast) + \nabla g(x^\ast) \cdot \lambda + \nabla h(x^\ast) \cdot \mu = 0 \\
g(x^\ast) \le 0, h(x^\ast) = 0, \lambda \ge 0, \lambda_ig_i(x^\ast) = 0 , \forall i
$$
and suppose that the condition
$$
d^\top \nabla^2_{xx}L(x^\ast, \lambda, \mu) d \gt 0, \forall d \in \mathcal C(x^\ast) - \{0\}
$$
is satisfied. Then,$x^\ast$ is a strict local minimizer.

##### Remark

Unlike our **SONC**, **SOSC** does not require the uniqueness of multipliers here. If there are multiple multipliers then as long as there exists such multiplier that fulfills SOSC, 

then, $x^\ast$ is already a strict local minimizer.

##### Proof

Suppose that $x^\ast$ is not a strict local minimizer. Then, there exists a sequence $(x_k)$ within $\Omega$, such that $\lim_{k \to \infty}x_k = x^\ast$ and
$$
f(x^k) \le f(x^\ast), \forall k \in \mathbb N ~~~~~(\star)
$$
Define $t_k := \| x^k - x^\ast\|$ and $d_k := \frac{1}{t_k}(x_k - x^\ast)$. Then, we have $\|d_k\| = 1$. Therefore, $(d_k)$ is bounded. Every bounded sequence has a convergent subsequence, so we can find $\mathcal K \subseteq \mathbb N$ with $d \in \mathbb R^n$ such that
$$
\lim_{k \to \infty, k \in \mathcal K} d_k = d
$$
Obviously, $\|d\| = 1$. Then by Taylor Expansion, we get
$$
\begin{aligned}
f(x_k) &= f(x^\ast + x_k - x^\ast)\\
&= f(x^\ast + t_k d_k)\\
&= f(x^\ast) + t_k\nabla f(x^\ast)^\top d_k + o(t_k)
\end{aligned}
$$
when $k \to \infty, k \in \mathcal K$, by $(\star)$ we know
$$
\begin{aligned}
~&t_k\nabla f(x^\ast)^\top d_k + o(t_k) \le 0 \\
\Rightarrow & \nabla f(x^\ast)^\top d_k + \frac{o(t_k)}{t_k} \le 0 \\
\Rightarrow & \nabla f(x^\ast)^\top d \le 0 ~~~~~~~~~~~~(\diamondsuit)
\end{aligned}
$$
Similarly, $\forall i \in \mathcal A(x^\ast)$, we have
$$
g_i(x_k) = g_i(x^\ast) + t_k\nabla g_i(x^\ast)^\top d_k + o(t_k)
$$
Since $g_i(x) \le 0$, we then get
$$
\nabla g_i(x^\ast)^\top d_k = \frac{1}{t_k}g_i(x_k) + \frac{o(t_k)}{t_k} \le \frac{o(t_k)}{t_k}
$$
As $k \to \infty, k \in \mathcal K$, 
$$
\nabla g_i(x^\ast)^\top d_k \le 0, \forall i \in \mathcal A(x^\ast)
$$
With similar tricks, for those equality constraints:
$$
h_j(x_k) = h_j(x^\ast) + t_k\nabla h_j(x^\ast)^\top d_k + o(t_k)
$$
So, as $k \to \infty, k \in \mathcal K$, 
$$
\nabla h_j(x^\ast)^\top d_k = 0, \forall j \in \{1,\dots,p\}
$$
Since $x^\ast$ is a KKT point, we have
$$
-\nabla f(x^\ast)^\top d = \sum_{i \in \mathcal A(x^\ast)} \lambda_i\nabla g_i(x^\ast)^\top d + \sum_{j = 1}^p \mu_j\nabla h_j(x^\ast) ^\top d \le 0
$$
With $(\diamondsuit)$, we know
$$
\nabla f(x^\ast)^\top d = 0
$$
which means
$$
d \in \mathcal C(x^\ast) - \{0\}
$$
By $(\star)$, 
$$
\begin{aligned} 
L(x_k,\lambda, \mu) &= f(x_k) + \sum_{i = 1}^m \lambda_ig_i(x_k) + \sum_{j = 1}^p \mu_jh_j(x_k) \\
& \le f(x_k) \\
& \le f(x^\ast) \\
& = f(x^\ast) + \sum_{i = 1}^m \lambda_ig_i(x^\ast) + \sum_{j = 1}^p\mu_jh_j(x^\ast) \\
& = L(x^\ast, \lambda, \mu)
\end{aligned} ~~~~~~~~(\circledast)
$$
By Second Order Taylor Expansion,
$$
\begin{aligned}
L(x_k, \lambda, \mu) &= L(x^\ast + t_kd_k, \lambda, \mu) \\
&= L(x^\ast, \lambda, \mu) + t_k\nabla_xL(x^\ast, \lambda, \mu)^\top d_k + \frac{t_k^2}{2} d_k^\top \nabla_{xx}^2L(x^\ast, \lambda, \mu )d^k + o(t_k^2)
\end{aligned}
$$
Notice that $\nabla_xL(x^\ast, \lambda, \mu)^\top = 0$. With $(\circledast)$, 
$$
\frac{t_k^2}{2} d_k^\top \nabla_{xx}^2L(x^\ast, \lambda, \mu )d^k + o(t_k^2) \le 0
$$
As $k \to \infty, k \in \mathcal K$, 
$$
d^\top \nabla_{xx}^2L(x^\ast, \lambda, \mu)d \le 0
$$
which leads to a contradiction. $\blacksquare$

### Solving Nonlinear Programs:  Strategy

#### General Strategy

- Check LICQ (if required).
- Derive KKT-conditions.
- Discuss different easy cases via the complementarity conditions (set multiplier or constraints to $0$) to find all KKT-points. 
- Calculate $\mathcal C(x)$ and $\nabla_{xx}^2L(x^\ast, \lambda, \mu)$ at KKT-points.
- Check second-order conditions

#### Additional Information

- Check if $f$ is coercive or if $\Omega$ is bounded. If so, the problem has global solutions (which must be KKT-points)!
- If the LICQ holds, then $\lambda$ and $\mu$ are always unique!
- Finding maximizer: apply all steps to $-f$.

### Visualization and Interpretation of the KKT Conditions

#### Visualization

At the KKT-point, we have
$$
-\nabla f(x^\ast) = \sum_{i \in \mathcal A(x^\ast)} \lambda_i\nabla g_i(x^\ast) + \sum_{j = 1}^p \mu_j\nabla h_j(x^\ast)
$$
This means $-\nabla f(x^\ast)$ is the linear combination of $\nabla g_i(x^\ast)$ and  $\nabla h_j(x^\ast)$. One can visualize the gradient using this property.

#### The Dual Perspective

The KKT-conditions imply that the LP
$$
\begin{aligned}
\max_{\lambda \ge 0, \mu} ~&0\\
s.t.~& \nabla f(x) + \sum_{i = 1}^m \lambda_i\nabla g_i(x) + \sum_{j = 1}^p\mu_j\nabla h_j(x) = 0
\end{aligned}
$$
is feasible.

The dual problem is
$$
\begin{aligned}
\min_d ~~& -\nabla f(x^\ast)^\top d  \\
s.t. ~~& \nabla g_i(x^\ast) d \ge 0,  \forall \mathcal i \in \mathcal A(x^\ast), \nabla h(x^\ast)^\top d = 0 
\end{aligned}
$$
By strong duality, the optimal value is $0$.

Define,
$$
T_{\ell}(x^\ast) := \{d:  \nabla g_i(x^\ast) d \le 0,  \forall \mathcal i \in \mathcal A(x^\ast), \nabla h(x^\ast)^\top d = 0 \}
$$
So the KKT-conditions is equivalent to
$$
\nabla f(x^\ast) d \ge 0, \forall d \in T_{\ell}(x^\ast)
$$
The set $T_{\ell}(x^\ast)$ is called **linearized tangent set**.

## References

- Lecture Notes 11-14 for MAT3007 (Optimization) by Dr. Andre Milzarek. Institute for Data and Decision Analytics, The Chinese University of  Hong Kong, Shenzhen.
- Introduction to Nonlinear Optimization. Theory, Algorithms, and Applications with MATLAB, MOS-SIAM Series Optimization (2014), by Beck.