##  ORPO(Odds Ratio Preference Optimization)

### **关键词:** 
- RL
- DeepLearning
- Algorithm


### **摘要:** 
ORPO(优势比偏好优化) 为一种**无需参考模型**的算法， 用于将语言模型和人类偏好对齐。

<details>
    <summary>常用的 LLM alignment Method</summary>
        <ul>
	      <li><strong>PPO（近端策略优化）</strong>： 作为典型的LLM alignment Algorithm, 需要根据人类偏好数据训练一个 RM (奖励模型), 然后再通过 RM 对决策模型 Action 进行评分优化策略</li>
        </ul>
</details>

### **内容**

1.  **算法思想** .

收集人类偏好数据 $(x, y_w, y_l)$ 其中 $y_w$ 为人类偏好回复(chosen response)，$y_l$ 为非人类偏好回复 (rejected response) , 输入序列 x ，生成长度为 m 个 tokens 的 输出序列 y 的 average log-likelihood 为 $\log_\theta P(y|x) = \frac{1}{m} \sum_{i=1}^{m} \log P_\theta(y_i|x)$, $\theta$ 为模型参数.

记输入序列 x, 生成输出序列 y 的概率与输出为非 y 的概率比为 $odds_\theta(y|x) = \frac{P_\theta(y|x)}{1-P_\theta(y|x)}$, 则生成符合人类偏好回复($y_w$)的概率 与 非人类偏好($y_l$)的概率比为 $OR_\theta(y_w, y_l|x) = \frac{odds_\theta(y_w|x)}{odds_\theta(y_l|x)}$, 通过提高符合人类偏好的回复的可能性相对于非符合的可能性分数，来优化模型符合人类偏好。

2. **集成于SFT(监督微调)**

- SFT 表示为：$L_{SFT} = -\frac{1}{M} \sum_{k=1}^{M}\sum_{i=1}^{|V|} y_{i}^{k} \log p_{i}^{k}$, $p_i$ 为生成 $y_i$ 的概率

- Odds ratio loss(优势比损失) 表示为: $L_{OR} = - log\sigma(log \frac{odds_\theta(y_w|x)}{odds_\theta(y_l|x)})$

- 集成损失函数: $L_{ORPO} = L_{SFT} + \lambda L_{OR}$

```Python
def compute_loss(model, inputs, return_outputs=False):
    """
  Reference: https://github.com/xfactlab/orpo/blob/d13cdc025f942bdc49ba9c793350a3b1f2df25a0/src/orpo_trainer.py#L38
    """
    
    # Generate the hidden states for 'chosen' and 'reject'.
    neg_labels = inputs['negative_input_ids'].clone()
    pos_labels = inputs['positive_input_ids'].clone()

    # Generate the outputs for 'chosen' and 'reject'.
    outputs_neg = model(**{'input_ids': inputs['negative_input_ids'],
                            'attention_mask': inputs['negative_attention_mask'],
                            'labels': neg_labels,}, output_hidden_states=True)      
    outputs_pos = model(**{'input_ids': inputs['positive_input_ids'],
                            'attention_mask': inputs['positive_attention_mask'],
                            'labels': pos_labels,}, output_hidden_states=True)
        
    # Calculate NLL（negative log likelihood） loss.
    pos_loss = outputs_pos.loss # torch.nn.CrossEntropy
    
    # Calculate the log probabilities of the 'chosen' and 'reject' outputs.
    # pos_prob: log p(y^w | x)
    pos_prob = self.compute_logps(prompt_attention_mask=inputs['attention_mask'],
                                    chosen_inputs=inputs['positive_input_ids'], 
                                    chosen_attention_mask=inputs['positive_attention_mask'], 
                                    logits=outputs_pos.logits)
    
    # neg_prob: log p(y^l | x)
    neg_prob = self.compute_logps(prompt_attention_mask=inputs['attention_mask'], 
                                    chosen_inputs=inputs['negative_input_ids'], 
                                    chosen_attention_mask=inputs['negative_attention_mask'], 
                                    logits=outputs_neg.logits)

    # Calculate the log odds and the ratio.
    # pos_prob - neg_prob: log p(y^w | x) - log p(y^l | x) = \frac{log p(y_w | x)}{log p(y_l | x)}
    log_odds = (pos_prob - neg_prob) - (torch.log(1 - torch.exp(pos_prob)) - torch.log(1 - torch.exp(neg_prob)))

    # representing how much more likely y^w is preferred over y^l
    sig_ratio = torch.nn.functional.sigmoid(log_odds)
    ratio = torch.log(sig_ratio)
    
    # Calculate the ORPO loss.
    loss = torch.mean(pos_loss - self.alpha * ratio).to(dtype=torch.bfloat16)
    
    return (loss, outputs_pos) if return_outputs else loss
```


### *问题*

1. 在预训练模型的基础上如何计算偏好（偏好概率）分数？
  - forward pass: 使用 Hf_transformers 如果传入模型 labels (目标tokens)
    1. 将预测的 token 转换为 teacher forcing
    2. 计算 token-wise logits
    3. 使用 CrossEntropyLoss 返回 loss