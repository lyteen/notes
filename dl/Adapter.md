##  Adapter(适配器)

### **关键词:** 
- DeepLearning
- fine-tuning
- transfer learning

### **摘要:** 
Adapter（适配器）是一种轻量级的模型结构，通过添加一些额外的、可训练的参数，使预训练模型能够更好地适应特定的任务或领域。

### **情形**

1.  **Bottleneck Adapter**  

- 向下投射到一个较小维度，经过一层非线性激活函数，然后向上投射到原有的维度。
![alt text](<assets/Bottleneeck Adapter.jpg>)

2. **AdapterFusion**

- 目的： 训练能执行多任务的模型， 不用更新预训练模型的参数，而是插入比较少的新的参数就可以地学会一个新任务
![alt text](assets/adpterFusion.jpg)

- 查询 (Query)、键 (Key) 和值 (Value)。查询 (Query) 将预训练的 Transformer 权重的输出作为输入。键 (Key) 和值 (Value) 均将各自适配器的输出作为输入。查询 (Query) 与所有键 (Key) 的点积被传入一个 softmax 函数
<details>
    <summary>其他多任务方法</summary>
        <ul>
	      <li><strong>sequential fine-tuning</strong>： 需要先验知识来确定顺序，且模型容易遗忘之前任务学到的知识</li>
        <li><strong>multi-task learning</strong>: 不同的任务互相影响，也难以平衡数据集大小差距很大的任务
        </ul>
</details>
```bash
```

<details>
    <summary>补充</summary>
        <ul>
	      <li><strong>.</strong>： .</li>
        </ul>
</details>

