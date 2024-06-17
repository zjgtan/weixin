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

