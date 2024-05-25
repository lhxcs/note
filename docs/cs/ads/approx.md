# Approximation Algorithms

在前一节我们已经知道，对于 NP-hard 的问题，我们目前不能找到一个多项式时间的算法来解决它。但是我们可以在多项式时间内找到该问题的一个近似解，这就是我们这节要介绍的近似算法。

**$\rho$-approxitmation algorithm**

- Guaranteed to run in poly-time.
- Guaranteed to solve arbitrary instance of the problem.
- Guaranteed to find solution within ratio $\rho$ of true optimum.

我们的挑战在于，在不知道具体的解是多少时，我们需要证明一个解是 $\rho$ 近似的。

## Load Balancing Problem

!!! Example "Definition"

    Input: $m$ identical machines, $n$ jobs, job $j$ has processing time $t_j$.

    - Job $j$ must run contiguousyly on one machine.
    - A machine can process at most one job at a time.

    **Def**: Let $J(i)$ be the subset of jobs assigned to machine $i$. The **load** of machine $i$ is $L_i=\sum_{j\in J(i)}t_j$.

    **Def**: The **makespan** is the maximum load on any machine $L=\max _i L_i$.

    Load balancing: **Assign each job to a machine to minimize makespan**.

我们现在需要为 Load Balancing 设计一个近似算法。首先我们很自然地考虑一个贪心策略：我们把 job $j$ 分配给目前 load 最小的 machine。显然这个算法的结果与我们 job 的输入顺序有关。

接下来我们分析这个贪心算法解的近似程度。我们需要将贪心算法得到的 makespan $T$ 和最优解 $T^{*}$ 进行比较。首先我们需要为最优解找一个下界。

!!! note "Lemma"

    1. The optimal makespan is at least $T^{*}\ge \frac{1}{m}\sum_j t_j$.
    2. The optimal makespan is at least $T^{*}\ge \max_j {t_j}$

    这两个 lower bound 都比较显然，之所以引入第二个 lower bound，是因为第一个 Bound 在一种输入的情况下会很松：有一个 job 的时间远大于其它所有 job 时间的和。

现在我们可以证明贪心算法是一个二近似算法。

**Theorem**: Greedy algorithm is a 2-approximation.

由于我们不知道最优解的具体值，我们需要将算法得到的解和我们已知的和最优解有关的信息，in this case, 两个 lower bound。所以我们的想法是将 $T$ 和 lower bound 联系起来。

我们考虑分配给 $M_i$ 的最后一个工作 $j$。分配时 $M_i$ 的 load 一定是最小的(贪心算法保证了这一点)。因此我们有如下不等式：

$$
\sum_k T_k \ge m(T_i-t_j)
$$

因此对于算法得到的解 $T$, 我们有

$$
T = (T-t_j)+t_j \le \frac{1}{m}\sum_k T_k +T^{*} \le 2T^{*}
$$

完成证明。

接下来我们要问的一个问题是近似比 $2$ 是否 tight? 如果能给出一个解使得 $T=2T^{*}$, 那么答案显然是肯定的。

!!! Example "tight example"

    $m$ machines, $m(m-1)$ jobs with length $1$, one job of length $m$.

    $T=2m-1, T^{*}=m$.

现在我们考虑如何优化算法使得近似比下降。贪心算法的瓶颈在于，如果先输入耗时短的工作，算法会将这些工作均匀分布给所有机器，这样的话最后输入耗时很长的工作会大大影响算法的解。因此我们考虑先对输入从大到小排个序。

**Lemma**: If there are more than $m$ jobs, then $T^{*}\ge 2t_{m+1}$.

如果 job 数目小于 $m$, 我们显然能得到最优解。因此我们只考虑 job 数目大于 $m$ 的情况。

由于我们对 job 的时间排了序，考虑前 $m+1$ 个时间，所有 job 的时间都大于 $t_{m+1}$。由于我们有 $m+1$ 个工作和 $m$ 台机器，由鸽巢原理，必定有一个机器被分配到至少两个的工作，因此 $T^{*}\ge 2t_{m+1}$。

**Theorem**: Algorithm Sorted-Balance is a $\frac{3}{2}$ approximation algorithm.

??? Exercise "Prove the theroem"

    和贪心算法的证明一模一样。


## Center Selection

!!! Example "Description"

    Input: Set of $n$ sites $s_1,s_2,\cdots,s_m$ and integer $k\ge 0$.

    Select $k$ centers $C$ so that maximum distance from a site to nearest center is minimized.

    distance satisfy the triangle inequality.

    Some notations:

    $dist(s_i, C)=\min _{c\in C}dist(s_i,c)$: distance from $s_i$ to closest center.

    $r(C)=\max _i dist(s_i,C)$: smallest covering radius.

    Goal: Find set of centers $C$ that minimizes $r(C)$, subject to $\left|C\right|=k$.

假设我们知道存在 $k$ 个 center $C^{*}$, 满足 $r(C^{*})\le r$(这里我们只知道最优半径 $r$)。那么我们能够找到 $k$ 个 covering radius 最多是 $2r$ 的 centers $C$。

考虑任意的 site $s\in S$, 一定存在一个 center $c^{*}\in C^{*}$ 覆盖了 $s$，覆盖半径最多是 $r$。我们的想法是将 $s$ 作为一个 center, 并且 $s$ 能够 cover 所有 $c^{*}$ cover 的点。显然我们只需要将 covering radius 设置成 $2r$ 即可。

因此我们可以得到一个算法：从空集开始，每次任意选择一个 site 加入 center 集合，并删除所有与该 site 距离小于 $2r$ 的点，直到删完为止，算法返回一个 center 集合 $C$。如果该集合的个数小于等于 $k$, 那么我们得到了我们想要的解，并且该解是 $2$ 近似的。

但是，我们能否保证算法返回集合的个数不超过 $k$ 呢？事实上，我们有如下的定理：

Suppose the algorithm selects more than $k$ centers. Then for any set $C^{*}$ of size at most $k$, the covering radius is $r(C^{*}) > r$

**Proof**: 我们采用反证法。假设 covering radius $r(C^{*})\le r$, 对于贪心算法选出来的 $c\in C$, 一定存在一个 $c^{*}\in C^{*}$, 使得 $dist(c, c^{*})\le r$。我们的目标是推出矛盾，即 $\left| C^{*}\right| > k$。因此我们考虑证明每一个 $c\in C$, 只有一个 $c^{*}\in C^{*}$, 使得 $dist(c, c^{*})\le r$。如果得证，那么 $\left| C^{*}\right| \ge \left| C\right| > k$，成功推出矛盾。

??? note "Exercise: Finish the proof"

    Hint: 三角不等式以及 $C$ 中点距离的性质。

接下来我们要给出不知道最优解 $r$ 情况下的算法。我们每次选择距离当前 center 集合最远的点加入 center。
我们可以证明：

This greedy algorithm returns a set $C$ of $k$ points such that $r(C)\le 2r(C^{*})$, where $C^{*}$ is an optimal set of $k$ points.

我们采用反证法，假设 $s$ 距离 $C$ 中每个点距离都大于 $2r$, 我们考虑算法的中间步骤，假设我们已经得到的 center 集合是 $C^{'}$, 并且我们下一个选择加入 center 集合的点是 $c^{'}$, 根据假设，我们可以得到以下不等式：
$$
dist(c^{'},C^{'})\ge dist(s, C^{'})\ge dist(s, C) > 2r
$$

这就回到了已知最优解贪心算法的形式，我们每次选择加入 center 集合的 site 都在当前 center $2r$ 距离以外，由于最后的 center 集合 $C$ 还有个点 $s$ 距离 $2r$, 因此算法返回的 center 集合个数大于 $k$ 造成矛盾。 

## Set Cover

!!! Example "Problem Description"

    与前一节介绍的 Set Cover 问题不同之处在于，我们对每个集合 $S_i$ 赋予一个权重 $\omega_i\ge 0$, 我们要找到一个集合覆盖，使得权重和最小。


我们考虑一个贪心算法：我们每次选一个集合，并希望下一次选的集合 **make the most progresss**。我们可以定义 $\frac{\omega_i}{\left|S_i\cap R\right|}$, 其中 $R$ 是未被覆盖的集合，可以很直观地理解这个定义，代表着 cost per element covered,即我们希望用尽可能少的权重覆盖尽可能多的点集。

现在我们想要知道贪心算法产生的解会比最优解 $\omega^{*}$ 大多少？

首先我们定义 $c_s=\frac{w_i}{\left|S_i\cap R\right|}$c, 代表我们选中的集合中每个元素的代价，很显然，$\sum_{S_i\in S} w_i=\sum_{s\in U}c_s$。
接下来我们希望给出 $\sum_{s\in S_k}c_s$ 的一个上界, 并且该上界是 $\omega_i$ 的多项式，代表着如果我想覆盖一些 cost, 我必须要多少的 weight。

??? Question "TO-DO"


## The Pricing Method: Vertex Cover

!!! Example "Problem Description"

    Weighted vertex cover: Given a graph $G$ with vertex weights, find a vertex cover of minimum weight.

Pricing Method 的定义：每一条边肯定会被某些顶点覆盖，对于边 $e=(i,j)$, 该边为使用点 $i,j$ 付出了 $p_e\ge 0$ 的代价。

Fairness: Edges incident to vertex $i$ should pay $\le w_i$ in total. 

**Lemma**: For any vertex cover $S$ and any fair prices $p_e$: $\sum_e p_e\le w(S)$


??? note "Proof"

    我们考虑任意一个 vertex cover $S^{*}$.
    根据 fiarness 的定义，对所有属于 $S^{*}$ 的顶点， $\sum_{e=(i,j)}p_e\le w_i$

    将这个不等式同时对 $S^{*}$ 中的所有点求和，得到 $\sum_{i\in S^{*}}\sum_{e=(i,j)}p_e\le\sum_{i\in S^{*}} w_i=w(S^{*})$

    显然左边的和式中，右边被算了不止一次，因此可以得到 $\sum_{e\in E}\le w(S^{*})$.


## The Knapsack Problem

现在我们要讨论一个问题，对于该问题，我们可以设计出一个多项式时间的近似算法。

!!! Example "Problem Description"

    We have $n$ items to pack in a knapsack, each item has two integer parameters: a weight $w_i$ and a value $v_i$. Given a knapsack capacity $W$, find a subset $S$ of items of maximum value subject to the restriction that the total weight of the set should not exceed $W$.

对于该算法，我们的输入是问题的输入加上一个精度 $\epsilon$, 算法会找到一个 $(1+\epsilon)$ 近似的解，并且对于任意固定的 $\epsilon$, 算法的运行时间是多项式的。但需要注意的是，运行时间关于 $\epsilon$ 并不是多项式的，当我们不断改变 $\epsilon$ 的值，使得近似算法的解不断接近最优时，算法就不再是一个多项式时间的算法。我们称这个算法是 polynomial-time approximation scheme(**PTAS**).

回顾使用动态规划求解背包问题，算法运行时间是个伪多项式 $O(nV)$, 同时注意到 $V\le nv_{max}$, 所以该算法复杂度可以写成 $O(n^2v_{max})$。

注意到对于伪多项式时间算法，如果 $v$ 很小的话，我们是可以在多项式时间内解决的。因此一个想法就是对于很大的 $v$, 我们先把 $v$ 相对于一个参数 $b$ 凑整，即 $\widetilde{v}_i=\lceil\frac{v_i}{b}\rceil b$, 这个新的值 $\widetilde{v}_i$ 与原先的 value 相差并不是很多 ($v_i \le \widetilde{v}_i \le v_i + b$)。在我们确保了所有新的 value 都有个因数 $b$ 之后，我们就可以通过将所有 value 同除一个 $b$, 得到 $\hat{v}_i=\frac{\widetilde{v}_i}{b}$作为原先 dp 算法的输入。

最后，我们直接给出 $b$ 的值，$b=\frac{\epsilon}{n}\max_i v_i$ (这里我们假定 $\frac{1}{\epsilon}$ 是一个整数)。

现在我们首先证明背包问题的近似算法对于固定的 $\epsilon\ge 0$, 是一个多项式时间的算法。

- rounding 取整的过程显然可以在多项式时间内完成。
- 考虑动态规划的步骤，运行时间是 $O(n^2\max_i v_i)$。因此该算法是否是多项式时间的算法就取决于 $\max_i v_i$。假设 $v_j=\max_i v_i$, 我们可以得到 $\max_i \hat{v_i}=\hat{v_j}=\lceil\frac{v_j}{b}\rceil=\frac{n}{\epsilon}$。因此我们的算法运行时间为 $O(\frac{n^3}{\epsilon})$。

最后，我们需要证明该算法提供了一个 $(1+\epsilon)$ 的近似比。即：

假设近似算法给出解集 $S$，最优解集为 $S^{*}$, 那么 $(1+\epsilon)\sum_{i\in S}v_i\ge \sum_{i\in S^{*}}v_i$。

**Proof**:
首先我们要分清楚，$S$ 是近似算法的解，即最大化了 $\sum\widetilde{v_i}$, $S^{*}$ 是原问题的解，最大化了没有 rounding 的价值 $\sum v_i$。

首先我们有
$$
\sum_{i\in S}\widetilde{v_i}\ge\sum_{i\in S^{*}}\widetilde{v_i}
$$

并且我们有如下不等式
$$
\sum_{i\in S^{*}}v_i\le \sum_{i\in S^{*}}\widetilde{v_i}\le \sum_{i\in S}\widetilde{v_i}\le \sum_{i\in S}(v_i+b)\le nb+\sum_{i\in S}v_i
$$

接下来为了得到最终的结果，我们需要建立 $nb$ 和 $\sum_{i\in S}v_i$ 的联系。我们设最大元素为 $v_j=\frac{2nb}{\epsilon}$, 并且$v_j=\widetilde{v_j}$($v_j$本身就有 $b$ 因子，round后结果一样)。因此我们有 $\sum_{i\in S}v_i\ge \sum_{i\in S}\widetilde{v_i}-nb\ge \widetilde{v_j}-nb$, 即 $nb\le \epsilon \sum_{i\in S}v_i$, 因此得证：
$$
\sum_{i\in S^{*}}v_i\le nb+\sum_{i\in S}v_i \le (1+\epsilon)\sum_{i\in S}v_i
$$