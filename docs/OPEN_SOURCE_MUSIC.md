# 开源音乐 / 音频项目参考

> 仓库当前仅发现 `/home/runner/work/audio-graph/audio-graph/README.md`，没有依赖文件和源码目录；因此本清单优先推荐“能直接搞到音频内容/数据/模型”的项目，并优先标注与 audio-graph 音频图谱、音频处理、可视化方向高度相关的项目。

## 仓库当前线索

- 已检查文件：`/home/runner/work/audio-graph/audio-graph/README.md`
- 未发现任何音频依赖（如 ffmpeg、librosa、torch、whisper 等）或源码实现。
- 结论：当前仓库更像空白起点，音乐/音频方向的开源参考可以直接决定后续技术栈。

## 项目清单

### 1. Amphion
- **GitHub 链接（permalink）**：https://github.com/open-mmlab/Amphion
- **用途说明**：面向音频、音乐、语音生成的综合 toolkit，覆盖 TTS、歌声、codec、voice conversion 等。
- **适合场景 / 如何获取可用内容**：
  - 可以直接拿公开模型、数据集和任务 README。
  - README 直接给出 Hugging Face 入口（`https://huggingface.co/amphion`）和 ModelScope 入口；还公开了 Emilia / Emilia-Large 数据集与多个预训练 checkpoint。
  - 如果你想一上来就“搞到大体量音频数据和模型”，这是最强候选之一。
- **与 troman123/audio-graph 的相关性说明**：**与仓库相关**；非常适合后续做音频图谱、声纹/语音/音乐特征节点、生成与分析混合流水线。
- **主要语言 & 许可证**：Python；MIT
- **重要集成提示**：仓库里按任务拆分 README（如 `models/.../README.md`）；可从 Hugging Face / ModelScope 批量拉模型，适合 Python 侧集成。

### 2. audiocraft
- **GitHub 链接（permalink）**：https://github.com/facebookresearch/audiocraft
- **用途说明**：Meta 的音频生成库，包含 MusicGen、AudioGen、MAGNeT、JASCO 等。
- **适合场景 / 如何获取可用内容**：
  - 最适合直接生成音乐或环境音。
  - 安装示例：`python -m pip install -U audiocraft`；README 还要求安装 `ffmpeg`。
  - 模型入口在仓库 `docs/MUSICGEN.md`、`docs/AUDIOGEN.md`、`docs/MAGNET.md` 等。
- **与 troman123/audio-graph 的相关性说明**：**与仓库相关**；如果 audio-graph 要做音频生成、样本构造、节点测试音频，这是最值得优先接入的项目之一。
- **主要语言 & 许可证**：Jupyter Notebook / Python 为主；MIT
- **重要集成提示**：Python 库为主；仓库中已按模型拆分文档；支持训练和推理两类接入方式。

### 3. whisper
- **GitHub 链接（permalink）**：https://github.com/openai/whisper
- **用途说明**：通用语音识别模型，可把语音/播客/视频音轨快速转文字。
- **适合场景 / 如何获取可用内容**：
  - 最适合“搞到可用文本内容/字幕/转录结果”。
  - `pip install -U openai-whisper` 后，模型会在首次推理时自动下载；命令示例：`whisper demo.mp3 --model turbo`。
  - 需要系统安装 `ffmpeg`。
- **与 troman123/audio-graph 的相关性说明**：**与仓库相关**；非常适合把音频内容先转录，再做关键词图谱、片段索引、语义节点。
- **主要语言 & 许可证**：Python；MIT
- **重要集成提示**：同时支持 CLI 和 Python；模型尺寸多，适合按机器资源切换；仓库自带 Colab notebook。

### 4. basic-pitch
- **GitHub 链接（permalink）**：https://github.com/spotify/basic-pitch
- **用途说明**：轻量级音频转 MIDI / 音高检测工具，可把录音转成可编辑符号音乐。
- **适合场景 / 如何获取可用内容**：
  - 直接把音频文件转成 MIDI，是“从现有音频搞出结构化内容”的典型工具。
  - 安装：`pip install basic-pitch`。
  - CLI 可直接对输入音频输出 MIDI，还支持 `--save-model-outputs` 保存原始模型输出。
- **与 troman123/audio-graph 的相关性说明**：**与仓库相关**；如果后续图谱要表示旋律、音高走向、音符关系，这个项目非常实用。
- **主要语言 & 许可证**：Python；Apache-2.0
- **重要集成提示**：带 CLI，也可嵌入 Python；支持 TensorFlow / CoreML / TFLite 等多种模型序列化格式。

### 5. mirdata
- **GitHub 链接（permalink）**：https://github.com/mir-dataset-loaders/mirdata
- **用途说明**：音乐信息检索（MIR）数据集统一加载器，重点不是生成，而是帮你“规范地下载公开音频数据集”。
- **适合场景 / 如何获取可用内容**：
  - 最适合直接搞数据集。
  - 安装：`pip install mirdata`。
  - README 示例可直接 `orchset.download()` 下载数据集；也支持统一 metadata / annotation / path 访问。
- **与 troman123/audio-graph 的相关性说明**：**与仓库相关**；后续做音频图谱、特征提取、基准评测时，这类数据加载层非常重要。
- **主要语言 & 许可证**：Python；BSD-3-Clause
- **重要集成提示**：Python API 很统一；文档站有各数据集说明；适合写批量下载、预处理和 benchmark 脚本。

### 6. librosa
- **GitHub 链接（permalink）**：https://github.com/librosa/librosa
- **用途说明**：Python 音频分析经典库，覆盖时频分析、节拍、音高、特征提取等。
- **适合场景 / 如何获取可用内容**：
  - 虽然它本身不是大模型库，但能直接下载示例音频并立刻做分析。
  - 安装：`python -m pip install librosa`。
  - 仓库代码和示例中广泛使用 `librosa.ex('trumpet')`、`librosa.ex('brahms')` 这类方式拉示例音频。
- **与 troman123/audio-graph 的相关性说明**：**与仓库相关**；这是 audio-graph 后续做频谱、梅尔特征、节拍图、相似度图时的基础库之一。
- **主要语言 & 许可证**：Python；ISC
- **重要集成提示**：Python 库；文档和 example gallery 非常成熟；适合与 NumPy / SciPy / matplotlib 一起用来做图谱原型。

## 如何快速上手 / 集成

1. **先把基础工具装起来**：
   - `sudo apt install ffmpeg`
   - `python -m pip install -U audiocraft openai-whisper basic-pitch mirdata librosa`
2. **最快拿到“可播放内容 / 可转录内容”**：
   - 转录：`whisper demo.mp3 --model turbo`
   - 音频转 MIDI：`basic-pitch ./out ./demo.wav`
3. **最快拿到公开数据集**：
   - `python -c "import mirdata; ds = mirdata.initialize('orchset'); ds.download()"`
4. **最快拿到样例音频做图谱实验**：
   - `python -c "import librosa; print(librosa.ex('trumpet'))"`
5. **需要生成全新音频时**：
   - 从 `https://github.com/facebookresearch/audiocraft` 的 `docs/MUSICGEN.md` 开始；
   - 如果需要更大模型/数据集入口，再接 `https://github.com/open-mmlab/Amphion`。
