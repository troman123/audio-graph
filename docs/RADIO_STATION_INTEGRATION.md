# 广播电台接入指南（Swift iOS）

本文档整理了在 iOS 应用中接入互联网广播电台流的方案，包括接入方式、可用音源说明以及版权风险分析。

---

## 目录

1. [接入方式（Swift iOS）](#1-接入方式swift-ios)
2. [可用接入源说明](#2-可用接入源说明)
3. [版权风险分析](#3-版权风险分析)
4. [风险缓解建议](#4-风险缓解建议)

---

## 1. 接入方式（Swift iOS）

### 1.1 核心框架：AVFoundation

iOS 原生支持通过 `AVPlayer` 播放 HTTP Live Streaming (HLS)、Icecast/Shoutcast (MP3/AAC) 等网络音频流。

```swift
import AVFoundation

class RadioPlayer {
    private var player: AVPlayer?

    func play(streamURL: URL) {
        let playerItem = AVPlayerItem(url: streamURL)
        player = AVPlayer(playerItem: playerItem)
        player?.play()
    }

    func stop() {
        player?.pause()
        player = nil
    }
}

// 使用示例
let url = URL(string: "https://stream.example.com/radio.mp3")!
let radioPlayer = RadioPlayer()
radioPlayer.play(streamURL: url)
```

### 1.2 后台播放配置

在 `Info.plist` 中添加：

```xml
<key>UIBackgroundModes</key>
<array>
    <string>audio</string>
</array>
```

在 AppDelegate 或播放器初始化时配置 Audio Session：

```swift
import AVFoundation

do {
    try AVAudioSession.sharedInstance().setCategory(.playback, mode: .default)
    try AVAudioSession.sharedInstance().setActive(true)
} catch {
    print("Audio Session 配置失败: \(error)")
}
```

### 1.3 锁屏与控制中心信息展示

```swift
import MediaPlayer

func updateNowPlayingInfo(title: String, artist: String) {
    var info = [String: Any]()
    info[MPMediaItemPropertyTitle] = title
    info[MPMediaItemPropertyArtist] = artist
    info[MPNowPlayingInfoPropertyIsLiveStream] = true
    MPNowPlayingInfoCenter.default().nowPlayingInfo = info
}
```

### 1.4 元数据（歌曲名称）获取

Icecast/Shoutcast 流会通过 ICY metadata 协议传递当前播放的曲目信息：

```swift
// 监听 AVPlayerItem 的 timedMetadata
NotificationCenter.default.addObserver(
    forName: .AVPlayerItemNewAccessLogEntry,
    object: playerItem,
    queue: .main
) { _ in
    // 处理日志
}

// 通过 KVO 监听元数据
playerItem.addObserver(self, forKeyPath: "timedMetadata", options: .new, context: nil)

override func observeValue(forKeyPath keyPath: String?,
                           of object: Any?,
                           change: [NSKeyValueChangeKey: Any]?,
                           context: UnsafeMutableRawPointer?) {
    if keyPath == "timedMetadata",
       let metadata = playerItem.timedMetadata {
        for item in metadata {
            if let title = item.stringValue {
                print("当前播放: \(title)")
            }
        }
    }
}
```

### 1.5 推荐第三方库

| 库名 | 用途 | 许可证 |
|------|------|--------|
| [FreeStreamer](https://github.com/muhku/FreeStreamer) | 低延迟音频流播放，支持 Shoutcast/Icecast | BSD-3 |
| [StreamingKit](https://github.com/tumtumtum/StreamingKit) | 流式音频播放引擎 | MIT |
| [AudioKit](https://github.com/AudioKit/AudioKit) | 高级音频处理框架 | MIT |

---

## 2. 可用接入源说明

### 2.1 公开免费广播流

| 来源 | 说明 | 流地址格式 | 许可状态 |
|------|------|-----------|---------|
| [Radio Browser API](https://www.radio-browser.info/) | 全球最大的开放广播电台目录，提供 RESTful API，可检索 30,000+ 电台 | 各电台提供的 HTTP 流地址 | 公开 API，电台内容版权各异 |
| [Icecast Directory](https://dir.xiph.org/) | Xiph.org 运营的开源流媒体目录 | Ogg/Opus/MP3 流 | 目录本身开源，内容版权各异 |
| [SomaFM](https://somafm.com/) | 非营利性独立电台，有多个频道 | MP3/AAC 流 | 允许个人收听，商业应用需授权 |
| [BBC Sounds](https://www.bbc.co.uk/sounds) | BBC 旗下广播频道 | HLS | 仅限英国地区，有地域限制 |
| [NPR](https://www.npr.org/) | 美国公共广播 | MP3 流 | 个人收听免费，二次分发需授权 |

### 2.2 Radio Browser API 接入示例

Radio Browser 是最推荐的电台目录接入方案，完全免费开源：

```swift
struct RadioStation: Codable {
    let name: String
    let url: String          // 流地址
    let urlResolved: String  // 解析后的实际流地址
    let country: String
    let language: String
    let codec: String
    let bitrate: Int

    enum CodingKeys: String, CodingKey {
        case name, url, country, language, codec, bitrate
        case urlResolved = "url_resolved"
    }
}

func searchStations(keyword: String) async throws -> [RadioStation] {
    let baseURL = "https://de1.api.radio-browser.info/json/stations/byname/"
    guard let url = URL(string: baseURL + keyword.addingPercentEncoding(
        withAllowedCharacters: .urlPathAllowed)!) else {
        throw URLError(.badURL)
    }
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode([RadioStation].self, from: data)
}
```

### 2.3 中国大陆广播源

| 来源 | 说明 | 备注 |
|------|------|------|
| 蜻蜓FM | 聚合国内各大广播电台 | 需要对接其 SDK/API，有商业授权要求 |
| 喜马拉雅 | 有部分广播电台直播频道 | 同样需要商业合作 |
| 央广网 | 中央人民广播电台官方流 | 可获取公开流地址，但商业使用需授权 |
| 各省市广播电台 | 各地方电台通常有自己的网络直播流 | 版权政策因台而异 |

---

## 3. 版权风险分析

### 3.1 风险等级总览

| 风险类别 | 等级 | 说明 |
|---------|------|------|
| 音乐作品版权 | 🔴 高 | 电台播放的音乐受著作权保护，转播/录制均需授权 |
| 电台节目版权 | 🔴 高 | 电台节目本身（主持、编排）属于广播组织权 |
| 流地址未经授权使用 | 🟡 中 | 部分电台流地址仅供官方客户端使用 |
| 地域限制绕过 | 🟡 中 | 绕过地域限制可能违反服务条款 |
| 元数据使用 | 🟢 低 | 歌曲名/艺人名等元数据一般属于事实信息 |

### 3.2 详细风险说明

#### A. 音乐著作权风险

- **邻接权（录音制作者权）**：录制或缓存电台中的音乐涉及复制权
- **信息网络传播权**：将电台流转播给用户可能构成信息网络传播
- **机械复制权**：任何将流内容保存到设备的行为需要额外授权
- **影响**：若 App 内播放电台流并提供录音/缓存功能，可能面临唱片公司索赔

#### B. 广播组织权风险

- 根据《中华人民共和国著作权法》第47条，广播电台享有广播组织权
- 未经许可转播广播节目属于侵权行为
- **影响**：即便只是"转播"而非"录制"，未经电台授权也可能侵权

#### C. App Store 审核风险

- Apple 审核指南 5.2.3 明确要求：App 中的音频内容需有合法授权
- 若被投诉，App 可能被下架
- **影响**：影响整个应用的正常运营

#### D. 技术滥用风险

- 部分电台限制并发连接数，大量用户接入可能被封禁 IP
- 频繁请求可能被认定为 DDoS 攻击
- **影响**：服务不稳定，可能面临法律追究

### 3.3 各接入方式的风险对比

| 接入方式 | 版权风险 | 技术风险 | 推荐度 |
|---------|---------|---------|--------|
| Radio Browser API + 公开流 | 中（仅提供流地址导航） | 低 | ⭐⭐⭐⭐ |
| 直接嵌入第三方电台流地址 | 高（未授权转播） | 中 | ⭐⭐ |
| 对接官方 SDK（蜻蜓FM等） | 低（有正式授权） | 低 | ⭐⭐⭐⭐⭐ |
| 爬取电台流地址 | 极高 | 高 | ⭐ |

---

## 4. 风险缓解建议

### 4.1 法律合规

1. **使用开放目录 API**：优先使用 Radio Browser 等开放目录，仅做"导航"功能，让用户自行选择
2. **不做录制/缓存**：App 中不应提供任何录音、下载、缓存电台内容的功能
3. **明确免责声明**：在 App 中声明"内容由第三方电台提供，版权归原权利人所有"
4. **DMCA/侵权投诉通道**：预留版权投诉处理流程
5. **商业使用获取授权**：如需大规模商业使用，与电台签订转播协议

### 4.2 技术层面

1. **不硬编码流地址**：通过 API 动态获取，电台下线时自动移除
2. **尊重 robots.txt 与 ToS**：不爬取未公开的流地址
3. **控制并发请求**：避免对单一电台服务器造成过大压力
4. **实现断线重连与降级**：流中断时优雅降级，不反复重试

### 4.3 App Store 合规

1. 在 App 描述中明确说明"聚合第三方公开广播流"
2. 提供用户举报/反馈入口
3. 及时响应权利人的下架请求

---

## 5. 完整接入示例架构

```
┌─────────────────────────────────────────────┐
│                iOS App                       │
├─────────────────────────────────────────────┤
│  UI Layer (SwiftUI/UIKit)                   │
│    ├── 电台列表                              │
│    ├── 播放控制                              │
│    └── 锁屏 & Control Center               │
├─────────────────────────────────────────────┤
│  Service Layer                              │
│    ├── RadioBrowserService (电台搜索/分类)    │
│    ├── StreamPlayerService (AVPlayer 封装)   │
│    └── MetadataService (ICY metadata 解析)  │
├─────────────────────────────────────────────┤
│  Network Layer                              │
│    ├── URLSession (API 请求)                │
│    └── AVPlayer (音频流)                    │
└─────────────────────────────────────────────┘
         │                    │
         ▼                    ▼
   Radio Browser API     各电台流服务器
```

---

## 参考链接

- [Radio Browser API 文档](https://de1.api.radio-browser.info/)
- [Apple AVFoundation 文档](https://developer.apple.com/documentation/avfoundation)
- [Apple 后台音频指南](https://developer.apple.com/documentation/avfaudio/avaudiosession)
- [Icecast 协议说明](https://icecast.org/docs/)
- [中国著作权法全文](http://www.npc.gov.cn/npc/c30834/202011/848e73f58d4e4c5b82f69d25d46048c6.shtml)
