# 数据结构基础理论题集

## 算法分析

- 

```c
if ( A > B ){     
  for ( i=0; i<N*2; i++ )         
    for ( j=N*N; j>i; j-- )             
      C += A; 
}
else {     
  for ( i=0; i<N*N/100; i++ )         
    for ( j=N; j>i; j-- ) 
      for ( k=0; k<N*3; k++)
        C += B; 
} 
```

The lowest upper bound of the time complexity is $O(N^3)$

本来看到`for`循环里有个`n*n`，直接以为复杂度是$O(N^4)$,但是要注意当`i`到`n`时下层循环就进不去了！

- 分析复杂度：

$$
T(1)=1,T(N)=T(N/3)+1\\T(1)=1,T(N)=3T(N/3)+1
$$

可以用直接代入法，直到$N/3^K$为$1$：
$$
T(N)=T(N/3)+1=\cdots=T(N/3^k)+k,k=clogN
$$
所以第一个式子的时间复杂度为$O(logN)$,类似地，第二个式子的时间复杂度为$O(N)$



