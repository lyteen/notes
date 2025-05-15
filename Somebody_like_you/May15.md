verl  architecture

- hybrid 混合了什么?
- 对与传统RLHF架构的区别在哪里
- 能不能使用?


1. What is critic and reward model, what different?

在RLHF中, actor是目标模型, critic 用于估计价值函数, 即依据奖励模型反馈的分数来判断actor的state和action的好坏, 进一步的指导actor的梯度更新

通常 critic 和 actor 共享Module但有一个单独的线性头(lm_head)来输出估值, 在训练期间， Critic 会更新最小化预测回报和观察到的回报之间的差异（使用 TD 误差或 GAE）。