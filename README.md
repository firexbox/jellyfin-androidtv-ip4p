<h1 align="center">Jellyfin for Android TV</h1>
<h3 align="center">Part of the <a href="https://jellyfin.org">Jellyfin Project</a></h3>

---

<p align="center">
<img alt="Logo banner" src="https://raw.githubusercontent.com/jellyfin/jellyfin-ux/master/branding/SVG/banner-logo-solid.svg?sanitize=true"/>
<br/><br/>
<a href="https://github.com/jellyfin/jellyfin-androidtv">
<img alt="GPL 2.0 License" src="https://img.shields.io/github/license/jellyfin/jellyfin-androidtv.svg"/>
</a>
<a href="https://github.com/jellyfin/jellyfin-androidtv/releases">
<img alt="Current Release" src="https://img.shields.io/github/release/jellyfin/jellyfin-androidtv.svg"/>
</a>
<a href="https://translate.jellyfin.org/projects/jellyfin-android/jellyfin-androidtv/">
<img alt="Translation Status" src="https://translate.jellyfin.org/widgets/jellyfin-android/-/jellyfin-androidtv/svg-badge.svg"/>
</a>
<br/>
<a href="https://opencollective.com/jellyfin">
<img alt="Donate" src="https://img.shields.io/opencollective/all/jellyfin.svg?label=backers"/>
</a>
<a href="https://features.jellyfin.org">
<img alt="Feature Requests" src="https://img.shields.io/badge/fider-vote%20on%20features-success.svg"/>
</a>
<a href="https://matrix.to/#/+jellyfin:matrix.org">
<img alt="Chat on Matrix" src="https://img.shields.io/matrix/jellyfin:matrix.org.svg?logo=matrix"/>
</a>
<br/>
<a href="https://play.google.com/store/apps/details?id=org.jellyfin.androidtv">
<img width="153" alt="Jellyfin on Google Play" src="https://jellyfin.org/images/store-icons/google-play.png"/>
</a>
<a href="https://www.amazon.com/gp/aw/d/B07TX7Z725">
<img width="153" alt="Jellyfin on Amazon Appstore" src="https://jellyfin.org/images/store-icons/amazon.png"/>
</a>
<a href="https://f-droid.org/en/packages/org.jellyfin.androidtv/">
<img width="153" alt="Jellyfin on F-Droid" src="https://jellyfin.org/images/store-icons/fdroid.png"/>
</a>
<br/>
<a href="https://repo.jellyfin.org/releases/client/androidtv/">Download archive</a>
</p>

Jellyfin for Android TV is a Jellyfin client for Android TV, Nvidia Shield, and Amazon Fire TV devices. We welcome all contributions and pull
requests! If you have a larger feature in mind please open an issue so we can discuss the implementation before you start. 

## Building

The app uses Gradle and requires the Android SDK. We recommend using Android Studio, which includes all required dependencies, for
development and building. For manual building without Android Studio make sure a compatible JDK and Android SDK are installed and in your
PATH, then use the Gradle wrapper (`./gradlew`) to build the project with the `assembleDebug` Gradle task to generate an apk file:

```shell
./gradlew assembleDebug
```

The task will create an APK file in the `/app/build/outputs/apk/debug` directory. This APK file uses a different app-id from our stable
builds and can be manually installed to your device.

## Branching

The `master` branch is the primary development branch and the target for all pull requests. It is **unstable** and may contain breaking
changes or unresolved bugs. For production deployments and forks, always use the latest `release-x.y.z` branch. Do not base production work
or long-lived forks on `master`.

Release branches are created at the start of a beta cycle and are kept up to date with each published release. Maintainers will cherry-pick
selected changes into release branches as needed for backports. These branches are reused for subsequent patch releases.

## Translating

Translations can be improved very easily from our [Weblate](https://translate.jellyfin.org/projects/jellyfin-android/jellyfin-androidtv)
instance. Look through the following graphic to see if your native language could use some work! We cannot accept changes to translation
files via pull requests.

<p align="center">
<a href="https://translate.jellyfin.org/engage/jellyfin-android/">
<img alt="Detailed Translation Status" src="https://translate.jellyfin.org/widgets/jellyfin-android/-/jellyfin-androidtv/multi-auto.svg"/>
</a>
</p>

---

## 关于此 Fork

本项目 Fork 自 [jellyfin/jellyfin-androidtv](https://github.com/jellyfin/jellyfin-androidtv)，增加了 **IP4P 地址支持**，允许通过 [natmap](https://github.com/heiher/natmap) 的 NAT 穿透方案直连 Jellyfin 服务器，无需公网 IP 或端口转发。

### 主要更改

| 文件 | 更改说明 |
|------|---------|
| `util/Ip4pParser.kt` | **新增** — IP4P 地址检测与解码（`2001::port:hi16:lo16` → `http://ip:port`） |
| `util/Ip4pResolver.kt` | **新增** — DNS AAAA 记录解析，支持域名 → IP4P 地址的自动发现 |
| `util/Ip4pResult.kt` | **新增** — IP4P 解析结果类型 |
| `auth/repository/ServerRepository.kt` | `addServer()` 支持 IP4P 预解析，在 SDK 探测前将 IP4P 地址解码为标准 URL |
| `auth/repository/SessionRepository.kt` | 重连时自动重新 DNS 解析（适配 NAT 映射变化） |
| `auth/model/AuthenticationStoreServer.kt` | 新增 `isIp4p` 字段，持久化 IP4P 服务器类型 |
| `auth/model/Server.kt` | 新增 `isIp4p` 字段 |
| `ui/startup/ServerAddFragment.kt` | 服务器地址输入界面增加 IP4P + HTTPS 复选框 |
| `ui/startup/ServerAddViewModel.kt` | 传递 `isIp4p` / `https` 参数 |
| `ui/startup/SelectServerFragment.kt` | 已存 IP4P 服务器显示 `[IP4P]` 标签 |
| `res/layout/fragment_server_add.xml` | IP4P + HTTPS 复选框布局 |
| `res/values/strings.xml` | 新增 IP4P 相关字符串资源 |

### IP4P 地址格式

```
2001::{port}:{ipv4-hi16}:{ipv4-lo16}

示例: 2001::1f90:cb00:7101
  → IPv4: 203.0.113.1
  → 端口: 8080
  → URL:  http://203.0.113.1:8080
```

### 使用方法

1. 在服务器端部署 [natmap](https://github.com/heiher/natmap)，配置 DNS 更新脚本
2. 在 App 的服务器地址栏输入域名（或原始 IP4P 地址）
3. 勾选 **IP4P address (natmap NAT traversal)** 复选框
4. 可选：勾选 **HTTPS** 复选框
5. 点击 **Connect** — App 通过 DNS AAAA 解析域名，解码 IP4P 地址，直连服务器
6. 服务器列表中 IP4P 服务器会显示 `[IP4P]` 标签，重连时自动重新 DNS 解析

### 前置条件

- 服务器端运行 [natmap](https://github.com/heiher/natmap)
- 所有 NAT 层必须为全锥形（NAT-1）
- DNS AAAA 记录已更新为 IP4P 地址（或手动输入原始 IP4P 地址）

## 开源协议

本项目基于 [Jellyfin for Android TV](https://github.com/jellyfin/jellyfin-androidtv)，沿用其 [GPL-2.0](LICENSE.md) 开源协议。新增代码同样以 GPL-2.0 协议发布。
