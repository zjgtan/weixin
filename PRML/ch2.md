



2.5 Nonparametric Methods

首先介绍了基于直方图的密度估计方法。

1. 将空间划分为宽度为$\Delta_i$的桶
2. 每个分桶的概率密度为$p_i=\frac{n_i}{N\Delta_i}$

通过例子发现，密度估计曲线的平滑程度受$\Delta$或每个分桶内样本数的控制。因此，引出了两种非参估计方法。

1. kernel density estimation

   为了估计$p(x)$，假设$x\in R$。

   假设观察到$N$个样本，其中$K$个样本落在邻域R中的概率为
   $$
   Bin(K|N,P)=\frac{N!}{K!(N-K)!} P^K (1-P)^{N-K}
   $$
   所以，$E[K/N]=P$，$var[K/N]=P(1-P)/N$，当$N$足够大时，$K=NP$。

   同时，邻域R的概率为 $P=\int_{R} p(x)dx$，当$R$充分小时，$P=p(x)V$，所以，$p(x)=\frac{K}{NV}$

   具体的，假设R为$k(u)=\begin{cases} 1, &  |u_i| \le 1/2 \\ 0, & \text{otherwise} \end{cases}$，那么落在以$x$为中心点的R邻域的样本数为
   $$
   K=\sum_{n=1}^N k(\frac{x-x_n}{h})
   $$
   则，$p(x)=\frac{1}{N} \sum_{n=1}^N \frac{1}{h^D} k(\frac{x-x_n}{h})$，也即通过看数据集中与$x$相似的样本的概率作为$x$的概率。

   也可以用更平滑的核函数，则
   $$
   p(x)=\frac{1}{N} \sum_{n=1}^N \frac{1}{(2\pi h^2)^{1/2}}\exp\left( -\frac{|x-x^n|^2}{2h^2} \right)
   $$

2. Nearest-neighbour methods

   NN方法则是看与$x$最近邻的K个样本所在邻域R的面积$V$，求得
   $$
   p(x)=\frac{K}{NV}
   $$
   



















2.4: 证明Binomial distribution的均值和方差为
$$
E[m]=\sum_{m=0}^N m Bin(m|N,\mu)=N \mu
$$

$$
var[m]=\sum_{m=0}^N (m-E[m])^2 Bin(m|N,\mu)=N \mu (1-\mu)
$$

方法一：因为$E[\sum_{i=1}^N x_i]=\sum_{i=1}^N E[x_i]$，$var[\sum_{i=1}^N x_i]=\sum_{i=1}^N var[x_i]$，同时$m=\sum_{i=1}^N x_i$，$x_i$服从$Bern(x|\mu)$，所以，$E[m]=N\mu$，$var[m]=N \mu (1-\mu)$

方法二：因为$\sum_{m=0}^N Bin(m|N,\mu)=1$，将等式两边对$\mu$求导，
$$
\sum_{m=0}^N \binom{N}{m} \left( \frac{m}{\mu} \mu^m (1-\mu)^{N-m} - \frac{N-m}{1-\mu} \mu^{m} (1-\mu)^{N-m} \right)=0
$$
整理得 $(1-\mu) E[m] = N\mu-\mu E[m]$，所以，$E[m]=N\mu$



