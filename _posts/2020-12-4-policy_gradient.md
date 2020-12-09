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
= \frac{1}{N} \sum_{n=1}^{N} R(\tau^n) \nabla log P_{\theta}(\tau) \tag{3}
$$

将公式2与公式3结合可得：

$$
\nabla R_{\theta} = \frac{1}{N} \sum_{n=1}^{N} \sum_{t=1}^{T} R(\tau^n) \nabla log P_{\theta}(a_t^n | s_t^n) \tag{4}
$$

其中公式2中的$P(s_{t+1}\|s_t, a_t)$与$\theta$ 无关，因此可以不考虑它。$R(\tau^n) = \sum_{t'=t}^{T_n} r_{t'}^{n}$，为第$n$条路径从$t$时刻开始的总收益。

因此，我们的Policy gradient 流程如下：


![Policy gradient 流程](/images/policy_gradient.png)

首先根据初始的policy $\pi_{\theta}$, 生成多个路径$\tau$, 然后我们根据这多个路径，
根据公式4来计算 $\nabla R_{\theta}$， 最后利用梯度上升来更新我们网络的参数。

# 3. 实现Tips

公式4中的$R(\tau)$可能一直为正数，可能会影响优化效率。因此我们将公式4进行一些修改：

$$
\nabla R_{\theta} = \frac{1}{N} \sum_{i=1}^{N} (\sum_{t=1}^{T} R(\tau^n)-b) \nabla log P_{\theta}(a_t^n | s_t^n) \tag{5}
$$

其中$b \approx E[R(\tau)]$。保证了$R(\tau)-b$的正负区间的平衡性。同时也可以对
reward进行归一化操作，具体细节可以参考下方代码实现。

公式5中，$R(\tau^n) = \sum_{t'=t}^{T_n} r_{t'}^{n}$，将长期收益与短期收益的权重视为一致。我们想让收益随着时间递减，因此，
我们将公式5修改如下，其中$\lambda \in [0,1]$:

$$
\nabla R_{\theta} = \frac{1}{N} \sum_{i=1}^{N} \sum_{t=1}^{T} (\sum_{t'=t}^{T_n} \lambda^{t'-t} r_{t'}^{n}-b) \nabla log P_{\theta}(a_t^n | s_t^n) \tag{6}
$$

另外，在代码实现过程中，公式6中的$log P_{\theta}(a_t^n | s_t^n)$可以用Pytorch中的
交叉熵损失函数实现（实现起来就是个分类问题），每经过一次eposide 更新一次。

接下来的代码实现中，我们将以公式6为核心进行实现。实现语言为Pytorch。

# 4. 代码实现
我们的代码实现基于Pytorch, gym 库，我们首先引入几个必要的库：
```python
from itertools import count
import torch
import torch.nn as nn
import torch.nn.functional as F
from torch.distributions import Bernoulli

import numpy as np
import gym
```
我们的实验基于'CartPole-v1'环境。我们首先实现一个agent，其policy网络由几层FC层组成，输出只有两个action (倒立摆向左或者向右)。
主要实现的功能有policy net 更新和动作选择:
```python
class FCN(nn.Module):
    ''' 全连接网络'''
    def __init__(self,n_states):
        super(FCN, self).__init__()
        # 24和36为hidden layer的层数，可根据n_states, n_actions的情况来改变
        self.fc1 = nn.Linear(n_states, 24)
        self.fc2 = nn.Linear(24, 36)
        self.fc3 = nn.Linear(36, 1)  # Prob of Left

    def forward(self, x):
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = F.sigmoid(self.fc3(x))
        return x

class PolicyGradient:
    def __init__(self, n_states, device='cpu', gamma=0.99, lr=0.01):
        self.gamma = gamma
        self.policy_net = FCN(n_states)
        self.optimizer = torch.optim.RMSprop(self.policy_net.parameters(), lr=lr)
    # 选择action，根据学到的P_{\theta}(a_t|s_t)进行采样
    def choose_action(self, state):
        state = torch.from_numpy(state).float()
        probs = self.policy_net(state)
        m = Bernoulli(probs)
        action = m.sample()
        
        action = action.data.numpy().astype(int)[0]
        return action
    # 利用累积的episode路径进行gradient decent
    def update(self, reward_pool, state_pool, action_pool):
        # Discount reward and normalize reward：根据公式6
        running_add = 0
        for i in reversed(range(len(reward_pool))):
            if reward_pool[i] == 0:
                running_add = 0
            else:
                running_add = running_add * self.gamma + reward_pool[i]
                reward_pool[i] = running_add
        reward_mean = np.mean(reward_pool)
        reward_std = np.std(reward_pool)
        for i in range(len(reward_pool)):
            reward_pool[i] = (reward_pool[i] - reward_mean) / reward_std
        self.optimizer.zero_grad()
        
        for i in range(len(reward_pool)):
            state = state_pool[i]
            action = torch.FloatTensor([action_pool[i]])
            reward = reward_pool[i]

            state = torch.from_numpy(state).float()
            probs = self.policy_net(state)
            m = Bernoulli(probs)
            # 公式6的实现：本质上是个分类问题,负号是为了将梯度上升转为梯度下降
            loss = -m.log_prob(action) * reward  # Negtive score function x reward
            # print(loss)
            loss.backward()
        self.optimizer.step()
``` 
我们接下来进行环境的初始化：
```python
def env_init():
    env = gym.make('CartPole-v1') 
    env.seed(1)
    n_states = env.observation_space.shape[0]
    n_actions = env.action_space.n
    return env,n_states,n_actions
```
最后进行整体训练代码的编写
```python
env, n_states, n_actions = env_init()
agent  = PolicyGradient(n_states,lr = 0.01)

state_pool = [] # 存放每batch_size个episode的state序列
action_pool = []
reward_pool = [] 
for i_episode in range(1200):
    state = env.reset()
    ep_reward = 0
    for t in count():
        action = agent.choose_action(state) # 根据当前环境state选择action
        next_state, reward, done, _ = env.step(action)
        ep_reward += reward
        if done:
            reward = 0
        state_pool.append(state)
        action_pool.append(float(action))
        reward_pool.append(reward)
        state = next_state
        if done:
            print('Episode:', i_episode, ' Reward:',  ep_reward)
            break
    if i_episode > 0 and i_episode % 5 == 0:
        agent.update(reward_pool,state_pool,action_pool)
        state_pool = [] # 每个episode的state
        action_pool = []
        reward_pool = []

state = env.reset()

for _ in range(100):
    # 显示部分
    env.render() # just update the data
    # 动作部分
    action = agent.choose_action(state) # 从动作空间中随机选取一个动作
    state, reward, done, _ = env.step(action) # 用于提交动作，括号内是具体的动作
env.close() # 关闭环境
```
我们运行上述代码，最后的实验结果如下：

![Policy gradient 流程](/images/result.gif)
## References

* [Intro to Reinforcement Learning (强化学习纲要）](https://github.com/zhoubolei/introRL)
* [神经网络与深度学习](https://nndl.github.io/)
* [百面深度学习](https://book.douban.com/subject/35043939/)
* [李宏毅深度强化学习笔记(LeeDeepRL-Notes)](https://datawhalechina.github.io/leedeeprl-notes/#/chapter4/chapter4)
