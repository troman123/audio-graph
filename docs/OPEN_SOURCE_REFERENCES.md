# Open Source References for Images / Music / Short-Video

## 简短总结

- 当前仓库（`troman123/audio-graph`）在 `main` 分支下内容非常精简：仅发现 `README.md`（标题为 `audio-graph`），未发现依赖清单（如 `package.json`、`pyproject.toml`、`requirements.txt`、`go.mod`、`Cargo.toml`）或代码目录；因此暂未看到图片/短剧方向的现有实现线索。
- 与“音频图谱/可视化/音频处理”最直接相关的优先推荐：
  1. **katspaugh/wavesurfer.js**（波形可视化，Web 可直接嵌入）
  2. **librosa/librosa**（频谱/特征提取，适合离线分析流水线）
  3. **FFmpeg/FFmpeg**（音视频解复用、转码与抽音频，适合作为底层处理链）
- 建议先在 `audio-graph` 中定义最小可行流程：`FFmpeg` 预处理音频 + `librosa` 生成特征 + `wavesurfer.js`（或前端可视化层）展示。

## 仓库现状检查（已查看路径）

- `README.md`
- 通过依赖文件模式扫描未命中：
  - `**/package.json`
  - `**/go.mod`
  - `**/pyproject.toml`
  - `**/Cargo.toml`
  - `**/requirements.txt`

---

## 图片（图像处理 / 生成 / 识别 / 管理）

| 项目名 | GitHub 链接（permalink） | 用途（1-2 行） | 适合场景 / 与 audio-graph 集成方式 | 主要语言 & 许可证 | 安装/集成提示 |
|---|---|---|---|---|---|
| OpenCV | https://github.com/opencv/opencv | 经典计算机视觉与图像处理库，覆盖检测、几何变换、滤波、跟踪等。 | 可为音频图增加“封面图/视频帧”分析能力，如镜头关键帧提取后与音频事件对齐。 | C++；Apache-2.0 | 提供 C++ 核心与多语言绑定；可作为本地库嵌入，也可命令行工具链集成。 |
| Pillow | https://github.com/python-pillow/Pillow | Python 图像读写与基础处理事实标准库。 | 适合生成音频可视化导出图（缩略图、贴图、标注层）。 | Python；Other（Pillow License） | 轻量、API 稳定，适合脚本化处理与批量任务。 |
| scikit-image | https://github.com/scikit-image/scikit-image | 科学计算导向的图像处理库，算法模块丰富。 | 可做谱图后处理（边缘、分割、形态学）辅助音频图特征增强。 | Python；Other（BSD 类） | 与 NumPy/SciPy 生态兼容，适合研究型与离线流水线。 |
| Ultralytics YOLO | https://github.com/ultralytics/ultralytics | 目标检测/分割/跟踪框架，工程化程度高。 | 用于视频画面语义理解，和音频节拍/事件做多模态时间对齐。 | Python；AGPL-3.0 | 提供 Python API 与 CLI；推理可独立为服务。 |
| Diffusers | https://github.com/huggingface/diffusers | 扩散模型框架，支持图像/视频/音频生成。 | 可用于“音频驱动视觉素材”或封面生成（与音频特征联动）。 | Python；Apache-2.0 | Python API 完整，可与 Hugging Face 模型生态直接结合。 |
| Immich | https://github.com/immich-app/immich | 高性能自托管照片/视频管理系统。 | 可作为媒体资产层，管理音频图项目产生的封面图、短视频素材。 | TypeScript；AGPL-3.0 | 提供 Web/移动端与服务端架构，适合集成媒体管理工作流。 |

---

## 音乐（音频处理 / 频谱 / 生成 / 可视化 / 音频图）

| 项目名 | GitHub 链接（permalink） | 用途（1-2 行） | 适合场景 / 与 audio-graph 集成方式 | 主要语言 & 许可证 | 安装/集成提示 |
|---|---|---|---|---|---|
| librosa | https://github.com/librosa/librosa | 音乐与音频分析库，包含 STFT、Mel、节拍、色度等。 | **与仓库相关**：可直接生成音频图谱核心特征（谱图、节奏、段落）。 | Python；ISC | Python API 成熟，适合离线特征提取与实验迭代。 |
| FFmpeg | https://github.com/FFmpeg/FFmpeg | 音视频编解码与处理基础设施。 | **与仓库相关**：作为输入标准化层，统一采样率、分离音轨、切片。 | C；Other（LGPL/GPL 组合） | 提供 CLI 与可链接库（libav*），适合 CI 与生产流水线。 |
| wavesurfer.js | https://github.com/katspaugh/wavesurfer.js | Web 端音频波形播放器与可视化库。 | **与仓库相关**：前端可直接展示波形、标注区间、交互定位。 | TypeScript；BSD-3-Clause | 浏览器可嵌入；插件机制支持 Regions、Timeline、Spectrogram。 |
| basic-pitch | https://github.com/spotify/basic-pitch | 音频转 MIDI（含 pitch bend）模型。 | **与仓库相关**：可把音频图进一步结构化为音符/旋律事件图。 | Python；Apache-2.0 | 提供 Python/TFJS 路线，适合服务端批处理或前端推理。 |
| Essentia | https://github.com/MTG/essentia | 音乐信息检索（MIR）库，覆盖描述符分析与合成。 | **与仓库相关**：适合高维音频特征工程（情感、音色、节奏）扩展。 | C++；AGPL-3.0 | C++ 核心 + Python 绑定；可作高性能分析后端。 |
| JUCE | https://github.com/juce-framework/JUCE | 跨平台音频应用/插件框架。 | 若 audio-graph 未来做桌面实时可视化或插件化，可作为 GUI+DSP 基础。 | C++；Other（JUCE license） | 支持 VST/AU/AAX/LV2，适合音频工具产品化。 |

---

## 短剧 / 短视频（脚本 / 编辑 / 模板 / 自动剪辑 / 字幕 / 分镜）

| 项目名 | GitHub 链接（permalink） | 用途（1-2 行） | 适合场景 / 与 audio-graph 集成方式 | 主要语言 & 许可证 | 安装/集成提示 |
|---|---|---|---|---|---|
| MoviePy | https://github.com/Zulko/moviepy | Python 视频剪辑库，支持拼接、转场、叠字、音视频轨道处理。 | **与仓库相关**：可把音频图分析结果（节拍点/高光段）自动生成短视频片段。 | Python；MIT | Python API 友好，适合快速搭建自动剪辑原型。 |
| LosslessCut | https://github.com/mifi/lossless-cut | 基于 FFmpeg 的无损音视频切割工具。 | **与仓库相关**：按音频事件时间戳做免重编码裁切，适合高效率粗剪。 | TypeScript；GPL-2.0 | 桌面工具 + FFmpeg 工作流，适合人工复核与快速导出。 |
| Shotcut | https://github.com/mltframework/shotcut | 开源跨平台视频编辑器。 | 可作为“自动粗剪 + 人工精修”链路中的后期编辑工具。 | C++；GPL-3.0 | GUI 工作流成熟，便于非开发者参与内容生产。 |
| OpenShot | https://github.com/OpenShot/openshot-qt | 开源视频编辑器（时间线、转场、标题）。 | 可承接音频图输出的节奏点，快速生成带字幕/转场的短视频。 | Python；Other（GPLv3 系） | 可结合其库与桌面编辑器能力做半自动流程。 |
| OpenTimelineIO | https://github.com/AcademySoftwareFoundation/OpenTimelineIO | 编辑时间线交换格式与 API。 | **与仓库相关**：把 audio-graph 的分析结果导出为标准 timeline，接入 NLE。 | Python；Apache-2.0 | 提供可编程 API，适合跨工具互操作（Premiere/Resolve 管线对接）。 |
| auto-editor | https://github.com/WyattBlue/auto-editor | 自动剪辑工具，按静音/节奏等规则裁剪素材。 | **与仓库相关**：可直接消费音频分析结果，做短剧/短视频自动粗剪。 | Nim；Unlicense | CLI 优先，便于在 CI 或批处理任务中自动化运行。 |

---

## 后续建议 / 集成步骤

1. **先补全仓库基础骨架**：在 `audio-graph` 新增最小依赖清单与示例目录（如 `examples/`、`scripts/`），明确语言栈。
2. **建立最小可运行链路（建议优先）**：
   - 输入预处理：`FFmpeg`（重采样/切片/音轨提取）
   - 特征提取：`librosa`（Mel/STFT/节拍/段落）
   - 前端可视化：`wavesurfer.js`（波形+区间标注）
3. **短视频联动**：将分析得到的时间点导出为 JSON/EDL，再对接 `MoviePy` 或 `auto-editor` 自动剪辑。
4. **时间线标准化**：中长期可引入 `OpenTimelineIO`，把“音频事件 -> 时间线”作为标准中间层，便于接入更多 NLE。
5. **CI 建议**：在后续 PR 中加入示例数据下载与最小 smoke test（例如：一段音频 -> 产出谱图 JSON + 可视化页面快照）。
6. **后续 PR 可拆分建议**：
   - PR1：项目脚手架与依赖锁定
   - PR2：音频分析核心（librosa + FFmpeg）
   - PR3：前端波形可视化（wavesurfer.js）
   - PR4：短视频自动剪辑原型（MoviePy/auto-editor）
