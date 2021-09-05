# Actor-Critic算法

策略梯度reinforce算法需要完整的状态序列，而且单独对策略函数做迭代更新，不太容易收敛。Actor-Critic是将*策略*和*价值*相结合的方法。

## 介绍

算法中的Actor(动作者)负责使用策略函数生成动作并和环境交互，Critic(评论家)使用价值函数评估Actor的表现，并指导Actor下一阶段的动作。在策略梯度中我们使用我们使用蒙特卡洛法来计算每一步的价值，但是场景比*较受限，因为它要求我们必须提前做动作有充分的采样，* ???*存疑*

在Actor-Critic中我们做两组近似，第一组是对策略函数做近似，

$$
\pi \theta(s, a)=P(a \mid s, \theta) \approx \pi(a \mid s)
$$

第二组是对价值函数做近似，状态价值函数和动作价值函数的近似分别是：

$$
\begin{array}{c}
\hat{v}(s, w) \approx v_{\pi}(s) \\
\hat{q}(s, a, w) \approx q_{\pi}(s, a)
\end{array}
$$

在蒙特卡洛策略梯度reinforce算法中，我们的策略的参数更新公式是：

$$
\theta=\theta+\alpha \nabla_{\theta} \log \pi \theta\left(s_{t}, a_{t}\right) v_{t}
$$