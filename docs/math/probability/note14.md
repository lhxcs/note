# Conditional Probability, Independence, and Combinations of Events



## Conditional Probability

For events $A,B \in \Omega$ in the same probability space such that $P[B]>0$ the conditional probability of $A$ given $B$ is:
$$
P[A|B]=\frac{P[A\cap B]}{P[B]}
$$


## Bayesian Inference

- Conditional probability is at the heart of a subject called *Bayesian inference*.
- Bayesian inference is a way to *update knowledge* after making an observation.

Example: we may have an estimate of the probability of a given event $A$. After event $B$ occurs, we can update this estimate to $P[A|B]$. In this interpretation, $P[A]$ can be thought of as a $prior$ probability: our assignment of the likelihood of an event of interest, $A$, **before** making an observation. $P[A|B]$ can be interpreted as the *posterior* probability of $A$ after observation.



We are given $P[A]$, which is the probability that the event of interest happens. We are given $P[B|A], P[B|\overline{A}]$, which quantify how noisy the observation is. Now we want to calculate $P[A|B]$,  the probability that the event of interest happens given we made the observation. We can proceed as follows:


$$
P[A|B]=\frac{P[A\cap B]}{P[B]}=\frac{P[B|A]P[A]}{P[B]}
$$

$$
P[B]=P[A\cap B]+P[\overline{A}\cap B]=P[B|A]P[A]+P[B|\overline{A}]P[\overline{A}] 
$$

$$
So\, P[A|B]=\frac{P[B|A]P[A]}{P[B|A]P[A]+P[B|\overline{A}]P[\overline{A}]}
$$






## Bayes's Rules and Total Probability Rule

- Bayes's Rule is useful when one wants to calculate $P[A|B]$ but is given $P[B|A]$ instead.
- Total Probability Rule is an application of the strategy of **dividing into cases**. If $A$ happens, the probability that $B$ happens is $P[B|A]$, if $A$ does not happen, the probability that $B$ happens is $P[B|\overline{A}]$

### Generalization

We say that an event $A$ is partitioned into $n$ events $A_1,\cdots, A_n$ if 

- $A=A_1\cup \cdots \cup A_n$ and
- $A_i\cap A_j=\varnothing$ for all $i \neq j$

In other words, each outcome in $A$ belongs to exactly one of the events $A_1,\cdots, A_n$



Now let $A_1,\cdots,A_n$ be a partition of the sample space $\Omega$. Then, the **Total Probability Rule** for any event $B$ is 


$$
P[B]=\sum_i^nP[B\cap A_i]=\sum_i^nP[B|A_i]P[A_i]
$$


while **Bayes' Rule** , assuming $P[B]\neq 0$ is given by:


$$
P[A_i|B]=\frac{P[B|A_i]P[A_i]}{P[B]}=\frac{P[B|A_i]P[A_i]}{\sum_{j=1}^nP[B|A_j]P[A_j]}
$$


## Combinational of Events

### Independent Events

#### Independence

Two events $A,B$ in the same probability space are said to be independent if $P[A\cap B]=P[A]\times P[B]$

The intuition behind this definition is the following:


$$
P[A|B]=\frac{P[A\cap B]}{P[B]}=\frac{P[A]\times P[B]}{P[B]}=P[A]
$$
Thus independence has the natural meaning that "the probability of $A$ is not affected by whether or not $B$ occurs".



#### Mutual independence

Events $A_1,\cdots,A_n$ are said to be <u>*mutually independent*</u> if for every subset $I\subset \left\{ 1,\cdots,n \right\} \ with\ size\ \vert I\vert\ge 2$,


$$
P[\cap_{i\in I}A_i]=\mathop{\Pi}_{i\in I}P[A_i]
$$
For mutually independent events $A_1,\cdots,A_n$, it is not hard to check from the definition of conditional probability that, for any $1\le i\le n$ and any subset $I\subset \left\{1,\cdots,n\right\}\setminus \left\{i\right\}$, we have:


$$
P[A_i|\cap_{j\in I}A_j]=P[A_j]
$$
!!! note

    The independence of every pair of events(so-called pairwise independence) does not necessarily imply mutual independence



### Intersections of Events

**Product Rule**

For any events $A,B$, we have
$$
P[A\cap B]=P[A]P[B|A]
$$


More generally, for any events $A_1,\cdots,A_n$,


$$
P[\cap_{i=1}^nA_i]=P[A_1]\times P[A_2|A_1]\times P[A_3|A_1\cap A_2]\times \cdots \times P[A_n|\cap_{i=1}^{n-1}A_i]
$$


### Unions of Events

**Inclusion-Exclusion**

Let $A_1,\cdots,A_n$ be events in some probability space, where $n\ge 2$. Then, we have
$$
P[A_1\cup \cdots \cup A_n]=\sum_{i=1}^nP[A_i]-\sum_{i<j}P[A_i\cap A_j]+\sum_{i<j<k}P[A_i\cap A_j\cap A_k]-\cdots +(-1)^{n-1}P[A_1\cap A_2\cap \cdots \cap A_n]
$$


- **Mutually exclusive events**: ($A_i\cap A_j=\varnothing$): $P[\cup_{i=1}^nA_i]=\sum_{i=1}^nP[A_i]$

- **Union bound**: $P[\cup_{i=1}^n]\le \sum_{i=1}^nP[A_i]$

This merely says that adding up the $P[A_i]$ can only *over*estimate the probability of the union
