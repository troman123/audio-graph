# 音乐方向开源项目清单

一句话总结：如果目标是“快速搞到可用音乐内容”，最有效的路径通常是先拿公开音频，再做转 MIDI、分轨、人声分离或生成式补充。

## 推荐项目

| 项目 | GitHub | 用途说明 | 如何获取内容 | 语言 / 许可证 | 集成提示 |
| --- | --- | --- | --- | --- | --- |
| yt-dlp | https://github.com/yt-dlp/yt-dlp | 公开音视频下载器 | 可从公开页面抓取音频流、元数据、字幕；也可仅抽取音频 | Python / Unlicense | 适合做“内容入口层”；务必只下载你有权获取的内容 |
| Basic Pitch | https://github.com/spotify/basic-pitch | 音频转 MIDI / 音符事件 | 输入任意常见音频文件，输出 MIDI、CSV 音符事件、可选 WAV 试听 | Python / Apache-2.0 | 适合把歌曲、哼唱、乐器素材结构化，接在检索或编曲流程前面 |
| Spleeter | https://github.com/deezer/spleeter | 预训练分轨模型 | 直接下载并使用预训练 2/4/5 stems 模型，把歌曲拆成人声/伴奏/鼓/贝斯等 | Python / MIT | 适合快速获得翻唱、BGM、配音所需的干净声部 |
| Demucs | https://github.com/facebookresearch/demucs | 高质量音乐源分离 | 使用预训练模型将混音拆分为人声、鼓、贝斯和其他轨道 | Python / MIT | 如果你更看重分离质量，可和 Spleeter 互为备选 |
| Muzic | https://github.com/microsoft/muzic | 音乐理解与生成项目集合 | 提供多类音乐生成/续写/歌词与旋律相关模型、示例与部分数据说明 | Python / MIT | 适合想做旋律续写、歌词生成、歌声相关能力的团队 |
| Magenta | https://github.com/magenta/magenta | 经典音乐生成与创作工具集 | 提供 MusicVAE、Music Transformer 等模型、示例 notebook 与训练代码 | Python / Apache-2.0 | 偏研究和创作原型，适合快速验证 AI 音乐玩法 |
| Jukebox | https://github.com/openai/jukebox | 生成式音乐模型 | 仓库提供生成模型代码与样例方向，可用于生成音乐片段 | Python / Other | 更适合做实验性生成，不太适合轻量生产环境 |

## 选型建议

- **先搞到公开音频素材**：优先 yt-dlp。
- **想把歌曲结构化成 MIDI / note events**：优先 Basic Pitch。
- **想拿到人声、伴奏、鼓点等分轨内容**：优先 Spleeter、Demucs。
- **想做 AI 作曲 / 续写 / 旋律生成**：优先 Muzic、Magenta、Jukebox。

## 3-5 步快速获取 / 集成示例

1. 抓取你有权限使用的公开音频：
   ```bash
   yt-dlp -x --audio-format mp3 -o "audio/%(title)s.%(ext)s" "<PUBLIC_URL>"
   ```
2. 把音频转成 MIDI：
   ```bash
   pip install basic-pitch
   basic-pitch ./out ./audio/demo.mp3
   ```
3. 把歌曲拆成 vocal / accompaniment：
   ```bash
   pip install spleeter
   spleeter separate -p spleeter:2stems -o output ./audio/demo.mp3
   ```
4. 如果想试生成式音乐，先看模型入口与任务说明：
   - Muzic: https://github.com/microsoft/muzic
   - Magenta: https://github.com/magenta/magenta
5. 若需要更高质量分轨，可追加评估 Demucs：
   https://github.com/facebookresearch/demucs
