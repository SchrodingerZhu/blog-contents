## Convexity

With KKT-conditions, we have already got some powerful tools to analysis the local minimizers, but when is a local minimizer also a global one? In a special category of optimization problems, the convex optimization, this is always true. In the following discussions, we are going to study this kind of problems.

Let us first go through several definitions

### Convex Sets

A set $\Omega \subseteq \mathbb R^n$ is convex if for any $x, y \in \Omega$ and any $\lambda \in [0, 1]$, we have
$$
\lambda x + (1 - \lambda)y \in \Omega
$$

### Convex Combination

For any $x_1, \dots, x_n$ and $\lambda_1, \dots, \lambda_n \ge 0$ satisfying
$$
\lambda_1 + \cdots + \lambda_n = 1
$$
we call $\sum_{i = 1}^n\lambda_ix_i$ a convex combination of $x_1, \dots, x_n$.

### Convex Function

A function $f$ on a convex set $\Omega$ is said to be convex if for every $x_1, x_2 \in \Omega$ and any $0\le\lambda \le 1$, we have
$$
f(\lambda x_1 + (1 - \lambda)x_2) \le \lambda f(x_1) + (1 - \lambda)g(x_2)
$$

### Concave Function

A function $f$ on a convex set $\Omega$ is said to be concave if and only if $-f$ is convex and for every $x_1, x_2 \in \Omega$ and any $0\le\lambda \le 1$, we have
$$
f(\lambda x_1 + (1 - \lambda)x_2) \ge \lambda f(x_1) + (1 - \lambda)g(x_2)
$$

----

We can check the convexity by definitions. However, it turns our that convexity can also be decided from derivatives. In fact, we have the following theorem:

### Theorem: Convexity via Hessian

Let $\Omega$ be a convex set and let $f$ be twice continuous differentiable on an open set containing $\Omega$. Then $f$ is convex on $\Omega$ if and only if this Hessian matrix is positive semidefinite, i.e.,
$$
d^\top \nabla^2 f(x) d \ge 0, \forall d \in \mathbb R^n, \forall x \in \Omega
$$

#### Proof

We first show: let $f : \mathbb R^n \to \mathbb R$ be continuously differentiable on an open set containing the convex set $\Omega \subseteq \mathbb R^n$. Then $f$ is convex on $\Omega$ iff
$$
f(y) - f(x) \ge \nabla f(x)^\top (y - x), \forall x, y \in \Omega
$$
Suppose $f$ is convex on $\Omega$. Then, for all $x, y \in \Omega$ and $\lambda \in (0, 1]$, we have:
$$
\begin{aligned}
&\frac{f(x + \lambda(y - x)) - f(x)}{\lambda} \\
=~& \frac{f((1 - \lambda)x + \lambda y) - f(x)}{\lambda}\\
\le~& \frac{(1 - \lambda)f(x) + \lambda f(y) - f(x)}{\lambda}\\
=~&f(y) - f(x)
\end{aligned}
$$
When $\lambda$ approaching $0^+$, we obtain
$$
\nabla f(x)^\top(y - x) \le f(y) - f(x) ~~~~~~~~(\circledast)
$$
To proof the other direction, suppose that the latter condition holds for all $x, y \in \Omega$. 

Set
$$
x_\lambda = (1 - \lambda)x + \lambda y
$$
for some arbitrary $\lambda \in [0, 1]$, we need to show
$$
f(x_\lambda) \le (1 - \lambda) f(x) + \lambda f(y)
$$
We have
$$
\begin{aligned}
(1 - \lambda) f(x) + \lambda(y) - f(x_\lambda) &\gt (1 - \lambda)\left[f(x) - f(x_\lambda) + \lambda(f(y) - f(x_\lambda))\right] \\
& \ge (1 - \lambda) \nabla f(x_\lambda)(x - x_\lambda) + \lambda \nabla f(x_\lambda) (y - x)~~\text{by } (\circledast) \\
& = \nabla f(x_\lambda)^\top [(1 - \lambda) x + 
\lambda y - x+\lambda] \\
& = 0
\end{aligned}
$$
Therefore, the function is convex on $\Omega$.

Now, let us get back to the proof of the theorem.

First suppose that $f$ is convex and let $x, y \in \Omega$ be arbitrary. By Taylor Expansion, we have
$$
f\left(x + t(y - x)\right) = f(x) + t\cdot \nabla f(x)^\top (y - x) + \frac{t^2}{2}(y - x)^\top \nabla^2f(x)(y - x) + o(t^2)
$$
When $t$ is sufficiently small. Specially, for all $t \in [0, 1]$, we have
$$
x + t(y - x) \in \Omega
$$
By $(\circledast)$,
$$
f(x + t(y - x)) - f(x) \ge t \nabla f(x)^\top (y - x)
$$
Therefore, for all $t \le 1$ sufficiently small,
$$
(y - x)^\top \nabla^2 f(x) (y - x) \ge - \frac{2o(t^2)}{t^2}
$$
Limiting $t \to 0$ gives
$$
(y - x)^\top \nabla^2 f(x) (y - x) \ge 0, \forall y \in \Omega
$$
**We need to additionally assume $\Omega$ is open to obtain the final form**. In such case, we can always choose $y = x + d$ with $d \in \mathbb R^n$ where $\|d\|$ is sufficiently small.

Then
$$
d^\top \nabla^2 f(x) d \ge 0, \forall d \in \mathbb R^n, \forall x \in \Omega
$$
Now we proof the other direction. Suppose the Hessian is PSD for all $x \in \Omega$. By Second Order Taylor Expansion, 
$$
\forall x, y \in \Omega, \exists \tau \in [0, 1], s.t.\\ 
f(y) - f(x) = \nabla f(x)^\top (y - x) + \frac{1}{2}(y - x)^\top \nabla^2 f(x + \tau (y - x)) (y - x)
$$
Since the Hessian is PSD, we get
$$
f(y) - f(x) \ge \nabla f(x)^\top (y - x)
$$
Therefore, $f$ is convex on $\Omega$.

----

Similarly, we also have a way to check concavity via Hessian:

### Theorem:  Concavity via Hessian

Let $\Omega$ be a convex set and let $f$ be twice continuous differentiable on an open set containing $\Omega$. Then $f$ is concave on $\Omega$ if and only if this Hessian matrix is negative semidefinite, i.e.,
$$
d^\top \nabla^2 f(x) d \le 0, \forall d \in \mathbb R^n, \forall x \in \Omega
$$

----

Moreover, there are several ways to combine convex (concave) functions while preserve the convexity (concavity).

### Lemma:  Sum Rule

If $a_1,\dots, a_m \ge 0$ and $f_1, \dots, f_m$ are convex (concave) functions, then
$$
\sum_{i = 1}^m a_if_i
$$
is a convex (concave) function.

### Lemma: Composition with Linear Functions

If $f: \mathbb R^n \to \mathbb R$ is convex (concave) and $A \in \mathbb R^{m\times n}, b \in \mathbb R^m$ are given, then
$$
\begin{aligned}
g &:  \mathbb R^n\mapsto \mathbb R\\
g &: x \to f(Ax + b)
\end{aligned}
$$
is also convex (concave).

### Lemma: Taking Maximum

If $f_1, \dots, f_m$ are convex functions, then 
$$
f(x) = \max \{f_1(x), \dots, f_m(x)\}
$$
is a convex function (this can be extended to uncountably many).

### Lemma: Taking Minimum

If $f_1, \dots, f_m$ are concave functions, then 
$$
f(x) = \min \{f_1(x), \dots, f_m(x)\}
$$
is a concave function (this can be extended to uncountably many).

### Typical Convex Sets

- Hyper-plane: $\mathcal H = \{x : a^\top x = b\}$
- Half-space: $\mathcal H = \{x : a^\top x \le b\}$
- Intersection of multiple convex sets
- Ray: $\mathcal C = \{x = x_0 + \theta v, v \ne 0, \theta \ge 0\}$
- Ball: $\mathcal B = \{x : \|x - x_c\| \le r\}$
- Ellipsoids: $\mathcal C = \{x : (x - x_c)^\top \mathbf P^{-1}(x - x_c) \le 1\}, \mathbf P \succcurlyeq 0$
- Polyhedron: $\mathcal C = \left\{~x : \begin{cases} a_i^\top x \le b_i , i = 1, \dots, m \\ c_j^\top x = d_j, j = 1, \dots, p\end{cases}~\right\}$

----

To grab more feelings of convexity, let us look at a special case:

Consider the following linear program:
$$
\min_x c^\top x, Ax = b, x \ge 0
$$
Now, if we fix $A$ and $b$, then the optimal value of the program is a function of $c$, denoted by $V(c)$. Moreover, we have an interesting theorem:

### Theorem:  Properties of $V$

$V$ is a concave function of $c$.

#### Proof

Trivially,
$$
V(c) = \min_{Ax = b, x \ge 0} \{c^\top x\}
$$
Then, by the lemmas above, $V(c)$ is convex.

----

We are equipped with plenty of lemmas and theorems. It is the high time to check how convexity can help. The following theorems connect convexity with global solutions:

### Theorem:  Convexity and Global Solutions

Let $f: \Omega \to \mathbb R$ be a convex function and $\Omega \subset \mathbb R^n$ be a convex set. Then any local minimizer of them problem:
$$
\min_x f(x), s.t. x \in \Omega
$$
is a global minimizer.

#### Proof

Assume $x^\ast$ is a local minimizer, however, there exists $\overline x \in \Omega$, such that
$$
f(\overline x) < f(x^\ast)
$$
Then, using convexity
$$
f(\lambda \overline x + (1 - \lambda)x^\ast) \le \lambda f(\overline x) + (1 - \lambda) f(x^\ast) \lt f(x^\ast)
$$
for any $0 < \lambda < 1$. This is a contradiction to $x^\ast$ being a local minimizer.

### Theorem:  Stationarity & Global Optimality

Let $f$ be convex and suppose that $\Omega := \{ x : g(x) \le 0, h(x) = 0\}$ is a convex set. Then, the KKT conditions for the problem
$$
\min_x f(x), s.t. x \in \Omega
$$
are sufficient for global optimality.

----

### Convex Problems

Since convexity saves a lot of works, there is a specific category for these programs. 

We call the optimization problems of the form:

- Minimize a convex function over a convex feasible region
- Maximize a concave function over a convex feasible region

**convex optimization problems**

However, most of the problems are written in the form of several constraint functions. We now take a look at how we can check the convexity of such problems.

#### Lemma:  Convex Level Sets

Let $f$ be a convex (concave) function. Then, for any $c$, the level set
$$
L_{\le c} = \{ x : f(x) \le c\} ~~ (L_{\ge c} = \{ x : f(x) \ge c\})
$$
is a convex set.

With this lemma, if we have:

- a constraint $g(x) \le 0$ where $g$ is convex
- a constraint $g(x) \ge 0$ where $g$ is concave

then we directly know that it is a convex constraint.

## Algorithms for Unconstrained Problems (Unidimensional)

After a long long journey with math, we now can finally get back to algorithms. To make life easier, let us first focus on Unconstrained Problems.

We are going talk about three algorithms:

- Bisection search and golden section search
- Gradient descent method
- Newton’s method

Optimization algorithms are iterative procedures:

- Starting from an initial point $x_0$, a sequence of iterates $\{x_k\}$ is generated.
- Reduction of the function values and convergence to an optimal solution.

We assume $f : \mathbb R \mapsto \mathbb R$ is a single variable function. We can use

- Bisection search
- Golden section search

to find a local minimizer of $f$.

### Bisection search

 Bitsection finds the root. Suppose we know $f'(x)$ and it is continuous and we can find $x_\ell$ and $x_r$ such that $f'(x_\ell)f'(x_r) < 0$. Then, by the intermediate value theorem, if $f'$ is continuous, there must exist a root of $f'$ in $[x_\ell, x_r]$, which is our candidate of the local minimizer.

There aren't so many things about this method, at each iteration:

- Define $x_m = \frac{x_\ell + x_r}{2}$
- If $g(x_m) = 0$, output $x_m$ and stop
- Otherwise,
  - If $g(x_m) > 0$, then let $x_r = x_m$
  - If $g(x_r) > 0$, then let $x_\ell = x_m$
- If $|x_r - x_\ell| < \varepsilon$, output $\frac{x_r - x_\ell}{2}$. Otherwise, go on to next iteration.

It is easy to see, we need at most
$$
\log_2 \frac{x_r - x_\ell}{\varepsilon}
$$
steps.

When $f'$ is unknown or too costly to compute, this method may become inappropriate.

#### Python Implementation

```c++
def bisection(df, s, t, eps=1e-8, iteration=1):
    mid = (s + t) / 2
    if t - s < eps:
        print("finished in {} iteration".format(iteration))
        return mid
    if df(mid) * df(s) > 0:
        return bisection(df, mid, t, eps, iteration + 1)
    else:
        return bisection(df, s, mid, eps, iteration + 1)
```

### Golden Section Search

If we know that $f$ has a unique local minimum $x^\ast$ in the range $[x_\ell, x_r]$, then we can efficiently find it even without the knowledge of $f'$.

- We call $f$ **unimodal** if it only has one single stationary point (on $\mathbb R$).
- Unimodal functions have the property that the local minimum is already global. (Similarly, if the stationary point is a local maximum).

The procedure of Golden Section Method is also simple. Assume we start with $[x_\ell, x_r]$ and we have a global constant $0 < \phi < 0.5$:

1. Set $x_l' = \phi x_r + (1 - \phi )x_\ell$ and $x_r' = (1 - \phi) x_r + \phi x_\ell$.
2. If $f(x_\ell') < f(x_r')$, then the minimizer must lie in $[x_\ell, x_r']$, so set $x_r = x_r'$
3. Otherwise, the minimizer must lie in $[x_\ell', x_r]$, so set $x_\ell = x_\ell'$.
4. If $x_r - x_\ell < \epsilon$, output $\frac{x_\ell + x_r}{2}$, otherwise go back to step $1$.

So why is it called **Golden** Search? When moving from $x_r$ to $x_r'$ (or $x_\ell$ to $x_\ell'$), we want to somehow reuse the other intermediate value $x_\ell'$ (or $x_r'$) in the next step, such that it will become the new $x_r'$ (or $x_\ell'$). In this way, we can save one function evaluation.

One can show that
$$
\phi = 1 - \hat \phi = \frac{3 - \sqrt 5}{2}
$$
is exactly the value we want. This gives the algorithm its golden name.

#### Python Implementation

```python
import math
φ = (3 - math.sqrt(5)) / 2
def golden_section(f, s, t, eps=1e-8, left=None, right=None):
    if t - s < eps:
        return (s + t) / 2
    a = φ * t + (1 - φ) * s
    b = φ * s + (1 - φ) * t
    if left is None:
        left = f(a)
    if right is None:
        right = f(b)
    if left < right:
        return golden_section(f, s, b, eps, right=left)
    else:
        return golden_section(f, a, t, eps, left=right)
```

## Higher-Dimensional Problems

When the problem enters higher-dimensions; there is no clear bisection or golden section in that case. There are some many directions to search. The general idea is

- each time, we first find a search direction
- then, we search for a good next step along the direction (this is a one dimensional problem)

This is described by:
$$
\vec x_k = \vec x_k + \alpha_k \vec d_k
$$
where $\alpha_k$ is the step size and $\vec d_k$ is the direction.

### Descent Direction

Assume $f$ is continuously differentiable.  A vector $d \in \mathbb R^n$ is a decent direction of $f$ at $x$ if $\nabla f(x) ^\top d \lt 0$.

By Taylor Theorem, we can find $\epsilon > 0$ s.t.
$$
f(x + \alpha d) \lt f(x), \forall \alpha \in (0, \epsilon].
$$
So indeed, we can reduce $f$ along the direction.

So now the general procedure is

1. Find a **initial point** $x_0 \in \mathbb R^n$
2. Pick a **descent direction** $d_k$
3. Find a **step-size** $\alpha_k$ satisfying $f(x_k + \alpha_k d_k) < f(x_k)$
4. Set $x_{k + 1} = k_k + \alpha_k d_k$
5. Check the **stop criterion** and decide to go on or stop. 

### Step Size

#### Constant Step Size

One can choose a constant value $\alpha_k = \overline \alpha$ during the whole process. However,

- If the chosen value is too small, it may takes too long time to converge.
- If the chosen value is too large, the algorithm may jump over the minimums and end up with divergence.

#### Exact Line Search

Another intuitive idea is to choose $\alpha_k$ to achieve the largest decent.
$$
\alpha_k = \text{argmin}_{\alpha \ge 0} ~f(x_k + \alpha d_k)
$$
In most cases, it is possible to know the exact value of $a_k$. Therefore, this method is called exact line search.

Moreover, an efficient method to find the value is to use the golden section algorithm.

Or, one can even use the analytical way to calculate the value at each iteration.

##### Python Implementation

```python
def exact_line_search(initial, f, grad):
    def target(α):
        value = [initial[i] - α * grad[i] for i in range(len(initial))]
        return f(*value)
    return golden_section(target, 0, 1)
```

#### Backtracking (Armijo) Line Search

There is a more cheaper way to provide the step size.

Let $\sigma, \gamma \in (0, 1)$ be given. We choose $\alpha_k$ as the largest element in $\{1, \sigma, \sigma^2, \sigma^3, \dots\}$ such that
$$
f(x_k + \alpha_k d_k) - f(x_k) \le \gamma \alpha_k \nabla f(x_k)^\top d_k
$$
This condition is called Armijo condition.

$\alpha_k$ can be determined after finitely many steps if $d_k$ is a decent direction.

By Taylor expansion, for sufficiently small $\alpha$, we have
$$
f(x_k + \alpha d_k) \approx f(x_k) + \alpha \nabla f(x_k)^\top d_k \lt f(x_k) + \gamma \alpha \nabla f(x_k)^\top d_k 
$$
Hence, we can find a suitable $\alpha_k$ for decent direction.

Define $\phi_k(\alpha) = f(x_k + \alpha d_k) - f(x_k)$, we have:
$$
\phi_k'(\alpha) = \nabla f(x_k + \alpha d_k)^\top d_k
$$
In this way, the Armijo condition is to:
$$
\text {find } \alpha \text{ with } \phi_k(\alpha) \le \gamma \alpha \phi_k'(0)
$$
Suppose the minimum of $\phi(\alpha)$ is within $(0, 1)$. Then, we kind of rotate the tangent line of the function at $0$ in $\gamma \alpha \phi_k'(0)$.

And we want to find the right most point on the function that is below the line, which is good enough to be an alternative of the minimum.

##### Python Implementation

```python
def backtracking(initial, f, grad):
    fx = f(*initial)
    def check(α):
        value = [initial[i] - α * grad[i] for i in range(len(initial))]
        s = sum(map(lambda x: -x*x, grad))
        return f(*value) - fx <= γ * α * s
    α = 1
    while not check(α):
        α = α * σ
    return α
```

### Gradient Descent

#### Decent Direction

One simple and feasible descent direction is
$$
d_k = -\nabla f(x_k)
$$
Then we have,
$$
\nabla f(x_k)^\top d_k = - \| \nabla f(x_k)\|^2 < 0
$$
as long as $\nabla f(x_k) \ne 0$.

This is exactly why **Gradient Descent Method** get its name.

#### Stop Criterion

A popular is stopping criterion is:  $\|\nabla f(x_{k + 1})\| \le \epsilon$ with a small tolerance $\epsilon > 0$.

When we stop at $x_{k + 1}$ by this criterion, it is called an **approximate stationary point**.

#### Python Implementation

```python
import numpy.linalg as lin
σ = 0.5
γ = 0.5
def gradient_decent(initial, f, gradient, search_method, eps=1e-5, iteration=0, col=None):
    grad = [f(*initial) for f in gradient]
    norm = lin.norm(grad)
    if col is not None:
        col[iteration] = [norm, initial]
    if norm <= eps:
        print("finished in {} iterations".format(iteration))
        return initial
    step = search_method(initial, f, grad)
    n = [initial[i] - step * grad[i] for i in range(len(initial))]
    return gradient_decent(n, f, gradient, search_method, eps, iteration + 1, col)
```

#### Convergence

It turns out that Gradient Decent Method has the **global convergence** property: independent of the chosen initial point, Gradient Decent Method can always find its way to stationary points.

We start with a definition of accumulation points:

##### Definition:  Accumulation Point

A point $x$ is an accumulation point of $(x_k)^k$ if for every $\epsilon \gt 0$, there are infinitely many numbers $k$ with $x_k \in B_\epsilon(x)$

- If $x$ is an accumulation point of $(x_k)^k$ then there exists a subsequence $(x_{k_\ell})^\ell$ that converges to $x$.

- If $(x_k)^k$ converges to some $x \in \mathbb R^n$, then $x$ is the unique accumulation point of the sequence.

- A bounded sequence always possesses at least one accumulation point.

##### Theorem: Global Convergence

Let $f: \mathbb R^n \mapsto \mathbb R$ be continuous differentiable and let $(x_k)^k$ be generated by the gradient method for solving:

$$
\min_x f(x) ~s.t. ~x \in \mathbb R^n
$$

with one of the following step size strategies:

- exact line search
- Armijo line search with $\sigma, \gamma \in (0, 1)$

Then, $(f(x_k))^k$ is non-increasing and every accumulation point of $(x_k)^k$ is  a stationary point of $f$.

Notice that if $\nabla f(x_k) \ne 0$ for all $k$, then the accumulation points of $(x_k)^k$ can only be the local/global minima or saddle point. Typical situations are polynomial functions.

Moreover, if the SOSC holds at an accumulation point $x^\ast$, then it is the strict local minimum that $(x_k)^k$ converges to.

##### Lipschitz Continuity

We say a function $f$ has **Lipschitz Continuity** if and only if:
$$
\|f(x) - f(y)\| \le L \|x - y\|, \forall x, y \in \mathbb R^n
$$
where $L \ge 0$ is the Lipschitz constant. We assume $\nabla f$ has **Lipschitz Continuity** in the discussion of the convergence of the gradient descent method. The class of functions with Lipschitz gradient with constant $L$ is denoted by
$$
\mathcal C_L^{1, 1} (\mathbb R^n) \text{  or } \mathcal C_L^{1, 1}
$$

- The linear function $f(x) = b^\top x + c, b \in \mathbb R^n, c \in \mathbb R$ is in $\mathcal C_0^{1, 1}$

-  The quadratic function: $f(x) = \frac{1}{2}x^\top A x + b^\top x + c$

  We have:
$$
\|\nabla f(x) - \nabla f(y)\| = \|A(x - y)\| \le \|A\|\cdot \|x - y\|
$$
  where is the spectral norm is defined as
$$
\|A\| = \sqrt {\lambda_{max} \left( A ^\top A\right)} = \max_{\|d\| = 1} \| Ad \|
$$
  Therefore, we have $f \in \mathcal C_{\|A\|}^{1, 1}$.

- If $f \in \mathcal C_L^{1, 1}$, we cal use the constant stepsizes $\overline \alpha \in \left(0, \frac{2}{L}\right)$ to reach the global convergence.

##### Lipschitz Continuity via Hessians

Let $f$ be a twice continuous differentiable function. Then, the following two conditions are equivalent:

- $f \in \mathcal C_L^{1, 1}(\mathbb R^n)$
- $\|\nabla^2 f(x)\| \le L$ for any $x \in \mathbb R^n$.

##### Linear Convergence

We say that $(x_k)_k$ converges linearly with rate $\eta \in (0, 1)$ to $x^\ast \in \mathbb R^n$ if there is $\ell \ge 0$ such that

$$
\|x_{k + 1} - x^\ast \| \le \eta \cdot \| x_k - x^\ast \|, \forall k \ge \ell
$$

For example, $x_k = 0.95^k$ has Linear Convergence Property. Under logarithmic, we can get a linear plot of the function. This gives the name of Linear Convergence.

##### Theorem: Rates for Convex Problems

Let $f \in \mathcal C_L^{1, 1}$ and suppose there exists $\mu \gt 0$ such that

$$
\mu \|d \|^2 \le d ^\top \nabla ^2 f(x) d  \le L\|d\|^2, \forall d, \forall x
$$

Let $(x_k)^k$ be generated by the gradient descent and let $x^\ast$ be the solution of $\min_x f(x)$. Then:
$$
(x_k)^k \text{ converges linearly to } x^\ast
$$
Further, setting $\eta = 1 - \frac{M \mu}{2}$ (for some constant $M$), it follows
$$
f(x_k) - f(x^\ast) \le \eta^k  \cdot [f(x_0) - f(x^\ast)]
$$
and we have
$$
\|\nabla f(x_k)\| \le \sqrt{\frac {L}{\mu} \eta^k} \cdot \| \nabla f(x_0)\|\\
\|x_k - x^\ast\| \le \sqrt {\frac {L}{\mu}\eta^k} \|x_0 - x^\ast\|
$$

- In the theorem a stronger notion of convexity is required – the so-called **strong convexity**.

- The constant $M$ depends on the chosen line search procedure:
  $$
  M = \begin{cases}
  \overline \alpha(1 - \frac{L \overline \alpha}{2}), &\text{constant step size } \overline \alpha \in \left(0, \frac{2}{L}\right) \\
  \frac{1}{2L}, &\text{exact line search} \\
  \gamma \min \{1, \frac{2\sigma(1 - \gamma)}{L}\}, &\text{Armijo line search}
  \end{cases}
  $$

#### Perpendicular Steps

when using exact line search, the directions between consecutive steps are perpendicular, i.e.,
$$
d_{k + 1}^\top d_k = 0
$$
this is always true when using exact line search:

$\alpha_k$ is the minimizer of $\phi(\alpha) = f(x_k + \alpha d_k)$. So, $\phi'(\alpha_k) = 0$, which means,
$$
0 = \phi'(\alpha_k) = \nabla f(x_k + \alpha_k d_k)^\top d_k = - d_{k + 1}^\top d_k
$$
For some steep functions, Perpendicular Steps may result in ping-pong effects, which will lead to slow convergence speed.

#### Pros and Cons

##### Pros

- Easy to understand and implement.
- Only need to know the first-order (gradient) information.
- Globally convergent, does not depend on the initial point

##### Cons

- Convergence speed may not be fast enough (linear convergence)

### Newton's Method (in $\mathbb R$)

We want to find the point $x$ where $g(x) = f'(x) = 0$. Newton's Method splits the finding procedure into iterations. At each iteration $k$, we do a Taylor Expansion
$$
g(x) \approx g(x_k) + g'(x_k)(x - x_k)
$$
This formula can be transformed into
$$
x = x_k - \frac{g(x_k)}{g'(x_k)}
$$
Assume $g'(x_k) \gt 0, \forall k$, we can use this $x$ to be the $x_{k + 1}$ and hopefully, we will reach a stationary point.

#### Global Convergence

However, consider $g(x) = \frac{x}{\sqrt {1 + x^2}}$. A trivial root is $x = 0$, but if we start with $x_0 = 1$, Newton's Method will jump into a loop of $\pm 1$. This indicates that Newton's Method does **not** have the the Global Convergence property as Gradient Descent Method.

#### Theorem:  Convergence Newton’s Method (Unidimensional)

If $g$ is twice continuous differentiable and $x^\ast$ is a root of $g$ at which $g'(x^\ast) \ne 0$, then provides that $\|x_0 - x^\ast\|$ is sufficiently small, the sequence generated by the Newton iterations:
$$
x_{k + 1} = x_k - \frac{g(x_k)}{g'(x_k)}
$$
will satisfy
$$
|x_{k + 1} - x^\ast| \le C| x_k - x^\ast|^2
$$
with $C = \sup_x \frac{1}{2}\left| \frac{g''(x)}{g'(x)}\right|$.

- We call this convergence speed quadratic convergence.

#### Quasi-Newton's Methods

One may have already spotted the connections between Newton's Method and Gradient Descent Method. For optimization problems, they are all in the form of
$$
x^{k + 1} = x_k - \alpha f'(x_k)
$$
Strictly speaking, all iterative algorithms with the above form is a quasi-newton's method.

#### Quadratic Approximation

This is another interpretation of Newton's Method. Consider the Second-Order Taylor Expansion of the target function at iteration $k$:
$$
f(x) \approx f(x_k) + f'(x_k)(x - x_k) + \frac{1}{2}f''(x_k)(x - x_k)^2
$$
Treat the second order approximation as a quadratic function of $x$. The minimizer of the Quadratic Approximation is exactly:
$$
x_k - \frac{f'(x_k)}{f''(x_k)}
$$

### Higher-dimensional Newton's Method

Again, we want to minimize the second order approximation in the higher dimensional form:
$$
f(x) \approx f(x_k) + \nabla f(x_k)^\top(x - x_k) + \frac{1}{2}(x - x_k)^\top\nabla f^2(x_k)(x - x_k)
$$
The minimizer is
$$
x = x_k - \left[\nabla^2 f(x_k)\right]^{-1} \nabla f(x_k)
$$
We call
$$
d_k = - \left[\nabla^2 f(x_k)\right]^{-1} \nabla f(x_k)
$$
the **Newton direction**.

Another approach is to consider finding the solution for $\nabla f(x) = 0$.

Using a Taylor expansion at $x_k$, we have:
$$
\nabla f(x) \approx f(x_k) + \nabla^2 f(x_k)(x - x_k)  =: q_k(x)
$$
The solution for $q_k(x) = 0$ is
$$
x = x_k - \left[\nabla^2 f(x_k)\right]^{-1} \nabla f(x_k)
$$

#### Descent Step

It does not escape our eyes that:
$$
\nabla f(x)^\top d = - \nabla^\top f(x) \left[\nabla^2 f(x_k)\right]^{-1} \nabla f(x_k)
$$
For convex problems, we have $\nabla^2 f(x)$ is PSD, hence, **Newton direction** is a decent direction.

#### Procedure of Newton’s Method

1. Initialization: Select an initial point $x^0 \in \mathbb R^n$.

For $k = 0, 1, ...$:

2. Compute the Newton direction $d_k$ which is the solution of the linear system:
   $$
   \nabla^2 f(x_k) d_k = - \nabla f(x_k)
   $$

3. Choose a step size $\alpha_k$ by backtracking line search and calculate
   $$
   x_{k + 1} = x_k + \alpha_k d_k
   $$

4. If $\| \nabla f(x_{k + 1})\| \le \epsilon$, then STOP and $x_{k + 1}$ is the output.

## References

- Lecture Notes 15-17 for MAT3007 (Optimization) by Dr. Andre Milzarek. Institute for Data and Decision Analytics, The Chinese  University of  Hong Kong, Shenzhen.
- Introduction to Nonlinear Optimization. Theory, Algorithms, and  Applications with MATLAB, MOS-SIAM Series Optimization (2014), by Beck.