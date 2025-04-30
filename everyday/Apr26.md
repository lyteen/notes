##  Apr26

### **关键词:** 
- 统计建模
- 蓝桥杯  
- Competition
- 纠错

### **摘要:** 
就最近的统计建模、蓝桥杯人工智能比赛做一个总结

- 未充分经过实验（demo）拟合的设想未必如想象中一样轻易实现
- 数据处理基础部分知识不扎实

### **情形**

1.  **统计建模** .

- REINFORCE 算法与模型架构： 

通过实验证明，语义向量(bert output logits)对于一个little模型的输入未必是理想的输入变量，也可能是坏的变量。
semantic + labels --> (predict) --> labels
结果是模型趋向于拟合最容易发生的labels [TO_THINK]

- 训练数据的问题:

模型在前部分batch就出现预测出最普遍分值的现象, 可解释的原因是:

训练数据集中的普遍分值(占比80%及以上), 导致模型未充分收敛, 停留在局部最优的状态. 因此更新解决方案: 将分值数据按一定比例分布.

2. **蓝桥杯**

- based method
onnxruntime 将神经网络模型（Pytorch Tensorflow）转化为不同框架部署（TensorRT TVM）时需要的中间步骤模型

Pytorch --> ONNX --> TensorRT / TVM
<details>
    <summary>TensorRT</summary>
        <ul>
	      <li><strong>What</strong>: TensorRT 是 NVIDIA 推出的高性能深度学习推理优化器和运行时库，它能显著提高深度学习模型在 NVIDIA GPU 上的推理速度</li>
          <li><strong>How</strong>: TensorRT 之所以具备高效推理能力, 1st.TensorRT 会对计算图进行优化，包括层融合、精度校准、内核自动调优. 2st.精度校准：支持 INT8 量化，通过校准过程最小化精度损失. 3st.内核自动选择：为特定 GPU 选择最优的内核实现. 4st.动态张量内存：最小化内存占用并高效重用内存. 5st.多流执行：支持并行处理多个输入流
          <li><strong>Why</strong>: 将 Pytorch --> ONNX: ONNX 作为通用交换格式，能被 TensorRT 解析 
        </ul>
</details>

示例 Pytorch --> ONNX --> TensorRT 
```Python
import os
# Pytorch
import torch
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim

# ONNX
import onnxruntime as ort

# TensorRT
import tensorrt as trt

# --- debug ---
import logging
import debug as db

db.set_logger()
logger = logging.setLogger(__name__)
# --- end ---

# Pytorch CNN Model
class CNN(nn.Module):
    def __init__(self):
        super(CNN, self).__init__()
        self.conv1 = nn.Conv2d(in_channels=3, out_channels=32, kernel_size=3, stride=1, padding=1)
        self.conv2 = nn.Conv2d(in_channels=32, out_channels=64, kernel_size=3, stride=1, padding=1)
        self.fc1 = nn.Linear(in_features=64 * 8 * 8, out_features=128, bias=False)
        self.fc2 = nn.Linear(in_features=128, out_features=10)
    
    def forward(self, x):
        x = F.relu(self.conv1(x))
        x = F.max_pool2d(x, 2)
        x = F.relu(self.conv2(x))
        x = F.max_pool2d(x, 2)
        x = self.fc1(x.view(-1, 64 * 8 * 8))
        x = F.relu(x)
        x = self.fc2(x)

# Trainer
model = CNN()

# --- config ---
bsz = 5
in_channels = 3
img_h = 32
img_w = 32
epochs = 5
# --- end ---

img = torch.randn(bsz, in_channels, img_h, img_w)
labels = torch.randint(0, 10, (bsz,))
optimizer = optim.Adam(model.parameters(), lr=0.001)
criterion = nn.CrossEntropyLoss()
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model.train()
for epoch in range(epochs):
    logits = model(x=img)
    loss = criterion(logits, labels)
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
# torch.save(model, 'path.pth')

# Export .ONNX
torch.onnx.export(
    model,
    img,
    onnx_path,
    input_names=["images"],
    output_names=["output"],
    dynamic_axes={
        "images": {0: "bsz"},
        "output": {0: "bsz"}
    }
)

# Convert to TensorRT
def build_engine(onnx_path, engine_path, precision="FP32"):
    logger = trt.Logger(trt.Logger.WARING)
    builder = trt.Builder(Logger)
    network = builder.create_network(1 << int(trt.NetworkDefinitionCreationFlag.EXPLICIT_BATCH))
    parser = trt.OnnxParser(network, logger)

    # Parse ONNX model
    with open(onnx_path, "rb") as f:
        if not parser.parse(f.read()):
            for error in range(parser.num_errors):
                print(parser.get_error(error))
            return None
    config = builder.create_builder_cofig()
    
    # Set precision
    if precision == "FP16":
        config.set_flag(trt.BuilderFlag.FP16)
    elif precision == "INT8":
        config.set_flag(trt.BuilderFlag.INT8)
    elif precision == "FP32":
        config.set_flag(trt.BuilderFlag.FP32)
    
    # Set workspace size 1 GB
    config.max_workspace_size = 1 << 30
    
    # Save TensorRT Engine
    with open(engine_path, "wb") as f:
        f.write(engine.serialize())
    
    return engine

onnx_path = "path.onnx"
engine_path = "path.engine"
engine = build_engine(onnx_path, engine_path, precision="FP32")

if engine:
    logger.info(f"engine create done")
else:
    logger.info(f"build error")
```

使用 TensorRT engine 进行推理

```Python
import pycuda.driver as cuda
import pycuda.autoinit
import numpy as np 

import logging
import debug as db

db.set_logger()
logger = logging.setLogger(__name__)

class HostDeviceMem:
    """Manage device and memory"""
    def __init__(self, size, dtype=np.float32):
        self.size = size
        self.dtype = dtype
        self.host = np.empty(size, dtype=dtype)
        self.device = cuda.mem_alloc(self.host.nbytes)
    
    def __str__(self):
        return f"Host:\n{self.host}\nDevice:\n{self.device}"
    
    def __del__(self):
        self.device.free()

def allocate_buffers(engine):
    """Allocate buffers to input and output"""
    inputs = []
    outputs = []
    bindings = []
    stream = cuda.Stream()
    
    for binding in engine:
        size = trt.volume(engine.get_binding_shape(binding)) * engine.max_batch_size
        dtype = trt.nptype(engine.get_binding_dtype(binding))
        
        # allocate device buffers
        mem = HostDeviceMem(size, dtype)
        bindings.append(int(mem.device))
        
        # add input list and output list
        if engine.binding_is_input(binding):
            inputs.append(mem)
        else:
            outputs.append(mem)
    
    return inputs, outputs, bindings, stream

def inference(context, bindings, inputs, outputs, stream, batch_size=1):
    """Inference"""
    # Cope input to device
    [cuda.memcpy_htod_async(inp.device, inp.host, stream) for inp in inputs]

    # Inference
    context.execute_async_v2(bindings=bindings, stream_handle=stream.handle)

    # Cope output to host from device
    [cuda.memcpy_dtoh_async(out.host, out.device, stream) for out in outputs]

    # Synchronization
    stream.synchronize()

    return [out.host for out in outputs]

# Load engine
with open(engine_path, "rb") as f, trt.Runtime(trt.Logger(trt.Logger.WARNING)) as runtime:
    engine = runtime.deserialize_cuda_engine(f.read())

# Set context
context = engine.create_execution_context()

# Allocate buffers for input list and output list
inputs, outputs, bindings, stream = allocate_buffers(engine)

# dataset e.g. Img
input_data = np.random.random((1, 3, 32, 32)).astype(np.float32)
inputs[0].host = input_data.reshape(-1)  # flatten

# inference
output = infer(context, bindings, inputs, outputs, stream)

logger.info(f"Inference completed!")
logger.info(f"Output shape: {output[0].shape}")
```

<details>
    <summary>补充</summary>
        <ul>
	      <li><strong>CNN中为什么要多次采样</strong>：提取更广的全局信息. e.g. 1st.单层感受野：conv1的3x3卷积仅能看到原始图像3x3区域(约9.4%的输入面积). 2st.双层叠加后：经过两次3x3卷积+池化后，最终每个特征点对应原始图像约14x14区域（约19%面积）
          </li>
        </ul>
</details>

