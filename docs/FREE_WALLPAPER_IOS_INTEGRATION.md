# 免费壁纸接入 iOS Swift App 指南

本文档整理了可免费接入 iOS Swift 应用的壁纸/图片资源 API，包括接入源、接入方式及风险说明。

---

## 一、免费壁纸 API 接入源

### 1. Unsplash

| 项目 | 说明 |
|------|------|
| 官网 | https://unsplash.com |
| API 文档 | https://unsplash.com/documentation |
| 许可证 | Unsplash License（免费商用，无需署名但鼓励署名） |
| 免费额度 | Demo 模式 50 次/小时；正式 Production 模式 5000 次/小时 |
| 图片质量 | 高质量摄影作品，适合壁纸场景 |

**Swift 接入示例：**
```swift
import Foundation

struct UnsplashService {
    private let accessKey = "YOUR_ACCESS_KEY"
    private let baseURL = "https://api.unsplash.com"

    func fetchRandomWallpaper() async throws -> URL {
        var components = URLComponents(string: "\(baseURL)/photos/random")!
        components.queryItems = [
            URLQueryItem(name: "orientation", value: "portrait"),
            URLQueryItem(name: "query", value: "wallpaper")
        ]
        var request = URLRequest(url: components.url!)
        request.setValue("Client-ID \(accessKey)", forHTTPHeaderField: "Authorization")

        let (data, _) = try await URLSession.shared.data(for: request)
        let json = try JSONSerialization.jsonObject(with: data) as! [String: Any]
        let urls = json["urls"] as! [String: String]
        return URL(string: urls["full"]!)!
    }
}
```

---

### 2. Pexels

| 项目 | 说明 |
|------|------|
| 官网 | https://www.pexels.com |
| API 文档 | https://www.pexels.com/api/documentation/ |
| 许可证 | Pexels License（免费商用，无需署名） |
| 免费额度 | 200 次/小时，20000 次/月 |
| 图片质量 | 高质量摄影作品 |

**Swift 接入示例：**
```swift
struct PexelsService {
    private let apiKey = "YOUR_API_KEY"
    private let baseURL = "https://api.pexels.com/v1"

    func searchWallpapers(query: String = "nature", page: Int = 1) async throws -> [[String: Any]] {
        var components = URLComponents(string: "\(baseURL)/search")!
        components.queryItems = [
            URLQueryItem(name: "query", value: query),
            URLQueryItem(name: "orientation", value: "portrait"),
            URLQueryItem(name: "per_page", value: "20"),
            URLQueryItem(name: "page", value: "\(page)")
        ]
        var request = URLRequest(url: components.url!)
        request.setValue(apiKey, forHTTPHeaderField: "Authorization")

        let (data, _) = try await URLSession.shared.data(for: request)
        let json = try JSONSerialization.jsonObject(with: data) as! [String: Any]
        return json["photos"] as! [[String: Any]]
    }
}
```

---

### 3. Pixabay

| 项目 | 说明 |
|------|------|
| 官网 | https://pixabay.com |
| API 文档 | https://pixabay.com/api/docs/ |
| 许可证 | Pixabay License（免费商用，无需署名） |
| 免费额度 | 100 次/分钟（需注册获取 API Key） |
| 图片质量 | 中高质量，数量庞大 |

**Swift 接入示例：**
```swift
struct PixabayService {
    private let apiKey = "YOUR_API_KEY"
    private let baseURL = "https://pixabay.com/api/"

    func fetchWallpapers(query: String = "wallpaper", page: Int = 1) async throws -> [[String: Any]] {
        var components = URLComponents(string: baseURL)!
        components.queryItems = [
            URLQueryItem(name: "key", value: apiKey),
            URLQueryItem(name: "q", value: query),
            URLQueryItem(name: "image_type", value: "photo"),
            URLQueryItem(name: "orientation", value: "vertical"),
            URLQueryItem(name: "per_page", value: "20"),
            URLQueryItem(name: "page", value: "\(page)")
        ]

        let (data, _) = try await URLSession.shared.data(for: URLRequest(url: components.url!))
        let json = try JSONSerialization.jsonObject(with: data) as! [String: Any]
        return json["hits"] as! [[String: Any]]
    }
}
```

---

### 4. Wallhaven

| 项目 | 说明 |
|------|------|
| 官网 | https://wallhaven.cc |
| API 文档 | https://wallhaven.cc/help/api |
| 许可证 | 免费使用，API 无需 Key 即可访问（部分 NSFW 内容需 API Key） |
| 免费额度 | 45 次/分钟（无 Key），无明确月度上限 |
| 图片质量 | 专注壁纸，分辨率极高 |

**Swift 接入示例：**
```swift
struct WallhavenService {
    private let baseURL = "https://wallhaven.cc/api/v1"

    func searchWallpapers(query: String = "landscape", page: Int = 1) async throws -> [[String: Any]] {
        var components = URLComponents(string: "\(baseURL)/search")!
        components.queryItems = [
            URLQueryItem(name: "q", value: query),
            URLQueryItem(name: "sorting", value: "toplist"),
            URLQueryItem(name: "purity", value: "100"),  // SFW only
            URLQueryItem(name: "atleast", value: "1080x1920"),
            URLQueryItem(name: "page", value: "\(page)")
        ]

        let (data, _) = try await URLSession.shared.data(for: URLRequest(url: components.url!))
        let json = try JSONSerialization.jsonObject(with: data) as! [String: Any]
        let dataObj = json["data"] as! [[String: Any]]
        return dataObj
    }
}
```

---

### 5. Lorem Picsum (简单随机图片)

| 项目 | 说明 |
|------|------|
| 官网 | https://picsum.photos |
| API 文档 | https://picsum.photos (页面即文档) |
| 许可证 | Unsplash License（图片来源于 Unsplash） |
| 免费额度 | 无明确限制（合理使用） |
| 图片质量 | 中等，适合占位或轻量壁纸 |

**Swift 接入示例：**
```swift
// 最简单的随机壁纸 URL 生成
let wallpaperURL = URL(string: "https://picsum.photos/1080/1920")!
// 指定图片 ID
let specificURL = URL(string: "https://picsum.photos/id/237/1080/1920")!
// 获取图片列表
let listURL = URL(string: "https://picsum.photos/v2/list?page=1&limit=30")!
```

---

## 二、接入源对比

| 平台 | 免费额度 | 图片质量 | 是否需要 Key | 适合场景 | 商用风险 |
|------|----------|----------|-------------|----------|----------|
| Unsplash | 5000次/时 | ⭐⭐⭐⭐⭐ | 是 | 高质量壁纸展示 | 低 |
| Pexels | 200次/时 | ⭐⭐⭐⭐⭐ | 是 | 壁纸 + 分类浏览 | 低 |
| Pixabay | 100次/分 | ⭐⭐⭐⭐ | 是 | 海量壁纸库 | 低 |
| Wallhaven | 45次/分 | ⭐⭐⭐⭐⭐ | 否（基础） | 专业壁纸 | 中（见风险说明） |
| Lorem Picsum | 无明确限制 | ⭐⭐⭐ | 否 | 快速原型/占位 | 低 |

---

## 三、接入风险说明

### 1. 版权与许可风险

| 风险等级 | 说明 |
|---------|------|
| 🟢 低风险 | **Unsplash / Pexels / Pixabay** — 均提供明确的免费商用许可，无需署名（但建议署名） |
| 🟡 中风险 | **Wallhaven** — 平台本身 API 免费，但图片来源多样，部分图片可能有版权，需自行审核 |
| 🟢 低风险 | **Lorem Picsum** — 图片来源于 Unsplash，遵循 Unsplash License |

**建议：**
- 优先使用 Unsplash / Pexels / Pixabay，它们有清晰的免费商用条款
- 使用 Wallhaven 时建议仅展示 SFW 内容，并在 App 中注明图片来源
- 保存图片的原始来源 URL 和作者信息，以备版权审计

### 2. API 稳定性与可用性风险

| 风险 | 说明 | 缓解措施 |
|------|------|----------|
| API 服务中断 | 第三方服务可能宕机或下线 | 本地缓存已下载图片；设置多源 fallback |
| 速率限制 | 超出配额被封禁 | 实现请求限流；缓存结果减少重复请求 |
| API 变更 | 接口可能升级或废弃 | 抽象 API 层，方便切换数据源 |
| 服务地区限制 | 部分地区可能无法访问 | 使用 CDN 缓存；考虑 App 内置部分壁纸 |

### 3. App Store 审核风险

| 风险 | 说明 | 缓解措施 |
|------|------|----------|
| 内容合规 | 壁纸中可能出现不适内容 | 仅拉取 SFW 内容；实现内容过滤或人工审核 |
| 最低功能要求 | 纯壁纸 App 可能被 Apple 拒绝 | 增加收藏、分类、设置壁纸等交互功能 |
| 隐私政策 | 使用第三方 API 需声明数据收集 | 在隐私政策中声明使用的第三方服务 |
| API Key 安全 | Key 不应硬编码在客户端 | 通过自建后端代理转发 API 请求 |

### 4. 性能与用户体验风险

| 风险 | 说明 | 缓解措施 |
|------|------|----------|
| 高分辨率图片加载慢 | 壁纸通常 > 5MB | 先加载缩略图，按需加载原图 |
| 流量消耗大 | 用户可能产生大量流量 | 提供仅 WiFi 下载选项；压缩预览图 |
| 存储占用 | 大量缓存占用设备空间 | 设置缓存上限；提供清除缓存功能 |

---

## 四、推荐架构

```
┌─────────────────────────────────────────────────┐
│                   iOS App                        │
├─────────────────────────────────────────────────┤
│  WallpaperManager (统一接口层)                    │
│    ├── UnsplashProvider                         │
│    ├── PexelsProvider                           │
│    ├── PixabayProvider                          │
│    └── WallhavenProvider                        │
├─────────────────────────────────────────────────┤
│  ImageCache (NSCache + Disk Cache)              │
├─────────────────────────────────────────────────┤
│  可选：自建后端代理 (隐藏 API Key + 缓存 + 过滤)   │
└─────────────────────────────────────────────────┘
```

**关键设计原则：**
1. **Protocol 抽象** — 定义 `WallpaperProvider` 协议，各平台实现该协议，方便切换
2. **多源 Fallback** — 主源失败自动切换备用源
3. **智能缓存** — 两级缓存（内存 + 磁盘），减少重复请求
4. **后端代理**（推荐） — API Key 不暴露在客户端，后端可做内容审核

---

## 五、快速开始（推荐路径）

1. **注册 API Key** — 在 Unsplash / Pexels 注册开发者账号，获取免费 API Key
2. **创建 Provider 协议** — 定义统一的壁纸获取接口
3. **实现首个 Provider** — 推荐先接 Unsplash（质量最高、文档最好）
4. **添加图片缓存** — 使用 `NSCache` + `FileManager` 或第三方库（如 Kingfisher / SDWebImage）
5. **实现 UI** — CollectionView / SwiftUI LazyVGrid 展示壁纸列表
6. **App Store 准备** — 确保隐私政策完整，内容过滤到位

---

## 六、第三方 Swift 库推荐

| 库名 | 用途 | License |
|------|------|---------|
| [Kingfisher](https://github.com/onevcat/Kingfisher) | 图片下载与缓存 | MIT |
| [SDWebImage](https://github.com/SDWebImage/SDWebImage) | 图片下载与缓存 | MIT |
| [Nuke](https://github.com/kean/Nuke) | 高性能图片加载 | MIT |
| [Alamofire](https://github.com/Alamofire/Alamofire) | 网络请求 | MIT |

---

## 七、总结

| 推荐优先级 | 平台 | 理由 |
|-----------|------|------|
| ⭐ 首选 | Unsplash | 质量最高、许可最清晰、额度充足 |
| ⭐ 备选 | Pexels | 质量优秀、免费商用、有视频资源 |
| 补充 | Pixabay | 数量多、覆盖面广 |
| 专业壁纸 | Wallhaven | 分辨率极高、壁纸专属，但需注意版权 |
| 快速原型 | Lorem Picsum | 零配置、适合开发测试阶段 |

> ⚠️ **重要提示**：以上所有服务的条款可能随时变更，接入前请务必阅读最新的 Terms of Service 和 License Agreement。建议定期检查各平台的 API 变更日志。
