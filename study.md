# YOLOv5 学习课程大纲

> **课程目标**: 从零开始掌握YOLOv5目标检测项目，理解其原理、实现和应用
> **学习方式**: 理论学习 + 代码实践 + 项目实战
> **预计时间**: 4-6周（每周5-8小时）

---

## 课程概览

| 阶段 | 主题 | 预计时间 | 难度 |
|------|------|----------|------|
| 第1阶段 | 环境搭建与基础概念 | 3-5天 | ⭐ |
| 第2阶段 | YOLOv5架构理解 | 5-7天 | ⭐⭐ |
| 第3阶段 | 训练与推理实践 | 5-7天 | ⭐⭐⭐ |
| 第4阶段 | 自定义数据训练 | 5-7天 | ⭐⭐⭐⭐ |
| 第5阶段 | 模型优化与部署 | 5-7天 | ⭐⭐⭐⭐⭐ |

---

## 第1阶段：环境搭建与基础概念

### 学习目标
- 配置YOLOv5开发环境
- 理解目标检测基本概念
- 掌握YOLOv5基本使用方法

### 课程内容

#### 1.1 环境配置（1天）
**学习内容**:
- Python 3.8+ 环境安装
- PyTorch 安装（CPU/GPU版本）
- YOLOv5项目克隆和依赖安装
- VSCode开发环境配置

**实践任务**:
```bash
# 1. 创建虚拟环境
python -m venv venv
venv\Scripts\activate  # Windows
source venv/bin/activate  # Linux/Mac

# 2. 安装依赖
pip install -r requirements.txt

# 3. 验证安装
python -c "import torch; print(torch.__version__)"
```

**检查点**: 能成功运行 `python detect.py --help`

---

#### 1.2 目标检测基础概念（1天）
**学习内容**:
- 什么是目标检测？
- 目标检测 vs 图像分类 vs 语义分割
- 常用指标：mAP、IoU、Precision、Recall
- YOLO系列发展历史（YOLOv1-v8）

**推荐阅读**:
- [目标检测入门教程](https://zhuanlan.zhihu.com/p/35584058)
- [YOLO系列论文解读](https://arxiv.org/abs/2004.10934)

**检查点**: 能解释什么是IoU、mAP，以及YOLO与其他检测算法的区别

---

#### 1.3 YOLOv5快速体验（1-2天）
**学习内容**:
- 使用预训练模型进行推理
- 理解detect.py的参数
- 尝试不同的输入源（图片、视频、摄像头）

**实践任务**:
```bash
# 1. 图片检测
python detect.py --weights yolov5s.pt --source data/images/bus.jpg

# 2. 视频检测
python detect.py --weights yolov5s.pt --source data/videos/video.mp4

# 3. 实时摄像头检测
python detect.py --weights yolov5s.pt --source 0

# 4. 调整置信度阈值
python detect.py --weights yolov5s.pt --source data/images/bus.jpg --conf-thres 0.5
```

**检查点**: 能成功运行推理并理解各参数的作用

---

### 阶段测试
**问题**:
1. 什么是目标检测？它与图像分类有什么区别？
2. 解释IoU和mAP的含义
3. 如何使用YOLOv5检测一张图片？

---

## 第2阶段：YOLOv5架构理解

### 学习目标
- 理解YOLOv5的网络架构
- 掌握核心模块的设计原理
- 理解损失函数和训练策略

### 课程内容

#### 2.1 YOLOv5整体架构（2天）
**学习内容**:
- YOLOv5的网络结构概览
- Backbone（CSPDarknet）设计
- Neck（PANet）特征融合
- Head（Detect）检测头

**代码阅读**:
- 阅读 `models/yolo.py` 中的 `DetectionModel` 类
- 阅读 `models/common.py` 中的基础模块（Conv, C3, SPPF）

**实践任务**:
```python
# 1. 查看模型结构
python -c "from models.yolo import Model; model = Model('yolov5s.yaml'); print(model)"

# 2. 可视化模型结构
# 使用Netron工具打开yolov5s.yaml
```

**检查点**: 能画出YOLOv5的网络结构图并解释各部分作用

---

#### 2.2 核心模块详解（2天）
**学习内容**:
- Conv模块：Conv2d + BatchNorm + SiLU
- C3模块：CSP Bottleneck结构
- SPPF模块：空间金字塔池化
- Detect模块：检测头设计

**代码阅读**:
- `models/common.py` 中的 `Conv`, `C3`, `SPPF` 类
- `models/yolo.py` 中的 `Detect` 类

**实践任务**:
```python
# 1. 测试单个模块
from models.common import Conv, C3
import torch

conv = Conv(3, 64, 3, 1)
x = torch.randn(1, 3, 640, 640)
y = conv(x)
print(f"Input: {x.shape}, Output: {y.shape}")
```

**检查点**: 能解释每个模块的作用和设计原理

---

#### 2.3 损失函数与训练策略（2-3天）
**学习内容**:
- YOLOv5的损失函数组成
- 边界框损失（CIoU Loss）
- 目标性损失（Objectness Loss）
- 分类损失（Classification Loss）
- 正负样本匹配策略

**代码阅读**:
- `utils/loss.py` 中的 `ComputeLoss` 类
- `utils/metrics.py` 中的 `bbox_iou` 函数

**实践任务**:
```python
# 1. 理解损失计算
from utils.loss import ComputeLoss
from models.yolo import Model
import torch

model = Model('yolov5s.yaml')
compute_loss = ComputeLoss(model)

# 模拟预测和标签
pred = [torch.randn(1, 255, 20, 20, 3)]
targets = torch.zeros((10, 6))  # [batch, class, x, y, w, h]
loss, loss_items = compute_loss(pred, targets)
print(f"Total Loss: {loss}, Loss Items: {loss_items}")
```

**检查点**: 能解释YOLOv5的损失函数组成和计算过程

----

### 阶段测试
**问题**:
1. 画出YOLOv5的网络结构图并解释各部分
2. 解释C3模块的设计原理
3. YOLOv5的损失函数由哪几部分组成？

---

## 第3阶段：训练与推理实践

### 学习目标
- 掌握YOLOv5训练流程
- 理解超参数调优
- 掌握模型验证和评估方法

### 课程内容

#### 3.1 使用公开数据集训练（2天）
**学习内容**:
- COCO128数据集介绍
- 数据集配置文件（.yaml）格式
- 训练参数详解
- 训练过程监控

**实践任务**:
```bash
# 1. 使用COCO128数据集训练
python train.py --data coco128.yaml --epochs 10 --weights yolov5s.pt --batch-size 16

# 2. 查看训练结果
# 训练日志保存在 runs/train/exp/
# 模型保存在 runs/train/exp/weights/

# 3. 使用训练好的模型推理
python detect.py --weights runs/train/exp/weights/best.pt --source data/images/bus.jpg
```

**检查点**: 能成功完成训练并理解训练过程

---

#### 3.2 超参数调优（2天）
**学习内容**:
- 学习率调度策略
- 数据增强技术
- 权重衰减和动量
- 早停策略

**实践任务**:
```bash
# 1. 使用不同超参数训练
python train.py --data coco128.yaml --epochs 10 --weights yolov5s.pt \
    --lr0 0.01 --lrf 0.1 --momentum 0.937 --weight_decay 0.0005

# 2. 启用数据增强
python train.py --data coco128.yaml --epochs 10 --weights yolov5s.pt \
    --hsv_h 0.015 --hsv_s 0.7 --hsv_v 0.4

# 3. 查看超参数配置
cat data/hyps/hyp.scratch-low.yaml
```

**检查点**: 能理解各超参数的作用并进行调优

---

#### 3.3 模型验证与评估（2-3天）
**学习内容**:
- 验证集和测试集的区别
- mAP计算方法
- 混淆矩阵分析
- PR曲线解读

**实践任务**:
```bash
# 1. 验证模型
python val.py --data coco128.yaml --weights runs/train/exp/weights/best.pt

# 2. 生成COCO格式结果
python val.py --data coco128.yaml --weights runs/train/exp/weights/best.pt \
    --task test --save-json

# 3. 速度测试
python val.py --data coco128.yaml --weights yolov5s.pt --task speed
```

**检查点**: 能理解评估指标的含义并进行模型性能分析

---

### 阶段测试
**问题**:
1. 解释训练过程中的各个参数
2. 如何评估一个目标检测模型的性能？
3. 什么是PR曲线？如何解读？

---

## 第4阶段：自定义数据训练

### 学习目标
- 掌握自定义数据集准备
- 学会训练专用模型
- 理解数据增强和标注工具

### 课程内容

#### 4.1 数据集准备（2天）
**学习内容**:
- YOLO格式数据集结构
- 数据标注工具（LabelImg, CVAT）
- 数据集划分策略
- 数据增强方法

**实践任务**:
```bash
# 1. 安装标注工具
pip install labelImg

# 2. 启动标注工具
labelImg

# 3. 创建数据集目录结构
mydata/
├── images/
│   ├── train/
│   └── val/
├── labels/
│   ├── train/
│   └── val/
└── data.yaml
```

**检查点**: 能准备符合YOLO格式的自定义数据集

---

#### 4.2 配置文件编写（1天）
**学习内容**:
- 数据集配置文件（.yaml）格式
- 类别定义和路径配置
- 超参数配置

**实践任务**:
```yaml
# data.yaml
path: ../mydata  # 数据集根目录
train: images/train  # 训练图片路径
val: images/val  # 验证图片路径

names:
  0: person
  1: car
  2: dog
```

**检查点**: 能正确编写数据集配置文件

---

#### 4.3 自定义数据训练（2-3天）
**学习内容**:
- 从头训练 vs 微调
- 训练策略选择
- 过拟合和欠拟合处理
- 模型选择和保存

**实践任务**:
```bash
# 1. 微调预训练模型
python train.py --data mydata.yaml --epochs 100 --weights yolov5s.pt

# 2. 从头训练
python train.py --data mydata.yaml --epochs 300 --weights '' --cfg yolov5s.yaml

# 3. 使用不同模型大小
python train.py --data mydata.yaml --epochs 100 --weights yolov5m.pt
```

**检查点**: 能成功训练自定义数据集并调优模型

---

#### 4.4 模型调优与问题解决（2天）
**学习内容**:
- 常见训练问题及解决方案
- 模型性能优化技巧
- 类别不平衡处理
- 小目标检测优化

**实践任务**:
```bash
# 1. 调整anchor boxes
python train.py --data mydata.yaml --epochs 100 --weights yolov5s.pt \
    --anchors 3

# 2. 调整输入尺寸
python train.py --data mydata.yaml --epochs 100 --weights yolov5s.pt \
    --img 640  # 或 512, 1280

# 3. 使用Focal Loss
# 修改utils/loss.py中的损失函数
```

**检查点**: 能诊断和解决训练中的常见问题

---

### 阶段测试
**问题**:
1. 如何准备YOLO格式的数据集？
2. 微调和从头训练有什么区别？
3. 如何处理类别不平衡问题？

---

## 第5阶段：模型优化与部署

### 学习目标
- 掌握模型导出和优化技术
- 学会在不同平台部署模型
- 理解模型压缩和加速方法

### 课程内容

#### 5.1 模型导出（2天）
**学习内容**:
- ONNX格式导出
- TensorRT格式导出
- CoreML格式导出
- TFLite格式导出

**实践任务**:
```bash
# 1. 导出为ONNX
python export.py --weights yolov5s.pt --include onnx

# 2. 导出为TensorRT（需要GPU）
python export.py --weights yolov5s.pt --include engine --device 0

# 3. 导出为TFLite
python export.py --weights yolov5s.pt --include tflite

# 4. 测试导出的模型
python detect.py --weights yolov5s.onnx --source data/images/bus.jpg
```

**检查点**: 能成功导出模型为不同格式

---

#### 5.2 模型优化（2天）
**学习内容**:
- 模型量化（INT8量化）
- 模型剪枝
- 知识蒸馏
- 推理加速

**实践任务**:
```bash
# 1. INT8量化
python export.py --weights yolov5s.pt --include onnx --half

# 2. 模型剪枝
# 使用第三方剪枝工具

# 3. 测试优化后的模型
python val.py --data coco128.yaml --weights yolov5s.onnx --task speed
```

**检查点**: 能理解并应用模型优化技术

---

#### 5.3 部署实践（2-3天）
**学习内容**:
- Python后端部署（Flask + ONNX）
- 移动端部署（Android + TFLite）
- 边缘设备部署（Jetson + TensorRT）
- Web服务部署

**实践任务**:
```python
# 1. Flask后端部署示例
from flask import Flask, request, jsonify
import cv2
import numpy as np
import onnxruntime as ort

app = Flask(__name__)
session = ort.InferenceSession("yolov5s.onnx")

@app.route('/detect', methods=['POST'])
def detect():
    file = request.files['image']
    img = cv2.imdecode(np.frombuffer(file.read(), np.uint8), cv2.IMREAD_COLOR)
    # 推理逻辑
    return jsonify(results)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

**检查点**: 能在不同平台部署YOLOv5模型

---

### 阶段测试
**问题**:
1. 为什么要导出模型为ONNX格式？
2. 模型量化的优缺点是什么？
3. 如何在移动端部署YOLOv5？

---

## 项目实战

### 实战项目：智能监控系统
**项目描述**: 使用YOLOv5构建一个实时智能监控系统，能够检测人员、车辆等目标并进行告警。

**项目要求**:
1. 使用摄像头实时检测
2. 检测到特定目标时触发告警
3. 保存检测记录和截图
4. 提供Web界面查看实时画面

**技术栈**:
- YOLOv5（目标检测）
- OpenCV（视频处理）
- Flask（Web服务）
- HTML/CSS/JavaScript（前端界面）

**预计时间**: 1-2周

---

## 学习资源

### 官方资源
- [YOLOv5官方文档](https://docs.ultralytics.com/yolov5)
- [YOLOv5 GitHub仓库](https://github.com/ultralytics/yolov5)
- [YOLOv5官方教程](https://github.com/ultralytics/yolov5/wiki)

### 推荐论文
- YOLOv5论文: [https://arxiv.org/abs/2004.10934](https://arxiv.org/abs/2004.10934)
- YOLO系列论文合集

### 推荐视频
- YOLOv5实战教程（B站）
- 目标检测基础讲解

### 推荐工具
- [LabelImg](https://github.com/tzutalin/labelImg) - 数据标注工具
- [Netron](https://netron.app/) - 模型结构可视化
- [Weights & Biases](https://wandb.ai/) - 实验跟踪工具

---

## 学习建议

### 学习方法
1. **理论与实践结合**: 每学一个概念就动手实践
2. **代码阅读**: 多阅读源码，理解实现细节
3. **实验记录**: 使用实验跟踪工具记录训练过程
4. **问题驱动**: 遇到问题主动查找解决方案

### 常见问题解决
- 训练不收敛：检查数据集、学习率、超参数
- 推理速度慢：尝试模型量化、使用TensorRT
- 检测精度低：增加训练数据、调整超参数
- 内存不足：减小batch size、使用梯度累积

### 进阶方向
- 研究YOLOv5的改进版本
- 尝试其他目标检测算法（Faster R-CNN, SSD）
- 学习实例分割和姿态估计
- 探索多模态融合

---

## 学习检查清单

### 第1阶段完成度
- [ ] 环境配置完成
- [ ] 理解目标检测基础概念
- [ ] 能成功运行YOLOv5推理

### 第2阶段完成度
- [ ] 理解YOLOv5网络架构
- [ ] 掌握核心模块原理
- [ ] 理解损失函数计算

### 第3阶段完成度
- [ ] 能使用公开数据集训练
- [ ] 理解超参数调优
- [ ] 能进行模型评估

### 第4阶段完成度
- [ ] 能准备自定义数据集
- [ ] 能训练专用模型
- [ ] 能解决训练问题

### 第5阶段完成度
- [ ] 能导出模型为不同格式
- [ ] 理解模型优化技术
- [ ] 能在不同平台部署

---

## 学习进度跟踪

**开始日期**: ___________
**预计完成日期**: ___________
**实际完成日期**: ___________

### 每周学习记录

| 周次 | 学习内容 | 完成度 | 遇到问题 | 解决方案 |
|------|----------|--------|----------|----------|
| 第1周 |          |        |          |          |
| 第2周 |          |        |          |          |
| 第3周 |          |        |          |          |
| 第4周 |          |        |          |          |
| 第5周 |          |        |          |          |
| 第6周 |          |        |          |          |

---

## 导师指导说明

作为你的课程导师，我会通过以下方式帮助你学习：

1. **阶段性检查**: 每完成一个阶段，我会检查你的学习成果
2. **问题解答**: 遇到问题时，我会提供详细的解答和指导
3. **代码审查**: 检查你的代码实现，提供改进建议
4. **项目指导**: 在项目实战阶段，我会提供技术指导
5. **学习调整**: 根据你的学习进度，调整学习计划

**开始学习前，请告诉我**:
- 你的Python和深度学习基础如何？
- 你希望重点学习哪个方向（研究/应用/部署）？
- 你每周能投入多少时间学习？

**现在开始你的YOLOv5学习之旅吧！** 🚀

---

## 附录：项目依赖评估

### 当前依赖分析

根据 `requirements.txt`，YOLOv5项目的核心依赖包括：

#### 核心依赖（必需）
| 包名 | 版本要求 | 预计大小 | 说明 |
|------|----------|----------|------|
| torch | >=1.8.0 | 800MB-2GB | 深度学习框架（CPU版较小，GPU版较大） |
| torchvision | >=0.9.0 | 20-50MB | 计算机视觉库 |
| opencv-python | >=4.6.0 | 50-100MB | 图像处理库 |
| numpy | >=1.23.5 | 20-30MB | 数值计算库 |
| matplotlib | >=3.3 | 30-50MB | 绘图库 |
| pillow | >=10.3.0 | 10-20MB | 图像处理库 |
| pandas | >=1.1.4 | 30-50MB | 数据分析库 |
| seaborn | >=0.11.0 | 10-20MB | 统计可视化库 |
| scipy | >=1.4.1 | 30-50MB | 科学计算库 |
| PyYAML | >=5.3.1 | 5-10MB | YAML解析库 |
| requests | >=2.32.2 | 5-10MB | HTTP库 |
| tqdm | >=4.66.3 | 5-10MB | 进度条库 |
| gitpython | >=3.1.30 | 10-20MB | Git操作库 |
| psutil | - | 5-10MB | 系统资源监控 |
| thop | >=0.1.1 | 5-10MB | FLOPs计算 |
| ultralytics | >=8.4.83 | 50-100MB | Ultralytics工具库 |
| urllib3 | >=2.6.0 | 5-10MB | URL处理库 |
| packaging | - | 5-10MB | 包管理工具 |
| setuptools | >=70.0.0 | 10-20MB | 包安装工具 |

#### 可选依赖（按需安装）
| 包名 | 版本要求 | 预计大小 | 用途 |
|------|----------|----------|------|
| tensorboard | >=2.4.1 | 50-100MB | 训练可视化 |
| onnx | >=1.10.0 | 50-100MB | ONNX导出 |
| onnxslim | >=0.1.82 | 10-20MB | ONNX优化 |
| coremltools | >=6.0 | 100-200MB | CoreML导出 |
| tensorflow | 2.0-2.19 | 500MB-1GB | TensorFlow导出 |
| openvino | >=2024.0 | 500MB-1GB | OpenVINO导出 |
| pycocotools | >=2.0.6 | 10-20MB | COCO评估 |

### 空间占用估算

#### 标准安装（直接运行 pip install -r requirements.txt）
- **总大小**: 约 1.1-2.6 GB
- **说明**: 自动安装requirements.txt中未注释的所有依赖（Base + Plotting + Deploy + Extras部分）
- **包含**: torch, torchvision, opencv-python, numpy, matplotlib, pandas, seaborn等18个核心包

#### 扩展安装（标准 + 常用可选）
- **总大小**: 约 1.7-3.2 GB
- **说明**: 标准安装 + 手动安装tensorboard、onnx等常用可选依赖
- **额外安装**: tensorboard, onnx, onnxslim

#### 完整安装（所有依赖）
- **总大小**: 约 3-6 GB
- **说明**: 取消注释requirements.txt中的所有可选依赖并安装
- **额外包含**: tensorflow, coremltools, openvino, clearml等所有导出和日志工具

#### 额外空间需求
- **虚拟环境**: 约 50-100 MB
- **训练数据集**: COCO128约 200MB，完整COCO约 20GB
- **预训练模型**: yolov5s.pt约 14MB，所有模型约 100MB
- **训练输出**: 每个实验约 100-500MB

### 推荐配置

#### 标准配置（推荐初学者）
```bash
# 直接安装requirements.txt中的所有未注释依赖
pip install -r requirements.txt
# 预计空间: 1.1-2.6 GB
# 包含：torch, opencv, numpy, matplotlib等18个核心包
```

#### 扩展配置（推荐进阶用户）
```bash
# 标准安装 + ONNX导出支持
pip install -r requirements.txt
pip install onnx onnxslim
# 预计空间: 1.7-3.2 GB
# 额外支持：模型导出为ONNX格式
```

#### 完整配置（推荐开发者）
```bash
# 取消注释requirements.txt中的可选依赖，然后安装
# 编辑requirements.txt，取消注释tensorboard, onnx, tensorflow等
pip install -r requirements.txt
# 预计空间: 3-6 GB
# 支持所有导出格式和日志工具
```

### PyTorch特殊说明

PyTorch是最大的依赖包，大小取决于安装方式：

**CPU版本**:
- 大小: 约 800MB-1GB
- 安装命令: `pip install torch torchvision`

**GPU版本（CUDA 11.8）**:
- 大小: 约 2-2.5GB
- 安装命令: `pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118`

**GPU版本（CUDA 12.1）**:
- 大小: 约 2-2.5GB
- 安装命令: `pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121`

### 磁盘空间建议

**最小配置**:
- 系统盘: 至少 5GB 可用空间
- 数据盘: 至少 10GB 可用空间（用于数据集和训练输出）

**推荐配置**:
- 系统盘: 至少 10GB 可用空间
- 数据盘: 至少 50GB 可用空间（用于完整数据集和多个实验）

**完整配置**:
- 系统盘: 至少 20GB 可用空间
- 数据盘: 至少 100GB 可用空间（用于多个数据集和大量实验）

### 安装建议

1. **使用虚拟环境**: 避免污染系统Python环境
2. **按需安装**: 根据实际需求选择安装哪些可选依赖
3. **定期清理**: 定期清理旧的训练输出和缓存文件
4. **使用SSD**: 将虚拟环境和数据集放在SSD上以提高性能

### 清理命令

```bash
# 清理pip缓存
pip cache purge

# 清理旧的训练输出
rm -rf runs/train/exp*

# 清理虚拟环境（如果需要重新安装）
rm -rf venv
```

### 总结

- **标准安装**: 1.1-2.6 GB（直接运行 `pip install -r requirements.txt`，适合学习和简单实验）
- **扩展安装**: 1.7-3.2 GB（标准安装 + ONNX支持，适合大多数应用）
- **完整安装**: 3-6 GB（取消注释所有可选依赖，适合完整开发和部署）

建议初学者直接运行 `pip install -r requirements.txt` 进行标准安装，根据需要后续手动添加ONNX等可选依赖。
