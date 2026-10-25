# TVBoxOS

> 基于 [q215613905/TVBoxOS](https://github.com/q215613905/TVBoxOS)（TVBox「俊版 / jun」）的 Android 电视 / 机顶盒影音播放器。本仓库为 fork，用于学习、二次开发与自托管。

TVBoxOS 是一个**通用影音播放与聚合壳（launcher）**：本身不内置任何影视资源，通过可配置的「采集接口（spider）」「解析接口（parses）」「直播源（lives）」拉取并播放内容，支持点播、直播、搜索、弹幕等能力。最低支持 Android 4.4，兼容主流电视与盒子。

## ✨ 功能特性

- **多源采集**：支持 `xml / json / jar / remote` 多种采集接口类型
- **解析 / 嗅探**：`parses` 支持嗅探返回直链与解析返回直链
- **直播**：`lives` 配置直播源，支持自定义 UA、`epg` 节目单、`logo` 台标
- **搜索**：首页搜索与二级页面（如 `vod_tag:folder`）兼容
- **弹幕**：内置弹幕加载与渲染（DanmakuFlameMaster）
- **倍速播放**：支持多档倍速控制
- **多播放内核**：系统播放器 / IJK / EXO 可切换
- **脚本扩展**：通过 QuickJS（JavaScript）与 Pyramid（Python）编写爬虫 / 解析脚本
- **DNS 防污染**：内置 DoH（DNS over HTTPS），默认腾讯 / 阿里 / 360；支持 `hosts` 域名映射
- **向下兼容**：最低 Android 4.4（API 19）

## 🧩 工程结构

| 模块 | 作用 |
| --- | --- |
| `app` | 主 Android 应用模块（Java），UI 与业务入口 |
| `player` | 独立播放器模块，封装 IJK / EXO 等播放内核 |
| `quickjs` | QuickJS JavaScript 引擎绑定，用于运行 JS 版 spider / 解析脚本 |
| `pyramid` | Python 脚本支持模块（对应 `python` 构建变体） |

构建变体（productFlavors）：`java / java32 / java64 / python / python32 / python64`，分别按架构（32 位 / 64 位）与是否内嵌 Python 运行时打包。

## 🛠 构建

### 环境要求

- **JDK 11**（AGPL 工程使用 AGP 7.2.2 / Gradle 7.5，需 JDK 11）
- **Android SDK**：`compileSdk 33` 及对应 `build-tools`
- **Android NDK**：用于 `player` / `quickjs` 原生代码编译
- **Gradle 7.5**（直接使用仓库自带的 `gradlew` 包装器）

### 步骤

```bash
git clone https://github.com/milesyul/TVBoxOS.git
cd TVBoxOS
./gradlew assembleJavaRelease     # 产出 app/build/outputs/apk/release/TVBox_release-java.apk
# 若需内嵌 Python 运行时：
./gradlew assemblePythonRelease
```

> 也可在 Android Studio 中直接打开本工程。Release 构建已开启 R8 混淆（`minifyEnabled true`）。

## ⚙️ 配置接口说明

App 通过一份 JSON 配置描述站点、解析、直播等来源。关键字段如下（与源码中 `app` 默认配置一致）：

```json
{
  "spider": "./your.jar",
  "wallpaper": "./api/img",
  "sites": [],
  "parses": [],
  "hosts": [
    "cache.ott.ystenlive.itv.cmvideo.cn=base-v4-free-mghy.e.cdn.chinamobile.com",
    "cache.ott.bestlive.itv.cmvideo.cn=ip"
  ],
  "lives": [],
  "rules": [],
  "doh": [
    { "name": "騰訊", "url": "https://doh.pub/dns-query" },
    { "name": "阿里", "url": "https://dns.alidns.com/dns-query" },
    { "name": "360",  "url": "https://doh.360.cn/dns-query" }
  ]
}
```

字段含义：

- `searchable`：搜索开关（0 关 / 1 开）
- `filterable`：首页是否可选（0 否 / 1 是）
- `playerType`：播放器类型（0 系统 / 1 IJK / 2 EXO）
- 采集接口类型：`0 xml / 1 json / 3 jar / 4 remote`
- `parses` 解析类型：`0 嗅探（自带播放器）/ 1 解析（返回直链）`
- 直播参数：`ua` 自定义 UA、`epg` 节目单地址、`logo` 台标地址

> 在 App 的「配置」中填入你的订阅地址即可使用：单仓直接填 JSON 地址；多仓填含 `sites` 的 JSON。第三方接口源由社区维护、链接会变动，请选择你有权使用的来源。

## 📜 许可证

本项目基于 **AGPL-3.0** 开源。若你修改并分发（包括以网络服务形式提供），必须以相同许可证开源你的修改，并保留原始版权与许可声明。详见仓库内 `LICENSE`。

## ⚠️ 免责声明

TVBoxOS 是一个**中性的播放器 / 聚合壳**，其可播放内容完全取决于用户自行配置的第三方接口。本仓库：

- 仅提供软件本身与构建方式，**不提供、也不推荐任何特定内容源**；
- 使用者应**仅接入其有权使用的来源**，并自行承担因使用未授权资源带来的法律风险；
- 请勿将本软件用于任何侵权内容的聚合与传播。

## 🙏 致谢

- 上游工程：[q215613905/TVBoxOS](https://github.com/q215613905/TVBoxOS)
- TVBox 开源生态与社区贡献者
