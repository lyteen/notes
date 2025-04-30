## llm_instruction_dataset

### **关键词:** 
- DeepLearning
- dataset
- LLM

### **摘要:** 
一种数据集，由于用户和LLMs的真实的对话数据可以帮助模型更好地学习多轮对话的上下文保持、回应生成能力。

### **情形**

1.  **Alpace**  

数据集格式要求如下：

- 指令监督微调
![alt text](assets/指令微调.avif)

指令微调格式：
```json
[
  {
    "instruction": "人类指令（必填）",
    "input": "人类输入（选填）",
    "output": "模型回答（必填）",
    "system": "系统提示词（选填）",
    "history": [
      ["第一轮指令（选填）", "第一轮回答（选填）"],
      ["第二轮指令（选填）", "第二轮回答（选填）"]
    ]
  }
]
```
数据集描述：
```json
"数据集名称": {
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

- 预训练数据
```json
[
  {"text": "document"},
  {"text": "document"}
]
```

- 偏好训练数据

用于奖励模型训练、DPO训练 和 ORPO 训练。对于系统指令和人类输入，偏好数据集给出了一个更优的回答和一个更差的回答

数据集格式：
```json
[
  {
    "instruction": "人类指令（必填）",
    "input": "人类输入（选填）",
    "chosen": "优质回答（必填）",
    "rejected": "劣质回答（必填）"
  }
]
```

数据集描述：
```json
"数据集名称": {
  "file_name": "data.json",
  "ranking": true,
  "columns": {
    "prompt": "instruction",
    "query": "input",
    "chosen": "chosen",
    "rejected": "rejected"
  }
}
```

- KTO数据

KTO 数据集与偏好数据集类似，但KTO 数据集对每一轮问答只给出一个 true/false 的 label

数据集格式：
```json
[
  {
    "instruction": "人类指令（必填）",
    "input": "人类输入（选填）",
    "output": "模型回答（必填）",
    "kto_tag": "人类反馈 [true/false]（必填）"
  }
]
```

数据集描述：
```json
"数据集名称": {
  "file_name": "data.json",
  "columns": {
    "prompt": "instruction",
    "query": "input",
    "response": "output",
    "kto_tag": "kto_tag"
  }
}
```
<details>
    <summary>KTO</summary>
        <ul>
	      <li><strong>KTO</strong>： 指“Knowledge Transfer Optimization”（知识迁移优化）相关的数据集。它用于优化模型在知识迁移过程中的能力，特别是在人机交互场景中，评估模型生成的答案是否符合人类的预期。</li>
        </ul>
</details>


- 多模态数据

多模态数据集需要额外添加一个 images 列，包含输入图像的路径。

数据集格式：
```json
[
  {
    "instruction": "人类指令（必填）",
    "input": "人类输入（选填）",
    "output": "模型回答（必填）",
    "images": [
      "图像路径（必填）"
    ]
  }
]
```
数据集描述
```json
"数据集名称": {
  "file_name": "data.json",
  "columns": {
    "prompt": "instruction",
    "query": "input",
    "response": "output",
    "images": "images"
  }
}
```

2. **ShareGPT**

ShareGPT 格式中的KTO数据集和多模态数据集与 Alpaca 格式的类似，再此基础上添加角色种类。预训练数据集不支持 ShareGPT 格式。


- 指令监督微调数据集

数据集描述
```json
{
  "conversations": [
    {
      "from": "human",
      "value": "你好，我出生于1990年5月15日。你能告诉我我今天几岁了吗？"
    },
    {
      "from": "function_call",
      "value": "{\"name\": \"calculate_age\", \"arguments\": {\"birthdate\": \"1990-05-15\"}}"
    },
    {
      "from": "observation",
      "value": "{\"age\": 31}"
    },
    {
      "from": "gpt",
      "value": "根据我的计算，你今天31岁了。"
    }
  ],
  "tools": "[{\"name\": \"calculate_age\", \"description\": \"根据出生日期计算年龄\", \"parameters\": {\"type\": \"object\", \"properties\": {\"birthdate\": {\"type\": \"string\", \"description\": \"出生日期以YYYY-MM-DD格式表示\"}}, \"required\": [\"birthdate\"]}}]"
}
```

数据集描述
```json
"数据集名称": {
  "file_name": "data.json",
  "formatting": "sharegpt",
  "columns": {
    "messages": "conversations",
    "system": "system",
    "tools": "tools"
  }
}
```

- 偏好数据集

数据集描述
```json
{
  "conversations": [
    {
      "from": "human",
      "value": "老虎会吃哪一个，草还是树叶？"
    }
  ],
  "chosen": {
    "from": "gpt",
    "value": "我很高兴能帮助你解答问题！不过，我必须指出，这个问题本身可能没有意义。老虎是食肉动物，通常不吃草或树叶。它们的饮食主要包括大型有蹄动物、小型哺乳动物和鸟类。\n\n如果你提出这个问题是出于好奇或有特定原因，我很乐意帮助你找到更多关于老虎饮食习惯的信息，或者建议资源让你更多了解这些壮丽的生物。请告诉我如何进一步协助你！"
  },
  "rejected": {
    "from": "gpt",
    "value": "一只老虎是食肉动物，这意味着它的饮食主要是肉类。老虎主要猎捕和吃其他动物，如鹿和野猪。它们不会选择吃草或树叶，因为这些不是它们自然饮食的一部分。"
  }
}
```

- OpenAI 格式

OpenAI 格式仅仅是 sharegpt 格式的一种特殊情况，其中第一条消息可能是系统提示词

数据集格式
```json
[
  {
    "messages": [
      {
        "role": "system",
        "content": "系统提示词（选填）"
      },
      {
        "role": "user",
        "content": "人类指令"
      },
      {
        "role": "assistant",
        "content": "模型回答"
      }
    ]
  }
]
```

数据集描述
```json
"数据集名称": {
  "file_name": "data.json",
  "formatting": "sharegpt",
  "columns": {
    "messages": "messages"
  },
  "tags": {
    "role_tag": "role",
    "content_tag": "content",
    "user_tag": "user",
    "assistant_tag": "assistant",
    "system_tag": "system"
  }
}
```