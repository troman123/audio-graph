# 短剧 / 短视频方向开源项目清单

一句话总结：如果目标是“搞到短剧/短视频可用内容”，最实用的组合通常是公开视频抓取 + 字幕转写 + 视频数据集 / 预训练模型 + 生成式补片能力。

## 推荐项目

| 项目 | GitHub | 用途说明 | 如何获取内容 | 语言 / 许可证 | 集成提示 |
| --- | --- | --- | --- | --- | --- |
| yt-dlp | https://github.com/yt-dlp/yt-dlp | 公开短视频/视频下载器 | 可抓公开视频、音轨、字幕、封面与元数据 | Python / Unlicense | 最适合作为短剧素材入口；务必确认版权与平台条款 |
| MMAction2 | https://github.com/open-mmlab/mmaction2 | 视频理解工具箱与基准仓库 | 仓库沉淀了 Kinetics、AVA、ActivityNet 等数据准备方式、模型与 demo | Python / Apache-2.0 | 适合做短剧分类、片段检索、镜头标签、动作识别 |
| PaddleVideo | https://github.com/PaddlePaddle/PaddleVideo | 视频理解与标注工具集 | 支持视频数据标注、识别、检测，并覆盖 ActivityNet / YouTube-8M 等生态 | Python / Apache-2.0 | 适合需要中文资料与完整工程范式的团队 |
| PyTorchVideo | https://github.com/facebookresearch/pytorchvideo | 视频研究库 | 提供公开视频理解模型、数据管道与训练组件 | Python / Apache-2.0 | 如果主栈在 PyTorch，接入成本较低 |
| WhisperX | https://github.com/m-bain/whisperX | 视频/音频逐词时间戳转写与说话人分离 | 可直接把短剧视频转成高精度字幕、说话人标签和对齐结果 | Python / BSD-2-Clause | 适合做字幕生成、台词切分、角色对话抽取 |
| Open-Sora | https://github.com/hpcaitech/Open-Sora | 开源视频生成项目 | 提供公开视频生成模型、示例与 checkpoint 生态，可补充宣传片段或过场镜头 | Python / Apache-2.0 | 适合做“缺素材时补镜头”的生成式工作流 |
| Open-Sora-Plan | https://github.com/PKU-YuanGroup/Open-Sora-Plan | Sora 复现路线与视频生成能力 | 提供训练/推理代码、样例和社区权重路线 | Python / MIT | 更适合研究或定制化二开 |
| Objectron | https://github.com/google-research-datasets/Objectron | 含大量短视频片段与逐帧标注的数据集 | 可直接获取约 15K 段 object-centric 短视频和对应图像标注 | Jupyter Notebook / Other | 如果你更偏“短视频素材理解”而非剧情文本，可直接复用 |

## 选型建议

- **想先拿公开短剧/短视频素材**：优先 yt-dlp。
- **想自动出字幕、台词轴、角色说话段**：优先 WhisperX。
- **想做标签、检索、审核、切片推荐**：优先 MMAction2、PaddleVideo、PyTorchVideo。
- **想生成过场镜头、补镜头、实验性短片**：优先 Open-Sora、Open-Sora-Plan。

## 3-5 步快速获取 / 集成示例

1. 抓取公开短视频素材（仅限你有权使用的内容）：
   ```bash
   yt-dlp -f "bv*+ba/b" -o "clips/%(id)s.%(ext)s" "<PUBLIC_VIDEO_URL>"
   ```
2. 为短剧视频自动生成字幕与时间轴：
   ```bash
   pip install whisperx
   whisperx ./clips/demo.mp4 --model small
   ```
3. 按任务挑选公开视频数据与模型：
   - MMAction2: https://github.com/open-mmlab/mmaction2
   - PaddleVideo: https://github.com/PaddlePaddle/PaddleVideo
   - PyTorchVideo: https://github.com/facebookresearch/pytorchvideo
4. 若需要生成补镜头或视觉过场，查看 Open-Sora：
   https://github.com/hpcaitech/Open-Sora
5. 若更关注带标注的短视频数据，可直接看 Objectron：
   https://github.com/google-research-datasets/Objectron
