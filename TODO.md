##  TODO Apr_13

1. 数据的标签处理： 包括相似度矩阵构建， 前后向相似度计算突破性， 专利突破性的可视化
  相似度矩阵的处理方式：
    1. 提取主元ipc号，以选取专利为时间基线，time windows为5年 []
    2. 将 bert-embedding 求均值 []
    3. 如果标题的 token 出现在摘要中那么可以认为该词为摘要的主要语义成分，并对其进行放缩 放缩比例为1 (1, 1 / n) []
    -> 检查每个token的差异是否只是存在于方向而无大小差异
    4. 对句子求平均得到摘要的总体语义 vector
    5. 保存为每个ipc主号下的文件中
    6. 前向（后向）求和得到总体
    7. 后向 / 前向 得到突破值

  - 专利摘要， IPC号， 发布时间（转化格式）， 专利公开号 -> IPC主类.csv [X] --> all sorts fix :法律状态 -time-formal [Done]
  - 专利摘要embedding layer + 映射到2d， 专利公开号 -> .pt [Done]
  - write to IPC主类.csv: bt_score, x-axis, y-axis [Done]

2. 时间的新颖性标签处理： 衰弱程度计算， 计算图的可视化

## TODO Apr_16

1. 研讨会的ppt
  1. Reward Function [Done]
  2. Parallel Compute With Masked [Done]
  3. Tearcher Forced [Done]
  4. Result [Done]

2. 统计建模的paper
  [TODO]

## TODO Apr_17

1. Learning model tuning factory
  1. Llama-factory
    - 傻瓜化fine-tuning
  2. Unsloth
    - Encapsulation code - as package usage
  3. Others?


## TODO Apr_19

1. 统计建模的paper -- [Continue]
  - Reinforce architecture [Done?]
  - Cross-attention [TODO]
  - Data Processing [TODO]
  - Study background [TODO]

$$ [g_1, g_2, ..., g_N] = G(\mathbf{x}; \mathbf{\Theta}_g) $$
$$ \mathbf{h}t^l = \sum{i=1}^{N} g_{i,t} \text{FFN}_i(\mathbf{u}_t^l) + \mathbf{u}_t^l $$ 

\begin{equation}
  a^2 + b^2 = c^2 \label{eq:pythagoras}
\end{equation}

In equation~asd\eqref{eq:pythagoras}, we see the relationship...

## TODO Apr_27

1. 研讨会
  各大主流的强化学习对齐手段
  公式解释+实际应用+具体情况
  - ORPO: STF + Odds Ratio Loss
  - RLFH:
  - DPO:
  - RPO:
  - GRPO:
  - ... 

## TODO Apr_29

1. 通过其他语言对Pytorch .pt .tensorRT .ONNX 的调度
2. 将Java课程finish
3. 