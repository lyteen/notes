##  Cloud Service

### 关键词: 
- Based config
- GPU
- LLM

### 摘要: 


### 情形:
1. bash

modelscope && 模型下载
```bash
pip install --upgrade modelscope
modelscope download --model="Qwen/Qwen3-0.6B" --local_dir ./model_dir
```
LlaMaFactory && 依赖 下载
```bash
git clone --depth 1 https://github.com/hiyouga/LLaMA-Factory.git
cd LLaMA-Factory
pip install -e ".[torch,metrics]"
```

```bash
pip install --upgrade modelscope
modelscope download --model="Qwen/Qwen3-0.6B" --local_dir ./model_dir
git clone --depth 1 https://github.com/hiyouga/LLaMA-Factory.git
cd LLaMA-Factory
pip install -e ".[torch,metrics]"
```

```bash
pip install -e ".[torch,metrics]"
```
-e: 表示为editable模式(可编辑模型), 本地源代码的任何修改都会反映到环境中, 而无需重新安装包.
.: 当前目录是一个 Python 包（必须包含 setup.py 或 pyproject.toml 文件）
[torch,metrics]: 安装该包中定义的两个额外依赖组
    metrics: 安装与指标相关的依赖（比如 scikit-learn, numpy 等）

```python
from setuptools import setup

setup(
    name="your_package",
    version="0.1",
    packages=["your_package"],
    install_requires=[],  # 基础依赖
    extras_require={
        "torch": ["torch"],
        "metrics": ["scikit-learn", "numpy"],
    },
)
```

<details>
    <summary>开发者模式安装</summary>
        <ul>
	      <li>在 site-packages 下创建一个 .pth 文件 例如: **venv/lib/python3.9/site-packages/my_package.pth**, 这个 .pth 文件的内容就是你当前项目的路经, 例如: **/path/to/your/my_project**</li>
          <li>Python 解释器启动时会读取 .pth 文件，并将这些路径加入 sys.path</li>
        </ul>
</details>

2. WebUI

LlaMafactory install successful

```ipynb
!llamafactory-cli version
```

run LlaMafactory

```ipynb
%cd LLaMA-Factory
!export USE_MODELSCOPE_HUB=1 && \
llamafactory-cli webui
```

<details>
    <summary>补充</summary>
        <ul>
	      <li>不cd LLaMA-Factory, WebUI程序会乱拉屎</li>
          <li>AutoDL 中需要通过 '初始界面-格外服务-登入'打开webui</li>
        </ul>
</details>