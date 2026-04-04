# SAM3 项目配置与使用说明

本文档为 facebookresearch/sam3 仓库（SAM 3: Segment Anything with Concepts）的本地配置、安装与快速上手指南，适用于希望在本地运行示例、Notebook 或做开发/微调的研究者与工程师。文档基于仓库 README 与官方说明整理，并补充了可复现的命令与常见问题排查要点。

目录
- 概要
- 前置条件
- 推荐的 Conda 环境（示例）
- PyTorch 与可选依赖安装
- 克隆与安装仓库（可编辑模式）
- 检查点（checkpoints）下载与 Hugging Face 授权
- 快速推理示例（图像/视频）
- 运行 Jupyter Notebook 示例
- 开发与训练（可选）
- 常见问题与排查
- 性能与调优建议
- 参考链接

---

## 概要
SAM3 是 Meta 发布的开源分割模型 (SAM 3)，支持基于文本与视觉提示的图像和视频分割与跟踪。仓库包含模型构建、示例 notebook、评估脚本与 SA-Co 数据集相关工具。

## 前置条件
- 操作系统：推荐 Ubuntu 20.04 / 22.04
- Python：3.12 或更高（官方要求）
- CUDA：12.6 或更高（请确保显卡驱动与 CUDA 版本兼容）
- GPU：支持 CUDA 的 NVIDIA GPU（建议 16GB+ 显存以便运行较大批次）
- 磁盘：50GB 以上可用空间（模型、缓存、数据集）

在开始之前，建议准备一个干净的 Conda 环境以避免依赖冲突。

## 推荐的 Conda 环境（示例）
以下示例基于 Conda（官方安装步骤也在 README 中给出）。

```bash
conda create -n sam3 python=3.12 -y
conda activate sam3
pip install --upgrade pip setuptools wheel
```

如果你更喜欢 `venv`，也可以创建并激活一个 Python 虚拟环境，但 Conda 在管理 CUDA/PyTorch 相关包时通常更方便。

## PyTorch 与可选依赖安装
请根据你的 CUDA 版本选择合适的 PyTorch wheel。以下示例使用 CUDA 12.8/cu128 的索引：

```bash
# 示例：安装兼容 cu128 的 PyTorch（请以 pytorch.org 的安装命令为准）
pip install torch==2.10.0 torchvision --index-url https://download.pytorch.org/whl/cu128
```

可选加速依赖（根据 README）：

```bash
pip install einops ninja
# flash-attn（可选，加速 transformer 推理）
pip install flash-attn-3 --no-deps --index-url https://download.pytorch.org/whl/cu128
# 一些额外库
pip install git+https://github.com/ronghanghu/cc_torch.git
```

注意：部分加速库可能需要与 CUDA/PyTorch 版本精确匹配，安装前请阅读对应包的安装说明。

## 克隆与安装仓库（可编辑模式）

```bash
git clone https://github.com/facebookresearch/sam3.git
cd sam3
# 可选切换到稳定 tag
git checkout main
# 可编辑安装，便于开发
pip install -e .
# 如需 notebooks 依赖
pip install -e ".[notebooks]"
# 开发/训练依赖
pip install -e ".[dev,train]"
```

安装后，你可以通过 `pip show sam3` 或者在 Python 中尝试导入模块来确认安装成功。

## 检查点（checkpoints）与 Hugging Face 授权
模型检查点由官方在 Hugging Face 发布（private/restricted 可能需要申请访问）。按照 README，你需要：

1. 访问 https://huggingface.co/facebook/sam3 并请求/接受访问权限。
2. 在本机配置 Hugging Face CLI：

```bash
pip install huggingface_hub
huggingface-cli login
```

3. 使用脚本下载或按 README 指定的路径放置权重文件。仓库中的 `examples` 与模型构建代码默认会读取约定的 checkpoint 路径。

如果你在下载时遇到权限错误，请确认 GitHub/HF token 已登录且有访问权。

## 快速推理示例
下面给出一个最小图像推理示例，基于仓库中 `sam3.model_builder` 与 `Sam3Processor`：

```python
from PIL import Image
from sam3.model_builder import build_sam3_image_model
from sam3.model.sam3_image_processor import Sam3Processor

# 加载模型（假设 checkpoint 已按 README 配置）
model = build_sam3_image_model()
processor = Sam3Processor(model)

# 读取图片
image = Image.open('path/to/your.jpg').convert('RGB')
state = processor.set_image(image)

# 文本提示
out = processor.set_text_prompt(state, prompt='a dog')
masks, boxes, scores = out['masks'], out['boxes'], out['scores']
print(len(masks), boxes.shape)
```

对于视频推理，请参考 `build_sam3_video_predictor()` 与 `examples/sam3_video_predictor_example.ipynb`。

## 运行 Jupyter Notebook 示例
仓库 `examples/` 下包含多个 notebook。要运行它们：

```bash
# 确保已安装 notebook 依赖
pip install -e ".[notebooks]"
jupyter notebook
# 或者启动 jupyter lab
jupyter lab
```

打开 `examples/sam3_image_predictor_example.ipynb` 等并根据笔记本开头的说明设置 checkpoint 与数据路径。

## 开发与训练（简要）
若你计划做进一步训练或开发：

```bash
pip install -e ".[train,dev]"
# 格式化代码
ufmt format .
# 运行训练脚本（示例，具体参数请参考仓库训练 README）
python train/some_train_script.py --cfg configs/xxx.yaml
```

请先阅读 `README_TRAIN.md` 与仓库中的 `scripts/` 目录以了解训练所需的配置、数据路径与资源需求。

## 常见问题与排查
- 错误：`ModuleNotFoundError` 或导入失败
  - 检查当前 Python 环境是否为你安装依赖时使用的环境（`which python`、`pip show sam3`）。
- 错误：Hugging Face 权限或下载被拒绝
  - 使用 `huggingface-cli login` 并确认 token 有访问模型权限。
- 错误：CUDA 版本不兼容或 `libcuda.so` 未找到
  - 运行 `nvidia-smi` 检查驱动；确认 PyTorch 与 CUDA 版本匹配。
- 错误：flash-attn / 编译依赖失败
  - 这些加速库通常依赖特定 CUDA/torch ABI，尝试使用 pip 轮子或参照项目文档编译安装。

若遇到具体错误，请把完整的 traceback 粘贴到 issue 或给我查看，我会基于日志给出建议。

## 性能与调优建议
- 在推理时减少图像分辨率或使用更小的 batch 以降低显存占用。
- 对于大规模批量处理，使用仓库中提供的 batched inference notebook 示例，并尽量开启多线程/向量化处理。
- 若需要更高速度，尝试安装并验证 `flash-attn` 与其他加速库，但注意版本兼容性。

## 参考链接
- GitHub: https://github.com/facebookresearch/sam3
- Paper: https://ai.meta.com/research/publications/sam-3-segment-anything-with-concepts/
- Demo / Blog / Hugging Face: 链接见仓库 README

---

如果你希望，我可以：
- 基于你的本机环境（请提供 OS、Python、CUDA、GPU 型号）生成精确的安装命令与 conda 环境文件（`environment.yml`）；或
- 帮你把 `examples` 中的 notebook 转为更简洁的 quickstart 脚本并在你的机器上验证运行。

请告诉我下一步偏好。 
