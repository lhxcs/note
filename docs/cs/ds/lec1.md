# Algorithm Analysis

## Definition

An algorithm is a finite set of instructions that, if followed, accomplishes a particular task. In addition, all algorithms must satisfy the following criteria:

- Input
- Output
- Definiteness
- Finiteness
- Effectiveness

!!! note

    A program is written in some programming language, and does not have to be infinte(operation system)
    An algorithm can be described by human languages, flow charts, some programming languages, or pseudo-code.



## What to Analyze

- Machine & compiler-dependent **run times**
- **Time and space complexities**: machine and compiler-***independent***

> Assumptions:
>
> - instructions are executed sequentially
> - each instruction is simple, and takes exactly one time unit
> - integer size is fixed and we have infinite memory
>
> Typically the following two functions are analyzed: $T_{avg}(N)$ and  $T_{worst}(N)$

Example: $T_{sum}(n)=2n+3$

```c
float sum(float list[],int n){
    float tempsum = 0; /*count = 1*/
    int i;
    for(i=0;i<n;i++)/*count++*/
        tempsum += list[i];/*count++*/
    return tempsum;/*count++*/
}
```



## Asymptotic Notation

$T(N)=O(f(N))$: if there are positive constants $c$ and $n_0$ such that $T(N)\le c \cdot f(N)$ for all $N \geq n_0$

$T(N)=\Omega (g(N))$: if there are positive constants $c$ and $n_0$ such that $T(N)\geq c \cdot g(N)$ for all $N\geq n_0$

$T(N)=\Theta(h(N))$: iff $T(N)=O(h(N))$ and $T(N)=\Omega (h(N))$

$T(N)=o(p(N))$: if $T(N)=O(p(N))$ and $T(N)\neq \Theta (p(N))$



### Rules of Asymptotic Notation

If $T_1(N)=O(f(N))$ and $T_2(N)=O(g(N))$, then:

- $T_1(N)+T_2(N)=max(O(f(N)),O(g(N)))$
- $T_1(N)\times T_2(N)=O(f(N)\times g(N))$

If $T(N)$ is a polynomial of  degree $k$, then $T(N)=\Theta (N^k)$

$log^kN=O(N)$ for any constant $k$. This tells us that **logarithms grow very slowly**

!!! note

    When comparing the complexities of two programs asymptotically, make sure that N is sufficiently large.

