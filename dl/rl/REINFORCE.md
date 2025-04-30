##  REINFORCE

### **关键词:** 
Reinforcement Learning  Algorithm  DeepLearing

### **摘要:** 
REINFORCE 基于蒙特卡洛策略梯度方法，通过采样得到的轨迹来估计策略的梯度。它利用了对数似然比技巧，将策略梯度表示为期望形式，使得可以通过采样来近似计算。

<details>
    <summary>蒙特卡洛策略梯度方法</summary>
        <ul>
        指在不清楚 MDP 状态转移概率的情况下，直接从经历完整的状态序列 (episode) 来估计状态的真实价值，并认为某状态的价值 （state value） 等于在多个状态序列中以该状态算得到的所有 return （action value） 的平均值。
        <li><strong>MDP(马尔科夫决策过程)</strong>: 在执行策略 π 时，状态从 s 转移至 s’ 的概率
        （即执行该状态下 (s) 所有行为 (a) 的概率与对应行为能使状态从 s 转移至 s’ 的概率的乘积的和）
        </ul>
</details>

### **内容**

1.  **初始化**： 初始化策略网络 $\pi_{\theta}(a|s)$ 的参数 $\theta$ ，设置学习率 $\alpha$ 等超参数

2. **采样轨迹**： 从当前策略 $\pi_{\theta}$ 中采样得到一条完整的轨迹 $\tau=(s_0, a_0, r_0, s_1, a_1, r_1, \cdots, s_T, a_T, r_T)$ 其中 T 是轨迹 （episode）的终止时刻

3. **累计奖励**： 对于轨迹 $\tau$, 计算从每个时刻 t 到轨迹结束的累计奖励 $R_t=\sum_{k = t}^{T}r_k$

4. **计算策略梯度**: 对于轨迹中的每个时间步 t ，计算策略梯度的估计值 $\nabla_{\theta}\log\pi_{\theta}(a_t|s_t)R_t$ ，然后对整个轨迹的梯度估计值进行求和，得到 $\nabla_{\theta}J(\theta)\approx\sum_{t = 0}^{T}\nabla_{\theta}\log\pi_{\theta}(a_t|s_t)R_t$

<details>
    <summary>why log </summary>
        <ul>
	      log function: 由于对数的导数比概率函数本身更容易计算
        </ul>
</details>

5. **更新策略参数**: 根据计算得到的策略梯度估计值，使用随机梯度上升法更新策略网络的参数，即 $\theta\leftarrow\theta+\alpha\nabla_{\theta}J(\theta)$

### **问题**


先考虑一个非常简单的单步 (one-step) MDP(马尔科夫决策过程) 问题：初始状态 $s \sim d(s)$，只经历一步得到一个即时奖励 $r=\mathcal{R}_{s,a}$ 就终止。由于是单步 MDP ，所以三种目标函数都是一样的，我们用似然比来计算策略梯度： 
$$
\begin{align*}
J(\theta) &= \mathbb{E}_{\pi_\theta}[r]\\
&=  \sum_s d^{\pi_\theta}(s)  \sum_a \pi_\theta(s,a)\mathcal{R}_s^a
\end{align*}
$$

- 目标函数解释 $J(\theta) = \mathbb{E}_{\pi_\theta}[r]$

  $J(\theta) = \mathbb{E}_{\pi_\theta}[r]$： 执行策略时预期回报(累积奖励)，即在一个回合(episode)中获得的平均总奖励

- 扩展目标函数 $J(\theta) = \sum_s d^{\pi_\theta}(s) \sum_a \pi_\theta(s,a)\mathcal{R}_s^a$

  $d^{\pi_\theta}(s)$: 指某一特定的状态

  $\mathcal{R}_s^a$: 在状态s下采取行动a后预期获得的即时奖励

总体上看为：所有状态下($\sum_{S}$)的各个行动($\sum_{A}$)获得的总体预期回报

$$
\begin{align*}
\nabla_\theta J(\theta) &= \sum_s d(s) \sum_a \pi_\theta(s,a)  \nabla_\theta \log\pi_\theta(s,a)  \mathcal{R_{s,a}} \\
&= \mathbb{E}_{\pi_\theta}[ \nabla_\theta \log\pi_\theta(s,a)  r]
\end{align*}
$$

- 策略网络的梯度 $\nabla_\theta(s,a)$

  根据策略网络的似然比
  $$
  \begin{align*}
  \nabla_\theta \pi_\theta(s,a) &= \pi_\theta(s,a)\frac{\nabla_\theta \pi_\theta(s,a) }{\pi_\theta(s,a)} \\
  &= \pi_\theta(s,a)  \nabla_\theta \log\pi_\theta(s,a) 
  \end{align*}
  $$

- 将 $r = \sum_s d^{\pi_\theta}(s) \sum_a\pi_\theta(s,a)$ 进行代换，得到最终的策略网络的梯度的表达式

即如果在状态s下采取的行动a带来的预期奖励 $\mathcal{R}_s^a$ 越大，那么梯度 $\pi_\theta(s,a)  \nabla_\theta \log\pi_\theta(s,a)$ 将 $\theta$ 朝向行动a的概率方向进行

