## Budget Constrained Bidding by Model-free Reinforcement Learning in Display Advertising

### 背景

budget constrained bidding: 客户目标是有限预算下最大化转化价值。由于竞价环境的复杂性和动态性，最优的bidding策略很难实现。本文将该场景建模为Markov Decision过程，并且采用model-free RL框架解决该问题。

### DQN

Q-learning是一种基于时序差分算法的强化学习方法，时序差分的更新方式为
$$
Q(s_t,a_t) \leftarrow Q(s_t,a_t) + \alpha [r_t+\gamma \ \max_{a}Q(s_{t+1},a)-Q(s_{t},a_t)]
$$
DQN则是在Q-learning基础上引入函数拟合思想，用神经网络表示Q函数，输入特征为状态s和动作a。对于一组输入数据$\{(s_i,a_i,r_i,s_i^{'})\}$
$$
\omega^{*}=\arg \min_{\omega}\frac{1}{2N} \sum_{i=1}^N [Q_{\omega}(s_i,a_i)-(r_i+\gamma \ \max_{a^{'}}Q_{\omega}(s_{i}^{'},a^{'}))]
$$
由于更新Q网络的样本只依赖下一步转移状态$s^{'}$，不依赖下一步动作，因此可以将数据收集后在训练。

以下还有两个重要模块，帮助DQN取得稳定、出色的性能。

1. 经验回放

具体做法：维护一个回放缓冲区，将每次从环境中采样得到的（状态、动作、reward、下一状态）存储到缓存区，迭代Q网络时再从回放缓冲区中随机采样进行训练。

2. 目标网络

DQN的训练目标是让$Q_{\omega}(s,a)$不断逼近$r+\gamma \ \max_{a^{'}}Q_{\omega}(s^{'},a^{'})$，但是每次参数更新会影响逼近目标，容易造成训练的不稳定。因此增加一个目标Q网络，用于计算逼近目标$r+\gamma \ \max_{a^{'}}Q_{\omega}(s^{'},a^{'})$，参数每个C步与训练网络同步一次。

### 本文的方法

A）CMDP定义

$S$：1）当前时间片 2）$B_t$:剩余预算 3）$ROL_t$：在时刻t剩余的调整机会 4) $BCR_t = (B_t - B_{t-1})/B_{t-1}$：预算消耗速率 5）$CPM_t$：当前时间片竞胜流量的cpm 6）$WR_t$: 竞胜率 7) $r_{t-1}$：t-1时间片竞得的转化价值

$A$：动作空间，$\lambda_t=\lambda_{t-1} \times (1+\beta_{a})$

$r_t$: 即时reward，$\sum_{i \in I_t}x_i v_i$

$c$：该时间片内的消费

$\gamma$：1

B）reward设计
$$
r(s,a)=\max_{e\in E(s,a)}\sum_{t=1}^T r_{t}^e
$$
含义：遍历所有包含(s,a)的episode，找到total价值最大的。

C）算法

1. DQN迭代

   ![p01](E:\微信公众号\BIDDING\配图\p01.png)

2. rewardnet

![p2](E:\微信公众号\BIDDING\配图\p2.png)