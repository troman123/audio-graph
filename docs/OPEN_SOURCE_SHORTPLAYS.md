# OPEN_SOURCE_SHORTPLAYS

仓库当前仅有 `README.md`（仓库路径：`README.md`）且无短剧处理流水线，建议优先采用 `yt-dlp + PySceneDetect + whisperX + moviepy` 快速搭建短剧拆解与内容入库。

## 仓库线索（已检查）
- 已检查文件：`README.md`
- 结果：未发现短剧/视频采集、切分、字幕、说话人识别相关依赖文件，需从外部开源项目补齐能力。

---

## 1) yt-dlp/yt-dlp
- **项目名**：yt-dlp
- **GitHub（permalink）**：https://github.com/yt-dlp/yt-dlp
- **用途**：下载短剧源视频/音频，支持批量 URL。
- **如何获取可用内容**：
  - 安装：`pip install yt-dlp`
  - 下载 MP4：`yt-dlp -f mp4 -o '%(id)s.%(ext)s' <短剧URL>`
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，Unlicense
- **重要集成提示**：优先保留原始 metadata（标题、上传时间、频道）。

## 2) Breakthrough/PySceneDetect
- **项目名**：PySceneDetect
- **GitHub（permalink）**：https://github.com/Breakthrough/PySceneDetect
- **用途**：自动镜头切分，快速把长视频拆为短剧片段。
- **如何获取可用内容**：
  - 安装：`pip install scenedetect[opencv]`
  - 场景检测：`scenedetect -i episode.mp4 detect-content list-scenes split-video`
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，BSD-3-Clause
- **重要集成提示**：场景边界时间戳可直接作为图谱中的“片段节点”。

## 3) m-bain/whisperX
- **项目名**：whisperX
- **GitHub（permalink）**：https://github.com/m-bain/whisperX
- **用途**：带词级时间戳和说话人分离的转写工具，适合短剧台词对齐。
- **如何获取可用内容**：
  - 安装：`pip install whisperx`
  - 转写示例：`whisperx episode.wav --model large-v2 --language zh`
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，BSD-2-Clause
- **重要集成提示**：可输出 diarization 结果，把角色与台词建立可查询关系。

## 4) Zulko/moviepy
- **项目名**：moviepy
- **GitHub（permalink）**：https://github.com/Zulko/moviepy
- **用途**：Python 视频编辑库，可拼接、裁剪、加字幕、导出片段。
- **如何获取可用内容**：
  - 安装：`pip install moviepy`
  - 裁剪示例：`python -c "from moviepy import VideoFileClip; VideoFileClip('in.mp4').subclip(0,30).write_videofile('clip.mp4')"`
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，MIT
- **重要集成提示**：和 PySceneDetect 联合可实现“自动切分 + 标准化导出”。

## 5) open-mmlab/mmaction2
- **项目名**：MMAction2
- **GitHub（permalink）**：https://github.com/open-mmlab/mmaction2
- **用途**：视频动作识别与时序定位，提供大量预训练模型。
- **如何获取可用内容**：
  - 安装文档：`docs/en/get_started/installation.md`
  - 预训练模型：`configs/` + Model Zoo（README 链接）
  - 推理示例：
    ```bash
    # 第1个参数: 输入视频；第2个参数: 配置文件；第3个参数: 预训练权重；--label-map: 类别标签
    python demo/demo.py demo/demo.mp4 \
      configs/recognition/tsn/tsn_imagenet-pretrained-r50_8xb32-1x1x3-100e_kinetics400-rgb.py \
      https://download.openmmlab.com/mmaction/v1.0/recognition/tsn/tsn_imagenet-pretrained-r50_8xb32-1x1x3-100e_kinetics400-rgb/tsn_imagenet-pretrained-r50_8xb32-1x1x3-100e_kinetics400-rgb_20220906-2692d16c.pth \
      --label-map tools/data/kinetics/label_map_k400.txt
    ```
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Python，Apache-2.0
- **重要集成提示**：可为短剧片段补充“动作/情节标签”作为图谱语义边。

## 6) pyannote/pyannote-audio
- **项目名**：pyannote-audio
- **GitHub（permalink）**：https://github.com/pyannote/pyannote-audio
- **用途**：说话人分离与语音活动检测，适合多角色短剧。
- **如何获取可用内容**：
  - 安装：`pip install pyannote.audio`
  - 预训练 pipeline（需 Hugging Face token）：`Pipeline.from_pretrained("pyannote/speaker-diarization-3.1")`
  - 模型入口：https://huggingface.co/pyannote/speaker-diarization-3.1
- **与 troman123/audio-graph 的相关性**：与仓库相关
- **主要语言与许可证**：Jupyter Notebook/Python，MIT
- **重要集成提示**：可将 speaker turn 与字幕时间轴对齐，构建“角色-台词-片段”三元关系。

---

## 快速上手（3-5 步）
1. 用 `yt-dlp` 批量下载短剧原始视频，按剧集编号落盘。  
2. 用 `PySceneDetect` 自动切分镜头，输出片段清单与切分后视频。  
3. 抽取音轨后用 `whisperX` 生成词级字幕与说话人标注。  
4. 用 `moviepy` 对片段统一分辨率/时长策略并导出标准素材。  
5. 用 `pyannote-audio` 细化角色说话区间，写入可检索的剧情图谱索引。
