# OPEN_SOURCE_MUSIC

仓库当前仅有 `README.md`（仓库路径：`README.md`）且无音频依赖清单，建议优先从 `yt-dlp + whisper + librosa` 建立“采集-转写-特征化”的音乐数据链路。

## 仓库线索（已检查）
- 已检查文件：`README.md`
- 结果：未发现 `ffmpeg/librosa/whisper` 等依赖声明文件，音乐方向目前可直接以文档中的 CLI 方案落地。

---

## 1) yt-dlp/yt-dlp
- **项目名**：yt-dlp
- **GitHub（permalink）**：https://github.com/yt-dlp/yt-dlp
- **用途**：从公开平台下载音视频，可直接抽取音轨，适合素材采集。
- **如何获取可用内容**：
  - 安装：`pip install yt-dlp`
  - 音频下载：`yt-dlp -x --audio-format mp3 -o '%(id)s.%(ext)s' <URL>`
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，Unlicense
- **重要集成提示**：注意目标站点 ToS 与版权；推荐把下载 ID 写入元数据做可追溯。

## 2) openai/whisper
- **项目名**：Whisper
- **GitHub（permalink）**：https://github.com/openai/whisper
- **用途**：通用语音识别，支持多语言转写与时间戳。
- **如何获取可用内容**：
  - 安装：`pip install -U openai-whisper`
  - 转写：`whisper input.mp3 --model medium --task transcribe --language zh`
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，MIT
- **重要集成提示**：建议输出 `json/srt/vtt` 三种格式，方便后续图谱节点（文本、片段、关键词）构建。

## 3) librosa/librosa
- **项目名**：librosa
- **GitHub（permalink）**：https://github.com/librosa/librosa
- **用途**：音乐信息检索与音频特征提取（MFCC、节拍、色度等）。
- **如何获取可用内容**：
  - 安装：`pip install librosa`
  - 示例：`python -c "import librosa; print(librosa.get_duration(path='input.wav'))"`
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，ISC
- **重要集成提示**：特征矩阵可直接作为图谱向量属性（曲目级、片段级）。

## 4) facebookresearch/audiocraft
- **项目名**：AudioCraft（含 MusicGen）
- **GitHub（permalink）**：https://github.com/facebookresearch/audiocraft
- **用途**：音乐/音频生成与压缩模型库，可快速产出可用示例音频。
- **如何获取可用内容**：
  - 安装：`pip install audiocraft`
  - 生成示例：
    ```bash
    python - <<'PY'
    from audiocraft.models import MusicGen
    import torchaudio
    model = MusicGen.get_pretrained("facebook/musicgen-small")
    model.set_generation_params(duration=8)
    wav = model.generate(["upbeat electronic track"])
    torchaudio.save("musicgen_sample.wav", wav[0].cpu(), model.sample_rate)
    PY
    ```
  - 模型入口：README 指向 Hugging Face 权重
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Jupyter Notebook/Python，MIT
- **重要集成提示**：生成内容建议保留 prompt 与 seed，便于后续可复现。

## 5) deezer/spleeter
- **项目名**：Spleeter
- **GitHub（permalink）**：https://github.com/deezer/spleeter
- **用途**：音乐源分离（人声/伴奏/鼓/贝斯等），内置预训练模型。
- **如何获取可用内容**：
  - 安装：`pip install spleeter`
  - 分离示例：`spleeter separate -p spleeter:2stems -o output input.mp3`
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，MIT
- **重要集成提示**：可将分离后 stem 分别建图，支持“人声事件”与“伴奏事件”独立检索。

## 6) mir-dataset-loaders/mirdata
- **项目名**：mirdata
- **GitHub（permalink）**：https://github.com/mir-dataset-loaders/mirdata
- **用途**：统一管理多个公开 MIR 数据集（元数据、注释、音频路径）。
- **如何获取可用内容**：
  - 安装：`pip install mirdata`
  - 数据集下载示例：`python -c "import mirdata; d=mirdata.initialize('gtzan'); d.download()"`
  - 文档：https://mirdata.readthedocs.io/en/stable/
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，BSD-3-Clause
- **重要集成提示**：适合作为标准化数据层，降低多数据集接入成本。

---

## 快速上手（3-5 步）
1. 用 `yt-dlp` 批量拉取可授权音频样本（保留来源 URL 与 ID）。  
2. 用 `whisper` 生成逐段转写与时间戳（`json + srt`）。  
3. 用 `librosa` 抽取节拍、频谱、情绪相关特征并存为结构化表。  
4. 对需要编曲分析的素材，用 `spleeter` 做 stem 分离并分别入库。  
5. 需要补充训练/演示素材时，使用 `audiocraft` 生成样本并记录 prompt。
