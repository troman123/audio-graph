# 开源短剧 / 短视频项目参考

> 仓库当前只有 `/home/runner/work/audio-graph/audio-graph/README.md`，没有视频、图像或音频依赖；因此这里优先推荐“能直接搞到短视频内容、模型、demo assets、公开 API 或自动生成能力”的项目，并补充哪些项目最容易与后续 audio-graph demo 联动。

## 仓库当前线索

- 已检查文件：`/home/runner/work/audio-graph/audio-graph/README.md`
- 当前仓库没有前端、视频流水线、FFmpeg 依赖或多模态处理代码。
- 结论：短剧/短视频方向更适合作为未来 demo、素材生成、宣传视频、案例资产扩展。

## 项目清单

### 1. OpenMontage
- **GitHub 链接（permalink）**：https://github.com/calesthio/OpenMontage
- **用途说明**：开源 agentic 视频制作系统，覆盖选题、脚本、资产生成、配音、剪辑和成片。
- **适合场景 / 如何获取可用内容**：
  - README 明确支持从 **YouTube 视频 / Shorts / Reels / TikTok / 本地片段** 出发，自动分析 transcript、节奏、镜头和风格。
  - 还能自动拼接 royalty-free music、AI 图片、配音和 Remotion 合成，属于“直接搞短视频内容”的重型方案。
  - 仓库给出模拟命令：`python scripts/backlot_simulate_run.py`。
- **与 troman123/audio-graph 的相关性说明**：间接相关；如果后续要把 audio-graph 做成宣传视频、案例短片或内容生产工具链，这是最完整的参考。
- **主要语言 & 许可证**：Python；AGPL-3.0
- **重要集成提示**：需要 Python + FFmpeg；README 提供手动安装路径，且包含 `remotion-composer` 前端/合成链路。

### 2. Pixelle-Video
- **GitHub 链接（permalink）**：https://github.com/ATH-MaaS/Pixelle-Video
- **用途说明**：全自动短视频引擎，支持脚本生成、分镜、素材生成、TTS、视频合成。
- **适合场景 / 如何获取可用内容**：
  - README 明确支持 **Direct Model APIs**，可直接接 OpenAI、DashScope、Kling、Seedream、Seedance 等媒体模型。
  - 也支持上传自定义照片/视频做 AI 分析与脚本生成，非常适合“小说改短剧 / 文案改短视频”。
  - 若你想快速搞出一条成片，这是非常强的现成方案。
- **与 troman123/audio-graph 的相关性说明**：间接相关；未来可拿 audio-graph 的分析结果作为脚本或旁白输入。
- **主要语言 & 许可证**：Python；Apache-2.0
- **重要集成提示**：以 WebUI + API 配置为主；支持多模型编排和原子能力替换，适合接外部生成服务。

### 3. CogVideo
- **GitHub 链接（permalink）**：https://github.com/zai-org/CogVideo
- **用途说明**：文本生成视频 / 图片生成视频 / 视频编辑视频的开源视频大模型项目。
- **适合场景 / 如何获取可用内容**：
  - README 提供 Hugging Face Space、API Platform 和本地推理脚本。
  - 仓库 `inference/cli_demo.py` 直接给出命令：`python cli_demo.py --prompt "A girl riding a bike." --model_path THUDM/CogVideoX1.5-5b --generate_type "t2v"`。
  - 支持 `t2v` / `i2v` / `v2v` 三种内容生产模式。
- **与 troman123/audio-graph 的相关性说明**：间接相关；适合后续给音频 demo 自动配动态视觉内容。
- **主要语言 & 许可证**：Python；Apache-2.0
- **重要集成提示**：可走 diffusers 风格本地推理，也可先从 Hugging Face demo 试效果；脚本路径明确，适合批量化改造。

### 4. HunyuanVideo
- **GitHub 链接（permalink）**：https://github.com/Tencent-Hunyuan/HunyuanVideo
- **用途说明**：腾讯混元视频生成框架，仓库同时提供预训练权重和采样代码。
- **适合场景 / 如何获取可用内容**：
  - 官方 `ckpts/README.md` 直接给下载命令：
    - `python -m pip install "huggingface_hub[cli]"`
    - `huggingface-cli download tencent/HunyuanVideo --local-dir ./ckpts`
  - 还给出文本编码器和 CLIP 编码器的下载步骤。
  - 适合需要“先把完整权重搞到本地，再走离线生成”的团队。
- **与 troman123/audio-graph 的相关性说明**：间接相关；如果后续需要自托管视频生成，不依赖闭源 SaaS，这是强备选。
- **主要语言 & 许可证**：Python；Other / 自定义许可
- **重要集成提示**：仓库已和 diffusers 集成；权重较大，适合在有 GPU 的环境做批量生成。

### 5. LivePortrait
- **GitHub 链接（permalink）**：https://github.com/KlingAIResearch/LivePortrait
- **用途说明**：高质量人像驱动与口型/动作迁移项目，能把静态头像“动起来”。
- **适合场景 / 如何获取可用内容**：
  - 这是“拿一张图 + 一段驱动视频/音频就做口播短视频”的高性价比方案。
  - README 给出权重下载命令：`huggingface-cli download KlingTeam/LivePortrait --local-dir pretrained_weights --exclude "*.git*" "README.md" "docs"`。
  - 本地推理命令：`python inference.py`；成功后会得到 `animations/...mp4`。
- **与 troman123/audio-graph 的相关性说明**：间接相关；若未来 audio-graph 需要“虚拟讲解员”或语音解说人像视频，这是很好用的组件。
- **主要语言 & 许可证**：Python；Other / 自定义许可
- **重要集成提示**：有 Hugging Face Space、Windows 一键包、`app.py` / `inference.py`；对 demo 场景很友好。

### 6. VideoCrafter
- **GitHub 链接（permalink）**：https://github.com/AILab-CVC/VideoCrafter
- **用途说明**：文本转视频 / 图片转视频开源项目，带公开 checkpoint 链接与 Gradio 界面。
- **适合场景 / 如何获取可用内容**：
  - README 直接列出多个 Hugging Face checkpoint，例如 `VideoCrafter2`、`Text2Video-1024` 等。
  - 仓库既给 `gradio_app.py`，也有 `predict.py`、`scripts/` 目录，适合一边试 demo、一边做脚本化接入。
  - 适合快速拉起本地视频生成实验。
- **与 troman123/audio-graph 的相关性说明**：间接相关；后续可以把音频分析结论映射成 prompt，再自动生成宣传视频或片段素材。
- **主要语言 & 许可证**：Python；Other / 自定义许可
- **重要集成提示**：优先先试 Hugging Face Space，再决定是否本地下载模型；`gradio_app.py` 是最快入口。

## 如何快速上手 / 集成

1. **先选一条最省事的路**：
   - 全自动工作流优先看 `OpenMontage` / `Pixelle-Video`；
   - 纯模型推理优先看 `CogVideo` / `HunyuanVideo` / `VideoCrafter`。
2. **本地快速跑短视频生成**：
   - CogVideo：`python inference/cli_demo.py --prompt "A girl riding a bike." --model_path THUDM/CogVideoX1.5-5b --generate_type "t2v"`
3. **先把大模型权重拉下来**：
   - HunyuanVideo：`python -m pip install "huggingface_hub[cli]" && huggingface-cli download tencent/HunyuanVideo --local-dir ./ckpts`
4. **需要口播 / 数字人效果时**：
   - LivePortrait：下载权重后运行 `python inference.py`，直接产出 mp4。
5. **做成完整生产链时**：
   - OpenMontage 先装 Python + FFmpeg；再按 README 的 `requirements.txt` 和 `remotion-composer` 路径安装，结合本地音频/文案/视频素材做端到端生成。
