###  Real-Time Bidding by Reinforcement Learning in Display Advertising
#### 背景：
广告智能出价通过动态分配有限预算下最大化价值。本文将出价决策过程归结为RL问题，通过建模状态转移过程，构建了MDP框架，用于学习最优出价策略。
#### MDP基础：
MDP由元组$<S, A, P, r, \gamma>$构成，其中

1. $S$是状态集合
2. $A$是动作集合
3. $\gamma$是折扣因子
4. $r(s,a)$是奖励函数，如果只取决于状态，可退化为$r(s)$
5. $p(s^{'}|s,a)$是状态转移函数，表示在状态$s$执行动作$a$后到达状态$s^{'}$的概率

策略$\pi=P(A_i=a|S_i=a)$，表示在输入状态为$s$情况下，采用动作$a$的概率。

状态价值函数$V^{\pi}(s)$表示基于策略$\pi$，从状态$s$出发获得的期望回报。$V^{\pi}(s)=E_{\pi}[G_t|S_t=s]$

动作价值函数$Q^{\pi}(s,a)$表示基于策略$\pi$，从状态$s$执行动作$a$得到的期望回报。$Q^{\pi}(s,a)=E_{\pi}[G_t|S_t=s,A_t=a]$

$V^{\pi}(s)=\sum_{a \in A} \pi(a|s)Q^{\pi}(s,a)$         $Q^{\pi}(s,a)=r(s,a)+\gamma \sum_{s \in S{'}}P(s^{'}|s,a)V^{\pi}(s^{'})$

**贝尔曼期望方程：递推公式**
$$
Q^{\pi}(s,a)=E_{\pi}[R_t+\gamma Q^{\pi}(s^{'}, a^{'})|S_t=s,A^t=a] \\
=r(s,a)+\gamma \sum_{s^{'}} P(s^{'}|s,a) \sum_{a^{'}} \pi(a^{'}|s^{'})Q^{\pi}(s^{'},a^{'})
$$

$$
V^{\pi}(s)=E_{\pi}[R_t+\gamma V^{\pi}(s^{'})|S_t=s] \\
=\sum_{a}\pi(a|s) \left(  r(s,a)+\gamma \sum_{s^{'}} P(s^{'}|s,a) V^{\pi}(s^{'}) \right)
$$

**最优策略**

定义：当且仅当对于任意的状态$s$都有$V^{\pi}(s) \ge V^{\pi^{'}}(s)$。

1. 最优状态价值函数 $V^{*}=\max_{\pi}V^{\pi}(s)$
2. 最优动作价值函数$Q^{*}(s,a)=\max_{\pi} Q^{\pi}(s,a)$
3. 关系：$V^{*}(s)=\max_{a} Q^{*}(s,a)$     $Q^{*}(s,a)=r(s,a)+\gamma \sum_{s \in S{'}}P(s^{'}|s,a)V^{*}(s^{'})$

**贝尔曼最优方程**
$$
V^{*}(s)=\max_{a} \left(  r(s,a)+\gamma \sum_{s^{'}} P(s^{'}|s,a) V^{*}(s^{'}) \right)
$$

$$
Q^{*}(s,a)=r(s,a)+\gamma \sum_{s^{'}} P(s^{'}|s,a) \max_{a^{'}} Q^{*}(s^{'},a^{'})
$$

**动态规划：求解最优策略**

**策略迭代**

1. 策略迭代：策略评估和策略提升不断循环交替，直至得到最优策略的过程。

2. 策略评估：计算一个策略的状态价值函数，即用上一轮的状态价值函数来计算当前轮的状态价值函数，迭代N轮，直至收敛。
   $$
   V^{k+1}(s)=\sum_{a \in A} \pi(a|s) \left( r(s,a)+\gamma \sum_{s^{'} \in S} P(s^{'}|s,a)V^{k}(s^{'}) \right)
   $$
   
3. 策略提升：根据当前策略的状态价值函数，改进策略。
   $$
   \pi^{'}(s)=\arg \max_{a} Q^{\pi}(s,a)=\arg \max_{a} \left( r(s,a) + \gamma \sum_{s^{'} \in S} P(s^{'}|s,a)V^{\pi}(s^{'}) \right)
   $$
   



**值迭代**

1. 更新状态价值函数

$$
V^{k+1}(s)=\max_{a \in A} \left( r(s,a) + \gamma \sum_{s^{'} \in S} P(s^{'}|s,a)V^{k}(s^{'}) \right)
$$

2. 返回一个确定性策略
   $$
   \pi^{'}(s)=\arg \max_{a} Q^{\pi}(s,a)=\arg \max_{a} \left( r(s,a) + \gamma \sum_{s^{'} \in S} P(s^{'}|s,a)V^{\pi}(s^{'}) \right)
   $$

#### 问题建模

MDP定义：

1. 状态$s$，定义为$(t,b,x_t)$，$S=\{0,...,T\} \times \{0,..,B\} \times X$

2. 动作$A_{(t,b,x_t)}=\{0,...,b\}$

3. 状态转移函数
   $$
   P((t-1,b-\delta,x_{t-1})|(t,b,x_t),a)=p_{x}(x_{t-1})m(\delta,x_t)
   $$

   $$
   P((t-1,b,x_{t-1})|(t,b,x_t),a)=p_{x}(x_{t-1}) \sum_{\delta=a+1}^{\infin} m(\delta,x_t)
   $$

4. 奖励函数

$$
r(a, (t,b,x_t),(t-1,b-\delta,x_{t-1}))=\theta_{x}(x_{t})
$$

$$
r(a, (t,b,x_t),(t-1,b,x_{t-1}))=0
$$



值迭代：
$$
V(t,b)=\max_{0 \le a \le b} \left( \sum_{\delta=0}^{a} m(\delta) \theta_{avg} + \sum_{\delta=0}^{a} m(\delta) V(t-1,b-\delta) + \sum_{\delta=a+1}^{\infty} m(\delta)V(t-1,b) \right)
$$
最优策略：
$$
a(t,b,x)=\arg\max_{0 \le a \le b} \left( \sum_{\delta=0}^a m(\delta,x) \left( \theta(x)+V(t-1,b-\delta) - V(t-1,b) \right) \right)
$$
