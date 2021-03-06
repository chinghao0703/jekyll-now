---
layout: post
title: Research update- QP and ecology
mathjax: true
comments: true
---

Recently we posted an [article on arxiv](https://arxiv.org/abs/1809.04221) about a suprising duality between optimization problem with inequality constraints, particularly [quadratic programming (QP)](https://en.wikipedia.org/wiki/Quadratic_programming), and one of the most famous models of ecological dynamics, MacArthur's Consumer Resource Model (MCRM). Here I want to provide a very gentle introduction to some basic ideas in optimization and convex duality, which we invoked upon in this paper, to appease those who are distracted by these jargons.  In the following, I draw heavily on the awesome book by [Stephen Boyd](https://web.stanford.edu/~boyd/cvxbook/) and the CVX lecture notes from [Ryan Tibshirani](http://www.stat.cmu.edu/~ryantibs/index.html). 



## Definition of QP

Simply put, optimization problems involve finding the extreme values (maximum of minimum) of a given (real) function $f(x)$ within an allow set of $x$. It formally takes the following form:

$$ 
\begin{equation}
\begin{aligned}
& \underset{x\in D}{\text{min}}
& & f(x) \\
& \text{subject to}
& & h_i(x) \leq 0, \; i = 1, \ldots, m\\
&&& l_j(x) =0, \; j=1, \ldots, r,
\end{aligned}
\end{equation}$$

where $D = \text{dom}(f)\cap\bigcap_{i=1}^m \text{dom}(h_i)\cap\bigcap_{j=1}^r \text{dom}(l_j)$. The function $f$ is called the objective function while $h_i$ and $l_j$ are the constraints. We call a problem convex when $f$ and $h_i$ are all convex functions of $x$ and $l_j$ is an affine (therefore convex) function of $x$. In this paper, we're concerned with a particular type of convex optimization called the (convex) quadratic programming (QP) where $f(x)=c^T x+ \frac{1}{2}x^T Q x$ with $Q\succeq 0$ being a positive semi-definite matrix. Formally, a convex QP is defined as:

$$ 
\begin{equation}
\begin{aligned}
& \underset{x}{\text{min}}
& & c^T x+ \frac{1}{2}x^T Q x \\
& \text{subject to}
& & D_i x \leq d_i, \; i = 1, \ldots, m\\
&&& A_j(x) =b_j, \; j=1, \ldots, r,
\end{aligned}
\end{equation}$$


Note that if $Q\nsucceq 0$, this problem is not convex!

### Examples of QP

QP has applications across disciplines. For in example, one can formulate [**portfolio optimization**](https://en.wikipedia.org/wiki/Portfolio_optimization) as QP:


$$ 
\begin{equation}
\begin{aligned}
& \underset{x}{\text{min}}
& & \mu^T x -\frac{\gamma}{2}x^TQx \\
& \text{subject to}
& & x\ge 0\\
&&& 1^T x =1,
\end{aligned}
\end{equation}
$$

with the following interpretation:

  * $\mu$: expected assets' return
  * $Q$: covariance matrix between assets' return
  * $\gamma$: risk aversion
  * $x$: percentage portfolio holdings

Another notable example is the [Support Vector Machine](https://en.wikipedia.org/wiki/Support_vector_machine):

 Given $y\in\{-1,1\}, X \in \mathbb{R}^{n\times p}$ with rows $x_1,\cdots, x_n$. A support vector machine (SVM) problem is defined as:

 $$ 
\begin{equation}
\begin{aligned}
& \underset{\beta, \beta_0,\xi}{\text{min}}
& & \frac{1}{2}||\beta||_2^2 + C\sum_{i=1}^n \xi_i \\
& \text{subject to}
& & \xi_i\ge 0,\; i = 1, \ldots, n\\
&&& y_i(x_i^T\beta+\beta_0)\ge 1-\xi_i, \; i = 1, \ldots, n
\end{aligned}
\end{equation}
$$

One might wonder why convexity matters? One simple answer is that **for convex problems, any local minimizer is a global minimizer**. This means that in the optimization procedure, as long as we're *going down the hill* and agree to stop when we can't go any further, then we've hit the global minimum. There are of course gazillions of nice properties of convex functions in terms of optimization which I refer readers to [Stephen Boyd's](https://web.stanford.edu/~boyd) book and course on [Convex Optimization](https://web.stanford.edu/~boyd/cvxbook/). 

Now having defined QP, I want to switch gear to talk about another concept that was invoked upon in this paper: convex duality.

## Convex Duality

Physicists are not that unfamiliar with the concept of duality (e.g.  AdS/CFT correspondence or gauge/gravity duality). Like many such problems, duality allows us to map a problem to its dual where solutions are more easily found. Convex duality is no exception! Recall that for convex problems **every local minimizer is a global minimizer** In physics, we're often interested in finding the ground state of a given physical system. This usually involves minimizing its energy function (i.e. Hamiltonian) with respect to the parameters we have control over. If such energy-minimization problem is convex, the property we just stated immediately implies that no matter what dimensionality the problem is defined in, one would never get stuck by local minima or saddales (since there's no such a thing at all!) What's even more interesting is that **the Lagrange dual of an optimization problem is always convex**! We'll see that duality also allows us to explore the property of solutions even without solving the problem itself!

### Lagrange dual and the dual problem ###

Consider a general optimization problem defined above and restated here which we call the **primal problem** (note that $f$ need not be convex)


$$ 
\begin{equation}
\begin{aligned}
& \underset{x}{\text{min}}
& & f(x) \\
& \text{subject to}
& & h_i(x) \leq 0, \; i = 1, \ldots, m\\
&&& l_j(x) =0, \; j=1, \ldots, r,
\end{aligned}
\end{equation}$$


We define the Lagrangian as 

$$
\begin{equation}L(x,u,v) =  f(x) + \sum_{i=1}^m u_i h_i(x) + \sum_{j=1}^r v_jl_j(x),
\end{equation}
$$

where $u\in\mathbb{R}^m, v\in\mathbb{R}^r$, with $u\ge 0$. One can show that for all $u\ge 0$ and $v$, $L(x,u,v)\le f(x)$ by applying the non-positivity of $h_i$ and zeroness of $l_j$. Let $C$ be the primal feasible set (i.e. the set of $x$ where contraints stated above are satisfied) and $f^{\star}$ be the primal optimal value (i.e. solution to the primal problem), we have

$$
\begin{equation}
f^{\star}  \ge \min_{x\in C} L(x,u,v)\ge \min_x L(x,u,v)\equiv g(u,v).
\end{equation}
$$

This motivates the definition of the **Lagrange dual function**, $g(u,v)\equiv \min_{x} L(x,u,v)$, which gives the lower bound on primal optimal $f^{\star}$ for all $u\ge 0$ and $v$, called **dual feasible** $u, v$. Hence, the tightest lower bound we can get is the maximum of the Lagrange dual, which defines the **dual problem**:

$$ 
\begin{equation}
\begin{aligned}
& \underset{u,v}{\text{max}}
& & g(u,v) \\
& \text{subject to}
& &u\ge 0\\
\end{aligned}
\end{equation}$$


Similarly, denote $g^\star$ as dual optimal (i.e. solution to the above dual problem), we have a very important property called **weak duality**: $f^\star \ge g^\star$. Note that since $g$ is concave in $(u,v)$ and $u\ge 0$ is an affine (therefore convex) constraint, the dual problem is a concave maximization probelm (hence a convex optimization problem). On ther other hand, if the ineqality turns out to be an equality (i.e. $f^\star = g^\star$), we say **strong duality** holds. 

A sufficient condition for strong duality is the **Slater's condition**: if the primal problem is convex, and there exists at least one strictly feasible $x\in\mathbb{R}^n$, namely, $\exists x\in\mathbb{R}^n $  s.t.
$$
\begin{equation}
\begin{aligned}
&h_i(x) < 0, \; i = 1, \ldots, m\\
& l_j(x) =0, \; j=1, \ldots, r,
\end{aligned}
\end{equation}
$$

then **strong duality holds**. As one shall see, this condition will establish the necessity and sufficiency of primal-dual optimality through the KKT conditions to be introduced later.

Let's recap:
  * Dual problem is always convex even if primal is not.
  * Primal and dual optimal always satisfy weak duality $f^\star \ge g^\star$.
  * If Slater's condition is satisfied, strong duality holds (i.e. $f^\star = g^\star$)


## Karush-Kuhn-Tucker (KKT) conditions ##

Again, consider the general optimization problem: 


$$ 
\begin{equation}
\begin{aligned}
& \underset{x}{\text{min}}
& & f(x) \\
& \text{subject to}
& & h_i(x) \leq 0, \; i = 1, \ldots, m\\
&&& l_j(x) =0, \; j=1, \ldots, r,
\end{aligned}
\end{equation}$$

The Karush-Kuhn-Tucker (KKT) conditions are:

  * $0\in \partial f(x) + \sum_{i=1}^m u_i \partial h_i(x) + \sum_{j=1}^r v_j\partial l_j(x)$ (**stationarity**)
  * $u_i h_i(x) = 0,\, \forall i$ (**complementary slackness**)
  * $h_i(x)\le 0, l_j(x)=0,\,\forall i,j$ (**primal feasibility**)
  * $u_i\ge 0,\forall i$ (**dual feasibility**)

  Note that we use the notation $\partial f$ to denote the subdifferential of $f$ (i.e. set of all subgradients of $f$). Particularly, a subgradient of a convex function $f$ at $x$ is any function $g\in\mathbb{R}^n$ such that

$$
\begin{equation} 
f(y)\ge f(x) + g^T (y-x),\text{  for any } y.
\end{equation}
$$

In convex analysis, optimality can often be characterized by the objective function's subgradient and monotonicity. For example, 

Let $f$ be a function (not necessarily convex), 

$$
\begin{equation} f(x^\star)=\min_x f(x)\, \Longleftrightarrow 0\in\partial f(x^\star),
\end{equation}
$$

meaning that **$x^\star$ is a minimizer of $f$ if and only if 0 is a subgradient of $f$ evaluated at $x^\star$**. In addition to this, subgradient is related to [convex geometry](https://en.wikipedia.org/wiki/Convex_geometry) which I refer readers to [Rockafellar's masterpiece on Convex Analysis](https://press.princeton.edu/titles/1815.html). Final word before coming back to KKT, if $f$ is differentiable, $\partial f = \{\nabla f\}$. 

One of the most important properties of the KKT conditions is the following:


For a problem with **strong duality** (assuming Slater condition holds), 

$$ 
\begin{equation}
\begin{aligned}
& x^\star \text{ and } u^\star, v^\star \text{ are primal and dual solutions} \\
& \Longleftrightarrow  x^\star \text{ and } u^\star, v^\star \text{ satisfy KKT conditions}
\end{aligned}
\end{equation}$$

Note that KKT conditions are always sufficient for optimality. The necessary part is, in this case, by virtue of Slater's condition. 

Now suppose strong duality holds. Given dual optimal $u^\star, v^\star$, any primal solution satisfies the KKT conditions. Particularly, the stationarity condition reads:

$$
\begin{equation} 
0\in \partial f(x^\star) + \sum_{i=1}^m u_i^\star \partial h_i(x^\star) + \sum_{j=1}^r v_j^\star\partial l_j(x^\star)
\end{equation}
$$

By definition, this implies that $x^\star$ solves $\min_x L(x, u^\star, v^\star)$!! If this problem happens to have a unique minimizer, then the corresponding solution must be the primal solution! In other words, we can use KKT to characterize or even compute the primal solutions. 

Before concluding, I want to introduce an important concept-- the **duality gap**.  Let $x$ and $u, v$ be primal and dual feasible, respectively. $f(x) -g(u,v)$ is called the duality gap between $x$ and $u,v$. Since $f^\star \ge g(u,v)$, we have 

$$
\begin{equation}f(x) - f(x^\star) \le f(x) - g(u,v).
\end{equation}
$$

An immediate corollary is that **zero duality gap implies optimality!**
