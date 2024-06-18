### Optimal Real-Time Bidding for Display Advertising

背景：

本文仍然是在预算约束下最优出价问题，但是没有用MDP建模，而是直接建模为约束优化问题，并通过拉格朗日乘子法，求得出价表达式。本文的核心是提出了非线性竞价函数，相比少量高CTR展示机会，大量低CTR的展示机会也值得出价。

建模：
$$
b()_{ORTB}= \arg \max_{b()} N_T \int_{x} \theta(x) w(b(\theta(x),x), x) p_{x}(x) dx
$$

$$
s.t. N_T \int_{x}  b(\theta(x),x) w(b(\theta(x),x), x) p_{x}(x) dx \le B
$$

直接求得出价函数$b(\theta)$和竞得率时间的关系
$$
\lambda w(b(\theta))=[\theta - \lambda b(\theta)] \frac{ \partial w(b(\theta))}{\partial b(\theta)}
$$
通过数据分析，假设了竞得率函数有如下非线性形式
$$
w(b(\theta))=\frac{b(\theta)}{c+b(\theta)}
$$
最后得到了非线性出价函数：
$$
b_{ORTB}(\theta)=\sqrt{\frac{c}{\lambda} \theta + c^2} - c
$$
其中，$c$通过历史数据拟合，$\lambda$通过调参确定。