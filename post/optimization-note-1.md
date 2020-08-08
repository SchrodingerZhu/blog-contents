## Modeling

Mathematically, an optimization problem is usually represented as:
$$
\begin{aligned}
\text{maximize/minimize}_x &~ f(x) \\
\text{subject to} &~ x \in \Omega
\end{aligned}
$$
where:

- $x$ is the decision variable
- $f$ is the objective function
- $\Omega$ is the set of constraints

In practice, we may split out the constraints into two parts:

- The Inequality constraints: $g_i(x)\le 0, \forall i = 1,..., m$
- The equality constraints: $h_j(x) = 0, \forall j = 1,..., p$

## Some Terminologies

- **Feasible point:** A decision that satisfies all constraints.
- **Feasible set (region, $\Omega$):**  The set of feasible points.
- **Optimal solution:** A (feasible) decision variable that attains an objective value that is as good as any other feasible point. 
- **Optimal value:** The objective value of any optimal solution.

Define $B_\varepsilon (y) =\{x\in \mathbb R^n:‖x−y‖< \varepsilon\}$ to be the open ball in $\mathbb R^n$ with center $y$ and radius $\varepsilon >0$.

- **local minimizer**: if $x \in \Omega$ and there exists $\varepsilon > 0$ such that $f(x)\ge f(x')$ for all $x \in \Omega \cap B_\varepsilon(x')$. 
- **strict local minimizer**:  if $x' \in \Omega$ and there is $\varepsilon >0$ with $f(x)>f(x')$ for all $x \in (\Omega \cap B_\varepsilon(x'))\backslash \{x'\}$.
- **global minimizer**: if $x \in \Omega $ and we have $f(x)\ge f(x')$ for all $x\in\Omega $.
- **strict global minimizer:** if $x'\in\Omega$ and it holds that $f(x)>f(x')$ for all $x\in \Omega \backslash\{x'\}$.

## Solution State

By classifying the different outcomes, an optimization problem may have the following states:

1. Infeasible.
2. Feasible, optimal value finite but not attainable. 
3. Feasible, optimal value finite and attainable.
4. Feasible, but optimal value is unbounded.

## Classifications

- **Unconstrained optimization**: If $\Omega =\mathbb R^n$ or $m=p=0$. Otherwise: **constrained optimization** .
- **Linear optimization (LP)**: Constraints and objective are linear.
- **Nonlinear optimization (NLP)**: Either some of the constraintsor the objective function is nonlinear.
- **Integer/Discrete optimization (IP)**: Some of the decision variables have to be integers $(x\in \mathbb Z^n)$.
- **Continuous optimization**: All the decision variables can take continuous values

## Case Study: Support Vector Machine

### General Setup:

- Given $m$ objects represented by vectors $x_1,x_2,...,x_m\in \mathbb R^n$ with labels $y_i\in \{−1,1\}$. 
- The two labels $\pm 1$ indicate that the data can be separated into two classes $A\equiv+1$ and $B\equiv−1$. 
- Learn a function $\mathcal l :\mathbb R^n\to \{−1,1\}$based on the training sample $(x_1,y_1),...,(x_m,y_m)$.Predict the label of a new object $z$ via $\mathcal l(z)$.

###  Hyperplane Modeling

Define $\mathcal l(x) = w^Tx+b$ as an hyperplane. Choosing $(w, b)$ s.t.
$$
y_i = \begin{cases}
+1, \mathcal l(x_i) \ge +1, \\
-1, \mathcal l(x_i) \le -1,
\end{cases}, \forall i
$$

> Notice that this formulation is scaled from:
> $$
> y_i = \begin{cases}
> +1, \mathcal l(x_i) \gt 0, \\
> -1, \mathcal l(x_i) \le 0,
> \end{cases}, \forall i
> $$

The associated optimization problem is a feasibility problem:

$$
\min_{w,b} 0  \text{ s.t. } y_i(x_i^Tw+b)\ge 1,\forall i
$$
Now we transform the question into finding the best hyperplane such that distance between the hyperplanes $\{x:w^Tx+b=1\}$ and $\{x:w^T x + b =−1\}$ is the largest (the distance is  $\frac{2}{‖w‖}$).

However, instead of maximize $\frac{2}{‖w‖}$ directly, we minimize $\frac{1}{2} ||w||^2$
$$
\min_{w,b} \frac{1}{2} ||w||^2  \text{ s.t. } y_i(x_i^Tw+b)\ge 1,\forall i
$$

### Misclassification Handling

Up to now, our SVM model is already pretty good. However, it hasn't take misclassification into consideration yet. To correct this, we just need to add some penalties into our object function.

 The penalty is measured by the following **Hinge-Loss Function**:
$$
\sum_{i = 1}^m \max \{0, 1 - y_i \mathcal l(x_i)\}
$$
Our problem is now in the following form:
$$
\min_{w,b} \frac{\lambda}{2} ||w||^2 + \sum_{i = 1}^m \max \{0, 1 - y_i \mathcal l(x_i)\}  \text{ s.t. } y_i(x_i^Tw+b)\ge 1,\forall i
$$
Notice that we imported a new scalar called $\lambda$ to balance the weight of margin and misclassification.

### Linear Representation

Now we show a linear re-write of the problem in the case $\lambda = 0$.

Define $t_i = \max \{0, 1 - y_i(x_i^Tw + b)\} = (1 - y_i(x_i^Tw + b))^+$

We are now trying to solve the following problem:
$$
\min_{w, b, t} \sum_{i = 1}^n t_i, t_i = (1 - y_i(x_i^Tw + b))^+
$$
 which can be relaxed into the following form:
$$
\min_{w, b, t} \sum_{i = 1}^n t_i, t_i \ge (1 - y_i(x_i^Tw + b))^+
$$
Just imagine that there is a gap between $t_i$ and $(1 - y_i(x_i^Tw + b))^+$, then we can always choose a smaller $t_i'$ to get a better solution. Hence, the relaxation preserves the optimal points.

The inequality form enables us to remove the positive constraints and get a linear program:
$$
\min_{w, b, t} \sum_{i = 1}^n t_i, t_i \ge (1 - y_i(x_i^Tw + b)), t_i \ge 0
$$


## Linear Program

Linear program requires objective function and all constraints functions to be (affine-)linear. We can always describe the problem with the following compact form:


$$
\begin{aligned}
\text{minimize/maximize}_{x\in\mathbb R^n} &~~ c^Tx \\
\text{subject to} &~~ A_1x \ge b \\
&~~ A_2x \le b \\
&~~ A_3x = b \\
&~~ x_i \ge 0, \forall i \in N_1 \\
&~~ x_i \le 0, \forall i \in N_2 \\ 
&~~ x_i  \text{ free}, \forall i \in N_3 
\end{aligned}
$$


### Standard Form

One may argue that the above form is already simple enough. However, all LPs are be represented in a even more compact but less intuitive form:
$$
\text{min}_{x\in \mathbb R^n} c^Tx, Ax=b, x\ge 0
$$
The re-writing strategies are the following:

- Use a negation on $c$ to transform a maximum problem into minimum

- Import slack variables to eliminate inequalities:
  $$
  Ax+s=b,s\ge 0 \text{ or } Ax−s=b,s\ge 0
  $$

- Use a negation to transform a non-positive variable into non-negative

- Split free variables:
  $$
  x_i = x_i^+ - x_i^-, x_i^+ \ge 0, x_i^- \ge 0
  $$
  

### Minimax/Maximin Problem

A maximin problem for time scheduling may take the following form:
$$
\begin{aligned}
&\max   \min_{j = 1}^{n - 1} ~ {t_{j + 1} - t_j}  \\ 
&t_j \in [a_j, b_j],  \forall j=1,...,n \\
&t_j \le t_{},       \forall j=1,...,n-1 
\end{aligned}
$$
This can be transformed into a LP by letting:
$$
\Delta = \min_{j = 1}^{n - 1} ~ {t_{j + 1} - t_j}
$$
and the problem is now taking the new form:
$$
\begin{aligned}
\max_{\Delta, t} & ~~ \Delta \\
\text{subject to} &~~ t_{j + 1} - t_{j} - \Delta \ge 0 , & \forall& j=1,...,n-1 \\
&~~ a_j \le t_j \le b_j,  &\forall& j=1,...,n \\
&~~ t_j \le t_{},       &\forall& j=1,...,n-1
\end{aligned}
$$
In general, for a minimax problem in the form of:
$$
\min_x \max_{i=1,...,n}\{c^T_ix+d_i\}, Ax=b, x\ge0
$$
we can define: 
$$
y = \max_{i=1,...,n}\{c^T_ix+d_i\}
$$
and consider
$$
\min_x y, y \ge c^T_ix+d_i, \forall i, Ax=b, x\ge0
$$

### Absolute Value Problem

Consider a optimization problem involving absolute values:
$$
\min \sum_{i = 1}^n |a_i^Tx_i + b|, Ax = b ~~~~~(1)
$$
We can re-write it into a LP:
$$
\min \sum_{i = 1}^n t_i, Ax = b, t_i \ge a_i^Tx_i + b, t_i \ge -(a_i^Tx_i + b) ~~~~~(2)
$$
Actually we have a lemma to guarantee the equivalence of the above two forms:

#### Lemma: Modeling Tool

If we have two model $A$ and $B$ such that:
$$
\begin{aligned}
\min_x& \sum^n_{i=1}f_i(x), x\in \Omega  &(A) \\
\min_{x, t}& \sum^n_{i=1}t_i, x\in \Omega , f_i(x)\le t_i, \forall i&(B)
\end{aligned}
$$
They are multivalent under the following senses:
$$
(x, f(x)) \text{ is a sol. of } A \iff  (x, f(x)) \text{ is a sol. of } B
$$
and both problems have the same optimal value.

#### Remarks

Notice a simple change of the problem (from $\min$ to $\max$):
$$
\max \sum_{i = 1}^n |a_i^Tx_i + b|, Ax = b ~~~~~(1)
$$
will make it impossible to re-write the problem into an LP due to the non-convexity of the feasible region.

### Fractional Problem

Consider a fractional problem:
$$
\min_x \frac{c^Tx + d}{e^Tx + f}, Ax \le b
$$
with the assumption that
$$
e^Tx + f \gt 0
$$
holds over the feasible set.

This can be linearized by importing:
$$
y = \frac{x}{e^Tx + f}, 
z = \frac{1}{e^Tx + f}
$$
Then the problem is transformed into:

$$
\min_{y,z} c^Ty+dz, Ay−bz\le0, e^Ty+fz=1, z\ge0
$$
### Geometry Definitions

We define the followings for the geometric object related to LP:

A **polyhedron** is a set that can be written in the form:
$$
\{x \in \mathbb R^n : Ax \ge b\}
$$
where $A$ is an $m \times n$ and $b \in \mathbb R^m$

A set $S \subseteq \mathbb R^n$ is **convex** if for any $x,y\in S$, and any $\lambda \in [0,1],\lambda x+ (1−λ)y\in S$.

For any $x_1,...,x_n $ and $\lambda_1,...,\lambda n≥0$ satisfying $\lambda_1+···+\lambda_n=1$, we call $∑^n_{i=1}\lambda _ix_i$ a **convex combination** of $x_1,...,x_n$.

Let $P$ be a polyhedron. A point $x\in P$ is said to be an extreme point of $P$ if we can not find two vectors $y,z\in P$ with $y,z\ne x$ and a scalar $\lambda \in [0,1]$, such that $x=\lambda y+ (1−\lambda)z$.

### Case Study: Max-flow Problem using CVXPY 

Here we show a simple example of CVXPY linear programming:

```python
import cvxpy as cp
from collections import defaultdict as ddict
edges = ddict(lambda: 0, {
    (1, 2): 11,
    (1, 3): 8,
    (2, 4): 14,
    (2, 3): 10,
    (3, 2): 1,
    (3, 5): 11,
    (4, 3): 4, 
    (4, 6): 15,
    (5, 4): 7,
    (5, 6): 4
})
flow = ddict(lambda: cp.Variable(1))
capacity_constaints = [
    flow[(x, y)] <= edges[(x, y)] for x in range(1, 7) for y in range(1, 7)
]
lowerbound_constaints = [
    flow[(x, y)] >= 0 for x in range(1, 7) for y in range(1, 7)
]
flow_constraints = [
    cp.sum([flow[(x, i)] for x in range(1, 7)]) == cp.sum([flow[(i, y)] for y in range(1, 7)]) for i in range(2, 6) 
]
constraints = [*capacity_constaints, *lowerbound_constaints, *flow_constraints]
objective = cp.Maximize(cp.sum([flow[(x, 6)] for x in range(1, 7)]))
problem = cp.Problem(objective, constraints)
print("max flow", problem.solve())
for (x, y) in [(x, y) for x in range(1, 7) for y in range(1, 7)]:
    if x != y and edges[(x, y)]:
        print(x, " -> ", y, ":", flow[(x, y)].value)
```

The code solves the max-flow problem in the network graph with the power of linear programming. At first, we define several edges between the node, and record the capacity of the edges in a hash dictionary. To make life easier, we set edges between directionally unconnected nodes with capacity $0$. This will not change the final result.

Let $v_{ij}$ be the flow from $i$ to $j$, our problem can be stated in the following form:

- Maximize $\sum_{i = 1}^{n - 1} v_{in}$
- Object to:
  - $v_{ij} \le c_{ij}, \forall i \ne j$
  - $\sum_{a \ne i} v_{ai} = \sum_{b \ne i} v_{bi}, \forall 1 < i < n$
  - $v_{ij} \ge 0, \forall i,j$

The three lines of constraints are translated into the `capacity_constaints`,

`flow_constaints` and `lowerbound_constaints` correspondingly and the objective function can also be stated trivially. Then, we can use the linear programming model to solve the max-flow problem.



## References

- Lecture Notes 1-3 for MAT3007 (Optimization) by Dr. Andre Milzarek. Institute for Data and Decision Analytics, The Chinese University of Hong Kong, Shenzhen.
- Introduction to Linear Optimization, by D. Bertsimas and J. Tsitsiklis