# Introduction to Optimization

## General Formulation of  The Optimization Problem

Let $x$ be an $n$-dimensional real vector: $x=(x^{(1)},……,x^{(n)})^T \in \mathbb R^n$

and $f_0(x),……, f_m(x)$ be some real-valued functions defined on a set $S\in \mathbb{R}^n$. We consider different variants of the following general minimization problem:


$$
minf_0(x)\\s.t. \ f_j(x)\leq0,\ j=1……m,\\x\in S
$$

- We call $f_0(x)$ the **objective** function of our problem

- The vector function $f( x)=(f_1(x),……,f_m(x))^T$ is called the vector of **functional constraints**(泛函约束).

- The set $S$ is the **basic** feasible set(基本可行解)
- The set $Q =\left\{x\in S| f_j(x)\le 0, j=1……m\right\}$ is the **entire** feasible set of the problem.

There exists a natural classification of the types of minimization problems:

- constrained problems: $Q\subset \mathbb{R}^n$
- unconstrained problems: $Q \equiv \mathbb R^n$
- smooth problems: all $f_j(x)$ are differentiable
- nonsmooth problems: there are several non-differentiable components $f_k(x)$  （例如绝对值函数）
- linearly constrained problems: the functional constraints are **affine** like:



$$
f_j(x)=\sum_{i=1}^na_j^{i}x^{(i)}+b_j\equiv<a_j,x>+b_j,j=1……m
$$


- linear optimization problems: $f_0(\cdot)$ is also affine
- **quadratic** optimization problem: $f_0(\cdot)$ is quadratic(二次)

There is also a classification based on properties of the feasible set

- called feasible if $Q\neq\varnothing$
- called **strictly feasible**, if there exists an $x\in int\ Q$（内点） such that $f_j(x)<0(or>0)$ for all inequality constraints and $f_j(x)=0$ for all equality constraints(***Slater condition***) 

Finally, we distinguish different types of solutions to the problem:

- $x^*$ is called the global optimal solution if $f_0(x^*)\le f_0(x)$ for all $x\in Q$
- called a **local optimal solution** if $\exists \delta$, for all $x \in NBR(x^*,\delta)\cap Q$, we have $f_0(x^*)\le f_0(x)$

Example:

Let our initial problem be as follows:
$$
Find\,  x\in \mathbb R^n \, such \, that\, f_j(x)=a_j\,j=1……m
$$
Then we can consider the problem
$$
\mathop{min}\limits_{x}\sum_{j=1}^m(f_j(x)-a_j)^2
$$


Sometimes our decision variables must be integers. This can be described by the following constraint:
$$
sin(\pi x^{(i)})=0
$$


## Interplay of Optimization and Machine Learning

