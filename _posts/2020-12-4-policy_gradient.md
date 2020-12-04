---
title: policy gradient
categories: 强化学习
tags:
- 强化学习
---

# 1. 前置知识
为了更好的理解本文，我们假设你有机器(深度)学习的基础知识，以及对 actor
, environment, reward 等概念有一些基本的了解。

# 2. Policy gradient 介绍

我们使用$s$ 代表状态，$a$ 代表action，1个episode的轨迹如下：

$$
\tau = \{s_1, a_1, s_2, a_2, ..., s_t, a_t\} \tag{1}
$$

则产生上述轨迹的概率$P_{\theta}(\tau)$ 为：

$$
P_{\theta}(\tau) = P(s_1)P_{\theta}(a_1|s_1)P(s_2|s_1, a_1)P_{\theta}(a_2|s_2)
P(s_3|s_2, a_2)...\\
= p(s_1) \prod \limits_{t=1}^TP_{\theta}(a_t|s_t)P(s_{t+1}|s_t, a_t) \tag{2}
$$

上述式子中，环境决定$P(s_{t+1}|s_t, a_t)$，agent 决定 $P_{\theta}(a_t|s_t)$, $\theta$ 为agent policy 网络的参数，是policy gradient 需要优化的参数。
policy gradient 算法最大化的目标为：所有可能产生的路径，奖励的总期望$R_{\theta}$：

$$
R_{\theta} = \sum_{\tau} R(\tau)P_{\theta}(\tau) \\
           = E_{\tau \sim P_{\theta}(\tau)}[R(\tau)]  \tag{3}
$$

想要最大化$R_{\theta}$，我们需要对agent policy网络的参数$\theta$求梯度：

$$
\nabla R_{\theta} = \sum_{\tau} R(\tau) \nabla P_{\theta}(\tau) \\
= \sum_{\tau} R(\tau) P_{\theta}(\tau) \frac{\nabla P_{\theta}(\tau)}{P_{\theta}(\tau)} \\
=\sum_{\tau} R(\tau) P_{\theta}(\tau) \nabla log P_{\theta}(\tau) \\
= E_{\tau \sim P_{\theta}(\tau)}[R(\tau) \nabla log P_{\theta}(\tau)] \\
= \frac{1}{N} \sum_{i=1}^{N} R(\tau) \nabla log P_{\theta}(\tau) \tag{3}
$$

将公式2与公式3结合可得：

$$
\nabla R_{\theta} = \frac{1}{N} \sum_{i=1}^{N} \sum_{t=1}^{T} R(\tau) \nabla log P_{\theta}(a_t^n | s_t^n) \tag{4}
$$

其中公式2中的$P(s_{t+1}\|s_t, a_t)$与$\theta$ 无关，因此可以不考虑它。

因此，我们的Policy gradient 流程如下：


![Policy gradient 流程](/images/policy_gradient.png)

首先根据初始的policy $\pi_{\theta}$, 生成多个路径$\tau$, 然后我们根据这多个路径，
根据公式4来计算 $\nabla R_{\theta}$， 最后利用梯度上升来更新我们网络的参数。

