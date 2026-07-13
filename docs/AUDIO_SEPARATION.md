# 音频分离（Audio Source Separation）领域梳理

## 概述

音频分离（Audio Source Separation / Music Source Separation）是将混合音频信号拆分为独立声源（人声、鼓、贝斯、其他乐器等）的技术。近年来深度学习方法已大幅超越传统信号处理方法，成为主流方案。

---

## 1. 主流开源工程

### 1.1 Demucs（Meta / Facebook Research）

| 项目 | 说明 |
|------|------|
| GitHub | https://github.com/facebookresearch/demucs |
| 语言 | Python (PyTorch) |
| 许可证 | MIT |
| 最新版本 | Hybrid Transformer Demucs (HTDemucs, v4) |
| 分离轨道 | vocals / drums / bass / other（可扩展到 6 轨） |
| 模型大小 | ~80 MB (压缩后) |
| 特点 | 当前 SOTA 级别，频域+时域混合架构，支持流式 |

**安装与使用：**
```bash
pip install demucs
# 分离音频为 4 轨
demucs input.mp3
# 指定模型
demucs --model htdemucs_ft input.mp3
```

### 1.2 Open-Unmix (UMX)

| 项目 | 说明 |
|------|------|
| GitHub | https://github.com/sigsep/open-unmix-pytorch |
| 语言 | Python (PyTorch) |
| 许可证 | MIT |
| 分离轨道 | vocals / drums / bass / other |
| 模型大小 | ~25 MB |
| 特点 | 轻量级基线模型，适合学术研究与嵌入式探索 |

**安装与使用：**
```bash
pip install openunmix
# Python API
import torchaudio
from openunmix import predict
estimates = predict.separate(audio_tensor, rate=44100)
```

### 1.3 Spleeter（Deezer Research）

| 项目 | 说明 |
|------|------|
| GitHub | https://github.com/deezer/spleeter |
| 语言 | Python (TensorFlow) |
| 许可证 | MIT |
| 分离轨道 | 2/4/5 stems 可选 |
| 模型大小 | ~100 MB |
| 特点 | 早期标杆项目，部署简单，已较少更新 |

**安装与使用：**
```bash
pip install spleeter
spleeter separate -p spleeter:4stems -o output/ input.mp3
```

### 1.4 Music Source Separation (ByteDance / BandIt)

| 项目 | 说明 |
|------|------|
| GitHub | https://github.com/bytedance/music_source_separation |
| 语言 | Python (PyTorch) |
| 许可证 | Apache 2.0 |
| 特点 | 字节跳动开源，多分辨率频带分离方案 |

### 1.5 BSRNN (Band-Split RNN)

| 项目 | 说明 |
|------|------|
| 参考实现 | https://github.com/amanteur/BandSplitRNN-Pytorch |
| 语言 | Python (PyTorch) |
| 特点 | 2023 MDX Challenge 冠军架构，频带分割+双向 RNN |

### 1.6 Audio Separator (Python 封装)

| 项目 | 说明 |
|------|------|
| GitHub | https://github.com/nomadkaraoke/python-audio-separator |
| 语言 | Python |
| 许可证 | MIT |
| 特点 | 整合多种模型（MDX-Net, VR, Demucs），提供统一 CLI/API |

### 1.7 UVR (Ultimate Vocal Remover)

| 项目 | 说明 |
|------|------|
| GitHub | https://github.com/Anjok07/ultimatevocalremovergui |
| 语言 | Python |
| 许可证 | MIT |
| 特点 | 桌面 GUI 工具，集成 MDX-Net/VR/Demucs 多模型 |

---

## 2. 当前发展方向

### 2.1 模型架构演进

| 方向 | 代表 | 说明 |
|------|------|------|
| 混合域（时域+频域） | HTDemucs | 同时利用波形和频谱信息 |
| 频带分割 + 序列建模 | BSRNN | 将频谱按频带分割后用 RNN 建模 |
| Transformer 架构 | HTDemucs, BandIt | 自注意力机制提升长程依赖建模 |
| 扩散模型 | DiffSep, MSDM | 生成式方法，质量高但推理慢 |
| 查询式分离 | AudioSep, USS | 通过文本/音频提示指定分离目标 |

### 2.2 关键趋势

1. **通用声音分离（Universal Sound Separation）**：从固定类别→任意声源分离
2. **查询驱动分离（Query-based Separation）**：用文本描述或参考音频指定要分离的声源
3. **实时/低延迟处理**：面向直播、游戏等场景的流式分离
4. **模型压缩与端侧部署**：知识蒸馏、量化、剪枝，目标是移动端实时
5. **多模态融合**：结合视觉信息（说话人面部、乐器视频）辅助分离
6. **自监督/半监督学习**：减少对大规模标注干净单轨数据的依赖

### 2.3 重要竞赛与基准

| 名称 | 说明 |
|------|------|
| MDX Challenge (Music Demixing) | ISMIR/AICrowd 主办，年度音乐分离竞赛 |
| MUSDB18-HQ | 标准评测数据集（150 首歌，4 轨分离） |
| SDR (Signal-to-Distortion Ratio) | 主要评价指标 |

---

## 3. 终端接入可行性分析

### 3.1 iOS / macOS 端

| 方案 | 可行性 | 说明 |
|------|--------|------|
| Core ML 转换 | ✅ 高 | PyTorch → ONNX → Core ML，苹果 Neural Engine 加速 |
| ONNX Runtime (Mobile) | ✅ 高 | 支持 iOS/Android，CPU/GPU/NPU |
| TensorFlow Lite | ⚠️ 中 | Spleeter 原生 TF，但新模型多为 PyTorch |

**Core ML 部署路径：**
```
PyTorch 模型 → torch.jit.trace → ONNX 导出 → onnx-coreml/coremltools 转换 → .mlmodel/.mlpackage
```

**性能参考（iPhone 15 Pro，A17 Pro）：**
- Open-Unmix (轻量)：~2-3x 实时（即 1 秒音频约 0.3-0.5 秒处理）
- HTDemucs (完整)：~0.5-1x 实时（需模型裁剪或量化）

**关键限制：**
- 内存：完整 HTDemucs 峰值内存 ~1-2 GB，需分块处理
- 模型大小：80 MB+ 影响包体积，建议按需下载
- 延迟：非流式模型需要完整音频输入，不适合实时场景

### 3.2 Android 端

| 方案 | 可行性 | 说明 |
|------|--------|------|
| ONNX Runtime | ✅ 高 | 支持 NNAPI/GPU 加速 |
| TFLite | ⚠️ 中 | 需手动转换 |
| NCNN / MNN | ✅ 高 | 国内厂商推理框架，优化好 |

### 3.3 终端部署建议

1. **轻量模型优先**：使用 Open-Unmix 或裁剪版 Demucs 作为端侧模型
2. **分块处理**：将长音频切分为 5-10 秒片段，逐块推理，overlap-add 拼接
3. **混合架构**：端侧做快速预分离（如仅人声/伴奏 2 轨），精细分离（4+ 轨）走云端
4. **模型按需加载**：首次使用时下载模型，不打入包体
5. **量化部署**：INT8 量化可减少 50-75% 模型大小，推理速度提升 2-3x

### 3.4 Swift / iOS 集成示例架构

```
┌─────────────────────────────────────────────┐
│              iOS App Layer                    │
├─────────────────────────────────────────────┤
│  AudioSeparationManager                      │
│  ├── loadModel() → Core ML / ONNX Runtime   │
│  ├── separate(audioURL:) → [Track]          │
│  └── streamSeparate(buffer:) → [Buffer]     │
├─────────────────────────────────────────────┤
│  Audio I/O (AVFoundation / AudioToolbox)     │
│  ├── 读取: AVAudioFile → PCM Float32        │
│  ├── 预处理: 重采样 → 分块 → STFT           │
│  └── 后处理: iSTFT → overlap-add → 输出     │
├─────────────────────────────────────────────┤
│  Model Runtime                               │
│  ├── Core ML (ANE/GPU/CPU)                   │
│  └── ONNX Runtime (CPU/CoreML EP)            │
└─────────────────────────────────────────────┘
```

---

## 4. 后台接入说明

### 4.1 推荐技术栈

```
┌──────────────────────────────────────────────────┐
│                  API Gateway                       │
│          (Nginx / Kong / AWS API Gateway)         │
├──────────────────────────────────────────────────┤
│             Task Queue / Message Broker            │
│            (Redis Queue / RabbitMQ / SQS)         │
├──────────────────────────────────────────────────┤
│              Worker Service (GPU)                  │
│  ├── Demucs / BSRNN 模型推理                      │
│  ├── 分块并行处理                                  │
│  └── 结果上传至 Object Storage                     │
├──────────────────────────────────────────────────┤
│           Object Storage (S3 / OSS / MinIO)       │
│  ├── 输入音频暂存                                  │
│  └── 分离结果存储（多轨 WAV/FLAC）                 │
└──────────────────────────────────────────────────┘
```

### 4.2 API 设计参考

```
POST /api/v1/separate
Request:
{
  "audio_url": "https://...",     // 或 multipart upload
  "model": "htdemucs_ft",         // 可选模型
  "stems": ["vocals", "drums", "bass", "other"],
  "output_format": "wav",         // wav / flac / mp3
  "sample_rate": 44100
}

Response:
{
  "task_id": "abc-123",
  "status": "processing",
  "estimated_time": 30            // 预计秒数
}

GET /api/v1/separate/{task_id}
Response:
{
  "task_id": "abc-123",
  "status": "completed",
  "results": {
    "vocals": "https://storage.../vocals.wav",
    "drums": "https://storage.../drums.wav",
    "bass": "https://storage.../bass.wav",
    "other": "https://storage.../other.wav"
  }
}
```

### 4.3 部署方案对比

| 方案 | 优点 | 缺点 | 适用场景 |
|------|------|------|----------|
| 单机 GPU 服务 | 简单直接 | 无法弹性伸缩 | 小规模/开发 |
| K8s + GPU Node Pool | 弹性伸缩、高可用 | 运维复杂 | 生产环境 |
| Serverless GPU (RunPod/Modal) | 按需付费、免运维 | 冷启动延迟 | 中小规模生产 |
| 云厂商 AI 平台 | 托管运维 | 定制性有限 | 快速上线 |

### 4.4 性能基准（参考值）

| 模型 | GPU | 处理速度（相对实时） | 显存占用 |
|------|-----|---------------------|----------|
| HTDemucs | A100 (80GB) | ~50x 实时 | ~4 GB |
| HTDemucs | T4 (16GB) | ~10-15x 实时 | ~4 GB |
| HTDemucs | RTX 3090 | ~30x 实时 | ~4 GB |
| Open-Unmix | T4 | ~100x 实时 | ~1 GB |
| BSRNN | A100 | ~40x 实时 | ~6 GB |

### 4.5 后台接入关键考虑

1. **异步处理**：音频分离耗时较长（一首 4 分钟歌曲约 5-15 秒），必须异步
2. **文件管理**：输入/输出文件走对象存储，避免本地磁盘瓶颈
3. **并发控制**：根据 GPU 显存限制并发数，单卡通常 2-4 路并发
4. **超时与重试**：设置合理超时（如 5 分钟），失败自动重试
5. **缓存策略**：相同音频（content hash）可缓存结果避免重复计算
6. **格式支持**：入口需支持 mp3/wav/flac/ogg 等常见格式，统一转为内部处理格式
7. **安全**：限制上传文件大小（建议 50-100 MB），防止恶意大文件

### 4.6 Python Worker 示例代码

```python
import demucs.separate
import torch
from pathlib import Path

def separate_audio(input_path: str, output_dir: str, model: str = "htdemucs_ft"):
    """
    使用 Demucs 进行音频分离
    """
    demucs.separate.main([
        "--mp3",                    # 输出 mp3 格式
        "--mp3-bitrate", "320",
        "-n", model,                # 模型名
        "-o", output_dir,           # 输出目录
        input_path                  # 输入文件
    ])
    
    # 返回分离结果路径
    stem_dir = Path(output_dir) / model / Path(input_path).stem
    return {
        "vocals": str(stem_dir / "vocals.mp3"),
        "drums": str(stem_dir / "drums.mp3"),
        "bass": str(stem_dir / "bass.mp3"),
        "other": str(stem_dir / "other.mp3"),
    }
```

---

## 5. 与 audio-graph 仓库的关联

音频分离是音频处理图（Audio Graph）中的核心节点之一，可作为：

1. **前处理节点**：将混合音频拆分后，各轨独立进入后续处理流程（EQ、混响、压缩等）
2. **分析节点**：分离后对单轨做节拍检测、音高识别、歌词对齐等
3. **内容生成**：伴奏提取 → 翻唱/remix；人声提取 → 语音克隆/TTS 训练数据

**建议集成优先级：**
1. Demucs (HTDemucs) — 效果最好，社区最活跃
2. python-audio-separator — 统一接口，灵活切换模型
3. Open-Unmix — 轻量端侧探索

---

## 6. 参考资源

- [Music Demixing Challenge](https://www.aicrowd.com/challenges/music-demixing-challenge-ismir-2023)
- [MUSDB18-HQ Dataset](https://sigsep.github.io/datasets/musdb.html)
- [SigSep - Open Resources for Music Separation](https://sigsep.github.io/)
- [Hybrid Transformers for Music Source Separation (论文)](https://arxiv.org/abs/2211.08553)
- [Band-Split RNN (论文)](https://arxiv.org/abs/2209.15174)
- [AudioSep: Separate Anything You Describe](https://github.com/Audio-AGI/AudioSep)
