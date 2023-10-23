---
changelog: True
---

# Variance and Total Expectation



## Random Variables: Variance and Covariance

### Variance

For a r.v. $X$ with expectation $E[X]=\mu$, the **variance** of $X$ is defined to be:
$$
Var(X)=E[(X-\mu)^2]
$$
The square root $\sigma(X)=\sqrt{Var(X)}$ is called the standard deviation of $X$.



- The point of taking the square root of variance is to put the standard deviation “on the same scale” as the r.v. itself.

**Theorem**: For a r.v. $X$ with expectation $E[X]=\mu$, we have $Var(X)=E[X^2]-{\mu}^2$

**Property**: For any random variable $X$ and constant $c$, we have $Var(cX)=c^2Var(X)$



### Sum of  Independent Random Variable

**Theorm**: For independent variables $X,Y$, we have $E[XY]=E[X]E[Y]$.

**Theorem**: For independent random variables $X,Y$, we have
$$
Var(X+Y)=Var(X)+Var(Y)
$$

> It is very important to remember that **neither** of the above two results is true in general when *X**,**Y* are not independent



### Covariance and Correlation

**Covariance**: The covariance of random variables $X$ and $Y$, denoted $Cov(X,Y)$ is defned as
$$
Cov(X,Y)=E[(X-\mu_x)(Y-\mu_Y)]=E[XY]-E[X]E[Y]
$$

- If $X,Y$ are independent, then $Cov(X,Y)=0$. However, the converse is **not** true

- $Cov(X,X)=Var(X)$

- Covariance is bilinear: for any collection of random variables$\left\{X_1,\cdots,X_n\right\},\left\{Y_1,\cdots,Y_n\right\}$ and fixed constants$\left\{a_1,\cdots,a_n\right\},\left\{b_1,\cdots,b_n\right\}$,
  $$
  Cov(\sum_{i=1}^na_iX_i,\sum_{j=1}^mb_jY_j)=\sum_{i=1}^n\sum_{j=1}^ma_ib_jCov(X_i,Y_j)
  $$
  

- For general random variables $X$ and $Y$: 

$$
Var(X+Y)=Var(X)+Var(Y)+2Cov(X,Y)
$$

<br></br>

While the sign of $Cov(X,Y)$ is informative of how $X$ and $Y$ are associated, its magnitude is difficult to interpret. A statistic that is easier to interpret is correlation:

**Correlation**: Suppose $X$ and $Y$ are random variables with $\sigma(X)>0$ and $\sigma(Y)>0$. Then, the correlation of $X$ and $Y$ is defined as
$$
Corr(X,Y)=\frac{Cov(X,Y)}{\sigma(X)\sigma(Y)}
$$
Correlation is more useful than covariance because the former always ranges between *−*1 and +1, as the following theorem shows:

For any pair of random variable $X$ and $Y$ with $\sigma(X)>0$ and $sigma(Y)>0$,
$$
-1\le Corr(X,Y)\le1
$$
