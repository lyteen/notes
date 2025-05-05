##  Qwen3阿里云训练

### 关键词: 
- LLaMaFactory
- Qwen3
- Modelscope

### 摘要: 
Qwen3监督微调尝试

### 情形:

1. Modelscope 下载模型

```bash
modelscope download --model="Qwen/Qwen3-1.7B" --local_dir ./model_sava_path
```

2. LLaMaFatory 训练

- FP8(8位浮点数)模型: (Qwen3-1.7B-FP8)需要特定的架构, Hopper架构(H100), Ada Lovelace架构(RTX 4090)仅支持FP8推理.

- 自定义数据集: 需要在LLaMaFactory的data文件夹下的dataset_info.json中添加数据集描述

Alpaca格式
```json
"dataset_name": {
  "file_name": "data.json",
  "columns": {
    "prompt": "instruction",
    "query": "input",
    "response": "output",
    "system": "system",
    "history": "history"
  }
}
```
数据集样例
![dataset](assets/dataset.png)

- 训练结果贴图

![train_dialog](assets/train_dialog.png)

<details>
    <summary>补充</summary>
        <ul>
	      <li><strong>.</strong>： .</li>
        </ul>
</details>