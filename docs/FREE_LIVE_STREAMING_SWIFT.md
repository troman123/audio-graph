# 免费直播源接入 Swift 工程方案

> 本文档梳理可免费接入、适合上架 App Store 测试使用的直播源/IPTV 资源，以及在 Swift 工程中的集成方式。

---

## 一、可免费合法使用的直播源

以下源均为**官方公开提供**或**开放授权**，适合作为测试素材上架 App Store。

### 1) Apple 官方 HLS 测试流

| 名称 | URL | 说明 |
|------|-----|------|
| Bipbop Basic | `https://devstreaming-cdn.apple.com/videos/streaming/examples/bipbop_4x3/bipbop_4x3_variant.m3u8` | Apple 官方 HLS 测试流，4x3 多码率 |
| Bipbop Advanced | `https://devstreaming-cdn.apple.com/videos/streaming/examples/img_bipbop_adv_example_ts/master.m3u8` | 高级多码率自适应测试流 |
| Bipbop fMP4 | `https://devstreaming-cdn.apple.com/videos/streaming/examples/img_bipbop_adv_example_fmp4/master.m3u8` | fMP4 封装格式测试流 |

- **授权**：Apple 官方提供，专为开发者测试使用
- **稳定性**：极高，长期可用
- **适合场景**：App Store 审核提交、功能开发测试

### 2) CGTN（中国国际电视台）

| 频道 | URL |
|------|-----|
| CGTN English | `https://news.cgtn.com/resource/live/english/cgtn-news.m3u8` |
| CGTN Français | `https://news.cgtn.com/resource/live/french/cgtn-f.m3u8` |
| CGTN Español | `https://news.cgtn.com/resource/live/spanish/cgtn-e.m3u8` |
| CGTN Arabic | `https://news.cgtn.com/resource/live/arabic/cgtn-a.m3u8` |
| CGTN Russian | `https://news.cgtn.com/resource/live/russian/cgtn-r.m3u8` |
| CGTN Documentary | `https://news.cgtn.com/resource/live/document/cgtn-doc.m3u8` |

- **授权**：官方公开直播流，无地区限制
- **稳定性**：高
- **适合场景**：新闻类直播功能测试

### 3) NASA TV

| 频道 | URL |
|------|-----|
| NASA TV Public | `https://ntv1.akamaized.net/hls/live/2014075/NASA-NTV1-HLS/master.m3u8` |
| NASA TV Media | `https://ntv2.akamaized.net/hls/live/2013923/NASA-NTV2-HLS/master.m3u8` |

- **授权**：美国政府公共域内容，免费使用
- **稳定性**：高
- **适合场景**：科技/教育类直播测试

### 4) Bloomberg TV（财经）

| 频道 | URL |
|------|-----|
| Bloomberg US | `https://www.bloomberg.com/media-manifest/streams/us.m3u8` |

- **授权**：官方公开直播
- **稳定性**：中-高
- **适合场景**：财经类直播测试

### 5) 其他公开源

| 频道 | URL | 说明 |
|------|-----|------|
| ABC News (AU) | `https://abc-iview-mediapackagestreams-2.akamaized.net/out/v1/6e1cc6d25ec0480ea099a5399d73bc4b/index.m3u8` | 澳大利亚公共广播 |
| Al Jazeera English | `https://live-hls-web-aje.getaj.net/AJE/01.m3u8` | 半岛电视台英文 |
| France 24 English | `https://stream.france24.com/F24_EN_LO_HLS/live_web.m3u8` | 法国国际新闻 |
| DW English | `https://dwamdstream102.akamaized.net/hls/live/2015525/dwstream102/index.m3u8` | 德国之声英文 |
| NHK World | `https://nhkworld.webcdn.stream.ne.jp/www11/nhkworld-tv/domestic/263942/live_wa_s.m3u8` | NHK 世界频道 |

---

## 二、Swift 工程集成方案

### 方案概览

使用 Apple 原生 `AVKit` + `AVFoundation` 框架，**零第三方依赖**，可直接上架 App Store。

### 1) 基础播放器 — AVPlayerViewController

```swift
import AVKit
import AVFoundation

class LiveStreamViewController: UIViewController {
    
    private var player: AVPlayer?
    
    func playStream(url: String) {
        guard let streamURL = URL(string: url) else { return }
        
        player = AVPlayer(url: streamURL)
        
        let playerVC = AVPlayerViewController()
        playerVC.player = player
        playerVC.allowsPictureInPicturePlayback = true
        
        present(playerVC, animated: true) {
            self.player?.play()
        }
    }
}
```

### 2) 自定义播放器 — AVPlayerLayer

```swift
import AVFoundation
import UIKit

class CustomPlayerView: UIView {
    
    override class var layerClass: AnyClass { AVPlayerLayer.self }
    
    private var playerLayer: AVPlayerLayer { layer as! AVPlayerLayer }
    private var player: AVPlayer?
    
    func configure(with url: URL) {
        let asset = AVURLAsset(url: url)
        let playerItem = AVPlayerItem(asset: asset)
        
        // 监听播放状态
        playerItem.addObserver(self, forKeyPath: "status", options: [.new], context: nil)
        
        player = AVPlayer(playerItem: playerItem)
        playerLayer.player = player
        playerLayer.videoGravity = .resizeAspect
    }
    
    func play() { player?.play() }
    func pause() { player?.pause() }
    
    override func observeValue(forKeyPath keyPath: String?,
                               of object: Any?,
                               change: [NSKeyValueChangeKey: Any]?,
                               context: UnsafeMutableRawPointer?) {
        if keyPath == "status",
           let item = object as? AVPlayerItem {
            switch item.status {
            case .readyToPlay:
                print("✅ 直播流就绪")
            case .failed:
                print("❌ 加载失败: \(item.error?.localizedDescription ?? "")")
            default:
                break
            }
        }
    }
}
```

### 3) M3U8 播放列表解析与频道管理

```swift
import Foundation

struct LiveChannel: Identifiable, Codable {
    let id: UUID
    let name: String
    let url: String
    let category: String
    let logo: String?
    
    init(name: String, url: String, category: String = "默认", logo: String? = nil) {
        self.id = UUID()
        self.name = name
        self.url = url
        self.category = category
        self.logo = logo
    }
}

class ChannelManager: ObservableObject {
    @Published var channels: [LiveChannel] = []
    
    /// 内置测试频道
    static let testChannels: [LiveChannel] = [
        LiveChannel(name: "Apple HLS Test", url: "https://devstreaming-cdn.apple.com/videos/streaming/examples/bipbop_4x3/bipbop_4x3_variant.m3u8", category: "测试"),
        LiveChannel(name: "CGTN English", url: "https://news.cgtn.com/resource/live/english/cgtn-news.m3u8", category: "新闻"),
        LiveChannel(name: "NASA TV", url: "https://ntv1.akamaized.net/hls/live/2014075/NASA-NTV1-HLS/master.m3u8", category: "科技"),
        LiveChannel(name: "France 24", url: "https://stream.france24.com/F24_EN_LO_HLS/live_web.m3u8", category: "新闻"),
        LiveChannel(name: "DW English", url: "https://dwamdstream102.akamaized.net/hls/live/2015525/dwstream102/index.m3u8", category: "新闻"),
    ]
    
    /// 从 M3U 文件解析频道列表
    func parseM3U(from content: String) -> [LiveChannel] {
        var channels: [LiveChannel] = []
        let lines = content.components(separatedBy: .newlines)
        
        var currentName: String?
        for line in lines {
            if line.hasPrefix("#EXTINF:") {
                // 格式: #EXTINF:-1,频道名
                currentName = line.components(separatedBy: ",").last?.trimmingCharacters(in: .whitespaces)
            } else if line.hasPrefix("http"), let name = currentName {
                channels.append(LiveChannel(name: name, url: line))
                currentName = nil
            }
        }
        return channels
    }
    
    /// 用户导入外部 M3U 文件
    func importFromURL(_ url: URL) async throws {
        let (data, _) = try await URLSession.shared.data(from: url)
        guard let content = String(data: data, encoding: .utf8) else { return }
        let parsed = parseM3U(from: content)
        await MainActor.run {
            self.channels.append(contentsOf: parsed)
        }
    }
}
```

### 4) SwiftUI 完整示例

```swift
import SwiftUI
import AVKit

struct LivePlayerView: View {
    @StateObject private var manager = ChannelManager()
    @State private var selectedChannel: LiveChannel?
    @State private var player: AVPlayer?
    
    var body: some View {
        NavigationView {
            List {
                ForEach(ChannelManager.testChannels) { channel in
                    Button(channel.name) {
                        selectedChannel = channel
                        playChannel(channel)
                    }
                }
            }
            .navigationTitle("直播频道")
        }
        .fullScreenCover(item: $selectedChannel) { channel in
            VideoPlayer(player: player)
                .ignoresSafeArea()
                .onDisappear { player?.pause() }
        }
    }
    
    private func playChannel(_ channel: LiveChannel) {
        guard let url = URL(string: channel.url) else { return }
        player = AVPlayer(url: url)
        player?.play()
    }
}
```

### 5) Info.plist 配置

```xml
<!-- 允许 HTTP 流媒体（部分源为 HTTP） -->
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>

<!-- 后台音频播放 -->
<key>UIBackgroundModes</key>
<array>
    <string>audio</string>
</array>
```

---

## 三、App Store 审核注意事项

| 要点 | 说明 |
|------|------|
| 内容来源 | 仅使用官方公开直播源，或由用户自行导入 |
| 免责声明 | App 内标注"直播内容由第三方提供，与本应用无关" |
| 用户导入 | 提供"添加自定义源"功能，审核时展示内置合法测试源 |
| 画中画 | 添加 PiP 支持有助于审核通过（展示 platform integration） |
| 年龄分级 | 如含新闻类内容，分级选择 12+ 或 17+ |

---

## 四、项目结构建议

```
YourApp/
├── Models/
│   └── LiveChannel.swift          # 频道数据模型
├── Services/
│   ├── ChannelManager.swift       # 频道管理 & M3U 解析
│   └── StreamValidator.swift      # 流有效性检测
├── Views/
│   ├── ChannelListView.swift      # 频道列表
│   ├── LivePlayerView.swift       # 播放器页面
│   └── AddChannelView.swift       # 用户添加自定义源
├── Resources/
│   └── default_channels.json      # 内置测试频道 JSON
└── Info.plist
```

---

## 五、总结

| 方面 | 选择 |
|------|------|
| 播放框架 | AVKit + AVFoundation（原生，无第三方依赖） |
| 直播格式 | HLS (M3U8)，iOS 原生支持 |
| 测试源 | Apple 官方测试流 + CGTN + NASA TV |
| 上架策略 | 内置合法源 + 用户自行导入 |
| 版权合规 | 仅使用官方公开流，App 内添加免责声明 |

---

## 六、参考资源

- [Apple HLS Authoring Specification](https://developer.apple.com/documentation/http-live-streaming/hls-authoring-specification-for-apple-devices)
- [AVFoundation Programming Guide](https://developer.apple.com/av-foundation/)
- [AVKit Documentation](https://developer.apple.com/documentation/avkit)
- [iptv-org/iptv](https://github.com/iptv-org/iptv) — 全球 IPTV 源合集（用户自行导入参考）
