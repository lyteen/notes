##  Cross-attn with patent

### **关键词:** 
Cross-attention  Gated Mechanisms   

### **摘要:** 
如何专利中不同序列中的交互（跨模态交互问题）

### **情形**

1. **如何组合来自异构源的数据**

2. **如何处理不同级别的噪音**

3. **更新策略**

Idear:
- 门控网络： 由模型决定信息的重要程度 [Done]

- 设计验证机制（如等高线图）: 用来和原始 'single seqlen' 预测进行比较 [TODO]
![alt text](assets/climbingLine.png)
  <details>
      <summary>图释</summary>
          <ul>
          <li><strong>Improvement in loss given thought</strong>： 使用tokens with thought 带来的 predict 准确性的提升</li>
            <li><strong>Original token prediction loss</strong>： 每个token 预测的准确性</li>
          </ul>
  </details>
  <details>
    <summary>灵感迁移</summary>
        <ul>
        纵轴表示为使用补充资料的程度，横轴表表示为相较于原始模型（只使用 摘要）的提升程度
        </ul>
  </details>


- 纠错: 不需要 k_v_cache
  <details>
      <summary>kv_cache</summary>
          <ul>
          <li>kv_cache 的设计用于<strong>语言模型</strong>模式为输入 t 个 token 预测下一个 token， 只需要存储之前 generated token + predicted token （hidden_layer output），而不需要重复计算 token</li>
          </ul>
  </details>

### 论文逻辑

**核心：**

1. **数据获取与处理**： 主要围绕在 labels( 突破性指标、新鲜度指标 )、text-embedding 处理

2. **模型原理**

- 技术路经

- 数学原理

- 创新性内容
  1.自学习的混合机制
    gated network + cross-attention
  2.强化学习策略
    loss function(reward function): MSE --> REINFORCE

3. **模型效果**

- 强化学习策略 VS 分类模型 （Cross-Entropy） VS 一般方法 (MSE)

- 损失函数的选取

- 

4. **消融实验与结果可视化**

- 消融实验变量选取

- 消融技术方法
  

```bash
  code
```

<details>
    <summary>补充</summary>
        <ul>
	      <li><strong>高斯近似</strong>： 利用后验的高斯近似来快速计算贝叶斯因子。</li>
          <li><strong>BFpack 实现</strong>： 在 R 包 BFpack 中实现该方法，以方便使用。</li>
        </ul>
</details>

